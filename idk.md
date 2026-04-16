# TIPS（必须按实际环境自定义路径，使用之前先看该TIPS）
- [screenshot.py](run_on_chip/screenshot.py)
  - STREAM_CONFIGS：摄像头配置列表，位于第 9 行
  - STREAM_CONFIGS[i]['url']：摄像头 RTSP 地址，首行位于第 10 行
  - STREAM_CONFIGS[i]['output_dir']：图片保存目录（每路摄像头的截图存放路径），首行位于第 10 行
  - STREAM_CONFIGS[i]['name']：摄像头标识前缀（用于文件名与日志），首行位于第 11 行
  - CAPTURE_INTERVAL：截图时间间隔（秒），位于第 24 行
- [sequential_main.py](run_on_chip/sequential_main.py)
  - camera_paths：图片目录与 JSON 输出目录的对应列表，首行位于第 176 行；图片目录需与 screenshot.py 的 output_dir 一一对应
  - --vehicle-model：车辆检测 ONNX 模型相对路径（默认在项目根目录），首行位于第 149 行
  - --license-plate-model：车牌检测 ONNX 模型相对路径（默认在项目根目录），首行位于第 152 行
  - --conf-thres：检测置信度阈值（无需修改，保留默认值即可），位于第 155 行
  - --iou-thres：NMS IoU 阈值（无需修改，保留默认值即可），位于第 156 行
- [json_send.py](data_switch/json_send.py)
  - SERVER_URL：服务器接收接口地址，需要改成实际服务器 IP 与端口，位于第 9 行
  - SOURCE_ROOT：开发板端待上传 JSON 的根目录；脚本会按 CAMERA_MAPPING 中的摄像头名称，到该目录下查找对应子目录中的 JSON 文件，位于第 10 行
  - CHECK_INTERVAL：轮询检查新 JSON 文件的时间间隔（秒），位于第 11 行
  - CAMERA_MAPPING：左值用于开发板发送端查找本地 JSON 文件夹，右值用于服务器接收端确定最终保存目录。例如 `"CAM_15_3": "CAM_4"`：发送端监控 `CAM_15_3` 的文件夹，接收端保存到 `CAM_4` 的文件夹（注意：发送端与接收端的此参数必须保持一致），首行位于第 14 行
- [json_receive.py](data_switch/json_receive.py)
  - RECEIVE_ROOT：服务器端接收并保存 JSON 文件的根目录，位于第 9 行
  - CAMERA_MAPPING：接收端映射规则（注意：必须与 json_send.py 中的 CAMERA_MAPPING 完全一致），首行位于第 13 行
  - 5000 端口：当前服务默认监听 5000 端口；如果服务器端口规划不同，需要同步调整脚本中的端口配置，首行位于第 85 行


- ## 经过测试：CAPTURE_INTERVAL 设为 60 秒时，4 路摄像头运行没有问题；设为 120 秒时，可支持 6 路摄像头运行。

# 停车场车位占用检测与车牌识别系统

本系统包含停车位坐标的预处理生成、在边缘端（开发板）运行的核心检测逻辑，以及局域网内开发板与服务器之间的数据交互。

## 1. 预处理：生成停车位坐标 JSON 配置文件 (`JSON_generation`)
在正式运行检测程序前，需要对各个摄像头画面中的停车位坐标进行标定。

- **前期准备**：从摄像头的实时视频流中截取一张清晰的静态图片（例如 `snapshot.jpg`）作为标定底图。
- **第一步：人工可视化选点** (`JSON_generation/image_coord_selection.py`)
  1. 打开脚本，将底部的 `image_path` 修改为准备好的底图路径，将 `txt_file_name` 修改为保存坐标的 txt 文件路径（例如 `CAM_1_Coord.txt`）。
  2. 运行脚本，将弹出包含截图画面的窗口。
  3. 使用鼠标在画面中依次点击停车位矩形框的**左上角**和**右下角**。系统会自动将这两个点的坐标组合，并写入 txt 文件。
  4. 所有车位标记完成后，按键盘任意键关闭窗口并保存退出。
- **第二步：自动写入 JSON 配置文件** (`JSON_generation/txt_JSON_initialization.py`)
  1. 打开脚本，将 `txt_file_path` 修改为第一步生成的 txt 文件路径，将 `json_file_path` 修改为目标 JSON 配置文件路径。
  2. 运行脚本，程序会自动读取 txt 文件中的坐标对，并将其按顺序覆盖到目标 JSON 文件内对应的停车位坐标字段中（`top_left` 和 `bottom_right`）。

## 2. 核心检测运行逻辑 (`run_on_chip`)
该模块运行在边缘端（开发板），负责截取实时画面、调用多路模型推理逻辑，并生成包含检测结果的 JSON 文件。

> **注意**：代码中调用的 `.onnx` 模型（`yolov8m_including_car_motorbike.onnx` 和 `license_plate_detection.onnx`）已配置为相对路径。请确保这两个模型文件位于本项目的根目录下（即 `run_on_chip` 文件夹的上一级目录）。

**启动顺序与工作流**：
1. **画面截图** (`run_on_chip/screenshot.py`)：
   首先运行截图脚本，该脚本会从配置好的多个摄像头 RTSP 流中循环截取画面，并将图片保存到各自摄像头对应的路径下。
2. **多路循环检测主程序** (`run_on_chip/sequential_main.py`)：
   - 随后运行此主程序。它会监控截图脚本保存图片的目录。
   - 当检测到有最新截取的图片时，`sequential_main.py` 会调用核心的推理逻辑模块 (`run_on_chip/total_logic.py`)。
   - 推理逻辑会加载车辆检测和车牌识别的 ONNX 模型，并结合全局初始化的 PaddleOCR 实例对图像进行分析。
   - 处理完成后，程序会在对应的 JSON 文件夹中生成带有识别结果（车位是否被占用、车牌号等）的新 JSON 文件。

## 3. 数据交互与同步 (`data_switch`)
该模块用于将边缘端（开发板）生成的 JSON 结果文件，通过局域网实时同步到中心端（服务器）。

- **服务器端接收** (`data_switch/json_receive.py`)：
  - 运行环境：Windows 服务器。
  - 功能：基于 Flask 启动 Web 服务（默认监听 5000 端口），提供 `/receive` 接口。接收到数据后，会根据预设的字典规则，将开发板的摄像头 ID 映射为标准目录名，并在服务器本地分类保存这些 JSON 数据。
  - **使用**：应先在服务器端启动此脚本，等待接收数据。

- **开发板端发送** (`data_switch/json_send.py`)：
  - 运行环境：开发板边缘端。
  - 功能：持续轮询监控开发板上存放结果 JSON 的文件夹。一旦发现新生成的 JSON 文件，会立即打包并通过 HTTP POST 请求发送至服务器的接收接口。
  - **使用**：确保配置好服务器的 IP 和接口地址后，在开发板端运行。

TIPS: pt模型文件使用的是相对路径，放在最外层即可，两个文件都在最外层父文件夹下

## Q&A

**Q: 这些代码和文件夹分别应该部署在什么设备上？**
A: 
- 只有 `data_switch/json_receive.py` 需要部署在**服务器**上，用于接收开发板传来的数据。
- 其余代码（包括模型文件、`run_on_chip` 和 `data_switch/json_send.py`）均部署在**开发板（边缘端）**上。
- `CAM_i` 文件夹（如 CAM_1, CAM_2 等）需要放在**开发板**上，程序运行时用于保存截图，并在此生成带检测结果的 JSON 文件。
- `JSON_generation` 目录下的脚本为预处理工具，用于事先生成配置好车位坐标的初始化 JSON 文件，生成完毕后需将这些 JSON 文件放入开发板对应的 `CAM_i/JSON` 文件夹中。

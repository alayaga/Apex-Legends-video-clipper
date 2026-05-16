# MP4 视频无损裁剪工具

面向 **Apex Legends** 游戏录制的后期处理：从一整段对局录像里，快速剪出多段精彩时刻（击杀、团战、高光操作等），导出为独立 MP4，方便剪辑、分享或归档。

基于 Python + tkinter 的桌面小工具，通过 **ffmpeg 流拷贝**（`-c copy`）批量裁剪 MP4 片段，不重新编码视频与音频，保持原始编码、分辨率、码率、帧率等参数不变，仅改变时长。适合 OBS、NVIDIA ShadowPlay 等录屏软件导出的高码率 MP4，避免二次编码带来的画质损失和长时间等待。

## 使用场景

- 一局游戏录成一条长视频，按时间轴标记多处高光，一次性批量导出
- 输出文件名可留空自动生成（如 `3m4s-3m35s.mp4`），也可自定义命名便于区分片段
- 默认输出目录指向 `Videos\Apex Legends`（可在界面中修改）

## 功能特点

- 图形界面，操作简单
- 选择单个 MP4 作为输入，指定输出文件夹
- 支持多组裁剪片段，可添加 / 删除，列表区域可滚动
- 起止时间以「分 + 秒」输入；输出文件名可留空自动生成（如 `3m4s-3m35s.mp4`）
- 后台线程执行裁剪，界面不卡顿，实时日志输出
- 启动时检测 ffmpeg 是否可用

## 环境要求

- **Python 3.10+**（使用了 `str | None` 等类型注解）
- **ffmpeg**：已安装并加入系统 `PATH`
- **tkinter**：Windows / macOS 自带；部分 Linux 需单独安装，例如 `sudo apt install python3-tk`

### 安装 ffmpeg

- Windows：可从 [ffmpeg 官网](https://ffmpeg.org/download.html) 或 [gyan.dev 构建](https://www.gyan.dev/ffmpeg/builds/) 下载，解压后将 `bin` 目录加入 PATH
- macOS：`brew install ffmpeg`
- Linux：`sudo apt install ffmpeg`（或对应发行版包管理器）

安装后在终端执行 `ffmpeg -version` 确认可用。

## 使用方法

1. 克隆或下载本仓库，进入项目目录：

   ```bash
   cd video_cutter
   ```

2. 运行脚本：

   ```bash
   python video_cutter.py
   ```

3. 在界面中：
   - 点击「浏览」选择本局 Apex Legends 的录制 MP4
   - 选择或修改输出文件夹（默认 `Videos\Apex Legends`）
   - 根据回放或记忆，为每组片段填写精彩时刻的起始 / 结束时间（分、秒）及可选的输出文件名
   - 一局有多段高光时，点击「添加片段」继续添加
   - 点击「开始裁剪」，在下方日志区域查看进度与结果

## 裁剪原理

每条片段使用如下命令（参数以列表形式传递给 `subprocess`，正确处理含空格的路径）：

```text
ffmpeg -y -ss <起始秒> -to <结束秒> -i "<输入文件>" -c copy -avoid_negative_ts make_zero "<输出文件>"
```

- `-c copy`：流拷贝，**不重新编码**
- 因关键帧位置，实际切点可能相对设定时间略有偏移，属流拷贝模式的正常现象

## 输入校验

- 分钟、秒须为非负整数
- 结束时间必须大于起始时间
- 输入文件须存在；输出目录不存在时会尝试自动创建

## 项目结构

```text
video_cutter/
├── video_cutter.py   # 主程序
├── README.md
└── .gitignore
```

## 注意事项

- 本工具针对 **MP4** 录制文件设计（常见于游戏录屏）；其他格式可能无法按预期处理
- 输出文件若已存在会被覆盖（`-y`）
- 默认输出路径在脚本内可配置（`DEFAULT_OUTPUT_DIR`，当前为 Apex Legends 视频目录），界面上也可随时修改

## License

可按需自行添加开源协议；未指定时保留所有权利。

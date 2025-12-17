# Real-Time Crowd Counting System using YOLOv8 and MCP

This repository contains the code and resources for a real-time crowd monitoring and autonomous alerting system. The core of the system uses the YOLOv8 object detection model for people counting and exposes this functionality as a standardized external service via the **Model Context Protocol (MCP)**.



---

## 💻 System Components

### 1. The Core Notebook (`mcp crowd .ipynb`)

This Jupyter Notebook encapsulates the entire functionality of the system:

* [cite_start]**Engine:** **YOLOv8n** is used for high-speed object detection, specifically targeting **class ID 0 (person)** for accurate counting[cite: 34, 37].
* [cite_start]**Interface:** The **Model Context Protocol (MCP)** is used to define and expose the crowd-counting capability as a callable tool[cite: 49].
* [cite_start]**Tool:** The central function exposed via MCP is `get_people_count`[cite: 56].
* [cite_start]**Autonomous Alerting:** Includes a side-effect logic to send an immediate email alert via SMTP if the detected people count exceeds the configured `ALERT_THRESHOLD` (default is 2)[cite: 72, 45].
* [cite_start]**Safety Mechanism:** A **60-second cooldown** (`ALERT_COOLDOWN_SEC`) is implemented to prevent email spamming[cite: 74].

### 2. The Model Context Protocol (MCP) Tool

[cite_start]The project implements a network-accessible service using MCP for LLM integration and tool orchestration[cite: 86].

| Component | Detail |
| :--- | :--- |
| **Tool Name** | [cite_start]`get_people_count` [cite: 56] |
| **Inputs** | [cite_start]`image_base64` (Standard MCP input) or `use_camera=True` (for local capture) [cite: 59, 61] |
| **Outputs** | [cite_start]Structured JSON with the detected count (`"count": N`) and the annotated image (`"snapshot_b64"`) [cite: 63] |
| **Deployment** | [cite_start]Served over HTTP using `mcp.serve_http(host="0.0.0.0", port=8080)` [cite: 52, 67] |

---

## ⚙️ Installation and Setup

### Prerequisites
* Python 3.8+
* A local webcam (if using the `use_camera=True` option)
* A Gmail account and an **App Password** for sending alerts (SMTP setup).

### Installation Steps (from Notebook Cell 1)

1.  **Install Dependencies:** Run the following command (or execute Cell 1 in the notebook):
    ```bash
    !pip install ultralytics opencv-python-headless==4.7.0.72 matplotlib numpy pillow
    !pip install fastmcp mcp 
    ```

2.  **Configure Email Settings:** Update **Cell 2** with your specific SMTP credentials and alert email addresses:
    * `SMTP_USER = "your.email@gmail.com"`
    * `SMTP_PASSWORD = "your_app_password"`
    * `ALERT_TO = "recipient@domain.com"`

### Execution

1.  Execute the notebook cells sequentially to define the helper functions, load the YOLO model, and register the MCP tool.
2.  To start the tool as a service, run **Cell 7** to start the HTTP server:
    ```python
    mcp.serve_http(host="0.0.0.0", port=8080)
    ```
3.  To test the service, run **Cell 8** (while the server is running) to simulate a client request:
    ```python
    resp = requests.post(url, json={"use_camera": True})
    print(resp.json())
    ```

---

## 📈 Future Work

The following steps are planned to extend the system's capabilities:
* Containerize the service (Docker) for Cloud Native deployment.
* Integrate the tool directly into a Large Language Model (LLM) for Tool Orchestration.
* Add new MCP tools (e.g., tracking or density estimation) that reuse the YOLO core logic.

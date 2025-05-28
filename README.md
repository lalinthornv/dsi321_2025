# EGAT Real-time Power Generation Scraper

สคริปต์ Python นี้ทำหน้าที่ดึงข้อมูลการผลิตไฟฟ้าแบบเรียลไทม์จากเว็บไซต์การไฟฟ้าฝ่ายผลิตแห่งประเทศไทย (กฟผ.) ที่ URL `https://www.sothailand.com/sysgen/egat/` โดยใช้ Selenium ในการโต้ตอบกับหน้าเว็บ และดึงข้อมูลจาก Console Log ของเบราว์เซอร์ ซึ่งเป็นที่ที่เว็บไซต์อัปเดตข้อมูลแบบไดนามิก ข้อมูลที่ดึงมาได้จะถูกบันทึกลงในไฟล์ CSV

สคริปต์ถูกออกแบบมาให้สามารถทำงานได้อย่างต่อเนื่อง เพื่อเก็บข้อมูลใหม่ตามช่วงเวลาที่กำหนด

## Project Directory Overview

```
DSI321_2025/
├── __pycache__/                         # Compiled Python bytecode
│   └── egat_pipeline.cpython-312.pyc
├── parquet/                             # Directory for Parquet-formatted data
│   └── egat_realtime_power_history.parquet
├── test-scraping/                       # Web scraping components
│   ├── .ipynb_checkpoints/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── run_scraper_and_save_to_lakefs.ipynb
├── UI/                                  # Streamlit interface
│   └── streamlit_app.py
├── docker-compose.yml                   # Docker Compose configuration
├── egat_pipeline.py                     # Main pipeline script
├── egat_realtime_power.csv              # Raw data file from scraping
├── prefect.yaml                         # Prefect configuration file
└── README.md


```

## Technologies and Tools

* **Web Scraping:** Selenium with webdriver_manager
* **Data Validation:** Pydantic
* **Data Storage:** lakeFS
* **Workflow Management:** Prefect
* **Interactive Dashboard:** Streamlit
* **Automation / CI/CD**  GitHub Actions

## System Requirements
* Docker-enabled environment
* Internet connectivity (for accessing EGAT website)
* Minimum of 4 GB RAM
* Available port for Prefect UI (default: localhost:4200)

## Setup Instructions
1. **1. Create and Activate a Virtual Environment:**
```bash
python -m venv .venv

# Windows
.\.venv\Scripts\activate

# macOS/Linux
source .venv/bin/activate
```

2. **Install Required Dependencies:**
```bash
# Windows
pip install -r test-scraping\requirements.txt

# macOS/Linux
pip install -r test-scraping/requirements.txt
```

## Using Prefect for Workflow Management
Setting Up Deployment
To build Docker images, push them to a registry, and pull for execution, use:

If you encounter issues with PREFECT_API_URL, configure it as follows:
```bash
# Windows
$env:PREFECT_API_URL = "http://127.0.0.1:4200/api"

# macOS/Linux
export PREFECT_API_URL="http://127.0.0.1:4200/api"
```

Then run:
```bash
prefect deploy
```

Start a worker to fetch jobs from the specified pool and queue:
```bash
prefect worker start --pool 'default-agent-pool' --work-queue 'default'
```

## Launching the Streamlit Dashboard
```bash
streamlit run UI/streamlit_app.py
```
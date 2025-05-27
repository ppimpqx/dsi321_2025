# ระบบตรวจสอบสภาพอากาศแบบเรียลไทม์สำหรับพื้นที่สมาคมศิษย์เก่าสาธิตรามคำแหง (Real-time Weather Monitoring System for Satitram Alumni Area)
DSI321: Big Data Infrastructure โครงสร้างพื้นฐานคอมพิวเตอร์สำหรับการประมวลผลข้อมูลขนาดใหญ่
##  Introduction

โครงการนี้มีวัตถุประสงค์เพื่อพัฒนาระบบเก็บและแสดงข้อมูลสภาพอากาศแบบเรียลไทม์ ครอบคลุมพื้นที่ สมาคมศิษย์เก่าสาธิตรามคำแหง และบริเวณใกล้เคียงรวม 15 แห่ง โดยดึงข้อมูลจาก OpenWeatherMap API ทุก ๆ 15 นาที และจัดเก็บข้อมูลด้วยระบบ version control ผ่าน LakeFS ซึ่งสามารถตรวจสอบย้อนหลังได้ และเหมาะสมกับการวิเคราะห์ข้อมูลเชิงลึกในระยะยาว

ข้อมูลที่ได้จะถูกแสดงผลแบบ interactive บน Streamlit Dashboard ที่สามารถดูข้อมูลแบบเรียลไทม์ได้ ทั้งอุณหภูมิ ความชื้น ความเร็วลม และปริมาณฝน พร้อมประยุกต์ใช้ K-Means Clustering เพื่อวิเคราะห์รูปแบบของสภาพอากาศ ทำให้สามารถแยกแยะประเภทของสภาพอากาศในแต่ละช่วงเวลา และวางแผนการใช้งานพื้นที่ได้อย่างเหมาะสม

## วัตถุประสงค์:

* ออกแบบและพัฒนาระบบ Data Pipeline ที่สามารถเก็บข้อมูลสภาพอากาศแบบเรียลไทม์จาก OpenWeatherMap API ได้ทุก ๆ 15 นาที สำหรับพื้นที่เป้าหมายและบริเวณใกล้เคียงรวม 15 แห่ง

* จัดเก็บข้อมูลด้วยแนวทาง version-controlled data storage ผ่าน LakeFS เพื่อความถูกต้องและตรวจสอบย้อนกลับได้

* พัฒนา Dashboard ด้วย Streamlit เพื่อให้ผู้ใช้สามารถดูข้อมูลสภาพอากาศแบบ Interactive และเข้าใจง่าย

* ประยุกต์ใช้ Machine Learning (K-Means Clustering) เพื่อจำแนกและวิเคราะห์ประเภทของสภาพอากาศในแต่ละช่วงเวลา

* วางโครงร่างของระบบ Data Engineering ด้วย Docker, Prefect และ LakeFS เพื่อให้ระบบสามารถขยายและนำไปใช้ในบริบทอื่นได้ในอนาคต

## เทคโนโลยีที่ใช้

* Docker: สร้าง environment ที่สามารถทำงานซ้ำได้ (reproducible) รันทุก component ใน environment เดียว เพื่อให้ deploy และ maintain ได้ง่าย

* Prefect: 	ระบบจัดการ workflow ที่สามารถตั้งเวลา (schedule) การเก็บข้อมูลจาก API อัตโนมัติทุก 15 นาที

* LakeFS: ระบบจัดเก็บข้อมูลแบบ version control เพื่อสนับสนุนการย้อนกลับ (rollback) และตรวจสอบความถูกต้องของข้อมูล

* Parquet: 	รูปแบบการจัดเก็บข้อมูลแบบ column-based สำหรับงานวิเคราะห์ข้อมูลขนาดใหญ่

* OpenWeatherMap API:	ดึงข้อมูลสภาพอากาศแบบ real-time จาก 15 พิกัดในพื้นที่รอบสาธิตรามฯ

* K-Means Clustering:	จำแนกลักษณะสภาพอากาศและช่วยให้คำแนะนำในการปฏิบัติตัวแต่ละสถานการณ์

* Streamlit: พัฒนา dashboard แสดงข้อมูลสภาพอากาศแบบ interactive

## โครงสร้างระบบ
### โครงสร้างระบบโดยรวม
* ดึงข้อมูลจาก OpenWeatherMap API ทุก 15 นาที ด้วย Prefect

* แปลงข้อมูลให้อยู่ใน schema ที่เหมาะสม

* จัดเก็บข้อมูลด้วย LakeFS

* วิเคราะห์ด้วย K-means Clustering

* แสดงผลผ่าน Streamlit Dashboard แบบเรียลไทม์

### Data Schema

| คอลัมน์ | คำอธิบาย |
|---------|----------|
| `timestamp` | เวลาเก็บข้อมูล |
| `created_at` | เวลาสร้าง record |
| `minute` | นาทีในแต่ละชั่วโมง |
| `province` | จังหวัดที่ตั้งของสถานที่เก็บข้อมูล |
| `location_name` | ชื่อสถานที่ เช่น Satitram Alumni |
| `api_location` | เขตหรือย่านที่ส่งไปใน API เช่น Bang Kapi	 |
| `weather_main` | ประเภทสภาพอากาศ เช่น Rain, Clear |
| `weather_description` | รายละเอียดสภาพอากาศ เช่น light rain |
| `main.temp` | อุณหภูมิ (°C) |
| `main.humidity` | ความชื้น (%) |
| `wind.speed` | ความเร็วลม (m/s) |
| `precipitation` | ปริมาณฝน (mm) |
| `day`, `hour`, `month`, `year` | รายละเอียดของวันที่และเวลา |

### Docker

* ระบบทุก component ถูกรวบรวมไว้ใน Docker container เดียว เช่น Python, Prefect agent, client ของ LakeFS และ Streamlit

* ช่วยให้ระบบสามารถ reproduce และย้ายไปใช้บนเครื่องอื่นได้ง่าย

* ลดปัญหา dependency conflict และช่วยให้ทีมสามารถพัฒนาและทดสอบได้บน environment เดียวกัน

### Prefect

Prefect ถูกนำมาใช้เป็นระบบจัดการ workflow โดยมีการตั้งเวลาในการดึงข้อมูลทุก 15 นาที ผ่าน Scheduler และ Flow ที่ประกอบด้วยขั้นตอนต่อไปนี้:

* ดึงข้อมูลจาก API: สำหรับ 15 พิกัด

* Data Transformation: แปลงให้อยู่ใน schema ที่เหมาะสม

* จัดเก็บใน LakeFS: แบบ version-controlled

ผู้ใช้งานสามารถดูสถานะ Flow ได้ผ่าน Prefect UI ที่รันบน http://localhost:4200

### LakeFS
เก็บข้อมูลแบบ version-controlled ลงใน object storage โดยใช้ไฟล์ Parquet ซึ่งเหมาะกับ Big Data และระบบ distributed

ตัวอย่างข้อมูลที่จัดเก็บ ได้แก่:
- เวลาที่เก็บ (`timestamp`, `created_at`)
- ตำแหน่ง (`province`, `location_name`, `api_location`)
- สภาพอากาศ (`weather_main`, `weather_description`)
- ตัวแปรทางสภาพอากาศ (`main.temp`, `main.humidity`, `wind.speed`, `precipitation`)
- รายละเอียดเวลา (`day`, `hour`, `month`, `year`)

LakeFS UI รันที่ http://localhost:8001

ตัวอย่างข้อมูลที่เก็บใน LakeFS:
![image](https://github.com/user-attachments/assets/9d9901c3-c94c-4e18-907c-14f0f6f7738f)


### Streamlit Dashboard
สร้าง dashboard แบบ interactive ด้วย Streamlit สำหรับแสดงผลแบบ real-time และย้อนหลัง

ความสามารถหลัก:
- สรุปสภาพอากาศแบบ real-time
- แสดงแนวโน้มอุณหภูมิ, ความชื้น และปริมาณฝนในช่วงเวลา
- แสดงความแปรปรวนของสภาพอากาศรายวัน
- วิเคราะห์ Pattern สภาพอากาศในแต่ละวัน

กราฟ:
  - แนวโน้มอุณหภูมิ / ความชื้น / ปริมาณฝน
  - ความแปรปรวนของอุณหภูมิ
  - Pattern ของสภาพอากาศ
    
ตัวอย่าง Streamlit Dashboard:
![image](https://github.com/user-attachments/assets/a734e5db-1fb9-49a9-ae29-e872636d744c)

![image](https://github.com/user-attachments/assets/db926341-ddfe-4765-9843-30db1cf52d4a)

![image](https://github.com/user-attachments/assets/fc048369-957c-47fd-941d-a353c30c710f)


### Machine Learning: K-means Clustering
นำข้อมูลที่เก็บมา มาใช้วิเคราะห์เชิงลึกโดยใช้เทคนิค K-means Clustering เพื่อจำแนกลักษณะของสภาพอากาศในแต่ละช่วงเวลา

- จำแนกประเภทของสภาพอากาศ (ฝนตกหนัก, ร้อนชื้น, ลมแรง ฯลฯ)
- ใช้ features ได้แก่ `temp`, `humidity`, `wind speed`, `precipitation`
- แสดงผลเชิง context เช่น:
  - **Cluster 1:** ฝนตกหนัก ควรหลีกเลี่ยงการเดินทาง
  - **Cluster 2:** อากาศร้อนและแห้ง ควรระวังปัญหาสุขภาพ

ตัวอย่าง Cluster:
| Cluster | main.temp (°C) | main.humidity (%) | wind.speed (m/s) | precipitation (mm) | Description           |
|---------|----------------|-------------------|-------------------|---------------------|------------------------|
| 0       | 29.505         | 98.5897           | 2.3209            | 0.3013              | ร้อนและชื้น           |
| 1       | 28.565         | 58.5941           | 5.6483            | 0.0908              | ฝนตกและลมแรง          |
| 2       | 29.4798        | 85.8143           | 2.8926            | 0.3359              | เย็นและแห้ง           |


## การใช้งานระบบ
```bash
# Clone Repository
git clone https://github.com/ppimpqx/dsi321_2025.git

cd dsi321_2025

# รัน Docker
docker-compose up --build

# เรียกดู Prefect UI
http://localhost:4200

# เข้าถึง UI ของ LakeFS
http://localhost:8001

# ดู Streamlit Dashboard
http://localhost:8503

# Real-time Weather Monitoring System for Satit Ramkhamhaeng Alumni Area
DSI321: Big Data Infrastructure โครงสร้างพื้นฐานคอมพิวเตอร์สำหรับการประมวลผลข้อมูลขนาดใหญ่
##  Introduction

โครงการนี้มีวัตถุประสงค์เพื่อพัฒนา ระบบเก็บและแสดงข้อมูลสภาพอากาศแบบเรียลไทม์ ครอบคลุมพื้นที่ สมาคมนักเรียนเก่าโรงเรียนสาธิตมหาวิทยาลัยรามคำแหง และบริเวณใกล้เคียงรวม 15 แห่ง โดยดึงข้อมูลจาก OpenWeatherMap API ทุก ๆ 15 นาที และจัดเก็บแบบ version-controlled ด้วย LakeFS เพื่อรองรับการตรวจสอบย้อนหลังและความถูกต้องของข้อมูล

ข้อมูลที่ได้จะถูกแสดงผลบน Streamlit Dashboard ซึ่งสามารถโต้ตอบและดูข้อมูลแบบเรียลไทม์ เช่น อุณหภูมิ ความชื้น ความเร็วลม และปริมาณฝน พร้อมทั้งประยุกต์ใช้ K-Means Clustering เพื่อวิเคราะห์และจำแนกประเภทของสภาพอากาศ ทำให้เข้าใจแนวโน้มในแต่ละพื้นที่ได้ชัดเจนและนำไปใช้ประโยชน์ต่อยอดได้อย่างมีประสิทธิภาพ

## วัตถุประสงค์:

* ออกแบบและพัฒนาระบบ Data Pipeline ที่สามารถเก็บข้อมูลสภาพอากาศแบบเรียลไทม์จาก OpenWeatherMap API ได้ทุก ๆ 15 นาที สำหรับพื้นที่เป้าหมายและบริเวณใกล้เคียงรวม 15 แห่ง

* จัดเก็บข้อมูลด้วยแนวทาง version-controlled data storage ผ่าน LakeFS เพื่อความถูกต้องและตรวจสอบย้อนกลับได้

* พัฒนา Dashboard ด้วย Streamlit เพื่อให้ผู้ใช้สามารถดูข้อมูลสภาพอากาศแบบ Interactive และเข้าใจง่าย

* ประยุกต์ใช้ Machine Learning (K-Means Clustering) เพื่อจำแนกและวิเคราะห์ประเภทของสภาพอากาศในแต่ละช่วงเวลา

* วางโครงร่างของระบบ Data Engineering ด้วย Docker, Prefect และ LakeFS เพื่อให้ระบบสามารถขยายและนำไปใช้ในบริบทอื่นได้ในอนาคต

## เทคโนโลยีที่ใช้

* Docker: สร้าง environment ที่สามารถทำงานซ้ำได้ (reproducible) รันทุก component ใน environment เดียว เพื่อให้ deploy และ maintain ได้ง่าย

* Prefect: จัดการ workflow สำหรับดึงข้อมูลอัตโนมัติทุก 15 นาที

* LakeFS: จัดเก็บข้อมูลพร้อมระบบ version control เพื่อทดลองและ rollback ได้

* Parquet: จัดเก็บข้อมูลแบบ columnar สำหรับประสิทธิภาพสูง

* OpenWeatherMap API:	ดึงข้อมูลสภาพอากาศแบบ real-time จาก 15 พิกัดในพื้นที่รอบสาธิตรามฯ

* K-Means Clustering:	จำแนกลักษณะสภาพอากาศและช่วยให้คำแนะนำในการปฏิบัติตัวแต่ละสถานการณ์

* Streamlit: พัฒนา dashboard แสดงข้อมูลสภาพอากาศแบบ interactive

## โครงสร้างระบบ

### Data Schema

| คอลัมน์ | คำอธิบาย |
|---------|----------|
| `timestamp` | เวลาเก็บข้อมูล (microsecond precision) |
| `created_at` | เวลาสร้าง record |
| `minute` | นาทีในแต่ละชั่วโมง |
| `province` | จังหวัดที่ตั้งของสถานที่เก็บข้อมูล |
| `location_name` | ชื่อสถานที่ เช่น Ramkhamhaeng Alumni |
| `api_location` | เขตหรือย่านที่ส่งไปใน API |
| `weather_main` | ประเภทสภาพอากาศ เช่น Rain, Clear |
| `weather_description` | รายละเอียดสภาพอากาศ เช่น light rain |
| `main.temp` | อุณหภูมิ (°C) |
| `main.humidity` | ความชื้น (%) |
| `wind.speed` | ความเร็วลม (m/s) |
| `precipitation` | ปริมาณฝน (mm) |
| `day`, `hour`, `month`, `year` | รายละเอียดของวันที่และเวลา |

### Docker

ระบบทั้งหมดถูก containerized ด้วย Docker เพื่อให้สามารถ deploy ได้ง่ายและรองรับการทำงานหลายเครื่อง

- รวมทุก dependency ไว้ใน container เดียว เช่น Python, Prefect agent, ไลบรารีสำหรับดึงและประมวลผลข้อมูล, LakeFS client, Streamlit
  
- รองรับการทำงานซ้ำ (reproducible) และย้ายระบบไปใช้งานบนเครื่องอื่นได้ง่าย

### Prefect
Prefect ถูกใช้ในการควบคุม workflow สำหรับการเก็บข้อมูลสภาพอากาศจาก OpenWeatherMap API ทุก ๆ 15 นาทีโดยอัตโนมัติ

- Prefect agent ทำงานใน Docker container
  
- Flow ประกอบด้วย:
  
  - ดึงข้อมูลจาก API (15 พิกัด)
    
  - แปลงข้อมูลให้อยู่ใน schema ที่เหมาะสม
    
  - ส่งข้อมูลไปยังระบบจัดเก็บ (LakeFS)

### LakeFS
เก็บข้อมูลแบบ version-controlled ลงใน object storage โดยใช้ไฟล์ Parquet ซึ่งเหมาะกับ Big Data และระบบ distributed

ข้อมูลที่จัดเก็บ ได้แก่:
- เวลาที่เก็บ (`timestamp`, `created_at`)
- ตำแหน่ง (`province`, `location_name`, `api_location`)
- สภาพอากาศ (`weather_main`, `weather_description`)
- ตัวแปรทางสภาพอากาศ (`main.temp`, `main.humidity`, `wind.speed`, `precipitation`)
- รายละเอียดเวลา (`day`, `hour`, `month`, `year`)

### Streamlit Dashboard
สร้าง dashboard แบบ interactive ด้วย Streamlit สำหรับแสดงผลแบบ real-time และย้อนหลัง

ความสามารถหลัก:
- สรุปสภาพอากาศแบบ real-time
- แนวโน้มอุณหภูมิ, ความชื้น และปริมาณฝนในช่วงเวลา
- แสดงความแปรปรวนของสภาพอากาศรายวัน
- วิเคราะห์ Pattern สภาพอากาศในแต่ละวัน

กราฟ:
  - แนวโน้มอุณหภูมิ
  - ความชื้น
  - ปริมาณฝน
  - ความแปรปรวนของอุณหภูมิ
  - Pattern ของสภาพอากาศ

### Machine Learning: K-means Clustering
นำข้อมูลที่เก็บมา มาใช้วิเคราะห์เชิงลึกโดยใช้เทคนิค K-means Clustering เพื่อจำแนกลักษณะของสภาพอากาศในแต่ละช่วงเวลา

- จำแนกประเภทของสภาพอากาศ (ฝนตกหนัก, ร้อนชื้น, ลมแรง ฯลฯ)
- ใช้ features ได้แก่ `temp`, `humidity`, `wind speed`, `precipitation`
- แสดงผลเชิง context เช่น:
  - **Cluster 1:** ฝนตกหนัก ควรหลีกเลี่ยงการเดินทาง
  - **Cluster 2:** อากาศร้อนและแห้ง ควรระวังปัญหาสุขภาพ

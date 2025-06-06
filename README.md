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



## Benefits
โครงการระบบวิเคราะห์ข้อมูลสภาพอากาศแบบเรียลไทม์บริเวณสมาคมศิษย์เก่าโรงเรียนสาธิตรามคำแหงและพื้นที่ใกล้เคียง มอบประโยชน์ที่หลากหลายให้กับผู้มีส่วนเกี่ยวข้อง ทั้งในเชิงการศึกษา การใช้งานจริง และในระดับองค์กร โดยสอดคล้องกับเป้าหมายของการเรียนรู้ในโครงการ papapipeline และการประยุกต์ใช้ในสถานการณ์จริง

3.1 Educational Benefits
* Hands-On Learning:
ฝึกพัฒนา Data Pipeline แบบครบวงจร ตั้งแต่การเก็บข้อมูล (ingestion), การประมวลผล (processing), การจัดเก็บข้อมูล (storage), การจัดการเวิร์กโฟลว์ (orchestration) ไปจนถึงการแสดงผล (visualization) ช่วยให้เข้าใจแนวคิดสำคัญในการเรียนรู้ด้าน Data Engineering

* Modern Tools:
โครงการใช้เครื่องมือมาตรฐานในอุตสาหกรรม ได้แก่ Prefect 3, Docker และ Streamlit ช่วยให้นักศึกษาเตรียมความพร้อมสำหรับงานสาย Data Engineer จริง

* Real-World Application:
แสดงให้เห็นถึงการนำทฤษฎี เช่น การจัดการเวิร์กโฟลว์ มาประยุกต์ใช้กับปัญหาในชีวิตจริง ช่วยเสริมความน่าสนใจของเนื้อหาวิชา

* Open-Source Contribution:
โค้ดและเอกสารใน GitHub Repository ของโครงการสามารถแชร์ต่อชุมชน เปิดโอกาสให้ผู้เรียนหรือผู้สนใจนำไปต่อยอด สนับสนุนแนวคิดการเรียนรู้แบบเปิด (open-source) ภายใต้โครงการ papapipeline

3.2 Practical Benefits
* Real-Time Monitoring:
ช่วยให้คนในพื้นที่ เช่น ครู เจ้าหน้าที่ หรือผู้จัดกิจกรรม สามารถติดตามสภาพอากาศบริเวณโรงเรียนได้แบบเรียลไทม์ ช่วยในการวางแผนกิจกรรมกลางแจ้งหรือตอบสนองต่อสภาพอากาศที่เปลี่ยนแปลง

* Scalability:
ด้วยโครงสร้างระบบที่ออกแบบแบบโมดูลาร์ สามารถขยายได้ในอนาคต เช่น เพิ่มพื้นที่เฝ้าระวัง เพิ่มแหล่งข้อมูล หรือย้ายขึ้นคลาวด์ได้อย่างยืดหยุ่น

* User-Friendly Visualization:
Dashboard ที่พัฒนาด้วย Streamlit มีหน้าตาที่เป็นมิตรกับผู้ใช้ ช่วยให้ผู้ที่ไม่มีความรู้ด้านเทคนิคสามารถเข้าใจและเข้าถึงข้อมูลได้ง่าย

* Weather-Based Recommendations (K-Means Clustering):
ระบบได้นำเทคนิค Machine Learning อย่าง K-Means Clustering มาใช้ในการจัดกลุ่มข้อมูลสภาพอากาศ เช่น อุณหภูมิ ความชื้น และความเร็วลม เพื่อจำแนกสภาพอากาศออกเป็นกลุ่มต่าง ๆ จากนั้นใช้ผลลัพธ์ในการแนะนำแนวทางการปฏิบัติ

3.3 Organizational Benefits
* Low-Cost Prototype:
ระบบถูกพัฒนาจากเครื่องมือแบบใช้งานฟรี เช่น OpenWeatherMap API, Parquet, Streamlit ซึ่งลดต้นทุนในการพัฒนา แต่ยังคงมีประสิทธิภาพสูง

* Foundation for Growth:
ทำหน้าที่เป็นต้นแบบ (Proof-of-Concept) สำหรับองค์กรที่ต้องการเริ่มต้นใช้ระบบข้อมูลแบบเรียลไทม์ สามารถนำไปปรับใช้และขยายต่อได้ตามความต้องการ

* Community Engagement:
ส่งเสริมการเรียนรู้ด้าน Data Engineering และการพัฒนาระบบแบบเปิดในชุมชนท้องถิ่น เช่น โรงเรียนหรือหน่วยงานในพื้นที่ประเทศไทย ซึ่งเป็นเป้าหมายสำคัญของ papapipeline

## บทสรุป
โครงการระบบจัดการข้อมูลสภาพอากาศแบบเรียลไทม์บริเวณสมาคมศิษย์เก่าโรงเรียนสาธิตรามคำแหงและพื้นที่ใกล้เคียงประสบความสำเร็จในการพัฒนาและติดตั้งระบบที่สามารถดึงข้อมูลสภาพอากาศจาก API ได้ทุก ๆ 15 นาที พร้อมประมวลผล จัดเก็บในรูปแบบ Parquet และแสดงผลผ่านแดชบอร์ดแบบโต้ตอบได้ด้วย Streamlit ซึ่งระบบทั้งหมดถูกออกแบบภายใต้สภาพแวดล้อมแบบ Docker และควบคุมด้วย Prefect 3 ทำให้สามารถกำหนดเวลาการทำงานอัตโนมัติ และขยายต่อได้ในอนาคต

การดำเนินงานในโครงการนี้ประสบผลสำเร็จในหลายด้าน ได้แก่:

* การเก็บข้อมูลสภาพอากาศที่มีความแม่นยำและต่อเนื่อง

* การแสดงผลแบบ interactive dashboard ที่เข้าถึงได้ง่ายสำหรับผู้ใช้ทั่วไป

* การประยุกต์ใช้เครื่องมือโอเพ่นซอร์สสมัยใหม่ เช่น Prefect 3, Docker, Streamlit

* การพัฒนาโมเดล Machine Learning ให้คำแนะนำการปฏิบัติตัวต่อสภาพอากาศนั้น ๆ

ข้อเสนอแนะและแนวทางการพัฒนาในอนาคต:

* ขยายพื้นที่การเก็บข้อมูลให้ครอบคลุมหลายจุดในกรุงเทพฯ เพื่อเปรียบเทียบและวิเคราะห์ระดับพื้นที่ (microclimate)

* เพิ่มระบบแจ้งเตือนอัตโนมัติ (alert) เมื่อพบสภาพอากาศที่อาจมีผลกระทบ เช่น ฝนตกหนัก หรืออุณหภูมิสูงผิดปกติ

* พัฒนาโมเดล Machine Learning เพิ่มเติมเพื่อคาดการณ์อุณหภูมิหรือปริมาณฝนล่วงหน้า

* ปรับปรุงประสิทธิภาพของระบบจัดเก็บข้อมูลให้สามารถเชื่อมต่อกับฐานข้อมูลในระบบคลาวด์สำหรับการใช้งานในระดับองค์กร

* เปิดให้สาธารณะเข้าถึงผ่านเว็บไซต์หรือ API ภายนอก เพื่อให้ชุมชนหรือผู้พัฒนารายอื่นสามารถนำข้อมูลไปใช้ได้อย่างง่ายดาย

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

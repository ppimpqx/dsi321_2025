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

### Docker

ใช้ Docker รวมทุก dependency ภายใน container เดียว เช่น Python, Prefect agent, ไลบรารี, LakeFS client และ Streamlit เพื่อให้ระบบสามารถ deploy และ reproduce ได้ง่ายทุกเครื่อง

✅ Docker Desktop ใช้ควบคุมและ monitor container ได้อย่างมีประสิทธิภาพ

✅ ระบบมีความเสถียร รองรับการทำงานหลายเครื่อง

### Prefect
ควบคุม workflow สำหรับ pipeline การดึงข้อมูลอัตโนมัติทุก 15 นาที โดย Prefect agent ทำงานใน Docker container เดียวกัน
ขั้นตอนสำคัญใน flow:

ดึงข้อมูลจาก OpenWeatherMap API (15 สถานที่)

แปลงข้อมูลให้เหมาะกับการจัดเก็บ

ส่งต่อข้อมูลไปยัง LakeFS

✅ รองรับ retry อัตโนมัติเมื่อเกิด error
✅ ตรวจสอบสถานะ flow แต่ละขั้นได้ชัดเจน

### LakeFS
เก็บข้อมูลแบบ version-controlled ลงใน object storage โดยใช้ไฟล์ Parquet ซึ่งเหมาะกับ Big Data และระบบ distributed เช่น Spark

ข้อมูลที่จัดเก็บ:

timestamp

location (ละติจูด, ลองจิจูด, จังหวัด, ชื่อสถานที่)

weather condition

temperature, humidity, wind speed, rainfall

วัน/เวลา

✅ ใช้ branching ทดลองแก้ไขข้อมูลโดยไม่กระทบกับข้อมูลจริง
✅ รองรับการ rollback หากเกิดข้อผิดพลาด

### Streamlit Dashboard
สร้าง dashboard เพื่อแสดงผล:

สภาพอากาศแบบ real-time

ข้อมูลย้อนหลัง

กราฟแนวโน้ม (อุณหภูมิ, ความชื้น, ปริมาณฝน)

Summary ข้อมูลในแต่ละช่วงเวลา

ความแปรปรวนของอุณหภูมิ

pattern ของสภาพอากาศในแต่ละวัน

✅ Backend เชื่อมต่อกับ Parquet บน LakeFS โดยตรง
✅ โหลดข้อมูลเร็วและยืดหยุ่นในการแสดงผล

### Machine Learning: K-means Clustering
วิเคราะห์ลักษณะสภาพอากาศจากข้อมูลจริง

จำแนกกลุ่ม weather pattern ที่คล้ายกัน

แสดงผลเชิงบริบทให้ผู้ใช้เข้าใจง่าย เช่น "ฝนตกหนัก ควรหลีกเลี่ยงการเดินทาง"

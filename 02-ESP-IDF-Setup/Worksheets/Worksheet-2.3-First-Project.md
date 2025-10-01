# Worksheet 2.3: การสร้างโปรเจคแรก
## การสร้างและทดสอบโปรเจค ESP-NOW แรก

### 🎯 วัตถุประสงค์
- สร้างโปรเจค ESP-NOW จากเริ่มต้น
- ทดสอบการทำงานของระบบ
- เรียนรู้ project structure
- เตรียมพร้อมสำหรับ development

---

## การสร้างโปรเจค

### ขั้นตอนการสร้าง
```bash
idf.py create-project my_first_espnow
cd my_first_espnow
```

### โครงสร้างโปรเจค
```
my_first_espnow/
├── main/
│   ├── main.c
│   └── CMakeLists.txt
├── CMakeLists.txt
└── sdkconfig
```

---

## โค้ดตัวอย่าง
[โค้ด ESP-NOW พื้นฐาน]

---

## การทดสอบ
1. Build project
2. Flash to ESP32
3. Monitor output

---

**✅ เสร็จสิ้นการเรียนรู้บทที่ 2**
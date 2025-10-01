# Worksheet 1.3: การวิเคราะห์ Architecture
## การศึกษาโครงสร้างและสถาปัตยกรรมของ ESP-NOW

### 🎯 วัตถุประสงค์
- เข้าใจสถาปัตยกรรมของ ESP-NOW อย่างลึกซึ้ง
- วิเคราะห์ Protocol Stack และ Data Flow
- ศึกษา Hardware และ Software Architecture
- ประเมินประสิทธิภาพและ Limitations

### 🛠️ ความรู้ที่ต้องมี
- พื้นฐาน Network Protocol Stack
- ความเข้าใจ Hardware Architecture
- การทำงานของ Wireless Communication
- ESP-IDF Framework เบื้องต้น

---

## ส่วนที่ 1: Protocol Stack Analysis

### 1.1 OSI Model Mapping

ESP-NOW ทำงานในระดับใดของ OSI Model? กรอกตารางต่อไปนี้:

| OSI Layer | Layer Name | ESP-NOW Component | หน้าที่ |
|-----------|------------|-------------------|---------|
| Layer 7 | Application | _________________ | _______ |
| Layer 6 | Presentation | _________________ | _______ |
| Layer 5 | Session | _________________ | _______ |
| Layer 4 | Transport | _________________ | _______ |
| Layer 3 | Network | _________________ | _______ |
| Layer 2 | Data Link | _________________ | _______ |
| Layer 1 | Physical | _________________ | _______ |

### 1.2 ESP-NOW Protocol Stack

วาดไดอะแกรม Protocol Stack ของ ESP-NOW:

```
[Application Layer]
    ↕
[________________]
    ↕  
[________________]
    ↕
[________________]
    ↕
[802.11 MAC Layer]
    ↕
[802.11 PHY Layer]
```

**คำถาม:**
1. **ESP-NOW ใช้ IEEE 802.11 ในส่วนไหนบ้าง?**
   ___________________________________________________

2. **ทำไม ESP-NOW ไม่ต้องใช้ TCP/IP Stack?**
   ___________________________________________________

---

## ส่วนที่ 2: Hardware Architecture Analysis

### 2.1 ESP32 Block Diagram

วาดและอธิบาย ESP32 components ที่เกี่ยวข้องกับ ESP-NOW:

```
[CPU Cores]  ──┐
               │
[WiFi Radio] ──┼── [Memory] ── [Peripherals]
               │
[Bluetooth]  ──┘
```

**คำอธิบาย:**
- CPU Cores: ________________________________________
- WiFi Radio: _______________________________________
- Memory: __________________________________________
- Peripherals: ______________________________________

### 2.2 RF (Radio Frequency) Path Analysis

ESP-NOW ใช้ความถี่ใดในการสื่อสาร?

1. **Frequency Band:** ______________________________
2. **Channel Range:** _______________________________  
3. **Channel Width:** _______________________________
4. **Modulation:** __________________________________

**แบบฝึกหัด:** คำนวณจำนวน Channel ที่ใช้ได้:
- เริ่มต้น: Channel 1 (2412 MHz)
- สิ้นสุด: Channel 13 (2472 MHz)  
- Channel spacing: 5 MHz
- จำนวน Channel: _______________________________

---

## ส่วนที่ 3: Software Architecture Deep Dive

### 3.1 ESP-IDF Framework Integration

วิเคราะห์วิธีที่ ESP-NOW รวมตัวกับ ESP-IDF:

```
Application Code
      ↓
ESP-NOW API
      ↓
[_______________]
      ↓
[_______________]  
      ↓
Hardware Abstraction Layer (HAL)
      ↓
ESP32 Hardware
```

กรอกส่วนที่ขาด และอธิบายหน้าที่ของแต่ละส่วน:

1. **ESP-NOW API:** ________________________________
2. **ชั้นที่ 3:** ____________________________________
3. **ชั้นที่ 4:** ____________________________________
4. **HAL:** ________________________________________

### 3.2 Memory Architecture

ESP-NOW ใช้ Memory ของ ESP32 อย่างไร?

**Memory Types ใน ESP32:**

| Memory Type | Size | ESP-NOW Usage | Purpose |
|-------------|------|---------------|---------|
| SRAM | 520 KB | _____________ | _______ |
| Flash | 4 MB+ | _____________ | _______ |
| RTC Memory | 8 KB | _____________ | _______ |
| Cache | 32 KB | _____________ | _______ |

**คำถาม:**
1. **Peer information เก็บใน Memory ประเภทใด?**
   ___________________________________________________

2. **Incoming messages buffer อยู่ที่ไหน?**
   ___________________________________________________

---

## ส่วนที่ 4: Data Structure Analysis

### 4.1 Peer Information Structure

วิเคราะห์ `esp_now_peer_info_t` structure:

```c
typedef struct {
    uint8_t peer_addr[6];    // MAC address
    uint8_t lmk[16];         // Local master key
    uint8_t channel;         // WiFi channel  
    wifi_interface_t ifidx;  // WiFi interface
    bool encrypt;            // Encryption flag
} esp_now_peer_info_t;
```

**การวิเคราะห์:**
1. **ทำไม MAC address ใช้ 6 bytes?**
   ___________________________________________________

2. **Local master key (lmk) ใช้สำหรับอะไร?**
   ___________________________________________________

3. **Channel field มีความสำคัญอย่างไร?**
   ___________________________________________________

4. **wifi_interface_t คืออะไร?**
   ___________________________________________________

### 4.2 Message Header Analysis

ESP-NOW message มี header หรือไม่? วิเคราะห์:

```
[ESP-NOW Message Format]
┌─────────────────┬─────────────────┬─────────────────┐
│                 │                 │                 │
│                 │                 │                 │  
│                 │                 │                 │
└─────────────────┴─────────────────┴─────────────────┘
```

กรอกส่วนประกอบของ ESP-NOW message:
1. **ส่วนที่ 1:** ___________________________________
2. **ส่วนที่ 2:** ___________________________________
3. **ส่วนที่ 3:** ___________________________________

---

## ส่วนที่ 5: Timing และ Performance Analysis

### 5.1 Transmission Timing

วิเคราะห์เวลาในการส่งข้อมูล ESP-NOW:

```
[Timeline Analysis]
t0: Application calls esp_now_send()
    ↓ (Processing Time: ___ ms)
t1: Data reaches WiFi layer  
    ↓ (Transmission Time: ___ ms)
t2: Data transmitted over air
    ↓ (Processing Time: ___ ms) 
t3: Receiver gets data
    ↓ (Callback Time: ___ ms)
t4: Application processes data

Total Latency: _______ ms
```

**คำถาม:**
1. **ส่วนไหนใช้เวลานานที่สุด?**
   ___________________________________________________

2. **วิธีการลด Latency:**
   ___________________________________________________

### 5.2 Throughput Calculation

**โจทย์:** คำนวณ Maximum Throughput ของ ESP-NOW

กำหนด:
- Maximum packet size: 250 bytes
- Minimum interval between packets: 1 ms
- Protocol overhead: 10%

**คำนวณ:**
1. **Effective data per packet:** ___________________
2. **Packets per second:** __________________________  
3. **Maximum throughput:** __________________________
4. **เปรียบเทียบกับ WiFi 802.11n:** __________________

---

## ส่วนที่ 6: Security Architecture

### 6.1 Encryption Mechanism

ESP-NOW รองรับการเข้ารหัสอย่างไร?

```
[Encryption Process]
Plaintext Data
      ↓
[________________]
      ↓
[________________]
      ↓  
Encrypted Data
      ↓
Transmission
```

กรอกขั้นตอนการเข้ารหัส:
1. **ขั้นตอนที่ 1:** ________________________________
2. **ขั้นตอนที่ 2:** ________________________________

**คำถาม:**
1. **ESP-NOW ใช้ Encryption algorithm อะไร?**
   ___________________________________________________

2. **Key management ทำอย่างไร?**
   ___________________________________________________

3. **การเข้ารหัสมีผลต่อ Performance อย่างไร?**
   ___________________________________________________

### 6.2 Security Limitations

วิเคราะห์ข้อจำกัดด้าน Security ของ ESP-NOW:

| Security Aspect | ESP-NOW Support | Limitation | Impact |
|-----------------|-----------------|------------|--------|
| Authentication | ______________ | __________ | ______ |
| Authorization | ______________ | __________ | ______ |
| Data Integrity | ______________ | __________ | ______ |
| Replay Protection | ______________ | __________ | ______ |
| Forward Secrecy | ______________ | __________ | ______ |

---

## ส่วนที่ 7: Error Handling Architecture

### 7.1 Error Types และ Handling

ESP-NOW มี Error handling mechanism อย่างไร?

**Error Categories:**
1. **Transmission Errors:**
   - ประเภท: _____________________________________
   - การจัดการ: __________________________________

2. **Configuration Errors:**  
   - ประเภท: _____________________________________
   - การจัดการ: __________________________________

3. **Resource Errors:**
   - ประเภท: _____________________________________
   - การจัดการ: __________________________________

### 7.2 Callback Architecture

วิเคราะห์ Callback system ของ ESP-NOW:

```c
// Send callback
void on_data_sent(const uint8_t *mac_addr, esp_now_send_status_t status);

// Receive callback  
void on_data_recv(const uint8_t *mac_addr, const uint8_t *data, int len);
```

**คำถาม:**
1. **Callback functions ทำงานใน context ใด?**
   ___________________________________________________

2. **ข้อจำกัดในการประมวลผลใน callback:**
   ___________________________________________________

3. **วิธีการปรับปรุงประสิทธิภาพ callback:**
   ___________________________________________________

---

## ส่วนที่ 8: Scalability Analysis

### 8.1 Peer Limitation Impact

ESP-NOW รองรับ 20 peers maximum มีผลกระทบอย่างไร?

**สถานการณ์:** Smart Home กับ 50 อุปกรณ์

```
[Network Architecture Design]

Central Hub
    ↓
[________________]
    ↓
[________________]
    ↓
End Devices (50 units)
```

**การแก้ปัญหา:**
1. **วิธีที่ 1:** ___________________________________
2. **วิธีที่ 2:** ___________________________________  
3. **วิธีที่ 3:** ___________________________________

### 8.2 Multi-hop Communication

ESP-NOW รองรับ Multi-hop หรือไม่?

**การวิเคราะห์:**
1. **Native multi-hop support:**
   ___________________________________________________

2. **วิธีการสร้าง multi-hop manually:**
   ___________________________________________________

3. **ข้อดี-ข้อเสียของ multi-hop:**
   ___________________________________________________

---

## ส่วนที่ 9: Power Management Architecture

### 9.1 Power States

ESP32 มี Power states ใดบ้างที่เกี่ยวข้องกับ ESP-NOW?

| Power State | Current Draw | ESP-NOW Capability | Wake-up Time |
|-------------|--------------|-------------------|--------------|
| Active | ____________ | _________________ | ____________ |
| Light Sleep | ____________ | _________________ | ____________ |
| Deep Sleep | ____________ | _________________ | ____________ |

### 9.2 Battery Life Calculation

**โจทย์:** Battery-powered sensor node
- Active time: 100ms (send data)
- Deep sleep: 60 seconds
- Battery: 2000 mAh
- Active current: 100 mA
- Deep sleep current: 10 μA

**คำนวณ:**
1. **Duty cycle:** ___________________________________
2. **Average current:** _____________________________
3. **Battery life:** ________________________________

---

## ส่วนที่ 10: Real-world Implementation Challenges

### 10.1 Interference Analysis

WiFi 2.4 GHz band มี interference จากแหล่งใดบ้าง?

**Interference Sources:**
1. **WiFi Networks:** _______________________________
2. **Bluetooth Devices:** ___________________________
3. **Microwave Ovens:** _____________________________
4. **Industrial Equipment:** _________________________

**วิธีการจัดการ Interference:**
- _______________________________________________
- _______________________________________________
- _______________________________________________

### 10.2 Range และ Obstacle Effects

วิเคราะห์ผลกระทบของสิ่งกีดขวางต่อการสื่อสาร:

| Obstacle Type | Signal Loss (dB) | Range Reduction | Mitigation |
|---------------|------------------|-----------------|------------|
| Wall (Concrete) | ______________ | ______________ | __________ |
| Wall (Wood) | ______________ | ______________ | __________ |
| Metal Sheet | ______________ | ______________ | __________ |
| Water | ______________ | ______________ | __________ |

---

## ส่วนที่ 11: Comparative Architecture Analysis

### 11.1 ESP-NOW vs WiFi Direct

เปรียบเทียบสถาปัตยกรรม:

| Architecture Aspect | ESP-NOW | WiFi Direct |
|-------------------|---------|-------------|
| Connection Setup | _______ | ___________ |
| Protocol Stack | _______ | ___________ |
| Security Layer | _______ | ___________ |
| Power Consumption | _______ | ___________ |
| Memory Usage | _______ | ___________ |

### 11.2 ESP-NOW vs Bluetooth LE

| Architecture Aspect | ESP-NOW | Bluetooth LE |
|-------------------|---------|--------------|
| Discovery Process | _______ | ____________ |
| Pairing Mechanism | _______ | ____________ |
| Data Transmission | _______ | ____________ |
| Error Recovery | _______ | ____________ |

---

## ส่วนที่ 12: Future Enhancement Analysis

### 12.1 Potential Improvements

วิเคราะห์การปรับปรุงที่เป็นไปได้:

1. **Protocol Level:**
   - ปัญหาปัจจุบัน: _______________________________
   - การปรับปรุงที่เสนอ: ____________________________

2. **Hardware Level:**
   - ปัญหาปัจจุบัน: _______________________________
   - การปรับปรุงที่เสนอ: ____________________________

3. **Software Level:**
   - ปัญหาปัจจุบัน: _______________________________
   - การปรับปรุงที่เสนอ: ____________________________

### 12.2 Integration Possibilities

ESP-NOW สามารถผสานกับเทคโนโลยีอื่นได้อย่างไร?

1. **ESP-NOW + WiFi:**
   ___________________________________________________

2. **ESP-NOW + Bluetooth:**
   ___________________________________________________

3. **ESP-NOW + LoRa:**
   ___________________________________________________

---

## ส่วนที่ 13: Practical Design Exercise

### 13.1 Architecture Design Challenge

**โจทย์:** ออกแบบระบบ Environmental Monitoring

**Requirements:**
- 100 sensor nodes (temperature, humidity, air quality)
- Coverage area: 1 km²
- Data collection every 5 minutes
- Battery life: 2 years minimum
- Central monitoring station
- Real-time alerts for critical values

**ให้ออกแบบ:**

1. **Overall System Architecture:**
   ```
   [วาดไดอะแกรม System Architecture]
   
   
   
   
   ```

2. **Network Topology:**
   ```
   [วาดไดอะแกรม Network Topology]
   
   
   
   
   ```

3. **Data Flow Design:**
   ```
   [วาดไดอะแกรม Data Flow]
   
   
   
   
   ```

4. **Power Management Strategy:**
   ___________________________________________________

5. **Error Recovery Mechanism:**
   ___________________________________________________

### 13.2 Performance Estimation

สำหรับระบบที่ออกแบบ คำนวณ:

1. **Total data throughput:** ________________________
2. **Network load per node:** _______________________
3. **Battery life estimation:** ______________________
4. **Maximum latency:** _____________________________
5. **System reliability:** ___________________________

---

## ✅ สรุปการเรียนรู้

หลังจากทำ Worksheet นี้เสร็จ นักเรียนควรสามารถ:

- [ ] เข้าใจสถาปัตยกรรมของ ESP-NOW ในทุกระดับ
- [ ] วิเคราะห์ Protocol Stack และ Data Flow ได้
- [ ] ประเมินประสิทธิภาพและข้อจำกัดได้
- [ ] ออกแบบระบบที่ใช้ ESP-NOW ได้อย่างมีประสิทธิภาพ
- [ ] เปรียบเทียบกับเทคโนโลยีอื่นได้อย่างลึกซึ้ง

### 🎯 Architecture Understanding Assessment:

ให้คะแนนความเข้าใจ (1-5):

- Protocol Stack: ___/5
- Hardware Architecture: ___/5
- Software Integration: ___/5
- Performance Analysis: ___/5
- Security Architecture: ___/5
- Scalability Considerations: ___/5

**Total Score: ___/30**

### 📝 Key Takeaways:

เขียนสรุป 3 สิ่งสำคัญที่ได้เรียนรู้:

1. ___________________________________________________
2. ___________________________________________________
3. ___________________________________________________

---

**🎉 ยินดีด้วย! คุณได้เสร็จสิ้นการศึกษาบทที่ 1: Introduction แล้ว**

**⏭️ พร้อมสำหรับ [บทที่ 2: ESP-IDF Setup](../../02-ESP-IDF-Setup/02-ESP-IDF-Setup.md)**
# Worksheet 1.1: ESP-NOW Setup แรก
## การทดลองและการตั้งค่าพื้นฐาน

### 🎯 วัตถุประสงค์
- ทำความเข้าใจกับการตั้งค่า ESP-NOW เบื้องต้น
- ทดลองโค้ดพื้นฐานสำหรับการสื่อสาร
- เรียนรู้การใช้งาน MAC Address และ Peer Management

### 📝 ความรู้ที่ต้องมี
- พื้นฐาน C Programming
- การใช้งาน ESP-IDF พื้นฐาน
- การเชื่อมต่อ ESP32 กับคอมพิวเตอร์

---

## ส่วนที่ 1: การเตรียมอุปกรณ์

### 🔧 อุปกรณ์ที่ต้องใช้
- [ ] ESP32 Development Board x 2 ตัว
- [ ] สาย USB x 2 เส้น (หรือใช้สลับกัน)
- [ ] คอมพิวเตอร์ที่ติดตั้ง ESP-IDF แล้ว
- [ ] Terminal หรือ Command Prompt

### ✅ Checklist การเตรียมความพร้อม
- [ ] ESP-IDF ติดตั้งเรียบร้อย (`idf.py --version` ทำงานได้)
- [ ] ESP32 ทั้ง 2 ตัวเชื่อมต่อและ Flash ได้
- [ ] Serial Monitor เปิดใช้งานได้
- [ ] เตรียมพื้นที่ทำงานในระยะ 2-3 เมตร

---

## ส่วนที่ 2: การสร้างโปรเจคพื้นฐาน

### ขั้นตอนที่ 1: สร้างโปรเจค ESP-NOW ตัวแรก

1. **เปิด ESP-IDF Command Prompt/Terminal**

2. **สร้างโปรเจคใหม่:**
   ```bash
   idf.py create-project espnow_basic_test
   cd espnow_basic_test
   ```

3. **แก้ไขไฟล์ main/CMakeLists.txt:**
   ```cmake
   idf_component_register(
       SRCS "main.c"
       INCLUDE_DIRS "."
       REQUIRES esp_wifi esp_now nvs_flash
   )
   ```

### ขั้นตอนที่ 2: สร้างโค้ดทดสอบพื้นฐาน

แก้ไขไฟล์ `main/main.c`:

```c
#include <stdio.h>
#include <string.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_system.h"
#include "esp_wifi.h"
#include "esp_event.h"
#include "esp_log.h"
#include "nvs_flash.h"
#include "esp_now.h"

static const char* TAG = "ESP_NOW_BASIC";

// ฟังก์ชันเริ่มต้น WiFi
void wifi_init(void) {
    ESP_ERROR_CHECK(esp_netif_init());
    ESP_ERROR_CHECK(esp_event_loop_create_default());
    
    wifi_init_config_t cfg = WIFI_INIT_CONFIG_DEFAULT();
    ESP_ERROR_CHECK(esp_wifi_init(&cfg));
    ESP_ERROR_CHECK(esp_wifi_set_storage(WIFI_STORAGE_RAM));
    ESP_ERROR_CHECK(esp_wifi_set_mode(WIFI_MODE_STA));
    ESP_ERROR_CHECK(esp_wifi_start());
    
    ESP_LOGI(TAG, "✅ WiFi initialized for ESP-NOW");
}

// ฟังก์ชันเริ่มต้น ESP-NOW
void espnow_init(void) {
    ESP_ERROR_CHECK(esp_now_init());
    ESP_LOGI(TAG, "✅ ESP-NOW initialized successfully");
}

// ฟังก์ชันแสดง MAC Address
void print_mac_address(void) {
    uint8_t mac[6];
    esp_wifi_get_mac(WIFI_IF_STA, mac);
    ESP_LOGI(TAG, "📍 My MAC Address: %02X:%02X:%02X:%02X:%02X:%02X", 
             mac[0], mac[1], mac[2], mac[3], mac[4], mac[5]);
    
    printf("\n=== MAC ADDRESS INFO ===\n");
    printf("Device MAC: %02X:%02X:%02X:%02X:%02X:%02X\n", 
           mac[0], mac[1], mac[2], mac[3], mac[4], mac[5]);
    printf("========================\n\n");
}

void app_main(void) {
    ESP_ERROR_CHECK(nvs_flash_init());
    
    wifi_init();
    espnow_init();
    print_mac_address();
    
    ESP_LOGI(TAG, "🚀 ESP-NOW Basic Test completed!");
    ESP_LOGI(TAG, "💡 Copy the MAC address above for use in other devices");
    
    while (1) {
        ESP_LOGI(TAG, "⏰ System running... (every 5 seconds)");
        vTaskDelay(pdMS_TO_TICKS(5000));
    }
}
```

---

## ส่วนที่ 3: การทดลองและการบันทึกผล

### การทดลองที่ 1: ทดสอบการเริ่มต้น ESP-NOW

1. **Build และ Flash โปรแกรม:**
   ```bash
   idf.py build flash monitor
   ```

2. **บันทึกผลการทดลอง:**

   **ผลลัพธ์ที่ได้รับ:**
   ```
   I (xxx) ESP_NOW_BASIC: ✅ WiFi initialized for ESP-NOW
   I (xxx) ESP_NOW_BASIC: ✅ ESP-NOW initialized successfully
   I (xxx) ESP_NOW_BASIC: 📍 My MAC Address: XX:XX:XX:XX:XX:XX
   ```

   **MAC Address ของอุปกรณ์ตัวที่ 1:**
   ```
   ___:___:___:___:___:___
   ```

3. **Flash โปรแกรมเดียวกันลงอุปกรณ์ตัวที่ 2**

   **MAC Address ของอุปกรณ์ตัวที่ 2:**
   ```
   ___:___:___:___:___:___
   ```

---

## ส่วนที่ 4: การทดสอบ Peer Management

### การทดลองที่ 2: เพิ่ม Peer และทดสอบการส่งข้อมูล

แก้ไขโค้ดในส่วน `app_main()`:

```c
void app_main(void) {
    ESP_ERROR_CHECK(nvs_flash_init());
    
    wifi_init();
    espnow_init();
    print_mac_address();
    
    // MAC Address ของอุปกรณ์อีกตัว (แก้ไขให้ถูกต้อง)
    uint8_t peer_mac[6] = {0x24, 0x6F, 0x28, 0xAA, 0xBB, 0xCC};
    
    // เพิ่ม Peer
    esp_now_peer_info_t peer_info = {};
    memcpy(peer_info.peer_addr, peer_mac, 6);
    peer_info.channel = 0;
    peer_info.encrypt = false;
    
    esp_err_t result = esp_now_add_peer(&peer_info);
    if (result == ESP_OK) {
        ESP_LOGI(TAG, "✅ Peer added successfully");
    } else {
        ESP_LOGE(TAG, "❌ Failed to add peer: %s", esp_err_to_name(result));
    }
    
    while (1) {
        ESP_LOGI(TAG, "⏰ System running... (every 5 seconds)");
        vTaskDelay(pdMS_TO_TICKS(5000));
    }
}
```

### คำถามสำหรับการทดลอง:

1. **อะไรเกิดขึ้นเมื่อ MAC Address ในโค้ดไม่ตรกับ MAC Address จริง?**
   
   คำตอบ: ________________________________

2. **ฟังก์ชัน `esp_now_add_peer()` return ค่าอะไรเมื่อสำเร็จ?**
   
   คำตอบ: ________________________________

3. **Channel ที่ตั้งเป็น 0 หมายความว่าอย่างไร?**
   
   คำตอบ: ________________________________

---

## ส่วนที่ 5: การทดลองการส่งข้อมูลพื้นฐาน

### การทดลองที่ 3: ส่งข้อความทดสอบ

เพิ่มฟังก์ชันสำหรับส่งข้อมูล:

```c
// Callback เมื่อส่งข้อมูลเสร็จ
void on_data_sent(const uint8_t *mac_addr, esp_now_send_status_t status) {
    if (status == ESP_NOW_SEND_SUCCESS) {
        ESP_LOGI(TAG, "✅ Message sent successfully to: %02X:%02X:%02X:%02X:%02X:%02X", 
                 mac_addr[0], mac_addr[1], mac_addr[2], 
                 mac_addr[3], mac_addr[4], mac_addr[5]);
    } else {
        ESP_LOGE(TAG, "❌ Failed to send message");
    }
}

// เพิ่มในฟังก์ชัน espnow_init()
void espnow_init(void) {
    ESP_ERROR_CHECK(esp_now_init());
    ESP_ERROR_CHECK(esp_now_register_send_cb(on_data_sent));
    ESP_LOGI(TAG, "✅ ESP-NOW initialized with callback");
}
```

แก้ไข main loop เพื่อส่งข้อมูล:

```c
while (1) {
    static int counter = 0;
    char message[50];
    sprintf(message, "Hello ESP-NOW! Count: %d", counter++);
    
    esp_err_t result = esp_now_send(peer_mac, (uint8_t*)message, strlen(message) + 1);
    if (result == ESP_OK) {
        ESP_LOGI(TAG, "📤 Sending: %s", message);
    } else {
        ESP_LOGE(TAG, "❌ Send failed: %s", esp_err_to_name(result));
    }
    
    vTaskDelay(pdMS_TO_TICKS(3000)); // ส่งทุก 3 วินาที
}
```

### การบันทึกผล:

**สิ่งที่สังเกตได้จาก Serial Monitor:**

1. ข้อความที่แสดงเมื่อส่งสำเร็จ:
   ```
   ________________________________
   ```

2. ข้อความที่แสดงเมื่อส่งไม่สำเร็จ:
   ```
   ________________________________
   ```

3. ความถี่ในการส่งข้อมูล:
   ```
   ทุก _____ วินาที
   ```

---

## ส่วนที่ 6: การตรวจสอบและ Troubleshooting

### ปัญหาที่อาจพบและวิธีแก้ไข:

| ปัญหา | สาเหตุที่เป็นไปได้ | วิธีแก้ไข |
|--------|-------------------|-----------|
| ESP-NOW init ไม่สำเร็จ | WiFi ยังไม่เริ่มต้น | เรียก `wifi_init()` ก่อน `esp_now_init()` |
| ไม่สามารถ add peer ได้ | MAC Address ผิด | ตรวจสอบ MAC Address |
| ส่งข้อมูลไม่สำเร็จ | Peer ยังไม่ถูกเพิ่ม | เรียก `esp_now_add_peer()` ก่อน |
| Serial Monitor ไม่แสดงผล | Baud rate ไม่ตรง | ตั้ง baud rate เป็น 115200 |

### การตรวจสอบระบบ (System Check):
- [ ] WiFi เริ่มต้นสำเร็จ
- [ ] ESP-NOW เริ่มต้นสำเร็จ
- [ ] MAC Address แสดงถูกต้อง
- [ ] Peer เพิ่มสำเร็จ
- [ ] ส่งข้อมูลได้โดยไม่มี error

---

## ส่วนที่ 7: คำถามทบทวน

### 📋 แบบทดสอบความเข้าใจ:

1. **ESP-NOW ต้องการ Internet connection หรือไม่? เพราะอะไร?**

2. **ทำไมต้องเรียก `esp_wifi_init()` ก่อน `esp_now_init()`?**

3. **MAC Address มีความสำคัญอย่างไรใน ESP-NOW?**

4. **Callback function `on_data_sent()` ทำงานเมื่อไร?**

5. **ถ้าต้องการส่งข้อมูลไปยัง ESP32 หลายตัว ต้องทำอย่างไร?**

### 💡 คำถามเพิ่มเติม:

6. **ข้อมูลที่ส่งผ่าน ESP-NOW สามารถมีขนาดสูงสุดเท่าไร?**

7. **การเข้ารหัสใน ESP-NOW ทำได้อย่างไร?**

8. **ถ้าอุปกรณ์ 2 ตัวอยู่ห่างกันมาก การสื่อสารจะเป็นอย่างไร?**

---

## ส่วนที่ 8: การบ้านและกิจกรรมขยาย

### 🏠 การบ้าน:
1. **ทดลองส่งข้อมูลประเภทต่างๆ** (int, float, struct)
2. **ทดสอบการส่งข้อมูลในระยะทางต่างๆ** (1m, 5m, 10m)
3. **ศึกษาเพิ่มเติมเกี่ยวกับ WiFi Channel ใน ESP-NOW**

### 🔬 กิจกรรมขยายความรู้:
1. **สร้างระบบแจ้งเตือนแบบง่าย** - เมื่อกดปุ่มที่อุปกรณ์หนึ่ง LED ที่อีกตัวจะกะพริบ
2. **ทดลองส่งข้อมูล sensor** - อ่านค่าจาก potentiometer ส่งไปแสดงที่อีกตัว
3. **ศึกษา Error handling** - จัดการกับกรณีที่การส่งข้อมูลล้มเหลว

---

## ✅ สรุปผลการเรียนรู้

หลังจากทำ Worksheet นี้เสร็จ นักเรียนควรสามารถ:

- [ ] ตั้งค่า ESP-NOW เบื้องต้นได้
- [ ] เข้าใจการทำงานของ MAC Address และ Peer Management
- [ ] ส่งข้อมูลพื้นฐานผ่าน ESP-NOW ได้
- [ ] ใช้ Callback function ได้
- [ ] แก้ไขปัญหาเบื้องต้นได้

### 📈 ระดับความเข้าใจ (ให้นักเรียนประเมินตนเอง):
- [ ] เริ่มต้น (1-3) - ทำตามขั้นตอนได้
- [ ] ปานกลาง (4-6) - เข้าใจหลักการและแก้ปัญหาเบื้องต้นได้  
- [ ] ขั้นสูง (7-10) - สามารถประยุกต์และขยายความรู้ได้

---

**⏭️ พร้อมสำหรับ [Worksheet 1.2: ทำความเข้าใจ Concepts](Worksheet-1.2-Understanding-Concepts.md)**
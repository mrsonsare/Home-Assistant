# ESP32 6-Relay Board with Home Assistant (Wyse 3040 + SSD Architecture)

This project demonstrates a **stable, low-storage-friendly Home Assistant setup**
using a **Dell Wyse 3040 thin client**, **external USB SSD**, **Docker**, and
an **ESP32-based 6-relay board flashed with ESPHome**.

The architecture is designed specifically for devices with **very limited internal storage**.

---

## 🧠 System Architecture Overview


### Hardware
- Dell Wyse 3040 (low internal eMMC storage)
- External USB SSD (primary data storage)
- ESP32 6-channel relay board

### Key Design Decisions
- Internal storage is used **only for OS**
- All heavy data (Docker, Home Assistant, ESPHome) is moved to **USB SSD**
- ESP32 runs independently and connects directly to Home Assistant
- ESPHome server is **optional** (CLI or Docker-based)

---

## 📂 Final Storage Layout
```bash
Internal Storage (8GB eMMC):
└── DietPi OS only
    ├── System files
    ├── Kernel
    └── Basic utilities

External SSD (/mnt/usbssd):
├── docker/          # Docker data root
├── homeassistant/   # Home Assistant config
└── esphome/        # ESPHome configs (optional)
```


Internal storage:
- DietPi OS only
- No Docker images
- No build cache
- No risk of disk-full crashes

---

## 🐳 Docker Installation (Data Root on SSD)

Docker is installed using DietPi and forced to store all data on the SSD.

```bash 

dietpi-software install 162

{
  "data-root": "/mnt/usbssd/docker"
}

docker info | grep "Docker Root Dir"
```

## 🏠 Home Assistant Installation (Docker)
```bash 
docker run -d \
  --name homeassistant \
  --restart unless-stopped \
  --network host \
  -v /mnt/usbssd/homeassistant:/config \
  -v /etc/localtime:/etc/localtime:ro \
  ghcr.io/home-assistant/home-assistant:stable
```
#### Access:
```bash
http://<WYSE_IP>:8123
```
## ESPHome Docker (Optional)
```bash
docker run -d \
  --name esphome \
  --restart unless-stopped \
  -p 6052:6052 \
  -v /mnt/usbssd/esphome:/config \
  ghcr.io/esphome/esphome
```
#### Access
```bash
http://<WYSE_IP>:6052

```

## 🔌 ESP32 Integration with Home Assistant

### Firmware

The ESP32 is flashed with ESPHome firmware (esp32-6relay.yaml).

#### Once flashed:

* ESP32 connects to Wi-Fi

* Communicates directly with Home Assistant using ESPHome API

* ESPHome server is NOT required to run continuously

### Adding ESP32 to Home Assistant :

#### Go to Settings → Devices & Services

#### ESPHome device is auto-discovered

#### Click Configure

#### Enter API encryption key if prompted

#### Manual method:

* Add ESPHome integration
* Enter ESP32 IP address
* Provide API key

| Relay   | GPIO   |
| ------- | ------ |
| Relay 1 | GPIO14 |
| Relay 2 | GPIO27 |
| Relay 3 | GPIO26 |
| Relay 4 | GPIO25 |
| Relay 5 | GPIO33 |
| Relay 6 | GPIO32 |

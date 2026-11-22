# Praktikum IoT: REST API Node-RED

**Nama:** Nabila Adhelia
**NPM:** 2410010105
**Mata Kuliah:** Pemrograman Internet of Things

## Deskripsi
Sistem monitoring dan kontrol IoT dengan alur:
ESP32 (simulator Python) -> MQTT (Mosquitto) -> Node-RED (REST API) -> Dashboard web (HTML/JS).

## File
| File | Fungsi |
|---|---|
| `simulasi_esp32.py` | Simulator ESP32, mengirim suhu dan kelembaban ke topik `iot/sensor` tiap 3 detik |
| `flow.json` | Flow Node-RED (backend REST API), diimpor lewat menu Import |
| `dashboard.html` | Dashboard web yang mengambil data lewat REST API |

## Endpoint
| Endpoint | Method | Fungsi |
|---|---|---|
| `/api/suhu` | GET | Data sensor terakhir |
| `/api/history` | GET | 50 data sensor terakhir |
| `/api/kontrol` | POST | Kirim perintah lampu ke topik `iot/kontrol` |

## Cara Menjalankan
1. Pastikan Mosquitto berjalan (service).
2. Jalankan Node-RED: `node-red`, buka `http://localhost:1880`.
3. Menu **Import**, pilih `flow.json`, lalu **Deploy**.
4. Aktifkan CORS di `settings.js` (hapus tanda `//` pada `httpNodeCors`), lalu restart Node-RED. Langkah ini diperlukan karena node OPTIONS pada Node-RED versi baru tidak mendukung wildcard.
5. Instal dependensi dan jalankan simulator:
   ```
   pip install paho-mqtt
   python simulasi_esp32.py
   ```
6. Buka `dashboard.html` di browser.

## Hasil Pengujian

### 1. Simulator ESP32
![Simulator ESP32](1-simulator.png)

### 2. Flow Node-RED (status connected)
![Flow Node-RED](2-nodered-flow.png)

### 3. Endpoint /api/suhu
![API suhu](3-api-suhu.png)

### 4. Dashboard (bagian atas)
![Dashboard atas](4-dashboard-atas.png)

### 5. Dashboard (tabel history dan log)
![Dashboard bawah](5-dashboard-bawah.png)

### 6. Bukti pesan MQTT pada topik iot/kontrol
![MQTT kontrol](6-mqtt-kontrol.png)

## Pengembangan (Nilai Tambah)

### Grafik real-time dengan Chart.js
Dashboard menampilkan grafik suhu dan kelembaban (20 data terakhir) yang diperbarui otomatis menggunakan Chart.js, diambil lewat endpoint `/api/history`.

![Grafik Chart.js](7-chartjs.png)


### Autentikasi API key
Semua endpoint (`/api/suhu`, `/api/history`, `/api/kontrol`) memeriksa header `X-API-Key`. Permintaan tanpa key yang benar ditolak dengan status **401 Unauthorized**.

Pengujian dengan curl:
```
curl -i http://localhost:1880/api/suhu
curl -i http://localhost:1880/api/suhu -H "X-API-Key: iot-kampus-2410010105"
```
- Tanpa key: `401` dan `{"status":"error","message":"API key tidak valid"}`
- Dengan key: `200` dan data sensor

`dashboard.html` mengirim header ini otomatis pada setiap permintaan.

![API key](8-api-key.png)

> Catatan: key ditulis langsung di kode hanya untuk keperluan belajar. Pada sistem sungguhan, key disimpan di pengaturan server dan tidak ditaruh di file dashboard.

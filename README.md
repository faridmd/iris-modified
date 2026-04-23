# Custom Iris X8 Octacopter for Gazebo & ArduPilot

Repositori ini berisi custom model Gazebo untuk drone konfigurasi X8 Octacopter (berbasis sasis Iris). Model ini sudah disesuaikan konfigurasi propeller (Prop In/Prop Out) dan pemetaan channel motornya agar dapat terbang stabil dengan ArduPilot SITL.

## Persyaratan
Pastikan Anda sudah menginstal perangkat lunak berikut di dalam lingkungan Linux (atau WSL):
- Gazebo Classic (versi 11)
- ArduPilot (dikonfigurasi untuk SITL)

---

## Langkah Instalasi

### 1. Clone Repositori
Unduh source code ke dalam direktori home (`~`) Anda:

```bash
git clone https://github.com/faridmd/iris-modified
```

### 2. Daftarkan Gazebo Model Path
Tambahkan direktori model custom ini ke dalam environment variable `GAZEBO_MODEL_PATH` agar file `.sdf` dan mesh dapat dibaca oleh Gazebo:

```bash
echo 'export GAZEBO_MODEL_PATH=~/iris-modified/models:$GAZEBO_MODEL_PATH' >> ~/.bashrc
source ~/.bashrc
```

### 3. Modifikasi Parameter Bawaan ArduPilot
Agar ArduPilot mengenali drone sebagai OctaQuad (X8) dan bukan Quadcopter standar, kita perlu menyesuaikan parameter SITL-nya. Buka file parameter bawaan ArduPilot:

```bash
nano ~/ardupilot/Tools/autotest/default_params/gazebo-iris.parm
```

Ubah `FRAME_CLASS` menjadi `4` dan `FRAME_TYPE` menjadi `1`. Pastikan isi file tersebut seperti berikut:

```plaintext
# Iris is X8 frame
FRAME_CLASS     4
FRAME_TYPE      1

# IRLOCK FEATURE
RC8_OPTION 39
PLND_ENABLED    1
PLND_TYPE       3

# SONAR FOR IRLOCK
SIM_SONAR_SCALE 10
RNGFND1_TYPE 1
RNGFND1_SCALING 10
RNGFND1_PIN 0
RNGFND1_MAX_CM 5000
```

(Simpan perubahan dan keluar dari editor).

---

## Cara Menjalankan Simulasi

Anda akan membutuhkan dua jendela terminal terpisah untuk menjalankan flight controller (SITL) dan simulator fisika (Gazebo) secara bersamaan.

### Terminal 1: Menjalankan ArduPilot SITL

```bash
cd ~/ardupilot/ArduCopter
../Tools/autotest/sim_vehicle.py -f gazebo-iris --console --map
```

(Tunggu hingga SITL selesai melakukan kompilasi dan MAVProxy siap menerima perintah).

### Terminal 2: Menjalankan Dunia Gazebo

```bash
cd ~/iris-modified
gazebo --verbose worlds/tole_x8_arducopter_runway.world
```

Setelah kedua terminal berjalan dan saling terhubung, Anda bisa memberikan perintah melalui konsol MAVProxy (contoh: `mode guided`, `arm throttle`, `takeoff 2`) dan melihat drone mengudara dengan mulus di Gazebo.

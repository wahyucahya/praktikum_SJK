# Tutorial Lengkap: Konfigurasi Jaringan Cisco dengan Router dan Switch

## 📋 Daftar Isi
1. [Topologi Jaringan](#topologi-jaringan)
2. [Persiapan Perangkat](#persiapan-perangkat)
3. [Menghubungkan Perangkat](#menghubungkan-perangkat)
4. [Konfigurasi Router X](#konfigurasi-router-x)
5. [Konfigurasi Router 1](#konfigurasi-router-1)
6. [Konfigurasi Router 2](#konfigurasi-router-2)
7. [Konfigurasi PC](#konfigurasi-pc)
8. [Testing Konektivitas](#testing-konektivitas)
9. [Troubleshooting](#troubleshooting)

---

## 🌐 Topologi Jaringan

```
        Internet (Cloud)
               |
          RouterX Gi0/1
         (203.0.113.2)
               |
          RouterX Gi0/0
         (10.10.10.1/29)
               |
            SWITCH
           /      \
          /        \
     Router1      Router2
    (10.10.10.2) (10.10.10.3)
    Gi0/0        Gi0/0
         |            |
    Router1 Gi0/1  Router2 Gi0/1
   (192.168.1.1)  (192.168.2.1)
         |            |
       PC1          PC2
  (192.168.1.10) (DHCP Client)
    IP Static    192.168.2.11-254
```

### Penjelasan Topologi:
- **RouterX**: Router utama yang menghubungkan jaringan internal ke Internet
- **Router1 & Router2**: Router cabang untuk jaringan lokal
- **Switch**: Menghubungkan RouterX dengan Router1 dan Router2
- **PC1**: Komputer dengan IP static di jaringan 192.168.1.0/24
- **PC2**: Komputer dengan IP dinamis (DHCP) di jaringan 192.168.2.0/24

---

## 🛠️ Persiapan Perangkat

### 1. Buka Cisco Packet Tracer

### 2. Tambahkan Perangkat dari Menu:

**Network Devices → Routers:**
- Pilih **Router 2911** (atau yang tersedia)
- Drag 3 router ke workspace
- Beri nama: RouterX, Router1, Router2

**Network Devices → Switches:**
- Pilih **Switch 2960**
- Drag 1 switch ke workspace

**End Devices → End Devices:**
- Pilih **PC-PT**
- Drag 2 PC ke workspace
- Beri nama: PC1, PC2

**Network Devices → WAN Emulation:**
- Pilih **Cloud-PT**
- Drag 1 cloud ke workspace

### 3. Layout Perangkat:

Susun perangkat seperti topologi:
```
      [Cloud]
         |
     [RouterX]
         |
      [Switch]
      /      \
[Router1]  [Router2]
    |          |
  [PC1]      [PC2]
```

---

## 🔌 Menghubungkan Perangkat

### Jenis Kabel yang Digunakan:
Gunakan **Copper Straight-Through** (kabel hitam dengan garis lurus) untuk SEMUA koneksi.

### Langkah Menghubungkan:

1. **Klik ikon kabel** (petir oranye) di pojok kiri bawah
2. **Pilih Copper Straight-Through** (kabel hitam)
3. **Hubungkan perangkat** sesuai tabel di bawah:

| Dari | Port | Ke | Port |
|------|------|-------|------|
| Cloud | Ethernet6 | RouterX | GigabitEthernet0/1 |
| RouterX | GigabitEthernet0/0 | Switch | FastEthernet0/1 |
| Switch | FastEthernet0/2 | Router1 | GigabitEthernet0/0 |
| Switch | FastEthernet0/3 | Router2 | GigabitEthernet0/0 |
| Router1 | GigabitEthernet0/1 | PC1 | FastEthernet0 |
| Router2 | GigabitEthernet0/1 | PC2 | FastEthernet0 |

### Tips:
- Klik perangkat pertama → pilih port → klik perangkat kedua → pilih port
- Kabel akan muncul **merah** dulu, nanti akan berubah **hijau** setelah dikonfigurasi
- Port bisa berbeda tergantung model router, sesuaikan dengan yang tersedia

---

## ⚙️ Konfigurasi Router X

### 1. Buka CLI Router X:
- Klik **RouterX**
- Pilih tab **CLI**

### 2. Lewati Dialog Awal:
```
Would you like to enter the initial configuration dialog? [yes/no]: no
Press RETURN to get started!
```
Ketik: **no** lalu tekan **Enter**

### 3. Masuk ke Mode Privileged:
```
Router> enable
Router#
```

### 4. Konfigurasi RouterX:

**Copy dan paste command di bawah ini SATU BLOK:**

```cisco
configure terminal
hostname RouterX

interface GigabitEthernet0/0
 ip address 10.10.10.1 255.255.255.248
 no shutdown
 exit

interface GigabitEthernet0/1
 ip address 203.0.113.2 255.255.255.0
 no shutdown
 exit

interface GigabitEthernet0/2
 shutdown
 exit

access-list 1 permit 10.10.10.0 0.0.0.7
access-list 1 permit 192.168.1.0 0.0.0.255
access-list 1 permit 192.168.2.0 0.0.0.255

ip nat inside source list 1 interface GigabitEthernet0/1 overload

interface GigabitEthernet0/1
 ip nat outside
 exit

interface GigabitEthernet0/0
 ip nat inside
 exit

ip route 0.0.0.0 0.0.0.0 203.0.113.1
ip route 192.168.1.0 255.255.255.0 10.10.10.2
ip route 192.168.2.0 255.255.255.0 10.10.10.3

end
write memory
```

### 5. Verifikasi Konfigurasi:

```cisco
show ip interface brief
```

**Output yang diharapkan:**
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     10.10.10.1      YES manual up                    up
GigabitEthernet0/1     203.0.113.2     YES manual up                    up
GigabitEthernet0/2     unassigned      YES unset  administratively down down
```

✅ Jika Gi0/0 dan Gi0/1 menunjukkan **up up**, RouterX berhasil dikonfigurasi!

### Penjelasan Konfigurasi RouterX:

- **Gi0/0 (10.10.10.1/29)**: Interface ke Switch untuk jaringan internal
- **Gi0/1 (203.0.113.2/24)**: Interface ke Cloud/Internet
- **NAT**: Mengubah IP private menjadi IP public untuk akses internet
- **Static Routes**: Mengarahkan traffic ke Router1 dan Router2

---

## ⚙️ Konfigurasi Router 1

### 1. Buka CLI Router 1:
- Klik **Router1**
- Pilih tab **CLI**
- Ketik: **no** untuk skip dialog

### 2. Konfigurasi Router1:

```cisco
enable
configure terminal
hostname Router1

interface GigabitEthernet0/0
 ip address 10.10.10.2 255.255.255.248
 no shutdown
 exit

interface GigabitEthernet0/1
 ip address 192.168.1.1 255.255.255.0
 no shutdown
 exit

interface GigabitEthernet0/2
 shutdown
 exit

ip route 0.0.0.0 0.0.0.0 10.10.10.1

end
write memory
```

### 3. Verifikasi:

```cisco
show ip interface brief
```

**Output yang diharapkan:**
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     10.10.10.2      YES manual up                    up
GigabitEthernet0/1     192.168.1.1     YES manual up                    up
```

### 4. Test Koneksi ke RouterX:

```cisco
ping 10.10.10.1
```

**Harusnya ada reply dengan tanda seru (!!!!!):**
```
!!!!!
Success rate is 100 percent (5/5)
```

✅ Router1 berhasil dikonfigurasi!

### Penjelasan Konfigurasi Router1:

- **Gi0/0 (10.10.10.2/29)**: Terhubung ke Switch untuk komunikasi dengan RouterX
- **Gi0/1 (192.168.1.1/24)**: Gateway untuk jaringan PC1
- **Default Route**: Semua traffic yang tidak diketahui dikirim ke RouterX (10.10.10.1)

---

## ⚙️ Konfigurasi Router 2

### 1. Buka CLI Router 2:
- Klik **Router2**
- Pilih tab **CLI**
- Ketik: **no** untuk skip dialog

### 2. Konfigurasi Router2:

```cisco
enable
configure terminal
hostname Router2

interface GigabitEthernet0/0
 ip address 10.10.10.3 255.255.255.248
 no shutdown
 exit

interface GigabitEthernet0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown
 exit

interface GigabitEthernet0/2
 shutdown
 exit

ip dhcp excluded-address 192.168.2.1 192.168.2.10

ip dhcp pool LAN2
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 8.8.8.8
 exit

ip route 0.0.0.0 0.0.0.0 10.10.10.1

end
write memory
```

### 3. Verifikasi:

```cisco
show ip interface brief
show ip dhcp pool
```

**Output interface:**
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     10.10.10.3      YES manual up                    up
GigabitEthernet0/1     192.168.2.1     YES manual up                    up
```

**Output DHCP Pool:**
```
Pool LAN2 :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0
 Total addresses                : 254
 Leased addresses               : 0
```

### 4. Test Koneksi:

```cisco
ping 10.10.10.1
ping 10.10.10.2
```

✅ Router2 berhasil dikonfigurasi!

### Penjelasan Konfigurasi Router2:

- **Gi0/0 (10.10.10.3/29)**: Terhubung ke Switch
- **Gi0/1 (192.168.2.1/24)**: Gateway untuk jaringan PC2
- **DHCP Server**: Memberikan IP otomatis ke PC2 (range: 192.168.2.11 - 254)
- **Excluded Address**: IP 192.168.2.1-10 tidak diberikan ke client (reserved)

---

## 💻 Konfigurasi PC

### Konfigurasi PC1 (IP Static):

1. **Klik PC1**
2. Pilih tab **Desktop**
3. Klik **IP Configuration**
4. Pilih **Static**
5. Isi:
   - **IPv4 Address**: `192.168.1.10`
   - **Subnet Mask**: `255.255.255.0`
   - **Default Gateway**: `192.168.1.1`
   - **DNS Server**: `8.8.8.8`

6. Tutup window IP Configuration

✅ PC1 siap digunakan!

### Konfigurasi PC2 (DHCP):

1. **Klik PC2**
2. Pilih tab **Desktop**
3. Klik **IP Configuration**
4. Pilih **DHCP**
5. Tunggu beberapa detik

PC2 akan otomatis mendapatkan:
- **IPv4 Address**: 192.168.2.11 (atau yang tersedia)
- **Subnet Mask**: 255.255.255.0
- **Default Gateway**: 192.168.2.1
- **DNS Server**: 8.8.8.8

✅ PC2 siap digunakan!

### Troubleshooting PC2 tidak dapat IP:

Jika PC2 tidak mendapatkan IP:
1. Klik **Static** → lalu klik **DHCP** lagi
2. Atau restart PC2: tutup dan buka lagi IP Configuration
3. Cek di Router2: `show ip dhcp binding` (harus muncul PC2)

---

## 🧪 Testing Konektivitas

### 1. Test dari PC1:

Buka **Command Prompt** di PC1 (Desktop → Command Prompt):

```
ipconfig
```
Pastikan IP: 192.168.1.10

```
ping 192.168.1.1
```
✅ **Harus berhasil** - Test koneksi ke gateway Router1

```
ping 10.10.10.1
```
✅ **Harus berhasil** - Test koneksi ke RouterX

```
ping 10.10.10.3
```
✅ **Harus berhasil** - Test koneksi ke Router2

```
ping 192.168.2.1
```
✅ **Harus berhasil** - Test koneksi ke gateway Router2

```
ping 192.168.2.11
```
✅ **Harus berhasil** - Test koneksi ke PC2 (jika PC2 dapat IP .11)

### 2. Test dari PC2:

Buka **Command Prompt** di PC2:

```
ipconfig
```
Pastikan PC2 dapat IP dari DHCP (192.168.2.11 - 254)

```
ping 192.168.2.1
```
✅ **Harus berhasil** - Test koneksi ke gateway Router2

```
ping 10.10.10.1
```
✅ **Harus berhasil** - Test koneksi ke RouterX

```
ping 10.10.10.2
```
✅ **Harus berhasil** - Test koneksi ke Router1

```
ping 192.168.1.1
```
✅ **Harus berhasil** - Test koneksi ke gateway Router1

```
ping 192.168.1.10
```
✅ **Harus berhasil** - Test koneksi ke PC1

### 3. Test dari Router:

Di RouterX, Router1, atau Router2:

```cisco
ping 10.10.10.1
ping 10.10.10.2
ping 10.10.10.3
ping 192.168.1.1
ping 192.168.2.1
```

### Hasil yang Diharapkan:

| Source | Destination | Status |
|--------|-------------|--------|
| PC1 | 192.168.1.1 | ✅ Berhasil |
| PC1 | 10.10.10.1 | ✅ Berhasil |
| PC1 | 192.168.2.1 | ✅ Berhasil |
| PC1 | PC2 | ✅ Berhasil |
| PC2 | 192.168.2.1 | ✅ Berhasil |
| PC2 | 10.10.10.1 | ✅ Berhasil |
| PC2 | PC1 | ✅ Berhasil |
| Router1 | RouterX | ✅ Berhasil |
| Router2 | RouterX | ✅ Berhasil |

---

## 📊 Verifikasi Akhir

### Checklist Konfigurasi:

#### RouterX:
- [ ] Gi0/0: 10.10.10.1/29 status **up up**
- [ ] Gi0/1: 203.0.113.2/24 status **up up**
- [ ] NAT sudah dikonfigurasi
- [ ] Static route ke 192.168.1.0/24 dan 192.168.2.0/24 ada

#### Router1:
- [ ] Gi0/0: 10.10.10.2/29 status **up up**
- [ ] Gi0/1: 192.168.1.1/24 status **up up**
- [ ] Default route ke 10.10.10.1 ada
- [ ] Bisa ping ke RouterX (10.10.10.1)

#### Router2:
- [ ] Gi0/0: 10.10.10.3/29 status **up up**
- [ ] Gi0/1: 192.168.2.1/24 status **up up**
- [ ] DHCP pool LAN2 sudah ada
- [ ] Default route ke 10.10.10.1 ada
- [ ] Bisa ping ke RouterX (10.10.10.1)

#### PC1:
- [ ] IP: 192.168.1.10
- [ ] Gateway: 192.168.1.1
- [ ] Bisa ping ke gateway
- [ ] Bisa ping ke RouterX
- [ ] Bisa ping ke PC2

#### PC2:
- [ ] Dapat IP dari DHCP (192.168.2.11-254)
- [ ] Gateway: 192.168.2.1
- [ ] Bisa ping ke gateway
- [ ] Bisa ping ke RouterX
- [ ] Bisa ping ke PC1

#### Kabel:
- [ ] Semua kabel berwarna **hijau**

---

## 🎓 Command Reference

### Command Dasar:

| Command | Fungsi |
|---------|--------|
| `enable` | Masuk ke privileged mode |
| `configure terminal` | Masuk ke config mode |
| `hostname RouterX` | Set nama router |
| `interface GigabitEthernet0/0` | Masuk ke config interface |
| `ip address 10.10.10.1 255.255.255.248` | Set IP address |
| `no shutdown` | Aktifkan interface |
| `exit` | Keluar satu level |
| `end` | Keluar ke privileged mode |
| `write memory` | Simpan konfigurasi |

### Command Verifikasi:

| Command | Fungsi |
|---------|--------|
| `show ip interface brief` | Lihat semua interface dan IP |
| `show ip route` | Lihat routing table |
| `show running-config` | Lihat konfigurasi aktif |
| `show ip dhcp pool` | Lihat DHCP pool |
| `show ip dhcp binding` | Lihat client DHCP |
| `show ip nat translations` | Lihat NAT translations |
| `show cdp neighbors` | Lihat neighbor router/switch |
| `show arp` | Lihat ARP table |
| `ping [IP]` | Test koneksi |

---

## 📝 Catatan Penting

### Subnet Information:

| Network | Subnet | Gateway | Range | Penggunaan |
|---------|--------|---------|-------|------------|
| 10.10.10.0/29 | 255.255.255.248 | - | 10.10.10.1-6 | Koneksi antar router |
| 192.168.1.0/24 | 255.255.255.0 | 192.168.1.1 | 192.168.1.1-254 | Jaringan PC1 |
| 192.168.2.0/24 | 255.255.255.0 | 192.168.2.1 | 192.168.2.11-254 | Jaringan PC2 (DHCP) |
| 203.0.113.0/24 | 255.255.255.0 | 203.0.113.1 | - | Simulasi Internet |

### IP Allocation:

| Device | Interface | IP Address | Subnet Mask |
|--------|-----------|------------|-------------|
| RouterX | Gi0/0 | 10.10.10.1 | 255.255.255.248 |
| RouterX | Gi0/1 | 203.0.113.2 | 255.255.255.0 |
| Router1 | Gi0/0 | 10.10.10.2 | 255.255.255.248 |
| Router1 | Gi0/1 | 192.168.1.1 | 255.255.255.0 |
| Router2 | Gi0/0 | 10.10.10.3 | 255.255.255.248 |
| Router2 | Gi0/1 | 192.168.2.1 | 255.255.255.0 |
| PC1 | Fa0 | 192.168.1.10 | 255.255.255.0 |
| PC2 | Fa0 | 192.168.2.x (DHCP) | 255.255.255.0 |
| Cloud | - | 203.0.113.1 | 255.255.255.0 |

---

## ✅ Selesai!

Jika semua langkah diikuti dengan benar:
- ✅ Semua router dapat berkomunikasi
- ✅ PC1 dan PC2 dapat saling ping
- ✅ Semua PC dapat akses gateway
- ✅ DHCP berfungsi untuk PC2
- ✅ NAT dikonfigurasi untuk internet sharing

**Selamat! Jaringan Anda sudah berfungsi dengan baik! 🎉**

---

### Tips untuk Presentasi/Demo:

1. Buka **Simulation Mode** (tombol stopwatch di pojok kanan bawah)
2. Pilih filter: **ICMP** saja
3. Lakukan ping dari PC1 ke PC2
4. Klik **Auto Capture/Play** untuk melihat paket berjalan
5. Jelaskan path yang dilalui paket

Ini akan mengesankan audiens! 🚀

AUTHOR: [yuriya-dev](https://github.com/yuriya-dev)

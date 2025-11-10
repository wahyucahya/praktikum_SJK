# Tutorial: Konfigurasi 2 Router dengan 2 Switch dan 4 PC

## 📋 Topologi Jaringan

```
        Router1 (Eth0) <---> (Eth0) Router2
       10.10.10.1/30         10.10.10.2/30
            |                      |
        Router1 (Eth1)        Router2 (Eth1)
       192.168.10.1/24       192.168.20.1/24
            |                      |
         Switch1                Switch2
         /     \                /     \
        /       \              /       \
      PC3       PC4          PC5       PC6
  192.168.10.3  192.168.10.4  192.168.20.5  192.168.20.6
```

---

## 📊 Informasi IP Address

### Router 1:
- **Eth0 (Gi0/0)**: 10.10.10.1/30 (koneksi ke Router2)
- **Eth1 (Gi0/1)**: 192.168.10.1/24 (gateway untuk PC1 & PC2)

### Router 2:
- **Eth0 (Gi0/0)**: 10.10.10.2/30 (koneksi ke Router1)
- **Eth1 (Gi0/1)**: 192.168.20.1/24 (gateway untuk PC3 & PC4)

### PC:
- **PC3**: 192.168.10.3/24, Gateway: 192.168.10.1
- **PC4**: 192.168.10.4/24, Gateway: 192.168.10.1
- **PC5**: 192.168.20.5/24, Gateway: 192.168.20.1
- **PC6**: 192.168.20.6/24, Gateway: 192.168.20.1

---

## 🛠️ Persiapan di Packet Tracer

### 1. Tambahkan Perangkat:
- **2 Router** (Router 2911 atau yang tersedia)
- **2 Switch** (Switch 2960)
- **4 PC** (PC-PT)

### 2. Susun seperti topologi di atas

---

## 🔌 Koneksi Kabel

**Gunakan Copper Straight-Through untuk semua koneksi:**

| Dari | Port | Ke | Port |
|------|------|-----|------|
| Router1 | GigabitEthernet0/0 | Router2 | GigabitEthernet0/0 |
| Router1 | GigabitEthernet0/1 | Switch1 | FastEthernet0/1 |
| Switch1 | FastEthernet0/2 | PC3 | FastEthernet0 |
| Switch1 | FastEthernet0/3 | PC4 | FastEthernet0 |
| Router2 | GigabitEthernet0/1 | Switch2 | FastEthernet0/1 |
| Switch2 | FastEthernet0/2 | PC5 | FastEthernet0 |
| Switch2 | FastEthernet0/3 | PC6 | FastEthernet0 |

---

## ⚙️ Konfigurasi Router 1

```cisco
enable
configure terminal

hostname Router1

! Interface ke Router2 (Point-to-Point)
interface GigabitEthernet0/0
 ip address 10.10.10.1 255.255.255.252
 no shutdown
 exit

! Interface ke Switch1 (LAN untuk PC1 & PC2)
interface GigabitEthernet0/1
 ip address 192.168.10.1 255.255.255.0
 no shutdown
 exit

! Matikan interface yang tidak dipakai
interface GigabitEthernet0/2
 shutdown
 exit

! Static Route ke jaringan Router2 (192.168.20.0/24)
ip route 192.168.20.0 255.255.255.0 10.10.10.2

end
write memory
```

### Verifikasi Router1:

```cisco
show ip interface brief
show ip route
```

**Output yang diharapkan:**
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     10.10.10.1      YES manual up                    up
GigabitEthernet0/1     192.168.10.1    YES manual up                    up
```

**Routing Table harus ada:**
```
C    10.10.10.0/30 is directly connected, GigabitEthernet0/0
C    192.168.10.0/24 is directly connected, GigabitEthernet0/1
S    192.168.20.0/24 [1/0] via 10.10.10.2
```

---

## ⚙️ Konfigurasi Router 2

```cisco
enable
configure terminal

hostname Router2

! Interface ke Router1 (Point-to-Point)
interface GigabitEthernet0/0
 ip address 10.10.10.2 255.255.255.252
 no shutdown
 exit

! Interface ke Switch2 (LAN untuk PC3 & PC4)
interface GigabitEthernet0/1
 ip address 192.168.20.1 255.255.255.0
 no shutdown
 exit

! Matikan interface yang tidak dipakai
interface GigabitEthernet0/2
 shutdown
 exit

! Static Route ke jaringan Router1 (192.168.10.0/24)
ip route 192.168.10.0 255.255.255.0 10.10.10.1

end
write memory
```

### Verifikasi Router2:

```cisco
show ip interface brief
show ip route
ping 10.10.10.1
```

**Output yang diharapkan:**
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     10.10.10.2      YES manual up                    up
GigabitEthernet0/1     192.168.20.1    YES manual up                    up
```

**Routing Table harus ada:**
```
C    10.10.10.0/30 is directly connected, GigabitEthernet0/0
C    192.168.20.0/24 is directly connected, GigabitEthernet0/1
S    192.168.10.0/24 [1/0] via 10.10.10.1
```

**Ping ke Router1 harus berhasil:**
```
ping 10.10.10.1
!!!!!
Success rate is 100 percent (5/5)
```

---

## 💻 Konfigurasi PC

### PC3:
1. Klik **PC3** → Tab **Desktop** → **IP Configuration**
2. Pilih **Static**
3. Isi:
   - **IP Address**: `192.168.10.3`
   - **Subnet Mask**: `255.255.255.0`
   - **Default Gateway**: `192.168.10.1`
   - **DNS Server**: `8.8.8.8`

### PC4:
1. Klik **PC4** → Tab **Desktop** → **IP Configuration**
2. Pilih **Static**
3. Isi:
   - **IP Address**: `192.168.10.4`
   - **Subnet Mask**: `255.255.255.0`
   - **Default Gateway**: `192.168.10.1`
   - **DNS Server**: `8.8.8.8`

### PC5:
1. Klik **PC5** → Tab **Desktop** → **IP Configuration**
2. Pilih **Static**
3. Isi:
   - **IP Address**: `192.168.20.5`
   - **Subnet Mask**: `255.255.255.0`
   - **Default Gateway**: `192.168.20.1`
   - **DNS Server**: `8.8.8.8`

### PC6:
1. Klik **PC6** → Tab **Desktop** → **IP Configuration**
2. Pilih **Static**
3. Isi:
   - **IP Address**: `192.168.20.6`
   - **Subnet Mask**: `255.255.255.0`
   - **Default Gateway**: `192.168.20.1`
   - **DNS Server**: `8.8.8.8`

---

## 🧪 Testing Konektivitas

### Test dari PC3:

Buka **Command Prompt** di PC3:

```
ipconfig
```
Pastikan IP: 192.168.10.3

```
ping 192.168.10.1
```
✅ Ping ke gateway Router1 - **HARUS BERHASIL**

```
ping 192.168.10.4
```
✅ Ping ke PC4 (dalam 1 subnet) - **HARUS BERHASIL**

```
ping 10.10.10.1
```
✅ Ping ke Router1 interface Gi0/0 - **HARUS BERHASIL**

```
ping 10.10.10.2
```
✅ Ping ke Router2 - **HARUS BERHASIL**

```
ping 192.168.20.1
```
✅ Ping ke Router2 gateway - **HARUS BERHASIL**

```
ping 192.168.20.5
```
✅ Ping ke PC5 (beda subnet) - **HARUS BERHASIL**

```
ping 192.168.20.6
```
✅ Ping ke PC6 (beda subnet) - **HARUS BERHASIL**

---

### Test dari PC5:

Buka **Command Prompt** di PC5:

```
ipconfig
```
Pastikan IP: 192.168.20.5

```
ping 192.168.20.1
```
✅ Ping ke gateway Router2 - **HARUS BERHASIL**

```
ping 192.168.20.6
```
✅ Ping ke PC6 (dalam 1 subnet) - **HARUS BERHASIL**

```
ping 10.10.10.2
```
✅ Ping ke Router2 interface Gi0/0 - **HARUS BERHASIL**

```
ping 10.10.10.1
```
✅ Ping ke Router1 - **HARUS BERHASIL**

```
ping 192.168.10.1
```
✅ Ping ke Router1 gateway - **HARUS BERHASIL**

```
ping 192.168.10.3
```
✅ Ping ke PC3 (beda subnet) - **HARUS BERHASIL**

```
ping 192.168.10.4
```
✅ Ping ke PC4 (beda subnet) - **HARUS BERHASIL**

---

### Test dari Router:

**Di Router1:**
```cisco
ping 10.10.10.2
ping 192.168.20.1
ping 192.168.20.5
ping 192.168.10.3
```

**Di Router2:**
```cisco
ping 10.10.10.1
ping 192.168.10.1
ping 192.168.10.3
ping 192.168.20.5
```

Semua harus **berhasil!** ✅

---

## 📊 Tabel Konektivitas

| Source | Destination | Status | Keterangan |
|--------|-------------|--------|------------|
| PC3 | 192.168.10.1 | ✅ Berhasil | Gateway lokal |
| PC3 | PC4 | ✅ Berhasil | Dalam 1 subnet |
| PC3 | 10.10.10.1 | ✅ Berhasil | Router1 Gi0/0 |
| PC3 | 10.10.10.2 | ✅ Berhasil | Router2 Gi0/0 |
| PC3 | PC5 | ✅ Berhasil | Beda subnet via routing |
| PC3 | PC6 | ✅ Berhasil | Beda subnet via routing |
| PC5 | 192.168.20.1 | ✅ Berhasil | Gateway lokal |
| PC5 | PC6 | ✅ Berhasil | Dalam 1 subnet |
| PC5 | PC3 | ✅ Berhasil | Beda subnet via routing |
| PC5 | PC4 | ✅ Berhasil | Beda subnet via routing |
| Router1 | Router2 | ✅ Berhasil | Direct connection |
| Router2 | Router1 | ✅ Berhasil | Direct connection |

---

## 🔧 Troubleshooting

### Problem 1: PC3 tidak bisa ping gateway (192.168.10.1)

**Solusi:**
1. Cek konfigurasi IP PC3
2. Cek kabel PC3 ke Switch1 (harus hijau)
3. Cek kabel Switch1 ke Router1 (harus hijau)
4. Di Router1: `show ip interface brief` - Gi0/1 harus **up up**

---

### Problem 2: PC3 bisa ping gateway tapi tidak bisa ping PC5

**Solusi:**

1. **Cek routing di Router1:**
   ```cisco
   show ip route
   ```
   Harus ada: `S    192.168.20.0/24 [1/0] via 10.10.10.2`

2. **Cek routing di Router2:**
   ```cisco
   show ip route
   ```
   Harus ada: `S    192.168.10.0/24 [1/0] via 10.10.10.1`

3. **Test koneksi antar router:**
   Di Router1:
   ```cisco
   ping 10.10.10.2
   ping 192.168.20.1
   ```

---

### Problem 3: Kabel Router1 - Router2 masih merah

**Solusi:**
1. Tunggu 10-15 detik
2. Klik **Fast Forward Time** beberapa kali
3. Pastikan kedua interface sudah `no shutdown`
4. Cek dengan:
   ```cisco
   show ip interface brief
   ```

---

### Problem 4: PC3 dan PC4 tidak bisa saling ping

**Solusi:**
1. Pastikan kedua PC dalam subnet yang sama (192.168.10.0/24)
2. Cek kabel ke Switch1 (harus hijau)
3. Switch1 tidak perlu konfigurasi
4. Test ping dari masing-masing PC ke gateway dulu

---

## 📋 Checklist Verifikasi

### Router1:
- [ ] Gi0/0: 10.10.10.1/30 status **up up**
- [ ] Gi0/1: 192.168.10.1/24 status **up up**
- [ ] Static route ke 192.168.20.0/24 via 10.10.10.2 ada
- [ ] Bisa ping 10.10.10.2
- [ ] Bisa ping 192.168.20.1

### Router2:
- [ ] Gi0/0: 10.10.10.2/30 status **up up**
- [ ] Gi0/1: 192.168.20.1/24 status **up up**
- [ ] Static route ke 192.168.10.0/24 via 10.10.10.1 ada
- [ ] Bisa ping 10.10.10.1
- [ ] Bisa ping 192.168.10.1

### PC3:
- [ ] IP: 192.168.10.3, Gateway: 192.168.10.1
- [ ] Bisa ping gateway (192.168.10.1)
- [ ] Bisa ping PC4 (192.168.10.4)
- [ ] Bisa ping PC5 (192.168.20.5)
- [ ] Bisa ping PC6 (192.168.20.6)

### PC4:
- [ ] IP: 192.168.10.4, Gateway: 192.168.10.1
- [ ] Bisa ping gateway dan semua PC

### PC5:
- [ ] IP: 192.168.20.5, Gateway: 192.168.20.1
- [ ] Bisa ping gateway (192.168.20.1)
- [ ] Bisa ping PC6 (192.168.20.6)
- [ ] Bisa ping PC3 (192.168.10.3)
- [ ] Bisa ping PC4 (192.168.10.4)

### PC6:
- [ ] IP: 192.168.20.6, Gateway: 192.168.20.1
- [ ] Bisa ping gateway dan semua PC

### Kabel:
- [ ] Semua kabel berwarna **hijau**

---

## 📚 Penjelasan Konsep

### Subnet /30 (255.255.255.252)

Digunakan untuk **point-to-point** connection (Router ke Router):
- **Network**: 10.10.10.0
- **Usable IPs**: 10.10.10.1 dan 10.10.10.2
- **Broadcast**: 10.10.10.3

Hanya 2 IP yang bisa digunakan, cocok untuk koneksi antar router!

### Subnet /24 (255.255.255.0)

Digunakan untuk **LAN** (banyak host):
- **Network 1**: 192.168.10.0
  - Usable: 192.168.10.1 - 192.168.10.254
  - Digunakan: .1 (gateway), .3 (PC3), .4 (PC4)
  
- **Network 2**: 192.168.20.0
  - Usable: 192.168.20.1 - 192.168.20.254
  - Digunakan: .1 (gateway), .5 (PC5), .6 (PC6)

### Static Routing

Karena Router1 tidak tahu tentang network 192.168.20.0/24, kita harus beritahu:
```cisco
ip route 192.168.20.0 255.255.255.0 10.10.10.2
```
Artinya: "Untuk ke 192.168.20.0/24, kirim paket ke 10.10.10.2 (Router2)"

Begitu juga Router2 harus tahu tentang 192.168.10.0/24:
```cisco
ip route 192.168.10.0 255.255.255.0 10.10.10.1
```

---

## 🔄 Alur Komunikasi PC3 → PC5:

```
PC3 (192.168.10.3)
    ↓
Switch1
    ↓
Router1 Gi0/1 (192.168.10.1) - Gateway PC3
    ↓
Router1 routing: "192.168.20.0 ada di 10.10.10.2"
    ↓
Router1 Gi0/0 (10.10.10.1) → Router2 Gi0/0 (10.10.10.2)
    ↓
Router2 Gi0/1 (192.168.20.1)
    ↓
Switch2
    ↓
PC5 (192.168.20.5)
```

### Verifikasi:
```cisco
show ip interface brief       # Lihat semua interface
show ip route                 # Lihat routing table
show running-config           # Lihat konfigurasi lengkap
ping [IP]                     # Test koneksi
```

### Troubleshooting:
```cisco
show cdp neighbors            # Lihat perangkat tetangga
show arp                      # Lihat ARP table
debug ip icmp                 # Debug ping (hati-hati, banyak output!)
```

---

## ✅ Selesai!

Jika semua checklist terpenuhi:
- ✅ Semua router dapat berkomunikasi
- ✅ Semua PC dapat saling ping antar subnet
- ✅ Static routing berfungsi dengan baik
- ✅ Topologi sesuai requirement

**Jaringan Anda sudah berfungsi dengan sempurna! 🎉**

---

## 💡 Tips Tambahan

### Untuk Demo/Presentasi:
1. Gunakan **Simulation Mode** untuk menunjukkan path paket
2. Ping dari PC3 ke PC5 dan tunjukkan paket melewati:
   - PC3 → Switch1 → Router1 → Router2 → Switch2 → PC5
3. Jelaskan peran static routing dalam menghubungkan 2 subnet berbeda

### Untuk Pengembangan:
- Tambahkan VLAN di Switch1 dan Switch2
- Implementasikan DHCP di Router1 dan Router2
- Tambahkan Access Control List (ACL) untuk security
- Implementasikan dynamic routing (RIP/OSPF/EIGRP)

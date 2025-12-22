# USE CASE: RIP untuk Sistem Irigasi Kebun Melon
## Implementasi 5 Router dengan RIP Protocol + Edge Router Internet Gateway

---

## 📋 USE CASE SCENARIO

### **Profil Kebun Melon XYZ:**
- **Lokasi:** Yogyakarta, Indonesia
- **Luas Area:** 5 Hektar (dibagi 3 zona)
- **Jumlah Router:** 5 unit (MikroTik/Cisco)
- **Perangkat IoT:** 8 sensor + 6 aktuator
- **Koneksi Internet:** Via ISP Fiber Optic
- **Tujuan:** Sistem irigasi otomatis dengan monitoring real-time + Cloud Integration

### **Pembagian Zona:**

#### **ZONA 1 - Area Utara (Router 1)**
- Luas: 2 hektar
- Sensor: 3 unit (suhu, kelembaban, tanah)
- Aktuator: 2 pompa air + 2 katup
- Network: 192.168.1.0/24

#### **ZONA 2 - Area Tengah (Router 2)**
- Luas: 1.5 hektar  
- Sensor: 2 unit (tanah, cuaca)
- Aktuator: 1 pompa air + 2 katup
- Network: 192.168.2.0/24

#### **ZONA 3 - Area Selatan (Router 3)**
- Luas: 1.5 hektar
- Sensor: 3 unit (suhu, kelembaban, tanah)
- Aktuator: 1 pompa air + 1 katup
- Network: 192.168.3.0/24

#### **ZONA 4 - Area Kantor (Router 4) - PUSAT MONITORING**
- Fungsi: Control Center & Management
- Perangkat: Server, Admin PC, Monitoring Station
- Network: 192.168.4.0/24
- **Lokasi:** Gedung kantor terpisah dari area kebun

#### **🌐 ZONA 5 - Edge Router (Router 5) - INTERNET GATEWAY**
- Fungsi: Gateway ke Internet (ISP)
- Services: NAT, Firewall, VPN, Cloud Sync
- Network: 192.168.5.0/24 (DMZ Network)
- **WAN IP:** Dari ISP (Dynamic/Static)
- **Lokasi:** Ruang server kantor

---

## 🗺️ TOPOLOGI JARINGAN 5 ROUTER

```
                    KEBUN MELON XYZ
         (5 Hektar - 3 Zona Field + Office + Internet)

                    ☁️ INTERNET (ISP)
                          |
                          | WAN (DHCP/PPPoE)
                          | 203.0.113.x
                          |
    +---------------------------------------------+
    |  🌐 ROUTER 5 - EDGE/INTERNET GATEWAY       |
    |     (Firewall, NAT, VPN, Cloud Access)     |
    |     WAN: 203.0.113.x (dari ISP)            |
    |     LAN: 192.168.5.1/24 (DMZ)              |
    |     RIP: Enabled                           |
    +---------------------------------------------+
                          |
                          | 10.0.5.0/30
                          | (Link ke Office)
                          |
    +---------------------------------------------+
    |  🏢 ROUTER 4 - OFFICE CONTROL CENTER       |
    |     (Central Hub untuk Field Networks)     |
    |     IP: 192.168.4.1/24                     |
    |     RIP: Enabled (HUB)                     |
    +---------------------------------------------+
              |           |           |
              |           |           |
   10.0.1.0/30|  10.0.2.0/30  10.0.3.0/30
              |           |           |
    +---------+-----------+-----------+---------+
    |         |           |           |         |
+-------+ +-------+ +-------+ +-------+ +-------+
|  R1   | |  R2   | |  R3   | |  R4   | |  R5   |
| Zona1 | | Zona2 | | Zona3 | |Office | |Gateway|
| Field | | Field | | Field | | Hub   | |Internet
+-------+ +-------+ +-------+ +-------+ +-------+
    |         |         |         |         |
[Sensors] [Sensors] [Sensors] [Server] [Firewall]
[Pumps]   [Pumps]   [Pumps]   [Admin]  [VPN]


DETAIL TOPOLOGY:

    +---------------------------------------+
    |         ZONA 1 - AREA UTARA         |
    |           (2 Hektar)                |
    |  [Router 1 - Field North]           |
    |    IP: 192.168.1.1                  |
    |    RIP: Enabled                     |
    |  +----------[Switch 1]----------+   |
    |  | Sensor Suhu-1    | Pompa-1   |   |
    |  | Sensor Tanah-1   | Katup-1   |   |
    |  | Sensor Humid-1   | Katup-2   |   |
    +---------------------------------------+
                     |
                     | 10.0.1.0/30
                     |
    +---------------------------------------+
    |       ZONA 2 - AREA TENGAH          |
    |         (1.5 Hektar)                |
    |  [Router 2 - Field Center]          |
    |    IP: 192.168.2.1                  |
    |    RIP: Enabled                     |
    |  +----------[Switch 2]----------+   |
    |  | Sensor Tanah-2   | Pompa-2   |   |
    |  | Sensor Cuaca     | Katup-3   |   |
    |  |                  | Katup-4   |   |
    +---------------------------------------+
                     |
                     | 10.0.2.0/30
                     |
    +---------------------------------------+
    |       ZONA 3 - AREA SELATAN         |
    |         (1.5 Hektar)                |
    |  [Router 3 - Field South]           |
    |    IP: 192.168.3.1                  |
    |    RIP: Enabled                     |
    |  +----------[Switch 3]----------+   |
    |  | Sensor Suhu-2    | Pompa-3   |   |
    |  | Sensor Tanah-3   | Katup-5   |   |
    |  | Sensor Humid-2   |           |   |
    +---------------------------------------+
                     |
                     | 10.0.3.0/30
                     |
    +---------------------------------------+
    |    🏢 ZONA 4 - AREA KANTOR         |
    |      (CONTROL CENTER HUB)           |
    |  [Router 4 - Main Office]           |
    |    IP: 192.168.4.1                  |
    |    RIP: Enabled (Central Hub)       |
    |  +----------[Switch 4]----------+   |
    |  | Server    | Monitor  | NVR   |   |
    |  | Admin PC  | Printer  | WiFi  |   |
    +---------------------------------------+
                     |
                     | 10.0.5.0/30
                     |
    +---------------------------------------+
    |   🌐 ZONA 5 - EDGE/INTERNET        |
    |      (GATEWAY & SECURITY)           |
    |  [Router 5 - Edge Gateway]          |
    |    WAN: 203.0.113.x (ISP)           |
    |    LAN: 192.168.5.1/24              |
    |    RIP: Enabled                     |
    |  +----------[Switch 5]----------+   |
    |  | Firewall  | VPN Server |     |   |
    |  | Proxy     | DNS Cache  |     |   |
    +---------------------------------------+
                     |
                     | WAN Interface
                     |
                ☁️ INTERNET ISP
           (Fiber Optic 100 Mbps)

LEGEND:
[Router] = Cisco Router dengan RIP
[Switch] = Layer 2 Switch  
[Sensor] = IoT Sensor Device
[Pompa]  = Water Pump Actuator
[Katup]  = Solenoid Valve
[Server] = Database & Application Server
[VPN]    = VPN Server untuk Remote Access
```

---

## 📊 TABEL IP ADDRESSING (5 ROUTER)

### **Router Interfaces:**

| Router | Interface | IP Address | Network | Description |
|--------|-----------|------------|---------|-------------|
| **R1** | Gi0/0 | 192.168.1.1/24 | Zona 1 LAN | Field North - Sensor & Actuator |
| **R1** | Gi0/1 | 10.0.1.1/30 | Link R1-R4 | Point-to-Point to Office |
| **R2** | Gi0/0 | 192.168.2.1/24 | Zona 2 LAN | Field Center - Sensor & Actuator |
| **R2** | Gi0/1 | 10.0.2.1/30 | Link R2-R4 | Point-to-Point to Office |
| **R3** | Gi0/0 | 192.168.3.1/24 | Zona 3 LAN | Field South - Sensor & Actuator |
| **R3** | Gi0/1 | 10.0.3.1/30 | Link R3-R4 | Point-to-Point to Office |
| **R4** | Gi0/0 | 10.0.1.2/30 | Link R4-R1 | Point-to-Point to Zone 1 |
| **R4** | Gi0/1 | 10.0.2.2/30 | Link R4-R2 | Point-to-Point to Zone 2 |
| **R4** | Gi0/2 | 10.0.3.2/30 | Link R4-R3 | Point-to-Point to Zone 3 |
| **R4** | Gi0/3 | 192.168.4.1/24 | Office LAN | Control Center & Monitoring |
| **R4** | Gi0/4 | 10.0.5.1/30 | Link R4-R5 | Point-to-Point to Edge Router |
| **R5** | Gi0/0 | 10.0.5.2/30 | Link R5-R4 | Point-to-Point to Office |
| **R5** | Gi0/1 | 192.168.5.1/24 | DMZ LAN | Edge Services (Firewall, VPN) |
| **R5** | Gi0/2 | DHCP/Static | WAN | **ISP Internet Connection** |

### **End Devices - Zona 1 (Field North):**

| Device | IP Address | Gateway | Fungsi |
|--------|------------|---------|--------|
| Sensor Suhu-1 | 192.168.1.10 | 192.168.1.1 | Temperature Monitoring |
| Sensor Tanah-1 | 192.168.1.11 | 192.168.1.1 | Soil Moisture |
| Sensor Humidity-1 | 192.168.1.12 | 192.168.1.1 | Air Humidity |
| Pompa Air-1 | 192.168.1.20 | 192.168.1.1 | Water Pump Controller |
| Katup-1 | 192.168.1.21 | 192.168.1.1 | Solenoid Valve |
| Katup-2 | 192.168.1.22 | 192.168.1.1 | Solenoid Valve |

### **End Devices - Zona 2 (Field Center):**

| Device | IP Address | Gateway | Fungsi |
|--------|------------|---------|--------|
| Sensor Tanah-2 | 192.168.2.10 | 192.168.2.1 | Soil Moisture |
| Sensor Cuaca | 192.168.2.11 | 192.168.2.1 | Weather Station |
| Pompa Air-2 | 192.168.2.20 | 192.168.2.1 | Water Pump Controller |
| Katup-3 | 192.168.2.21 | 192.168.2.1 | Solenoid Valve |
| Katup-4 | 192.168.2.22 | 192.168.2.1 | Solenoid Valve |

### **End Devices - Zona 3 (Field South):**

| Device | IP Address | Gateway | Fungsi |
|--------|------------|---------|--------|
| Sensor Suhu-2 | 192.168.3.10 | 192.168.3.1 | Temperature Monitoring |
| Sensor Tanah-3 | 192.168.3.11 | 192.168.3.1 | Soil Moisture |
| Sensor Humidity-2 | 192.168.3.12 | 192.168.3.1 | Air Humidity |
| Pompa Air-3 | 192.168.3.20 | 192.168.3.1 | Water Pump Controller |
| Katup-5 | 192.168.3.21 | 192.168.3.1 | Solenoid Valve |

### **End Devices - Zona 4 (Office):**

| Device | IP Address | Gateway | Fungsi |
|--------|------------|---------|--------|
| Server Database | 192.168.4.10 | 192.168.4.1 | Central Database & App Server |
| Admin PC | 192.168.4.20 | 192.168.4.1 | System Administrator |
| Printer | 192.168.4.30 | 192.168.4.1 | Network Printer |
| Laptop Manager | 192.168.4.40 | 192.168.4.1 | Manager Workstation |
| NVR CCTV | 192.168.4.50 | 192.168.4.1 | Video Surveillance |
| WiFi Access Point | 192.168.4.60 | 192.168.4.1 | Wireless for Mobile Devices |
| Monitor Dashboard | 192.168.4.100 | 192.168.4.1 | Main Monitoring Station |

### **End Devices - Zona 5 (Edge/DMZ):**

| Device | IP Address | Gateway | Fungsi |
|--------|------------|---------|--------|
| Firewall Server | 192.168.5.10 | 192.168.5.1 | Security & Threat Protection |
| VPN Server | 192.168.5.20 | 192.168.5.1 | Remote Access VPN |
| DNS Cache Server | 192.168.5.30 | 192.168.5.1 | Internal DNS Resolver |
| Proxy Server | 192.168.5.40 | 192.168.5.1 | Web Proxy & Content Filter |
| Cloud Sync Server | 192.168.5.50 | 192.168.5.1 | Data Backup to Cloud |

---

## 🔧 KONFIGURASI LENGKAP - CISCO PACKET TRACER

### 🔵 **ROUTER 1 (Field North - Zona Utara)**

```cisco
enable
configure terminal

! ========================================
! ROUTER 1 - ZONA UTARA (FIELD NORTH)
! Network: 192.168.1.0/24
! ========================================

! Set hostname
hostname R1-Field-North

! Konfigurasi Interface LAN Zona 1
interface GigabitEthernet 0/0
 description "LAN Zona 1 - Sensor & Actuator Field North"
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit

! Konfigurasi Interface ke Router 4 (Office Hub)
interface GigabitEthernet 0/1
 description "Link to R4-Office Hub"
 ip address 10.0.1.1 255.255.255.252
 no shutdown
exit

! Konfigurasi RIP Version 2
router rip
 version 2
 ! Advertise networks
 network 192.168.1.0
 network 10.0.0.0
 ! Disable auto-summary untuk VLSM
 no auto-summary
 ! Passive interface untuk LAN (tidak kirim RIP updates ke end devices)
 passive-interface GigabitEthernet 0/0
exit

! DHCP Pool untuk Zona 1
ip dhcp pool ZONA1-FIELD-NORTH
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8 8.8.4.4
 domain-name melon-farm.local
 lease 7
exit

! Exclude static IP range
ip dhcp excluded-address 192.168.1.1 192.168.1.9
ip dhcp excluded-address 192.168.1.10 192.168.1.25

! Security - Basic
service password-encryption
enable secret melon2025!

! Console security
line console 0
 password console123
 login
 logging synchronous
exit

! VTY (Telnet/SSH) security
line vty 0 4
 password vty123
 login
 transport input ssh telnet
exit

! Banner
banner motd #
================================================
  ROUTER 1 - ZONA UTARA (FIELD NORTH)
  Kebun Melon XYZ - 2 Hektar
  Network: 192.168.1.0/24
  Unauthorized access is PROHIBITED!
================================================
#

! Logging
logging buffered 4096
logging console warnings

! Save configuration
end
write memory
```

---

### 🟢 **ROUTER 2 (Field Center - Zona Tengah)**

```cisco
enable
configure terminal

! ========================================
! ROUTER 2 - ZONA TENGAH (FIELD CENTER)
! Network: 192.168.2.0/24
! ========================================

! Set hostname
hostname R2-Field-Center

! Konfigurasi Interface LAN Zona 2
interface GigabitEthernet 0/0
 description "LAN Zona 2 - Sensor & Actuator Field Center"
 ip address 192.168.2.1 255.255.255.0
 no shutdown
exit

! Konfigurasi Interface ke Router 4 (Office Hub)
interface GigabitEthernet 0/1
 description "Link to R4-Office Hub"
 ip address 10.0.2.1 255.255.255.252
 no shutdown
exit

! Konfigurasi RIP Version 2
router rip
 version 2
 ! Advertise networks
 network 192.168.2.0
 network 10.0.0.0
 ! Disable auto-summary
 no auto-summary
 ! Passive interface untuk LAN
 passive-interface GigabitEthernet 0/0
exit

! DHCP Pool untuk Zona 2
ip dhcp pool ZONA2-FIELD-CENTER
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 8.8.8.8 8.8.4.4
 domain-name melon-farm.local
 lease 7
exit

! Exclude static IP range
ip dhcp excluded-address 192.168.2.1 192.168.2.9
ip dhcp excluded-address 192.168.2.10 192.168.2.25

! Security - Basic
service password-encryption
enable secret melon2025!

! Console security
line console 0
 password console123
 login
 logging synchronous
exit

! VTY security
line vty 0 4
 password vty123
 login
 transport input ssh telnet
exit

! Banner
banner motd #
================================================
  ROUTER 2 - ZONA TENGAH (FIELD CENTER)
  Kebun Melon XYZ - 1.5 Hektar
  Network: 192.168.2.0/24
  Unauthorized access is PROHIBITED!
================================================
#

! Logging
logging buffered 4096
logging console warnings

! Save configuration
end
write memory
```

---

### 🟠 **ROUTER 3 (Field South - Zona Selatan)**

```cisco
enable
configure terminal

! ========================================
! ROUTER 3 - ZONA SELATAN (FIELD SOUTH)
! Network: 192.168.3.0/24
! ========================================

! Set hostname
hostname R3-Field-South

! Konfigurasi Interface LAN Zona 3
interface GigabitEthernet 0/0
 description "LAN Zona 3 - Sensor & Actuator Field South"
 ip address 192.168.3.1 255.255.255.0
 no shutdown
exit

! Konfigurasi Interface ke Router 4 (Office Hub)
interface GigabitEthernet 0/1
 description "Link to R4-Office Hub"
 ip address 10.0.3.1 255.255.255.252
 no shutdown
exit

! Konfigurasi RIP Version 2
router rip
 version 2
 ! Advertise networks
 network 192.168.3.0
 network 10.0.0.0
 ! Disable auto-summary
 no auto-summary
 ! Passive interface untuk LAN
 passive-interface GigabitEthernet 0/0
exit

! DHCP Pool untuk Zona 3
ip dhcp pool ZONA3-FIELD-SOUTH
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
 dns-server 8.8.8.8 8.8.4.4
 domain-name melon-farm.local
 lease 7
exit

! Exclude static IP range
ip dhcp excluded-address 192.168.3.1 192.168.3.9
ip dhcp excluded-address 192.168.3.10 192.168.3.25

! Security - Basic
service password-encryption
enable secret melon2025!

! Console security
line console 0
 password console123
 login
 logging synchronous
exit

! VTY security
line vty 0 4
 password vty123
 login
 transport input ssh telnet
exit

! Banner
banner motd #
================================================
  ROUTER 3 - ZONA SELATAN (FIELD SOUTH)
  Kebun Melon XYZ - 1.5 Hektar
  Network: 192.168.3.0/24
  Unauthorized access is PROHIBITED!
================================================
#

! Logging
logging buffered 4096
logging console warnings

! Save configuration
end
write memory
```

---

### 🏢 **ROUTER 4 (Main Office - CONTROL CENTER HUB)**

```cisco
enable
configure terminal

! ========================================
! ROUTER 4 - OFFICE CONTROL CENTER (HUB)
! Network: 192.168.4.0/24
! ========================================

! Set hostname
hostname R4-Office-ControlCenter

! Konfigurasi Interface ke Router 1 (Zona 1)
interface GigabitEthernet 0/0
 description "Link to R1-Field-North"
 ip address 10.0.1.2 255.255.255.252
 no shutdown
exit

! Konfigurasi Interface ke Router 2 (Zona 2)
interface GigabitEthernet 0/1
 description "Link to R2-Field-Center"
 ip address 10.0.2.2 255.255.255.252
 no shutdown
exit

! Konfigurasi Interface ke Router 3 (Zona 3)
interface GigabitEthernet 0/2
 description "Link to R3-Field-South"
 ip address 10.0.3.2 255.255.255.252
 no shutdown
exit

! Konfigurasi Interface LAN Office
interface GigabitEthernet 0/3
 description "Office LAN - Control Center & Management"
 ip address 192.168.4.1 255.255.255.0
 no shutdown
exit

! Konfigurasi Interface ke Router 5 (Edge/Internet Gateway)
interface GigabitEthernet 0/4
 description "Link to R5-Edge-Gateway (Internet)"
 ip address 10.0.5.1 255.255.255.252
 no shutdown
exit

! Konfigurasi RIP Version 2
router rip
 version 2
 ! Advertise ALL networks (HUB Router)
 network 10.0.0.0
 network 192.168.4.0
 ! Disable auto-summary
 no auto-summary
 ! Passive interface untuk LAN Office
 passive-interface GigabitEthernet 0/3
 ! Default route dari R5 akan di-advertise via RIP
exit

! DHCP Pool untuk Office Network
ip dhcp pool OFFICE-NETWORK
 network 192.168.4.0 255.255.255.0
 default-router 192.168.4.1
 dns-server 8.8.8.8 8.8.4.4
 domain-name melon-farm.local
 lease 7
exit

! Exclude static IP range
ip dhcp excluded-address 192.168.4.1 192.168.4.9
ip dhcp excluded-address 192.168.4.10 192.168.4.60

! Default Route ke Edge Router (R5) untuk Internet
ip route 0.0.0.0 0.0.0.0 10.0.5.2

! Security - Enhanced ACL
! ACL 100: Allow Office to access all zones
access-list 100 permit ip 192.168.4.0 0.0.0.255 any
! ACL 101: Allow Field zones to access Office Server only
access-list 101 permit ip 192.168.1.0 0.0.0.255 host 192.168.4.10
access-list 101 permit ip 192.168.2.0 0.0.0.255 host 192.168.4.10
access-list 101 permit ip 192.168.3.0 0.0.0.255 host 192.168.4.10
access-list 101 deny ip any any

! Apply ACL (optional - uncomment jika ingin enforce)
! interface GigabitEthernet 0/3
!  ip access-group 100 in

! Security - Basic
service password-encryption
enable secret melon2025!

! Username database
username admin privilege 15 secret Admin@Melon2025
username operator privilege 7 secret Operator@123
username manager privilege 10 secret Manager@789

! Console security
line console 0
 password console123
 login local
 logging synchronous
 exec-timeout 10 0
exit

! VTY security with SSH
line vty 0 4
 login local
 transport input ssh
 exec-timeout 15 0
exit

! SSH Configuration
ip domain-name melon-farm.local
crypto key generate rsa general-keys modulus 2048
ip ssh version 2
ip ssh time-out 60
ip ssh authentication-retries 3

! HTTP Server untuk Web Management
ip http server
ip http secure-server
ip http authentication local

! SNMP untuk Monitoring
snmp-server community public RO
snmp-server community private RW
snmp-server location "Office Control Center - Kebun Melon XYZ"
snmp-server contact "admin@melon-farm.local"

! NTP untuk Time Synchronization
ntp server 192.168.5.30
clock timezone WIB 7

! Logging ke Syslog Server
logging host 192.168.4.10
logging trap informational

! Banner
banner motd #
==================================================
  🏢 ROUTER 4 - OFFICE CONTROL CENTER (HUB)
  Kebun Melon XYZ - Main Hub Router
  Network: 192.168.4.0/24
  Connects: 3 Field Zones + Edge Router
  Internet Access: Via R5 (10.0.5.2)
  Unauthorized access is PROHIBITED!
==================================================
#

! Save configuration
end
write memory
```

---

### 🌐 **ROUTER 5 (Edge Router - INTERNET GATEWAY)**

```cisco
enable
configure terminal

! ========================================
! ROUTER 5 - EDGE/INTERNET GATEWAY
! WAN: ISP Connection | LAN: 192.168.5.0/24
! ========================================

! Set hostname
hostname R5-Edge-Gateway

! Konfigurasi Interface ke Router 4 (Office Hub)
interface GigabitEthernet 0/0
 description "Link to R4-Office-Hub (Internal Network)"
 ip address 10.0.5.2 255.255.255.252
 no shutdown
exit

! Konfigurasi Interface LAN DMZ (Edge Services)
interface GigabitEthernet 0/1
 description "DMZ Network - Firewall, VPN, DNS, Proxy"
 ip address 192.168.5.1 255.255.255.0
 no shutdown
exit

! Konfigurasi Interface WAN (Internet ISP)
interface GigabitEthernet 0/2
 description "WAN Interface - ISP Fiber Optic Connection"
 ! Jika ISP menggunakan DHCP
 ip address dhcp
 ! ATAU jika ISP memberikan Static IP, contoh:
 ! ip address 203.0.113.10 255.255.255.252
 no shutdown
exit

! Konfigurasi RIP Version 2
router rip
 version 2
 ! Advertise internal networks ONLY
 network 10.0.0.0
 network 192.168.5.0
 ! Disable auto-summary
 no auto-summary
 ! Passive interface untuk DMZ dan WAN
 passive-interface GigabitEthernet 0/1
 passive-interface GigabitEthernet 0/2
 ! Redistribute default route ke RIP
 default-information originate
exit

! Default Route ke Internet (ISP Gateway)
! Jika ISP DHCP, default route otomatis
! Jika Static IP, set manual:
! ip route 0.0.0.0 0.0.0.0 203.0.113.1

! NAT Configuration (PAT) - CRITICAL untuk Internet Access
! Define Inside interface (Internal Network)
interface GigabitEthernet 0/0
 ip nat inside
exit

interface GigabitEthernet 0/1
 ip nat inside
exit

! Define Outside interface (WAN/Internet)
interface GigabitEthernet 0/2
 ip nat outside
exit

! NAT Pool & Access List
access-list 1 permit 10.0.0.0 0.0.255.255
access-list 1 permit 192.168.1.0 0.0.0.255
access-list 1 permit 192.168.2.0 0.0.0.255
access-list 1 permit 192.168.3.0 0.0.0.255
access-list 1 permit 192.168.4.0 0.0.0.255
access-list 1 permit 192.168.5.0 0.0.0.255

! PAT (Port Address Translation) - Overload
ip nat inside source list 1 interface GigabitEthernet 0/2 overload

! Port Forwarding untuk VPN Server (optional)
! ip nat inside source static tcp 192.168.5.20 1723 interface GigabitEthernet0/2 1723

! DHCP Pool untuk DMZ Network
ip dhcp pool DMZ-EDGE-NETWORK
 network 192.168.5.0 255.255.255.0
 default-router 192.168.5.1
 dns-server 8.8.8.8 1.1.1.1
 domain-name edge.melon-farm.local
 lease 3
exit

! Exclude static IP range
ip dhcp excluded-address 192.168.5.1 192.168.5.9
ip dhcp excluded-address 192.168.5.10 192.168.5.60

! Firewall - Basic Access Control Lists
! ACL 110: Block malicious incoming traffic
access-list 110 deny ip any host 192.168.5.1
access-list 110 deny ip any host 192.168.4.1
access-list 110 deny ip any host 192.168.1.1
access-list 110 deny ip any host 192.168.2.1
access-list 110 deny ip any host 192.168.3.1
access-list 110 permit ip any any

! ACL 120: Allow internal networks to Internet
access-list 120 permit ip 192.168.0.0 0.0.255.255 any
access-list 120 permit ip 10.0.0.0 0.0.255.255 any
access-list 120 deny ip any any

! Apply Firewall ACL ke WAN interface (optional)
! interface GigabitEthernet 0/2
!  ip access-group 110 in
!  ip access-group 120 out

! Zone-Based Firewall (Advanced - Optional)
! zone security INSIDE
! zone security OUTSIDE
! zone-pair security IN-TO-OUT source INSIDE destination OUTSIDE
!  service-policy type inspect INSIDE-TO-OUTSIDE-POLICY

! Security - Enhanced
service password-encryption
security passwords min-length 10
enable secret Edge@Melon2025!

! Username database
username admin privilege 15 secret Admin@Edge2025!
username netadmin privilege 15 secret NetAdmin@Edge!
username monitor privilege 7 secret Monitor@123

! Console security
line console 0
 password EdgeConsole@123
 login local
 logging synchronous
 exec-timeout 5 0
exit

! VTY security - SSH only
line vty 0 4
 login local
 transport input ssh
 exec-timeout 10 0
exit

! SSH Configuration
ip domain-name edge.melon-farm.local
crypto key generate rsa general-keys modulus 2048
ip ssh version 2
ip ssh time-out 60
ip ssh authentication-retries 2

! Disable unused services (Security Hardening)
no ip http server
no ip http secure-server
no cdp run
no ip bootp server
service tcp-keepalives-in
service tcp-keepalives-out

! SNMP untuk Monitoring
snmp-server community EdgePublic RO
snmp-server community EdgePrivate RW
snmp-server location "Edge Router - Internet Gateway"
snmp-server contact "netadmin@melon-farm.local"

! NTP Server (sync dari internet)
ntp server 0.id.pool.ntp.org
ntp server 1.id.pool.ntp.org
clock timezone WIB 7

! Logging
logging buffered 8192
logging host 192.168.4.10
logging trap informational
logging source-interface GigabitEthernet 0/0

! DNS Forwarder (optional)
ip dns server
ip name-server 8.8.8.8
ip name-server 1.1.1.1

! Banner
banner motd #
======================================================
  🌐 ROUTER 5 - EDGE/INTERNET GATEWAY
  Kebun Melon XYZ - Network Edge & Security
  WAN: ISP Connection | DMZ: 192.168.5.0/24
  Services: NAT, Firewall, VPN, Cloud Access
  ** CRITICAL INFRASTRUCTURE **
  Unauthorized access is STRICTLY PROHIBITED!
  All activities are logged and monitored.
======================================================
#

! Save configuration
end
write memory
```

---

## 🔍 VERIFIKASI & TESTING (5 ROUTER)

### **1. Verifikasi RIP Database di Router 5 (Edge)**

```cisco
R5# show ip rip database
```

**Expected Output:**
```
10.0.0.0/8        auto-summary
10.0.5.0/30       directly connected, GigabitEthernet0/0
192.168.5.0/24    directly connected, GigabitEthernet0/1
192.168.4.0/24    [1] via 10.0.5.1, 00:00:15, GigabitEthernet0/0
192.168.1.0/24    [2] via 10.0.5.1, 00:00:15, GigabitEthernet0/0
192.168.2.0/24    [2] via 10.0.5.1, 00:00:15, GigabitEthernet0/0
192.168.3.0/24    [2] via 10.0.5.1, 00:00:15, GigabitEthernet0/0
10.0.1.0/30       [1] via 10.0.5.1, 00:00:15, GigabitEthernet0/0
10.0.2.0/30       [1] via 10.0.5.1, 00:00:15, GigabitEthernet0/0
10.0.3.0/30       [1] via 10.0.5.1, 00:00:15, GigabitEthernet0/0
```

✅ **Router 5 dapat reach semua internal networks!**

---

### **2. Verifikasi Routing Table di Router 4 (Office Hub)**

```cisco
R4# show ip route
```

**Expected Output:**
```
Gateway of last resort is 10.0.5.2 to network 0.0.0.0

S*   0.0.0.0/0 [1/0] via 10.0.5.2
     192.168.4.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.4.0/24 is directly connected, GigabitEthernet0/3
L       192.168.4.1/32 is directly connected, GigabitEthernet0/3
     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C       10.0.1.0/30 is directly connected, GigabitEthernet0/0
L       10.0.1.2/32 is directly connected, GigabitEthernet0/0
C       10.0.2.0/30 is directly connected, GigabitEthernet0/1
L       10.0.2.2/32 is directly connected, GigabitEthernet0/1
C       10.0.3.0/30 is directly connected, GigabitEthernet0/2
L       10.0.3.2/32 is directly connected, GigabitEthernet0/2
C       10.0.5.0/30 is directly connected, GigabitEthernet0/4
L       10.0.5.1/32 is directly connected, GigabitEthernet0/4
R    192.168.1.0/24 [120/1] via 10.0.1.1, 00:00:23, GigabitEthernet0/0
R    192.168.2.0/24 [120/1] via 10.0.2.1, 00:00:23, GigabitEthernet0/1
R    192.168.3.0/24 [120/1] via 10.0.3.1, 00:00:23, GigabitEthernet0/2
R    192.168.5.0/24 [120/1] via 10.0.5.2, 00:00:23, GigabitEthernet0/4
```

✅ **Default route ke Internet via R5 ada! (S* 0.0.0.0/0)**

---

### **3. Verifikasi RIP Protocol Status**

```cisco
R4# show ip protocols
```

**Expected Output:**
```
Routing Protocol is "rip"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Sending updates every 30 seconds, next due in 12 seconds
  Invalid after 180 seconds, hold down 180, flushed after 240
  Redistributing: rip
  Default version control: send version 2, receive version 2
  Automatic network summarization is not in effect
  Maximum path: 4
  Routing for Networks:
    10.0.0.0
    192.168.4.0
  Passive Interface(s):
    GigabitEthernet0/3
  Routing Information Sources:
    Gateway         Distance      Last Update
    10.0.1.1        120           00:00:25
    10.0.2.1        120           00:00:22
    10.0.3.1        120           00:00:28
    10.0.5.2        120           00:00:18
  Distance: (default is 120)
```

---

### **4. Test NAT Configuration di Router 5**

```cisco
R5# show ip nat translations
R5# show ip nat statistics
```

**Expected Output:**
```
Total active translations: 5 (0 static, 5 dynamic; 5 extended)
Peak translations: 12
Outside interfaces:
  GigabitEthernet0/2
Inside interfaces:
  GigabitEthernet0/0, GigabitEthernet0/1
Hits: 1523  Misses: 0
```

---

### **5. Test Internet Connectivity**

#### **Dari Router 5 (Edge):**
```cisco
R5# ping 8.8.8.8
R5# ping google.com
```

#### **Dari Router 4 (Office):**
```cisco
R4# ping 8.8.8.8
R4# ping 1.1.1.1
```

#### **Dari PC di Office (192.168.4.100):**
```bash
ping 8.8.8.8
ping google.com
tracert google.com
```

**Expected Traceroute:**
```
1  192.168.4.1    (Router 4 - Office)      < 1 ms
2  10.0.5.2       (Router 5 - Edge)        2 ms
3  203.0.113.1    (ISP Gateway)            5 ms
4  ...            (Internet hops)
```

---

### **6. Test Connectivity dari Field ke Internet**

#### **Dari Sensor di Zona 1 (192.168.1.10):**
```bash
ping 192.168.4.10   # Ping Server Office
ping 8.8.8.8        # Ping DNS Google (via NAT di R5)
```

**Expected:**
- Ping ke 192.168.4.10 → SUCCESS ✅
- Ping ke 8.8.8.8 → SUCCESS ✅ (via NAT)

---

### **7. Verifikasi RIP Convergence di Semua Router**

**Test: Matikan interface Gi0/1 di Router 1**
```cisco
R1# configure terminal
R1(config)# interface GigabitEthernet 0/1
R1(config-if)# shutdown
R1(config-if)# end
```

**Wait 30-60 seconds, lalu cek routing table di R4:**
```cisco
R4# show ip route rip
```

**Expected:**
- Route 192.168.1.0/24 akan HILANG dari routing table
- Setelah interface di-no shutdown, route akan KEMBALI dalam ~60 detik

**Test RIP Convergence Time:**
```cisco
R1# configure terminal
R1(config)# interface GigabitEthernet 0/1
R1(config-if)# no shutdown
R1(config-if)# end
```

---

## 🎯 USE CASE SCENARIOS dengan Internet (5 Router)

### **Scenario 1: Cloud Data Backup**

**Kondisi:**
- Server Office (192.168.4.10) perlu backup data sensor ke Cloud Storage
- Cloud Sync Server (192.168.5.50) handle upload

**Flow:**
```
1. Server Office (192.168.4.10) 
   ↓
2. Router 4 (192.168.4.1) → Gateway ke R5
   ↓
3. Router 5 (NAT) → Translate ke Public IP
   ↓
4. Internet → Cloud Storage (AWS S3 / Google Cloud)
```

**Command untuk test:**
```bash
# Dari Server Office
curl -I https://storage.googleapis.com
wget https://speedtest.net/mini.php
```

---

### **Scenario 2: Remote Monitoring via VPN**

**Kondisi:**
- Manager di luar kota ingin akses Dashboard Office
- VPN Server di DMZ (192.168.5.20) provide akses

**Flow:**
```
Manager Laptop (Internet)
    ↓ VPN Client
Router 5 (Public IP) → VPN Server (192.168.5.20)
    ↓ Tunnel
Router 4 → Office Network (192.168.4.0/24)
    ↓
Dashboard Monitor (192.168.4.100)
```

**VPN Configuration (L2TP/IPSec - optional):**
```cisco
R5# crypto isakmp policy 10
R5(config-isakmp)# encryption aes 256
R5(config-isakmp)# hash sha256
R5(config-isakmp)# authentication pre-share
R5(config-isakmp)# group 14
R5(config-isakmp)# exit
```

---

### **Scenario 3: Weather API Integration**

**Kondisi:**
- Sensor Cuaca (192.168.2.11) fetch data dari OpenWeatherMap API
- Perlu akses Internet untuk API calls

**Flow:**
```
Sensor Cuaca (192.168.2.11)
    ↓
Router 2 (192.168.2.1)
    ↓ via RIP
Router 4 (Office Hub)
    ↓
Router 5 (NAT + Firewall)
    ↓
Internet → api.openweathermap.org
```

**Test API Access:**
```bash
# Dari Sensor (atau PC di Zona 2)
curl "http://api.openweathermap.org/data/2.5/weather?q=Yogyakarta&appid=YOUR_API_KEY"
```

---

### **Scenario 4: Email Alerts via Internet**

**Kondisi:**
- Server Office (192.168.4.10) kirim email alert jika sensor detect masalah
- SMTP via Gmail/Outlook

**Flow:**
```
Server Office detects: Sensor Tanah-2 (192.168.2.10) = 30% (LOW!)
    ↓
Python script send email via SMTP
    ↓
Router 4 → Router 5 (NAT)
    ↓
Internet → smtp.gmail.com (Port 587)
    ↓
Email dikirim ke Manager
```

**Python Script Example:**
```python
import smtplib
from email.mime.text import MIMEText

def send_alert(zone, sensor_ip, value):
    msg = MIMEText(f"ALERT! {zone} - {sensor_ip}: Moisture = {value}%")
    msg['Subject'] = 'Irrigation Alert - Melon Farm'
    msg['From'] = 'alert@melon-farm.local'
    msg['To'] = 'manager@company.com'
    
    with smtplib.SMTP('smtp.gmail.com', 587) as server:
        server.starttls()
        server.login('alert@melon-farm.local', 'password')
        server.send_message(msg)

# Trigger
send_alert('Zona 2', '192.168.2.10', 30)
```

---

### **Scenario 5: OTA Firmware Update untuk IoT Devices**

**Kondisi:**
- IoT sensors perlu firmware update dari cloud server
- Download firmware via HTTPS

**Flow:**
```
Firmware Server (Cloud) → firmware-v2.bin
    ↓
Router 5 (Internet Gateway)
    ↓ via RIP
Router 1/2/3 (Field Routers)
    ↓
IoT Sensors download & install firmware
```

**Test Download:**
```bash
# Dari IoT device
wget https://firmware.melon-farm.com/sensor_v2.bin
md5sum sensor_v2.bin
```

---

## 📊 MONITORING & MANAGEMENT

### **Dashboard dengan Internet Monitoring**

Update `dashboard_office.html` dengan monitoring internet:

```javascript
// Check Internet Connectivity
async function checkInternet() {
    try {
        const response = await fetch('https://8.8.8.8', { mode: 'no-cors' });
        return true;
    } catch (error) {
        return false;
    }
}

// Display Internet Status
setInterval(async () => {
    const isOnline = await checkInternet();
    document.getElementById('internet-status').textContent = 
        isOnline ? '🟢 ONLINE' : '🔴 OFFLINE';
}, 10000);
```

---

### **SNMP Monitoring Script**

```python
from pysnmp.hlapi import *

def get_router_stats(router_ip, community='public'):
    """Get router interface statistics via SNMP"""
    iterator = getCmd(
        SnmpEngine(),
        CommunityData(community),
        UdpTransportTarget((router_ip, 161)),
        ContextData(),
        ObjectType(ObjectIdentity('1.3.6.1.2.1.2.1.0'))  # ifNumber
    )
    
    errorIndication, errorStatus, errorIndex, varBinds = next(iterator)
    
    if not errorIndication and not errorStatus:
        for varBind in varBinds:
            print(f'{router_ip}: {varBind}')

# Monitor all routers
routers = [
    '192.168.1.1',  # R1
    '192.168.2.1',  # R2
    '192.168.3.1',  # R3
    '192.168.4.1',  # R4
    '192.168.5.1'   # R5
]

for router in routers:
    get_router_stats(router)
```

---

## 🔒 SECURITY BEST PRACTICES

### **1. Firewall Rules di Router 5**

```cisco
! Block incoming connections to internal networks
access-list 110 deny ip any 192.168.0.0 0.0.255.255
access-list 110 deny ip any 10.0.0.0 0.255.255.255
access-list 110 permit tcp any any established
access-list 110 permit udp any any eq 53
access-list 110 permit icmp any any echo-reply
access-list 110 deny ip any any log

interface GigabitEthernet 0/2
 ip access-group 110 in
```

### **2. Rate Limiting untuk Security**

```cisco
! Prevent DDoS attacks
access-list 130 permit tcp any any
access-list 130 permit udp any any
access-list 130 permit icmp any any

class-map match-any RATE-LIMIT
 match access-group 130

policy-map LIMIT-TRAFFIC
 class RATE-LIMIT
  police 10000000 1000000 1000000 conform-action transmit exceed-action drop

interface GigabitEthernet 0/2
 service-policy input LIMIT-TRAFFIC
```

### **3. Login Banner & Legal Notice**

```cisco
banner login ^
========================================================
              AUTHORIZED ACCESS ONLY
  
  This system is for authorized use only.
  Unauthorized access is strictly prohibited and
  will be prosecuted to the fullest extent of law.
  
  All activities are monitored and logged.
========================================================
^
```

---

## 🚨 TROUBLESHOOTING GUIDE

### **Problem 1: No Internet Access dari Field Zones**

**Symptoms:**
- PC di Zona 1/2/3 tidak bisa ping 8.8.8.8
- Bisa ping Office (192.168.4.x)

**Diagnosis:**
```cisco
# Cek di Router 4
R4# show ip route | include 0.0.0.0
# Expected: S* 0.0.0.0/0 [1/0] via 10.0.5.2

# Cek di Router 5
R5# show ip nat translations
R5# show ip nat statistics

# Cek interface WAN
R5# show ip interface brief | include GigabitEthernet0/2
```

**Solution:**
```cisco
# Pastikan default route ada
R4# show ip route 0.0.0.0

# Jika tidak ada, tambahkan:
R4(config)# ip route 0.0.0.0 0.0.0.0 10.0.5.2

# Pastikan NAT aktif di R5
R5(config)# interface GigabitEthernet0/0
R5(config-if)# ip nat inside
R5(config-if)# exit
R5(config)# interface GigabitEthernet0/2
R5(config-if)# ip nat outside
```

---

### **Problem 2: RIP Routes Tidak Muncul**

**Symptoms:**
- Router 5 tidak terima routes dari internal networks

**Diagnosis:**
```cisco
R5# show ip rip database
R5# debug ip rip
```

**Solution:**
```cisco
# Pastikan RIP network statements benar
R5(config)# router rip
R5(config-router)# version 2
R5(config-router)# network 10.0.0.0
R5(config-router)# network 192.168.5.0
R5(config-router)# no auto-summary

# Pastikan interface tidak passive
R5(config-router)# no passive-interface GigabitEthernet0/0
```

---

### **Problem 3: NAT Tidak Berfungsi**

**Symptoms:**
- Internal IP muncul di traceroute ke internet
- Connection timeout ke website

**Diagnosis:**
```cisco
R5# show ip nat translations
R5# show ip nat statistics
R5# show access-list 1
```

**Solution:**
```cisco
# Reconfigure NAT
R5(config)# no ip nat inside source list 1 interface GigabitEthernet0/2 overload
R5(config)# ip nat inside source list 1 interface GigabitEthernet0/2 overload

# Clear NAT translations
R5# clear ip nat translation *
```

---

### **Problem 4: Slow Internet Speed**

**Possible Causes:**
1. MTU mismatch
2. QoS tidak configured
3. Too many NAT translations

**Solution:**
```cisco
# Adjust MTU
R5(config)# interface GigabitEthernet0/2
R5(config-if)# ip mtu 1492
R5(config-if)# ip tcp adjust-mss 1452

# Clear stale NAT entries
R5# clear ip nat translation *

# Implement QoS (optional)
R5(config)# class-map match-any PRIORITY-TRAFFIC
R5(config-cmap)# match access-group 150
R5(config)# policy-map QOS-WAN
R5(config-pmap)# class PRIORITY-TRAFFIC
R5(config-pmap-c)# priority 50000
```

---

## 📈 PERFORMANCE OPTIMIZATION

### **1. RIP Timer Optimization**

```cisco
router rip
 timers basic 15 90 90 120
 ! Update: 15s | Invalid: 90s | Holddown: 90s | Flush: 120s
```

### **2. Interface Bandwidth Settings**

```cisco
interface GigabitEthernet 0/2
 bandwidth 100000  ! 100 Mbps WAN
 ip mtu 1500
 duplex full
 speed 1000
```

### **3. DNS Caching**

```cisco
R5(config)# ip dns server
R5(config)# ip name-server 8.8.8.8
R5(config)# ip name-server 1.1.1.1
R5(config)# ip domain-lookup
```

---

## 📝 CONFIGURATION BACKUP

### **Backup Script (Bash)**

```bash
#!/bin/bash
# Backup all router configs via TFTP

TFTP_SERVER="192.168.4.10"
ROUTERS=("192.168.1.1" "192.168.2.1" "192.168.3.1" "192.168.4.1" "192.168.5.1")
DATE=$(date +%Y%m%d)

for router in "${ROUTERS[@]}"; do
    echo "Backing up $router..."
    # Trigger backup via SSH (jika support)
    ssh admin@$router "copy running-config tftp://$TFTP_SERVER/$router-$DATE.cfg"
done

echo "Backup completed!"
```

---

## 🎓 SUMMARY & BEST PRACTICES

### **Key Points:**
1. ✅ **5 Router Setup**: 3 Field + 1 Office Hub + 1 Edge Gateway
2. ✅ **RIP Version 2**: Dynamic routing dengan no auto-summary
3. ✅ **NAT/PAT**: Internet access untuk semua internal networks
4. ✅ **Security**: Firewall, ACL, SSH, password encryption
5. ✅ **Scalability**: Mudah tambah zona baru (R6, R7, dst)

### **Best Practices:**
- 🔐 Always use SSH, disable Telnet
- 🔒 Implement strong passwords (min 10 chars)
- 📊 Enable SNMP untuk monitoring
- 🕒 Configure NTP untuk time sync
- 💾 Regular config backups
- 🔥 Implement firewall di Edge Router
- 📝 Document semua changes

### **Maintenance Checklist:**
- [ ] Weekly: Cek `show ip route` di semua router
- [ ] Monthly: Review logs (`show logging`)
- [ ] Monthly: Update firmware jika ada security patches
- [ ] Quarterly: Backup configurations
- [ ] Yearly: Audit security settings

---

## 📞 SUPPORT & CONTACT

**Technical Support:**
- Email: netadmin@melon-farm.local
- Phone: +62-xxx-xxxx-xxxx
- Emergency: 24/7 On-Call

**Documentation Version:** 2.0
**Last Updated:** December 2025
**Author:** Network Team - Kebun Melon XYZ

---

**END OF DOCUMENT**

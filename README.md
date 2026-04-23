# Instituto Tecnológico de Costa Rica - Taller Integrador Server GR9

# APRS Server with iGate Integration and Tracker Visualization

This project implements an APRS (Automatic Packet Reporting System) server using APRSC, designed to communicate with an iGate and provide real-time visualization of a tracked module on a map interface.

## Overview

The system is built on **Ubuntu Server 24.04 LTS** and integrates APRSC with web-based management tools to create a functional APRS infrastructure. It allows receiving, processing, and displaying APRS packets from trackers via an iGate.

## System Architecture

```
[ APRS Tracker ] 
        ↓
     (RF Signal)
        ↓
      [ iGate ]
        ↓
   (Internet / TCP/IP)
        ↓
     [ APRSC Server ]
        ↓
   [ Web Interface / Map Visualization ]
```

## Technologies Used

- Ubuntu Server 24.04 LTS
- APRSC – APRS-IS core server
- Webmin – Web-based system administration
- APRS Protocol 

## Installation & Setup

### 1. Update system
```bash
sudo apt update && sudo apt upgrade -y
```

## Installing APRSC

### 1. Confifure package repository for apt.
Create a file a using 

```bash
deb http://aprsc-dist.he.fi/aprsc/apt noble main
```

Add the gpg key used to sign the packages.

```bash
gpg --keyserver keyserver.ubuntu.com --recv D43AD4708A2DA1139F250B3294E40E5320D8AE3C
sudo gpg --export D43AD4708A2DA1139F250B3294E40E5320D8AE3C > /etc/apt/trusted.gpg.d/aprsc.key.gpg
```

### 2. Install APRSC
```bash
sudo apt-get update
sudo apt-get install aprsc
```

### 3. Configure APRSC
 
```bash
sudo nano /opt/aprsc/etc/aprsc.conf
```
 
### Basic Server Configuration
 
Edit the configuration file and define the main parameters:
 
**Main Parameters:**
- `ServerId`: `my_callsing`
- `PassCode`: `my_passcode`
- `MyAdmin`: `"my_user, my_callsing"`
- `MyEmail`: `email@example.com`
 
**Listening Ports:**
- TCP/UDP 10152 → backbone (full feed)
- TCP/UDP 14580 → clients/igate
- UDP 8080 → packet sending
 
**APRS-IS Uplink Configuration:**
 
```
Uplink "Core rotate" full tcp rotate.aprs.net 10152
```
 
**Logs and Resources:**
- Run directory: `RunDir data`
- Log rotation: `LogRotate 10 5`
- File/connection limits: `FileLimit 10000`
- HTTP Status and Upload enabled on ports 14501 and 8080
 
**Example configuration snippet:**
 
```conf
ServerId TI2ABC
PassCode 22441
MyAdmin "server, TI2ABC"
MyEmail email@example.com
 
# Listening ports
Listen fullfeed  tcp 10152 fullfeed
Listen fullfeed  udp 10152 fullfeed
Listen igate     tcp 14580
Listen igate     udp 14580
Listen dupesent  udp 8080
 
# Uplink
Uplink "Core rotate" full tcp rotate.aprs.net 10152
 
# Resources
RunDir data
LogRotate 10 5
FileLimit 10000
```
 
---
 
## 3b. Connect a Client to Receive Traffic
 
After the server is running, you can connect a local client using a different SSID:
 
```bash
telnet localhost 14580
user TI2ABC-1 pass 22441 filter b/TI3WTI-10
```
 
This allows you to receive and filter traffic from a test iGate (e.g., TI3WTI-10).
 
---
 
## 3c. Manage the service with systemd
 
**Enable service to start automatically:**
```bash
sudo systemctl enable aprsc
```
 
**Start the service:**
```bash
sudo systemctl start aprsc
```
 
**Stop the service:**
```bash
sudo systemctl stop aprsc
```
 
**Restart the service:**
```bash
sudo systemctl restart aprsc
```
 
**View service status:**
```bash
sudo systemctl status aprsc
```
 
**View logs in real-time:**
```bash
tail -f /opt/aprsc/logs/aprsc.log
```
 
**View entire log:**
```bash
cat /opt/aprsc/logs/aprsc.log
```
 
---
 
## 3d. Update aprsc in the future
 
```bash
sudo apt-get update && sudo apt-get upgrade
```
 
---

### 5. Install Webmin
```bash
sudo apt install wget
wget http://www.webmin.com/download/deb/webmin-current.deb
sudo dpkg -i webmin-current.deb
sudo apt --fix-broken install -y
```

Access Webmin:
```
https://<server-ip>:10000
```
## Packets received
<img width="717" height="897" alt="image" src="https://github.com/user-attachments/assets/ddaf23bf-4707-457f-8ca0-383dd3ff5a12" />

## Features

- Real-time APRS packet processing
- Integration with RF-based iGate
- Centralized APRS-IS server
- Web-based system management
- Scalable architecture

## Project Structure

```
.
├── config/
├── scripts/
├── docs/
└── README.md
```

## Security Considerations

- Use firewall (ufw)
- Strong Webmin credentials
- Limit connections

---

## Architecture

### Backend
- `aprsc`: APRS-IS server that manages packet traffic  
- Connection to the global APRS network via *uplink*

### Frontend
- `TrackDirect`: real-time map visualization  
- WebSocket for dynamic updates

### Database
- PostgreSQL for historical data storage  

<img width="1154" height="881" alt="diagrama_red" src="https://github.com/user-attachments/assets/aae07874-70b9-4621-8e0f-120dcbd0b825" />
---

## Data Flow

1. Tracker sends APRS packets  
2. iGate retransmits to APRS-IS  
3. `aprsc` receives and processes  
4. TrackDirect collects data  
5. Data is stored in PostgreSQL  
6. Data is displayed on the map  

<img width="1159" height="585" alt="flujo_datos" src="https://github.com/user-attachments/assets/cf227ce6-be11-46e6-8006-aa3eeb6bb90a" />

---

## Components 

- **Port 14580** → filtered clients (TrackDirect)  
- **Port 10152** → full feed  
- **WebSocket** → `ws://<IP>:8090`  

---

## Result

Real-time visualization of APRS stations on a web map, with storage and historical data access.
<img width="1600" height="801" alt="image" src="https://github.com/user-attachments/assets/d2a4bf62-46a7-44d5-81bb-3a2f25268bbc" />
<img width="1600" height="802" alt="image" src="https://github.com/user-attachments/assets/f3dc204a-bfe5-43c5-a0f9-9292aba6ea8e" />

---

## Notes

- A valid APRS callsign and passcode are required  
- Can run entirely on a single machine  
- Scalable to integration with real iGates

## Testing

- Send APRS packets via iGate
- Check logs
- Verify on map

## Authors

- Carlos Gutierrez
- Jorge Schofield

## References

Canonical Ltd., ``Ubuntu Server Documentation (LTS),'' 2024. [online]. Available: https://ubuntu.com/server/docs. [Accessed: 2026].

\bibitem{ubuntu_server}
Canonical Ltd., ``Ubuntu Server Guide,'' 2024. [online]. Available: https://help.ubuntu.com. [Accessed: 2026].

\bibitem{webmin}
Webmin Developers, ``Webmin Documentation,'' 2024. [online]. Available: https://www.webmin.com. [Accessed: 2026].

\bibitem{aprsc}
aprsc Developers, ``aprsc: APRS-IS Server Software,'' 2024. [online]. Available: https://github.com/hessu/aprsc. [Accessed: 2026].

\bibitem{aprs_spec}
B. Bruninga, ``APRS Protocol Reference Specification,'' 2000. [online]. Available: http://www.aprs.org/doc/APRS101.PDF. [Accessed: 2026].

\bibitem{trackdirect}
TrackDirect Project, ``TrackDirect: APRS Tracking Platform,'' 2024. [online]. Available: https://github.com/qvarforth/trackdirect. [Accessed: 2026].

\bibitem{websocket}
IETF, ``RFC 6455: The WebSocket Protocol,'' 2011. [online]. Available: https://datatracker.ietf.org/doc/html/rfc6455. [Accessed: 2026].


## Notes

Educational and experimental APRS deployment project.

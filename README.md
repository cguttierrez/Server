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


## Testing

- Send APRS packets via iGate
- Check logs
- Verify on map

## Authors

- Carlos Gutierrez
- Jorge Schofield

## Notes

Educational and experimental APRS deployment project.

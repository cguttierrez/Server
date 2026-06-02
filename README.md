# Instituto Tecnológico de Costa Rica - Taller Integrador Server GR9

# APRS Server with iGate Integration and Tracker Visualization

This project implements an APRS (Automatic Packet Reporting System) server using APRSC, designed to communicate with an iGate and provide real-time visualization of a tracked module on a map interface.

## Overview

The system is built on **Ubuntu Server 24.04 LTS** and integrates APRSC with web-based management tools to create a functional APRS infrastructure. It allows receiving, processing, and displaying APRS packets from trackers via an iGate.

## Installation & Setup

### System Requirements
- OS: Ubuntu Server 24.04 LTS
- Processor: Any modern CPU or single-board computer.
- Memory: 256 MB to 512 MB
- Storage: 5 GB
- Internet access
- User with sudo priviliges
- Available ports: 
  - 14580 (APRS-IS)
  - 14501 (status)
  - 80 (web)
  - 10000 (interface)

### Step 1. Update system
```bash
sudo apt update && sudo apt upgrade -y
```

### Step 2. Install APRSC using apt-get

First, it's necessary to configure package repository for apt. Create a file a using:
```bash
deb http://aprsc-dist.he.fi/aprsc/apt noble main
```

Add the gpg key used to sign the packages:
```bash
gpg --keyserver keyserver.ubuntu.com --recv D43AD4708A2DA1139F250B3294E40E5320D8AE3C
sudo gpg --export D43AD4708A2DA1139F250B3294E40E5320D8AE3C > /etc/apt/trusted.gpg.d/aprsc.key.gpg
```

Next, download the package indexes and install aprsc:
```bash
sudo apt-get update
sudo apt-get install aprsc
```

### Step 3. Install docker
 ```bash
sudo apt install -y git docker.io docker-compose nginx php php-fpm
```

Enable docker:
 ```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Verify the installation:
 ```bash
docker --version
```

### Step 4. Clone the repository
 ```bash
git clone https://github.com/qvarforth/trackdirect
```

### Step 5. Install Webmin
```bash
sudo apt install wget
wget http://www.webmin.com/download/deb/webmin-current.deb
sudo dpkg -i webmin-current.deb
sudo apt --fix-broken install -y
```

Start Webmin and check its status:
```bash
sudo systemctl start webmin
sudo systemctl status webmin
```

You can access your Webmin interface by opening a web browser and navigating to your server's IP address and port. The default port will be 10000, which can be later changed:
```
https://<server-ip>:10000
```

### Step 6. Configure APRSC

Open the tools tab inside the Webmin dashboard and click the File Manager tab. Look for the copied repository and open the config folder.  

<img width="283" height="648" alt="image" src="https://github.com/user-attachments/assets/08c4cbb9-3432-45e1-a040-3b22ea954cf5" />

```
<Your_copied_path>/trackdirect/config
```

<img width="328" height="196" alt="image" src="https://github.com/user-attachments/assets/9a1adc18-cc90-49ec-8a10-765845a70887" />

Edit the configuration file and define the main parameters: 

**Main Parameters:**
- `ServerId`: `my_callsing`
- `PassCode`: `my_passcode`
- `MyAdmin`: `"my_user, my_callsing"`
 
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
ServerId TI3SRV
PassCode 18094
MyAdmin "Server, TI3SRV"
 
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
**Important**

You can use this link to generate a passcode based on your callsign:
```
https://www.iz3mez.it/aprs-passcode/
```
---

### Step 7. Start the server 
```bash
sudo docker compose up -d
```

Verify the APRSC container appears as Up:
```bash
docker ps
```

### Step 8. Access the Server
Open a browser and go to:
```
http://<SERVER_IP>
```

## Troubleshooting
View docker logs:
```bash
docker compose logs -f
```

Restart services:
```bash
docker compose restart
```

Check used ports:
```bash
sudo lsof -i -P -n
```

Fix container startup issues:
```bash
docker compose down
docker compose up --build -d
```

## Result example 

Real-time visualization of APRS stations on a web map, with storage and historical data access.
<img width="1600" height="801" alt="image" src="https://github.com/user-attachments/assets/d2a4bf62-46a7-44d5-81bb-3a2f25268bbc" />
<img width="1600" height="802" alt="image" src="https://github.com/user-attachments/assets/f3dc204a-bfe5-43c5-a0f9-9292aba6ea8e" />

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


## Data Flow

1. Tracker sends APRS packets  
2. iGate retransmits to APRS-IS  
3. `aprsc` receives and processes  
4. TrackDirect collects data  
5. Data is stored in PostgreSQL  
6. Data is displayed on the map  

<img width="1159" height="585" alt="flujo_datos" src="https://github.com/user-attachments/assets/cf227ce6-be11-46e6-8006-aa3eeb6bb90a" />

## Authors

- Carlos Gutierrez
- Jorge Schofield

## References

Canonical Ltd., ``Ubuntu Server Documentation (LTS),'' 2024. [online]. Available: https://ubuntu.com/server/docs. [Accessed: 2026].

Canonical Ltd., ``Ubuntu Server Guide,'' 2024. [online]. Available: https://help.ubuntu.com. [Accessed: 2026].

Webmin Developers, ``Webmin Documentation,'' 2024. [online]. Available: https://www.webmin.com. [Accessed: 2026].

aprsc Developers, ``aprsc: APRS-IS Server Software,'' 2024. [online]. Available: https://github.com/hessu/aprsc. [Accessed: 2026].

B. Bruninga, ``APRS Protocol Reference Specification,'' 2000. [online]. Available: http://www.aprs.org/doc/APRS101.PDF. [Accessed: 2026].

TrackDirect Project, ``TrackDirect: APRS Tracking Platform,'' 2024. [online]. Available: https://github.com/qvarforth/trackdirect. [Accessed: 2026].

IETF, ``RFC 6455: The WebSocket Protocol,'' 2011. [online]. Available: https://datatracker.ietf.org/doc/html/rfc6455. [Accessed: 2026].


## Notes

Educational and experimental APRS deployment project.

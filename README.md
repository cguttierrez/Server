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
Before starting edit the configuration file. 

```bash
sudo nano /etc/aprsc/aprsc.conf
```

### 4. Enable and start service
```bash 
sudo systemctl enable aprsc
sudo systemctl start aprsc
```

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

# nzyme-docker
Run nzyme from within a docker container on a Raspberry Pi.

## Installation and Setup
Flash a Micro-SD card with the newest Raspberry Pi OS (tested with a Pi 3B+). Then, install docker:
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Clone this repository on your Raspberry Pi. Edit the configuration (see below). Finally, run `docker-compose up -d` (to start the containers in the background). Messages should appear in your GrayLog instance (if you have configured a GELF TCP input listening on the port from the `.env` file).

## Configuration
The docker compose file automatically detects a `.env` file. Copy `.env.example` to `.env` and edit it. The GrayLog server and the WIFI interface will automatically be replaced in the nzyme configuration during docker container creation. This means, that 

## Monitor Mode
Most adapters support nzyme changing to monitor mode automatically. If this is not the case with your adapter, change line 131 in `nzyme/nzyme.conf` to `true`. In this case, the adapter must be set into monitor mode manually.
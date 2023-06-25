# Garden control
> Based on example MQTT app from esp-idf framework

## Prerequisite
Assumes you already have an MQTT broker service running or are going to use a public service like test.mosquitto.org to test out the pub/sub of the messages (strongly recommend running local server to keep control/messages in the LAN: https://mosquitto.org/)


## Setup
1. Install esp-idf (use espressif vs-code plugin or manually install)
1. Run the `install.sh` and `. export.sh` scripts or corresponding batch files to setup the env vars for idf.py build system
1. Run `idf.py menuconfig` adjust the `Example Configuration` with the appropriate MQTT host server URL and adjust the `Example Connection Configuration` with the appropriate WiFi username/password.
1. Hit S to save the `sdkconfig` file and Q to quit
1. Run `idf.py build flash monitor` to test the compilation and flash any esp32 devices found on the USB ports

## Usage
The firmware is setup to control a 4-relay module board from Xinyuan-LilyGo a similar firmware comes with the device but depends on Arduino as opposed to IDF and I wanted to have a version based on IDF since it would be easier to migrate to AWS FreeRTOS and generally opens the ability to have task based execution of any sensor reading and actuation.

https://github.com/Xinyuan-LilyGO/LilyGo-T-Relay/blob/main/examples/T-Relay-4-ESP32/simple/Simple.ino

The firmware will connect to the wifi network then publishes a message `/garden/autoWatering` with a message of `online`, it then subscribe to the topics `/garden/watering` and `/garden/lighting` awaiting messages with values of `on` or `off`.  The `on/off` command value is used to then turn on or off a relay which controls power for the pump and lighting.

To test the firmware you can use the mosquitto_pub and mosquitto_sub apps which can be installed with: `sudo apt install mosquitto-clients`

Terminal 1 (Listen for all messages):

```bash
mosquitto_sub -t "#" -h 192.168.1.11
```

Terminal 2 (Broadcast specific messages):

```bash
mosquitto_pub -t /garden/watering -h 192.168.1.11 -m "on"
mosquitto_pub -t /garden/watering -h 192.168.1.11 -m "off"
```

Where 192.168.1.11 is the MQTT host IP

---

# Base Example README

## MQTT demo application that runs on linux

### Overview

This is a simple example demonstrating connecting to an MQTT broker, subscribing and publishing some data.
This example uses IDF build system and could be configured to be build and executed:
* for any ESP32 family chip
* for linux target

### How to use example

#### Hardware Required

To run this example, you need any ESP32 development board or just PC/virtual machine/container running linux operating system.

#### Host build modes

Linux build is supported in these two modes:
* `WITH_LWIP=0`: Without lwIP component. The project uses linux BSD socket interface to interact with TCP/IP stack. There's no connection phase, we use the host network as users. This mode is often referred to as user-side networking.
* `WITH_LWIP=1`: Including lwIP component, which is added to the list of required components and compiled on host. In this mode, we have to map the host network (linux TCP/IP stack) to the target network (lwip). We use IDF's [`tapif_io`](https://github.com/espressif/esp-idf/tree/master/examples/common_components/protocol_examples_tapif_io) component to create a network interface, which will be used to pass packets to and from the simulated target. Please refer to the [README](https://github.com/espressif/esp-idf/tree/master/examples/common_components/protocol_examples_tapif_io#readme) for more details about the host side networking.

#### Building on linux

1) Configure linux target
```bash
idf.py --preview set-target linux
```

2) Build the project with preferred components (with or without lwip)
```bash
idf.py -DWITH_LWIP=0 build  # Building without lwip (user networking)
idf.py -DWITH_LWIP=1 build  # Building with lwip (TAP networking)
```

3) Run the project

It is possible to run the project elf file directly, or using `idf.py` monitor target (no need to flash):
```bash
idf.py monitor
```
idf.py -DWITH_LWIP=0 build  # Building without lwip (user networking)

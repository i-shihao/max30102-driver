# Max30102 spo2 pulse Oximetry sensor driver

## Table of Contents
 - [Overview](#overview)
 - [Features](#features)
 - [Hardware Connections](#hardware-connections)
 - [Device Tree Example](#device-tree-example)
 - [Build and Install](#build-and-install)
 - [Usage](#usage)
 - [Examples](#examples)
 - [Troubleshooting](#troubleshootin)
 - [Screenshot](#screenshot)
 - [License](#license)

## Overview

 A max30102 spo2 heatrate oximeter sensor driver based on Linux i2c subsystem. Developed and tested on ARM-based
 platfroms such as Raspberry Pi 4 Model B. Exposes sensor data through Linux iio intefaces. Designed and developed for
 learning linux kernel device driver development.

## Features

 - Device tree support
 - Linux kernel I2C Subsystem based communication
 - IIO inteface for data channels
 - Interrupt driven data sample handeling
 - Runtime power management

## Hardware Connection

### Pin Connections

 | Sensor Pin | Connects to | Description |
 |------------|-------------|-------------|
 | VIN        | 3.3v        | Power supply|
 | GND        | GND         | Ground      |
 | SCL        | I2C-SCL     | Clock Line  |
 | SDA        | I2C_SDA     | Data line   |
 | INT        | GPIO-x      | Interupt    |

Note: Sensor pins IR and RED must be left untouched

## Device Tree Example

 Device tree node for max30102 driver
```
i2c@7e804000 {
    max30102@57 {
         compatible ="adi,max30102";
         status =  "okay";
         reg = <0x57>;
         interrupt-gpios = <&gpio 17 0x01>;
        vdd-supply = <&reg_3v3>;
        vled-supply = <&reg_3v3>;
     };
};
```

## Build and Install

### Requirements
 - Linux kernel: 6.18
 - Architecture: ARM64
 - Board: Raspberry Pi 4 Model B
 - Compiler: GCC (15.2.1)

### Build
    sudo  make
### Insert driver
    sudo insmod max30102.ko
### Verify
    sudo dmesg | grep max30102
### Remove
    sudo rmmod max30102

## Usage

 Following sucessful module insertion the driver probes the device and initializes the chip
 following configured init sequence. The driver exposes iio interface entries under its device path
 can be used to read values from the sensor through its iio interface entries. Dmesg and other kernel
 logs can be used to verify driver probe , initialization and functionality.

### Check device  entry
    ls /sys/bus/i2c/devices/i2c-1/1-00057/
### Check entries
    ls /sys/bus/i2c/devices/i2c-1/1-0057/iio:deviceX/

### Enable scan channels
    echo 1 | sudo tee scan_elements/in_intensity_ir_en
    echo 1 | sudo tee scan_elements/in_intensity_red_en

### Enable buffer
    echo 1 | sudo tee buffer/enable

### Read raw binary stream
    sudo hexdump /dev/iio:deviceX

## Examples

    cd /sys/bus/iio/devices/iio:deviceX

    echo 1 | sudo tee scan_elements/in_intensity_ir_en

    echo 1 | sudo tee scan_elements/in_intensity_red_en

    echo 1 | sudo tee buffer/enable

    sudo hexdump /dev/iio:deviceX

## Troubleshooting

### Check module dependencies
    modinfo max30102.ko
### Ensure modules are present
    lsmod | grep industrialio
    lsmod | grep kfifo_buf
### If no loaded load manually
    sudo modprobe industrialio
    sudo modprobe kfifo_buf
### Try inserting the driver again
    sudo insmod max30102.ko
### Verify device 
    i2cdetect -y 1
### Check module status now
    sudo dmesg | grep max30102
 
## License

 This driver is license under the GNU General Public License version v2.0 GPL-2.0. For more information
 refer to License file.



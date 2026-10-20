# ASU Greenhouse Monitoring System

A custom greenhouse monitoring project built around STM32 microcontrollers. The repository contains a master-slave embedded system designed to read environmental data for multiple greenhouse zones, display the information over UART, and simulate realistic monitoring behavior for five crop sections.

This project is implemented primarily in C and is structured as two STM32 CubeIDE projects:

- `milestone_3_master` – the control/master node
- `milestone_3_slave` – the sensor/response node

The repository also includes a simulation video and project file for demonstration and validation.

## Project Overview

The greenhouse system is organized as a 5-zone monitoring network:

- Zone 1: Tomato
- Zone 2: Cucumber
- Zone 3: Pepper
- Zone 4: Lettuce
- Zone 5: Onion

Each zone contains simulated environmental conditions, including:

- Temperature
- Soil humidity
- Crop identification

The master controller receives a zone ID from the user via UART, requests data from the slave node over SPI, interprets the response, converts it to actual values, and prints the status for that greenhouse zone.

## System Architecture

### Master Node (`milestone_3_master`)

The master board is responsible for:

- Initializing the UART interface
- Initializing SPI communication
- Waiting for user input (1 to 5)
- Sending a request to the slave for the selected zone
- Reading a 3-byte response
- Decoding the temperature and humidity payload
- Displaying the greenhouse condition over UART

The main logic in `milestone_3_master/Core/Src/main.c` does the following:

1. Reads a numeric zone ID from the serial terminal.
2. Builds a request packet for the selected zone.
3. Uses GPIO control to select the SPI transaction.
4. Sends the request over SPI to the slave.
5. Receives a 24-bit response representing:
   - 12 bits for temperature
   - 12 bits for humidity
6. Converts the raw ADC-like values into meaningful readings.
7. Prints the output in a clean greenhouse status report.

### Slave Node (`milestone_3_slave`)

The slave board stores pre-defined environmental data for the five zones in a `Data` structure:

- ID
- Temperature
- Humidity

It is configured as an SPI slave and waits for a request from the master. Once it receives the request, it:

- Matches the ID to a stored zone profile
- Converts the stored values to raw encoded data
- Sends the 24-bit payload back to the master

The slave logic is implemented in `milestone_3_slave/Core/Src/main.c`.

## Communication Protocol

The communication between the master and slave is based on SPI.

### Master Request

The master sends a 2-byte request packet:

- Upper byte and lower byte form a 16-bit request ID
- Example: zone 1 request = `0x1001`

### Slave Response

The slave replies with 3 bytes containing a packed 24-bit payload:

- Bits 23–12: temperature data
- Bits 11–0: humidity data

This is then decoded using the formula:

- Temperature = raw_temp * 500 / 4095
- Humidity = raw_humidity * 100 / 4095

The result is displayed in the output message, such as:

```text
--- Greenhouse Zone 1 Status ---
Product       : Tomato
Temperature   : 24.5 C
Soil Humidity : 65 %
```

## Hardware and Software Tools

### Hardware

This project is designed for STM32 microcontroller boards using the STM32F1 series, especially the STM32F103C8Tx family.

Typical hardware components include:

- STM32 development boards
- SPI communication wiring between master and slave
- UART serial connection for user monitoring
- GPIO-controlled chip select / transaction control
- Simulated greenhouse sensor logic

### Software

The project uses:

- C programming language
- STM32 HAL drivers
- STM32CubeIDE project structure
- SPI and UART peripheral configuration

## Repository Structure

```text
ASU_Greenhouse/
├── README.md
├── Simulation Video & File/
│   ├── Simulation Video.mp4
│   └── greenhouse_simulation.pdsprj
├── milestone_3_master/
│   ├── .cproject
│   ├── .mxproject
│   ├── .project
│   ├── Core/
│   ├── Drivers/
│   ├── Debug/
│   ├── STM32F103C8TX_FLASH.ld
│   └── milestone_3_master.ioc
├── milestone_3_slave/
│   ├── .cproject
│   ├── .mxproject
│   ├── .project
│   ├── Core/
│   ├── Drivers/
│   ├── Debug/
│   ├── STM32F103C8TX_FLASH.ld
│   └── milestone_3_slave.ioc
└── ...
```

## Key Files

### `milestone_3_master/Core/Src/main.c`

Contains the master node logic for:

- UART input handling
- SPI communication
- Raw data decoding
- Sensor request/response flow
- greenhouse status output

### `milestone_3_slave/Core/Src/main.c`

Contains the slave node logic for:

- zone database definition
- SPI slave reception
- encoding and sending environmental payloads

### `Simulation Video & File/`

Includes demonstration media and simulation files for project validation or presentation.

## How the System Works

1. The user enters a greenhouse zone number from 1 to 5.
2. The master sends the corresponding zone request over SPI.
3. The slave looks up the preset values for that zone.
4. The slave encodes the temperature and humidity into a 24-bit packed value.
5. The master receives the value and decodes it.
6. The system displays the crop name, temperature, and soil moisture.
7. The process repeats for each user command.

## Example Output

```text
--- Greenhouse Zone 3 Status ---
Product       : Pepper
Temperature   : 21.8 C
Soil Humidity : 85 %
```

## Build and Run Instructions

### Requirements

- STM32CubeIDE or equivalent ARM toolchain
- STM32F103C8Tx-compatible boards
- USB/UART serial terminal (for example, PuTTY, RealTerm, or Tera Term)
- SPI wiring between the two MCUs

### Steps

1. Open the `milestone_3_master` project in STM32CubeIDE.
2. Open the `milestone_3_slave` project in STM32CubeIDE.
3. Configure the hardware and compile both projects.
4. Flash the master firmware to the master board.
5. Flash the slave firmware to the slave board.
6. Connect the SPI lines and UART serial port.
7. Run the system and enter a value from `1` to `5`.
8. Observe the greenhouse zone status on the serial monitor.

## Features

- Multi-zone greenhouse monitoring
- Temperature sensing simulation
- Soil humidity monitoring
- Crop-specific zone data
- Master/slave SPI architecture
- UART-based user interface
- Easy expansion for additional crop zones or sensor types

## Notes

- The project is intended for embedded systems education and prototype greenhouse automation.
- The climate values are stored as preset data in the slave node and represent a simulated greenhouse environment.
- The repository does not currently include a formal license file.
- The project was created as a practical embedded systems application using STM32 hardware and C development.

## Summary

This project demonstrates a greenhouse control and monitoring system using two STM32 microcontrollers. The master reads a user-selected zone, communicates with a slave node over SPI, and reports the environment status for that area. It is a useful embedded systems project that combines communication protocols, environmental monitoring, and microcontroller-based control.

## Project Status

The repository is organized and ready for STM32 firmware development and demonstration purposes. It includes the core firmware for both master and slave operation, along with simulation media for presentation or testing.

---

Created for the ASU Greenhouse project repository.

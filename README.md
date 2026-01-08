# JSON-Serialization-Library-for-w-M-Bus-Related-Firmware
Embedded-friendly C library for serializing structured smart-meter (w-M-Bus–related) data into a strictly defined JSON format. Designed for STM32 firmware, transport-agnostic, with controlled memory usage and no external JSON dependencies.
Project Overview

This project implements an embedded-friendly JSON serialization library for smart-meter / w-M-Bus–related firmware use cases.

The library converts structured meter and gateway data into a strictly predefined JSON format, suitable for forwarding to a gateway or backend system.

Scope intentionally excludes:

w-M-Bus radio communication

OMS protocol handling

Encryption

Real meter or hardware interaction

The focus is on:

Clean software architecture

Deterministic JSON serialization

Embedded suitability

Robust memory handling

Clear API design

Chosen Platform & Language
Platform

STM32CubeIDE (STM32 microcontrollers)

Justification:

Industry-standard embedded development environment

Commonly used in smart-meter and gateway firmware

Demonstrates realistic embedded integration

Project remains hardware-independent

No physical hardware is required to evaluate this assignment.

Programming Language

C (C99)

Justification:

Widely used in embedded systems

Predictable memory usage

No runtime allocation required

Easy integration into any MCU or RTOS project

Project Structure
/
├── Core/
│   ├── Inc/
│   │   └── wmbus_json.h
│   └── Src/
│       ├── wmbus_json.c
│       └── main.c
├── README.md
└── LICENSE (optional)

Data Model

The following internal data structures are used (simplified description):

Gateway Metadata

gatewayId (string)

date (YYYY-MM-DD)

deviceType (string)

interval_minutes (integer)

total_readings (integer)

Measurement Data

device_count (integer)

List of device readings:

media

meter

deviceId

unit

List of data points:

timestamp

meter_datetime

total_m3 (float)

status

All structures are defined in wmbus_json.h.

Mandatory JSON Output Format

The library generates exactly the following structure:

[
  {
    "gatewayId": "gateway_1234",
    "date": "1970-01-01",
    "deviceType": "stromleser",
    "interval_minutes": 15,
    "total_readings": 1,
    "values": {
      "device_count": 1,
      "readings": [
        {
          "media": "water",
          "meter": "waterstarm",
          "deviceId": "stromleser_50898527",
          "unit": "m3",
          "data": [
            {
              "timestamp": "1970-01-01 00:00",
              "meter_datetime": "1970-01-01 00:00",
              "total_m3": 107.752,
              "status": "OK"
            }
          ]
        }
      ]
    }
  }
]

Important Guarantees

Field names are unchanged

Structure is unchanged

Numbers are not serialized as strings

No additional fields

Outer JSON element is always an array

Public API
Function Prototype
int serialize_to_json(
    const gateway_data_t *input,
    char *output_buffer,
    size_t buffer_size
);

Behavior

Serializes input data into JSON

Writes result into caller-provided buffer

Always null-terminates on success

No dynamic memory allocation

Transport-agnostic

Return Codes

0 → Success

-1 → Invalid input

-2 → Output buffer too small

Memory & Safety Considerations

No malloc / free

Controlled stack usage

Buffer size is checked before every write

Uses snprintf to prevent overflows

Maximum JSON size is documented and deterministic

Example Application

The example application in main.c:

Creates sample gateway and device data

Calls the JSON serialization library

Stores the JSON output in a buffer

Output Inspection

The generated JSON can be:

Inspected via debugger watch window

Forwarded to any transport layer (UART, MQTT, file, etc.)

Transport is intentionally not implemented to keep the library reusable.

Build Instructions
STM32CubeIDE

Open STM32CubeIDE

Import project:

File → Import → General → Existing Projects into Workspace


Select the project directory

Build:

Project → Build Project


No hardware is required.

Running the Project

This project is a library demonstration, not a standalone firmware application.

The example code executes in main()

JSON output is written into a buffer

Output can be inspected via debugger or reused elsewhere

For functional verification, the same code can also be compiled and run on a host PC using GCC.

Design Decisions & Assumptions

JSON is generated manually (no external libraries)

Fixed schema ensures backend compatibility

Library is transport-agnostic by design

Focus is on clarity, safety, and maintainability

Hardware-specific functionality is intentionally excluded

Possible Extensions

Multiple devices and multiple data points

Support for different media types

Integration with UART / MQTT / HTTP

Unit tests for boundary conditions

Streaming JSON generation for very large payloads

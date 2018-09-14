﻿# Open Thermal Camera Protocol
Open Thermal Camera Protocol between Android application and STM32F042F6 hardware

# Version - 0.1

## Protocol structure
Message based protocol featuring commands and responses. Encoded with COBS protocol. 
Commands are issued by host
Respondes are issued by device

## Endianess
The protocol uses big endian for transmission of multibyte values

### Message
| Message start | Command / Response | Message end |
| ------ | ------ | ------ |
| 0x00 | COBS encoded command or response | 0x00 |

### Command 
| Command | Data length | Data |
| ------ | ------ | ------ |
| 1 Byte (unsigned) | 2 Bytes (unsigned) | {dataLength} bytes |

#### List of commands
| Command | Data length | Data | Name | Description |
| ------ | ------ | ------ | ----- | ----- |
| 0x00 | 1 | int8_t | Ping | Pings the device with a byte of data which is multiplied by 2 and responsed back to application |
| 0x01 | 0 | / | DumpEE | Request a eeprom dump of sensor | 
| 0x02 | 0 | / | GetFrameData | Request a frame data once |
| 0x03 | 1 | uint8_t - resolution (see resolutions) | SetResolution | Sends the desired resolution at which the sensor should sample the image |
| 0x04 | 0 | / | GetCurResolution | Request the current resolution at which sensor samples the image |
| 0x05 | 1 | uint8_t - refresh rate (see refresh rates) | SetRefreshRate | Sends the desired refresh rate at which the sensor should sample the image |
| 0x06 | 0 | / | GetRefreshRate | Request the current refresh rate at which sensor samples the image |
| 0x07 | 1 | uint8_t - mode (see modes) | SetMode | Sends the desired mode of subframe storing |
| 0x08 | 0 | / | GetCurMode | Request the current mode of subframe storing |
| 0x09 | 1 | uint8_t - auto (see auto frame sending) | SetAutoFrameDataSending | Starts or stops the automated frame data sending | 


### Response
| Response | Data length | Data code | Data |
| ------ | ------ | ----- | ------ |
| 1 Byte (unsigned) | 2 Bytes (unsigned) | 1 Byte (signed) | {dataLength} bytes |

#### List of responses
| Response | Data length | Data code | Data | Name | Description |
| ------ | ------ | ------ | ----- | ----- | ----- |
| 0x00 | 1 | 0x00 | int8_t - ping command value * 2 | Ping | Responds to ping and multiply the received value by 2 |
| 0x01 | 832*2 | 0 ok, -1 nack | uint16_t[832] | DumpEE | EEPROM dump of sensor | 
| 0x02 | 834*2 | 0 ok, -1 nack, -8 I2C frequency too low | uint16_t[834] | GetFrameData | Responds with frame data |
| 0x03 | 0 | 0 ok, -1 nack, -2 written value not same | / | SetResolution | Respondes with status |
| 0x04 | 1 | 0 ok, -1 nack | uint8_t - resolution (see resolutions) | GetCurResolution | Responds with current resolution at which sensor samples the image |
| 0x05 | 0 | 0 ok, -1 nack, -2 written value not same | / | SetRefreshRate | Respondes with status |
| 0x06 | 1 | 0 ok, -1 nack | uint8_t - refresh rate (see refresh rates) | GetRefreshRate | Responds with current refresh rate at which sensor samples the image |
| 0x07 | 0 | 0 ok, -1 nack, -2 written value not same | / | SetMode | Sends the desired mode of subframe storing |
| 0x08 | 1 | 0 ok, -1 nack | uint8_t - mode (see modes) | GetCurMode | Request the current mode of subframe storing |
| 0x09 | 1 | 0 ok | uint8_t - auto (see auto frame sending) | SetAutoFrameDataSending | Respondes with previous auto farme sending value | 

## Additional information

### Refresh rates
| Value | Rate |
| ----- | ----- |
| 0x00 | 0.5Hz |
| 0x01 | 1Hz |
| 0x02 | 2Hz |
| 0x03 | 4Hz |
| 0x04 | 8Hz |
| 0x05 | 16Hz |
| 0x06 | 32Hz |
| 0x07 | 64Hz |


### Resolutions
| Value | Resolution |
| ----- | ----- |
| 0x00 | 16-bit |
| 0x01 | 17-bit |
| 0x02 | 18-bit |
| 0x03 | 19-bit |


### Modes
| Value | Mode |
| ----- | ----- |
| 0x00 | interleaved |
| 0x01 | chess pattern |
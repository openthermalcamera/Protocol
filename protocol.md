# Open Thermal Camera Protocol
Open Thermal Camera Protocol between Android application and STM32F042F6 hardware

# Version - 0.1

## Description
Message based protocol featuring commands and responses. Encoded with COBS protocol. 
Commands are issued by host
Responds are issued by device

## COBS - Consistent overhead byte stuffing 
Replaces all 0x00 bytes from data with different values and 2 bytes of overhead (data length < 254 )

This enables the message end delimiters. It also provides for message resynchronization in case of faulty messages

### COBS - Implementation / library
COBS implementation in C by **Craig McQueen** : https://github.com/cmcqueen/cobs-c

Port of above mentioned libary in Java by **TheMarpe** : https://github.com/themarpe/cobs-java

## Protocol - Endianess
The protocol uses **big endian** for transmission of multibyte values

## Protocol - Message
| Command / Response | Message end delimiter |
| ------ | ------ |
| COBS encoded command or response | 0x00 |

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
| Response | Data code | Data length | Data |
| ------ | ------ | ----- | ------ |
| 1 Byte (unsigned) | 1 Byte (signed) | 2 Bytes (unsigned) | {dataLength} bytes |

#### List of responses
| Response | Data code | Data length | Data | Name | Description |
| ------ | ------ | ------ | ----- | ----- | ----- |
| 0x00 | 0 ok | 1 | int8_t - ping command value * 2 | Ping | Responds to ping and multiply the received value by 2 |
| 0x01 | 0 ok, -1 nack | 832*2 | uint16_t[832] | DumpEE | EEPROM dump of sensor | 
| 0x02 | 0 ok, -1 nack, -8 I2C frequency too low | 834*2 | uint16_t[834] | GetFrameData | Responds with frame data |
| 0x03 | 0 ok, -1 nack, -2 written value not same | 0 | / | SetResolution | Respondes with status |
| 0x04 | 0 ok, -1 nack | 1 | uint8_t - resolution (see resolutions) | GetCurResolution | Responds with current resolution at which sensor samples the image |
| 0x05 | 0 ok, -1 nack, -2 written value not same | 0 | / | SetRefreshRate | Respondes with status |
| 0x06 | 0 ok, -1 nack | 1 | uint8_t - refresh rate (see refresh rates) | GetRefreshRate | Responds with current refresh rate at which sensor samples the image |
| 0x07 | 0 ok, -1 nack, -2 written value not same | 0 | / | SetMode | Sends the desired mode of subframe storing |
| 0x08 | 0 ok, -1 nack | 1 | uint8_t - mode (see modes) | GetCurMode | Request the current mode of subframe storing |
| 0x09 | 0 ok | 1 | uint8_t - auto (see auto frame sending) | SetAutoFrameDataSending | Respondes with previous auto farme sending value | 

## Protocol - Additional information

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

### Auto frame sending
| Value | function |
| ----- | ----- |
| 0x00 | Disabled |
| 0x01 | Enabled |
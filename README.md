# SimpleGS - Simple Ground Station

Serial decoder for UBC Rocket's COBS-encoded, CRC-protected protobuf packets.

## Packet Format

The firmware sends packets in the following format:

1. **Protobuf payload** - Serialized `HelloWorldPacket`
2. **CRC32** - 4 bytes, little-endian (standard CRC-32 polynomial)
3. **COBS encoding** - The payload+CRC is COBS encoded
4. **Delimiter** - `0x00` byte marks end of packet

## Installation

```bash
uv sync
```

## Usage

```bash
# Basic usage
uv run simple-gs /dev/ttyUSB0

# With custom baud rate
uv run simple-gs /dev/ttyACM0 -b 9600

# Verbose mode (shows raw hex data)
uv run simple-gs /dev/ttyUSB0 -v

# Custom timeout
uv run simple-gs /dev/ttyUSB0 -t 2.0
```

## Example Output

```
Opening /dev/ttyUSB0 at 115200 baud...
Connected. Listening for packets...

[1] HelloWorldPacket:
       counter = 42
       message = "Hello from rocket!"

[2] HelloWorldPacket:
       counter = 43
       message = "Hello from rocket!"
```

## Regenerating Protobuf

If you update the `.proto` file:

```bash
protoc --python_out=simple_gs --proto_path=falcon-protos falcon-protos/HelloWorldPacket.proto
```

## CRC Mismatch

If the CRC doesn't match, you'll see a warning but the packet will still be decoded:

```
[WARNING] CRC mismatch! Received: 0xDEADBEEF, Computed: 0x12345678
[1] HelloWorldPacket:
       counter = 42
       message = "Hello from rocket!"
```

# SimpleGS - Simple Ground Station

Serial decoder for UBC Rocket's COBS-encoded, CRC-protected protobuf packets.

## Packet Format

The firmware sends packets in the following format:

1. **Protobuf payload** - Serialized `TelemetryPacket`
2. **CRC16** - 2 bytes, little-endian (CRC16-CCITT, init 0x0000)
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

[1] t=    1000ms | STANDBY        | Alt:     0.00m | Vel:    0.00m/s | GPS: 49.26061,-123.24599 (8sats) | B0:OK B1:OK
[2] t=    1100ms | STANDBY        | Alt:     0.00m | Vel:    0.00m/s | GPS: 49.26061,-123.24599 (8sats) | B0:OK B1:OK
```

## Regenerating Protobuf

If you update the `.proto` files:

```bash
protoc --python_out=simple_gs -Ifalcon-protos falcon-protos/TelemetryPacket.proto falcon-protos/HelloWorldPacket.proto
```

## CRC Mismatch

If the CRC doesn't match, you'll see a warning but the packet will still be decoded:

```
[WARNING] CRC mismatch! Received: 0xDEAD, Computed: 0x1234
[1] t=    1000ms | STANDBY        | Alt:     0.00m | Vel:    0.00m/s | GPS: 49.26061,-123.24599 (8sats) | B0:OK B1:OK
```

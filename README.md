# stk500-esm

A modern, ESM-compatible, TypeScript implementation of the STK500v1 protocol for programming Arduino boards directly from Node.js or the browser.

## Features

- Full JavaScript/TypeScript implementation of the STK500v1 programmer
- ESM (ECMAScript Modules) compatible
- Can be used in Node.js or browser environments
- No dependency on avrdude or the Arduino IDE
- TypeScript support for improved developer experience

## Installation

```bash
npm install stk500-esm
```

## Usage

Here's a basic example of how to use stk500-esm to program an Arduino:

```typescript
import { SerialPort } from "serialport";
import intel_hex from "intel-hex";
import Stk500 from "stk500-esm";
import fs from "fs/promises";

const stk = new Stk500();

const board = {
  name: "Arduino Uno",
  baud: 115200,
  signature: Buffer.from([0x1e, 0x95, 0x0f]),
  pageSize: 128,
  timeout: 400,
};

async function upload(path: string) {
  let serialPort;
  try {
    const hexData = await fs.readFile("path/to/your/sketch.hex", {
      encoding: "utf8",
    });
    const hex = intel_hex.parse(hexData).data;
    serialPort = new SerialPort({ path, baudRate: board.baud });
    await stk.bootload(serialPort, hex, board, false);
    console.log("Programming successful!");
  } catch (error) {
    console.error("Programming failed:", error);
  } finally {
    if (serialPort) {
      await serialPort.close();
    }
  }
}

upload("/dev/ttyACM0"); // Replace with your Arduino's serial port
```

## Examples

For more detailed examples, please check the `examples` folder in the repository. It contains several TypeScript files demonstrating how to use stk500-esm with different Arduino boards:

- `avr4809.ts`: Example for AVR4809 based boards
- `diecimila-duemilanove168.ts`: Example for Arduino Diecimila and Duemilanove (ATmega168)
- `duemilanove328.ts`: Example for Arduino Duemilanove (ATmega328)
- `nano.ts`: Example for Arduino Nano
- `uno.ts`: Example for Arduino Uno

These examples show how to set up the board configuration, read hex files, and upload them to the respective Arduino boards.

To run an example, use:

```bash
npx tsx examples/uno.ts /dev/ttyACM0
```

Replace `uno.ts` with the appropriate example file and `/dev/ttyACM0` with your Arduino's serial port.

## API

The main class `Stk500` provides the following methods:

- `bootload(stream: NodeJS.ReadWriteStream, hex: Buffer, opt: Board, use_8_bit_addresses = false): Promise<void>`
- `sync(stream: NodeJS.ReadWriteStream, attempts: number, timeout: number): Promise<Buffer>`
- `verifySignature(stream: NodeJS.ReadWriteStream, signature: Buffer, timeout: number): Promise<Buffer>`
- `upload(stream: NodeJS.ReadWriteStream, hex: Buffer, pageSize: number, timeout: number, use_8_bit_addresses = false): Promise<void>`
- `verify(stream: NodeJS.ReadWriteStream, hex: Buffer, pageSize: number, timeout: number, use_8_bit_addresses = false): Promise<void>`

For more detailed API information, please refer to the TypeScript definitions or the source code.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

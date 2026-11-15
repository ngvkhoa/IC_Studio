# Nano IC Studio

A browser tool for designing and testing logic ICs with an Arduino Nano (or Uno).
The web page draws the circuit, generates the test vectors, and checks the real chip through the board.

## What you need

- An Arduino Nano (ATmega328P) or an Arduino Uno. Both have the same pins used here.
- A ZIF-16 socket (or a DIP socket wired to the same 16 positions).
- Jumper wires.
- A USB cable.

## Wiring

The firmware uses 16 Nano pins for the 16 socket positions. Connect each socket position to the Nano pin in this table:

| ZIF socket position | Nano pin |
|---|---|
| 1 | D2 |
| 2 | D3 |
| 3 | D4 |
| 4 | D5 |
| 5 | D6 |
| 6 | D7 |
| 7 | D8 |
| 8 | D9 |
| 9 | D10 |
| 10 | D11 |
| 11 | D12 |
| 12 | D13 |
| 13 | A0 |
| 14 | A1 |
| 15 | A2 |
| 16 | A3 |

Socket layout (as drawn in the app): left column 1–8 from top to bottom, right column 16–9 from top to bottom.

### Power and ground

- The firmware drives the socket positions that you mark as **VCC** HIGH and the ones marked **GND** LOW, using the Nano pins above. You do not need to connect the Nano 5V pin to the chip.
- **Do not** connect the Nano 5V pin to any socket position.
- Connect one Nano **GND** pin to the socket position marked GND. This gives the chip a solid ground reference.
- The VCC pin is powered from a Nano I/O pin, so use this tool for small logic ICs (74HC series, NE555). Do not drive loads from the chip.

### Pins you do not use

Leave unused socket positions unconnected. Pins that are left floating are reported as `Z` and make the test fail.

## Quick start

1. Open the web page and choose the package (DIP-8, DIP-14 or DIP-16) in the top toolbar.
2. Pick an IC from the **IC** menu, or start with an empty board.
3. Check the power pins: each board needs one VCC and one GND.
4. Wire the IC's inputs and outputs to the function blocks on the canvas. Click a dot to start a wire, then click the target dot.
5. Plug the Nano into USB. Click **Connect COM** and choose the Nano's port.
6. If the Nano has no test firmware yet, click **Flash firmware**. This writes the built-in firmware (no file needed).
7. Insert the IC into the socket, then click **Run test**. The result appears in the **Test Vector** table.

Use **Simulation** mode to check the truth table in the browser without any hardware.

## Multiple pages

Changing the DIP package while a board has content opens a new page. Your current page is kept. Use the tabs under the top bar to switch.

## IC library

ICs are stored in `libraries/` as `.ic` files. `libraries/index.json` lists them and is rebuilt automatically by a GitHub Action whenever a `.ic` file changes.

Example `libraries/NE555.ic`:

```
NAME: NE555 (Timer IC)
DESC: Astable / monostable timer IC (DIP-8)
CATEGORY: Other
DIP: 8
VCC: 8
GND: 1
BLOCK: U1 ASTABLE 890 240
WIRE: PIN.2 -> U1.TRIG
...
```

To add an IC: create `libraries/NAME.ic`, commit, and push. The web page picks it up the next time it is opened.

How the web page syncs the library:

- New `.ic` files on the server are added.
- IC files you have not edited in the browser are updated when the server version changes.
- IC files you have edited in the browser are kept.
- IC files you deleted in the browser are not brought back.

The library is stored in the browser (localStorage) for each address, so `github.io` and a local server keep separate copies.

## Publish on GitHub Pages

1. Push this folder to a GitHub repository (`index.html`, `libraries/`, `.github/`, `README.md`).
2. Go to **Settings → Pages**, choose **Deploy from a branch**, branch `main`, folder `/ (root)`.
3. Open `https://<your-user>.github.io/<repo>/`.

If the Action does not rebuild `index.json`, check the **Actions** tab and make sure **Settings → Actions → General → Workflow permissions** is set to **Read and write permissions**.

## Run locally

Opening `index.html` directly (double-click) cannot read the `libraries/` folder, so the library will be empty. Use a local server instead:

- Windows: double-click `start_server.bat`
- macOS / Linux: run `./start_server.command`

Both need Python 3 installed.

## Troubleshooting

- **Library is empty**: open the page through the server (or GitHub Pages), or use **Load folder** in the IC menu.
- **COM port cannot be opened**: close the Arduino IDE serial monitor and any other program using the port.
- **Nano not responding**: click **Flash firmware**. If it still fails, try the bootloader option for your board (old bootloader 57600, or new Optiboot 115200).
- **Test shows FAIL on every row**: check the wiring table, that the IC is seated, and that VCC and GND are on the right socket positions.

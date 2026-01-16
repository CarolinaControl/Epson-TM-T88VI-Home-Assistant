# Home Assistant Receipt Printer Integration

A comprehensive guide to setting up the [ha-receipt_printer](https://github.com/zacs/ha-receipt_printer) integration specifically for the **Epson TM-T88VI**  thermal printers over a network connection.

---

## Table of Contents
1. Printer Hardware Setup
2. Network Configuration (Static IP)
3. Home Assistant Integration Installation
4. Configuration (YAML)
5. Testing & Printing Your First Receipt


---

## 1. Printer Hardware Prep
Before integrating with Home Assistant, you must identify the printer's current network status.

### The Self-Test (Finding the IP)
1. Turn the printer **OFF**.
2. Hold the **FEED** button down.
3. Turn the printer **ON** while continuing to hold **FEED**.
4. Release the button once it begins printing.
5. It will print a status sheet. Look for the **IP Address** section:
   * **Default IP:** Usually `192.168.192.168`.
   * **DHCP:** It may show an address assigned by your router.
   * **0.0.0.0:** The printer has no connection or is waiting for a DHCP lease.

---

## 2. Network Configuration
To ensure Home Assistant never loses the connection, you **must** assign a static IP address to the Epson.

### Using EpsonNet Config (Recommended)
1. Download the **EpsonNet Config** utility from the [Epson Support Site](https://download.epson-biz.com/).
2. Connect the printer to your network via Ethernet.
3. Open the utility; it will scan and find your TM-T88VI.
4. Select the printer and go to **TCP/IP** -> **Basic**.
5. Change the method to **Manual**.
6. Assign a static IP (e.g., `192.168.1.50`), Subnet Mask (`255.255.255.0`), and Gateway (your router).
7. Click **Transmit** to save. The printer will reboot.

### Using the Web Interface
1. Set your computer's IP to the same subnet as the printer (e.g., if the printer is 192.168.192.168, set your PC to 192.168.192.10).
2. Open a browser and type the printer's current IP from the printed reciept 
3.Login (Default: User: epson, Pass: <serial number of printer>).
4.Navigate to TCP/IP Settings and set your Static IP.

## 3.ha-receipt_printer Home Assistant Integration Installation
### HACS (Recommended)

1. In HACS, click on "Integrations"
2. Click the three dots in the top right corner
3. Select "Custom repositories"
4. Add the URL `https://github.com/zacs/ha-receipt_printer` and select the category "Integration"
5. Click the "+" button in the bottom right corner
6. Search for "Receipt Printer"
7. Click "Install"
8. Restart Home Assistant

### Manual Installation

1. Using the tool of choice open the directory (folder) for your HA configuration (where you find `configuration.yaml`)
2. If you do not have a `custom_components` directory there, you need to create it
3. In the `custom_components` directory create a new folder called `receipt_printer`
4. Download all the files from the `custom_components/receipt_printer/` directory in this repository
5. Place the files you downloaded in the new `receipt_printer` directory you created
6. Restart Home Assistant

## Configuration

1. In the Home Assistant UI, navigate to "Configuration" -> "Integrations"
2. Click the "+" button to add a new integration
3. Search for "Receipt Printer"
4. Enter the following information:
   - **Printer Name**: A friendly name for your printer (e.g., "Kitchen Receipt Printer")
   - **Printer IP Address**: The IP address of your receipt printer on your network
   - **Columns (Font A)**: Number of text columns for font A (default: 42, suitable for TM-T88VI)
   - **Columns (Font B)**: Number of text columns for font B (default: 56, suitable for TM-T88VI)
   - **Image Max Width**: Maximum image width in pixels (default: 512, suitable for TM-T88VI)

The integration will attempt to connect to the printer and verify it's accessible. The column and image settings can be adjusted to match your specific printer model.
Be patient as it may take 1-2 mins initially to get going.

### Printer Configuration Notes

Different printer models support different widths:
- **Epson TM-T88V/VI** (80mm): Font A = 42 columns, Font B = 56 columns, Image width = 512 pixels
- For other models, consult your printer's documentation or the [python-escpos printer profiles](https://python-escpos.readthedocs.io/en/latest/printer_profiles/available-profiles.html)

## Supported Printers

This integration uses the [python-escpos](https://github.com/python-escpos/python-escpos) library and supports **Epson ESC/POS compatible receipt printers** with network (TCP/IP) connectivity. 

Tested models:
- Epson TM-T88VI

Other ESC/POS compatible printers should work but may require adjusting the column widths and image size settings during configuration.

**Note**: USB and serial printers are not currently supported.

#### `receipt_printer.print_text`

Print text to the receipt printer with formatting options.

**Service Data:**
```yaml
service: receipt_printer.print_text
data:
  text: "Hello World!\nThis is a test receipt."
  align: center  # Options: left, center, right
  font: a  # Options: a, b
  bold: false
  double_height: false
  double_width: false
  wrap: true  # Enable automatic word wrapping (default: true)
  cut: true  # Cut paper after printing
```

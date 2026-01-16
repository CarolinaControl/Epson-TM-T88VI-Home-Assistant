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

## 3.Home Assistant ha-receipt_printer Integration Installation
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

## 4. Configuration

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

## 5. Usage & Formatting

Once the integration is active, you can send print jobs using the notify.epson_printer service. This can be done via scripts, automations, or the Developer Tools UI.
### Test
Basic Service Call to verify your setup, go to Developer Tools > Services and paste the following:

```
yaml
service: notify.epson_printer
data:
  message: "HELLO FROM HOME ASSISTANT"
  data:
    align: "center"
    format: "bold"
```

###Formatting Attributes
The Epson TM-T88 series supports hardware-level styling via the data block. You can combine multiple formats in a single call.AttributeOptionsDescriptionalignleft, center, rightHorizontal text alignmentformatbold, underline, double_height, double_widthText styling (can be combined)fonta, ba is standard (42 chars/line), b is small (56 chars/line)

### Automation YAML
```yaml
automation:
  - alias: "Print Welcome Receipt"
    trigger:
      - platform: state
        entity_id: person.john
        to: "home"
    action:
      - service: receipt_printer.print_text
        data:
          text: |
            ================================
                 WELCOME HOME JOHN!
            ================================
            
            Time: {{ now().strftime('%H:%M:%S') }}
            Date: {{ now().strftime('%Y-%m-%d') }}
            
            Temperature: {{ states('sensor.living_room_temperature') }}°C
            
            ================================
          align: center
          cut: true
```
## Troubleshooting

### Printer not connecting

1. Verify the printer's IP address is correct
2. Ensure the printer is powered on and connected to your network
3. Check that your Home Assistant instance can reach the printer's IP (try pinging it)
4. Verify no firewall is blocking communication on the printer's port (typically 9100)

### Print quality issues

- Clean the printer's print head according to manufacturer instructions
- Check paper quality and ensure it's compatible with thermal printing
- Verify paper is loaded correctly

### Paper status not updating

The paper status depends on the printer's sensors. Some printer models may not support all status queries. The integration will still function for printing even if status updates are unavailable.

## Credits
- 90% of this guide is from [ha-receipt_printer](https://github.com/zacs/ha-receipt_printer)
- Built on top of the [python-escpos](https://github.com/python-escpos/python-escpos) library
- Integration structure based on [integration_blueprint](https://github.com/ludeeus/integration_blueprint)

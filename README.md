```
 ██████╗██╗   ██╗██████╗ ███████╗██████╗ ██████╗ ██╗   ██╗███╗   ██╗██╗  ██╗
██╔════╝╚██╗ ██╔╝██╔══██╗██╔════╝██╔══██╗██╔══██╗██║   ██║████╗  ██║██║ ██╔╝
██║      ╚████╔╝ ██████╔╝█████╗  ██████╔╝██████╔╝██║   ██║██╔██╗ ██║█████╔╝
██║       ╚██╔╝  ██╔══██╗██╔══╝  ██╔══██╗██╔═══╝ ██║   ██║██║╚██╗██║██╔═██╗
╚██████╗   ██║   ██████╔╝███████╗██║  ██║██║     ╚██████╔╝██║ ╚████║██║  ██╗
 ╚═════╝   ╚═╝   ╚═════╝ ╚══════╝╚═╝  ╚═╝╚═╝      ╚═════╝ ╚═╝  ╚═══╝╚═╝  ╚═╝
```
# CYBERPUNK ESPHOME SENSOR DISPLAY

A fully animated cyberpunk-style sensor display for ESPHome with neon colors, 3D perspective effects and real-time Home Assistant integration.

---

## FEATURESesphome:

### Visual Effects
- **3D Perspective Grid** - Lines converge to a vanishing point in the center, creates depth effect
- **Pulsing Neon Borders** - All panels pulse in different neon colors
- **Animated Time Display** - Real-time clock with blue pulse effect
- **Dynamic Color Coding** - Temperature automatically changes color (blue=cold, orange=normal, magenta=warm)
- **Cyberpunk Boot Sequence** - Cool startup animation with status updates

### Sensor Data
- 🌡️ **Temperature** - With digital thermometer bar visualization
- 💨 **Air Quality** - NOx and VOC measurements
- 💧 **Humidity** - Percentage display
- ⚡ **Power Consumption** - kW or W, pulses at high consumption (>1.5kW)
- 🌫️ **Particulate Matter Sensors** - PM1, PM2.5, PM4, and PM10 measurements

### Intelligent Boot
- Waits for WiFi connection
- Waits for Home Assistant API connection
- Waits until all sensors have data
- Shows status with animated loading dots and progress bar

---

## HARDWARE REQUIREMENTS

### Minimum Required:
- **ESP8266** (ESP12E or similar)
- **ST7789V Display** (240x240 pixels)
- **Home Assistant** with ESPHome integration

### Pin Configuration:
```yaml
SPI Interface:
  CLK:  GPIO14
  MOSI: GPIO13

Display:
  DC:    GPIO00
  RESET: GPIO02

Backlight:
  PWM:   GPIO05
```

---

## INSTALLATION

### Step 1: ESPHome Setup
1. Copy `screen.yaml` to your ESPHome configuration folder
2. Adjust the device name to your preference:
   ```yaml
   esphome:
     name: my-cyberpunk-display
     friendly_name: Office Display
   ```

### Step 2: Secrets File
Create a `secrets.yaml` with your credentials:
```yaml
# WiFi Configuration
wifi_ssid: "YourWiFiNetwork"
wifi_password: "YourWiFiPassword"

# Fallback AP
ap_ssid: "Display-Fallback"
ap_password: "fallback123"

# API & OTA
api_key: "generate-with-esphome"
ota_password: "your-ota-password"
```

**Tip:** Generate a secure API key with: `esphome wizard`

### Step 3: Adjust Sensor Entities
Change ALL sensor entity IDs to your Home Assistant sensors:

```yaml
sensor:
  # Temperature sensor
  - platform: homeassistant
    id: office_temp
    entity_id: sensor.YOUR_TEMP_SENSOR  # ← Change this!

  # Air quality sensors
  - platform: homeassistant
    id: office_nox
    entity_id: sensor.YOUR_NOX_SENSOR   # ← Change this!
  
  - platform: homeassistant
    id: office_voc
    entity_id: sensor.YOUR_VOC_SENSOR   # ← Change this!
  
  - platform: homeassistant
    id: office_hum
    entity_id: sensor.YOUR_HUMIDITY_SENSOR  # ← Change this!
  
  # Power consumption
  - platform: homeassistant
    id: office_power
    entity_id: sensor.YOUR_POWER_SENSOR  # ← Change this!
  
  # Particulate matter sensors
  - platform: homeassistant
    id: pm_1u
    entity_id: sensor.YOUR_PM1_SENSOR   # ← Change this!
  
  - platform: homeassistant
    id: pm_25u
    entity_id: sensor.YOUR_PM25_SENSOR  # ← Change this!
  
  - platform: homeassistant
    id: pm_4u
    entity_id: sensor.YOUR_PM4_SENSOR   # ← Change this!
  
  - platform: homeassistant
    id: pm_10u
    entity_id: sensor.YOUR_PM10_SENSOR  # ← Change this!
```

### Step 4: Compile and Upload
```bash
# Via ESPHome CLI
esphome run screen.yaml

# Or via ESPHome Dashboard
# Click "INSTALL" button in web interface
```

**First time:** Connect ESP8266 via USB  
**After that:** OTA updates via WiFi possible!

---

## CUSTOMIZATION

### Change Colors
Modify the neon colors to your preference:
```cpp
// In display lambda, at the top:
Color neon_cyan = Color(0, 255, 255);      // #00FFFF
Color neon_magenta = Color(255, 0, 255);   // #FF00FF
Color neon_purple = Color(138, 43, 226);   // #8A2BE2
Color neon_pink = Color(255, 20, 147);     // #FF1493
Color neon_green = Color(57, 255, 20);     // #39FF14
Color neon_orange = Color(255, 140, 0);    // #FF8C00
Color dark_bg = Color(10, 0, 20);          // #0A0014
```

### Adjust Animation Speed
```cpp
// Perspective grid animation
float anim_offset = time_sec * 20;  // Increase number = faster

// Panel pulse speed
int panel_pulse = (int)(150 + 105 * sin(time_sec * 2.5));  
//                                                    ↑
//                                              Increase for faster
```

### Modify Temperature Thresholds
```cpp
if (id(office_temp).state < 20) {
  temp_color = neon_cyan;     // Below 20°C = Blue
} else if (id(office_temp).state <= 25) {
  temp_color = neon_orange;   // 20-25°C = Orange
} else {
  temp_color = neon_magenta;  // Above 25°C = Magenta
}
```

### Set Timezone
```yaml
time:
  - platform: sntp
    id: esptime
    timezone: Europe/Amsterdam  # Adjust to your region
    servers:
      - pool.ntp.org
```

**Examples:**
- `Europe/Amsterdam` (Netherlands)
- `Europe/Brussels` (Belgium)
- `America/New_York` (US - Eastern)
- `Asia/Tokyo` (Japan)

### Adjust Display Brightness
Via Home Assistant or in the config:
```yaml
light:
  - platform: monochromatic
    name: "Backlight"
    output: pwm_output
    restore_mode: RESTORE_AND_ON
```

---

## TROUBLESHOOTING

### Display Shows Nothing
1. Check SPI connections:
   - CLK → GPIO14
   - MOSI → GPIO13
2. Verify display pins:
   - DC → GPIO00
   - RESET → GPIO02
3. Verify voltage (3.3V for display!)
4. Test backlight PWM pin (GPIO05)

### Boot Screen Stuck

**"CONNECTING WIFI..."**
- Check WiFi SSID and password in `secrets.yaml`
- Verify WiFi 2.4GHz (ESP8266 doesn't support 5GHz!)
- Check WiFi signal strength

**"CONNECTING API..."**
- Check Home Assistant ESPHome integration
- Verify API encryption key
- Check firewall settings
- Restart Home Assistant

**"LOADING SENSORS..."**
- Verify sensor entity IDs exist in Home Assistant
- Check if sensors have data (not "unavailable")
- Wait 30-60 seconds for first data

### Display is Slow/Laggy
1. **Reduce animation complexity:**
   ```cpp
   // Fewer perspective lines
   for (int i = 0; i < 240; i += 40) {  // Was 30, now 40
   ```

2. **Enable CPU overclock** (already enabled):
   ```yaml
   platformio_options:
     board_build.f_cpu: 160000000L  # 160MHz
   ```

3. **Increase SPI speed** (already at maximum):
   ```yaml
   data_rate: 40000000  # 40MHz
   ```

### Sensor Data Shows "nan" or Wrong Values
1. Check entity ID spelling in YAML
2. Wait for sensor to take first measurement
3. Check Home Assistant logs:
   ```
   Settings → System → Logs
   Search for: "esphome" or your device name
   ```

4. Test sensor in Home Assistant Developer Tools:
   ```
   Developer Tools → States
   Search for your sensor entity
   ```

### OTA Update Fails
1. Check OTA password in `secrets.yaml`
2. Verify ESP8266 is online
3. Try via USB if OTA doesn't work
4. Reset ESP8266 and try again

---

## UPDATES & EXTENSIONS

### Add New Sensor

**Step 1:** Add sensor to YAML:
```yaml
sensor:
  - platform: homeassistant
    id: my_new_sensor
    entity_id: sensor.new_sensor_entity
```

**Step 2:** Add display code (e.g., after power sensor):
```cpp
// New sensor display
it.printf(10, 155, id(font_small), neon_green, TextAlign::LEFT, "New:");
it.printf(120, 155, id(font_small), neon_cyan, TextAlign::RIGHT, "%.1f", id(my_new_sensor).state);
```

**Step 3:** Enlarge panel if needed:
```cpp
it.filled_rectangle(5, 75, 125, 105, Color(20, 0, 40, 200));
//                              ↑ Was 85, now 105 for extra sensor
```

### Customize Boot Screen Text
Change "cyb3rpunk" to your own name:
```cpp
it.print(120, 60, id(font_medium), neon_cyan, TextAlign::CENTER, "YOUR NAME");
```

---

## TIPS & TRICKS

### Auto-Dim at Night
Create an automation in Home Assistant:
```yaml
automation:
  - alias: "Display Dim at Night"
    trigger:
      - platform: time
        at: "22:00:00"
    action:
      - service: light.turn_on
        target:
          entity_id: light.small_tv_backlight
        data:
          brightness_pct: 20
          
  - alias: "Display Bright During Day"
    trigger:
      - platform: time
        at: "07:00:00"
    action:
      - service: light.turn_on
        target:
          entity_id: light.small_tv_backlight
        data:
          brightness_pct: 100
```

### Energy Saving
Turn off display when away:
```yaml
automation:
  - alias: "Display Off When Away"
    trigger:
      - platform: state
        entity_id: person.your_name
        to: "not_home"
        for: "00:05:00"
    action:
      - service: light.turn_off
        target:
          entity_id: light.small_tv_backlight
```

### Data Refresh Rate
- Sensors update automatically via Home Assistant
- Display refreshes continuously for smooth animations
- No manual polling required!

---

## COLOR REFERENCE

| Sensor | Label Color | Data Color | Usage |
|--------|-------------|------------|-------|
| NOx | Neon Orange | Cyan | Air quality |
| VOC | Neon Green | Cyan | Volatile compounds |
| Humidity | Light Blue | Cyan | Moisture |
| Power | Magenta/Red | Cyan | Electricity (pulses at >1.5kW) |
| PM1/2.5/4/10 | Purple | Cyan | Particulate matter |
| Temperature | Dynamic | Dynamic | Blue/Orange/Magenta |

---

## SCREEN LAYOUT

```
┌─────────────────────────────────────────┐
│  Pulsing Corner    TIME     Corner      │
│                  HH:MM:SS               │
│                                         │
│  ┌──────────────┐  ┌─────────────────┐  │
│  │  NOx:    123 │  │  PM1:       12  │  │
│  │  VOC:    456 │  │  PM2.5:     23  │  │
│  │  HUM:    65% │  │  PM4:       34  │  │
│  │  kW:    1.23 │  │  PM10:      45  │  │
│  └──────────────┘  └─────────────────┘  │
│                                         │
│         ┌──────────────────┐            │
│         │   ┃            │ │            │
│         │   ┃   23.5°C   │ │            │
│         │   ●            │ │            │
│         └──────────────────┘            │
│  Corner                      Corner     │
└─────────────────────────────────────────┘
```

---

## SPECIFICATIONS

| Item | Specification |
|------|---------------|
| **Platform** | ESP8266 @ 160MHz |
| **Display** | ST7789V 240x240 16-bit color |
| **SPI Speed** | 40MHz |
| **Memory** | 12.5% buffer (optimized) |
| **Refresh Rate** | ~30 FPS |
| **Power** | ~500mA @ 5V |
| **Connectivity** | WiFi 2.4GHz |
| **Protocol** | ESPHome API |

---

## LICENSE

Open source - use and modify as you wish!  
No warranties, use at your own risk.

---

## SUPPORT

**Problems?**
1. Check this README troubleshooting section
2. View ESPHome logs in Home Assistant
3. Test hardware connections
4. Verify sensor entities

**Questions?**
- ESPHome documentation: https://esphome.io
- Home Assistant forum: https://community.home-assistant.io

---

**Enjoy your Cyberpunk Display!**



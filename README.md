# Draw to My Keychain OLED

This package contains:
- `index.html`: browser drawing website (128x64 monochrome canvas)
- `keychain_draw_receiver.ino`: ESP32-WROOM firmware with Wi-Fi, MQTT, OLED, and buttons

## How it works
The website sends a 1024-byte 1-bit image over MQTT. The ESP32 subscribes to the same topic and displays the drawing on its SSD1306 128x64 OLED. A new drawing switches the display to the Drawing screen automatically.

## 1. Choose a private-ish device code
Change `change-this-7f3a9d2c` in BOTH:
- the website input (the code field)
- `DEVICE_CODE` in the Arduino sketch

Use a hard-to-guess code. The public MQTT broker is shared and unauthenticated, so this is suitable for a hobby prototype only, not sensitive/private drawings.

## 2. Configure Arduino
1. Install the `PubSubClient` library from Arduino IDE > Library Manager.
2. Keep `WiFi.h`, `Wire.h`, and `U8g2lib.h` installed as in your existing project.
3. In `keychain_draw_receiver.ino`, replace `YOUR_WIFI_PASSWORD` with your Wi-Fi password.
4. Select your ESP32-WROOM board and upload.
5. Open Serial Monitor at 115200 baud. Look for `Drawing relay connected.`

This sketch uses the tested ESP32-WROOM pins:
- OLED SDA GPIO21, SCL GPIO22
- Previous GPIO18, Next GPIO19, Love GPIO23
- Buttons connect from GPIO to GND (INPUT_PULLUP)

Do not upload this WROOM pin layout to the ESP32-C3 SuperMini without checking and changing its pin assignments.

## 3. Publish the website online with GitHub Pages
This creates a public HTTPS website; it does not run on your computer after publishing.

1. Sign in to GitHub and create a new public repository, for example `oled-draw-keychain`.
2. Upload `index.html` to the repository root.
3. Open repository Settings > Pages.
4. Under Build and deployment, select Deploy from a branch; choose `main` and `/ (root)`, then Save.
5. Wait for GitHub Pages to publish. Its URL will look like `https://YOUR-USERNAME.github.io/oled-draw-keychain/`.
6. Open that URL from any internet-connected phone or computer.
7. Enter the same device code as `DEVICE_CODE` in the firmware, then press Connect.
8. Draw and press Send to OLED.

GitHub Pages deployment options may be worded slightly differently as GitHub updates its UI.

## Notes
- The page uses MQTT.js from a CDN and the shared HiveMQ public broker at `broker.hivemq.com` (WebSocket TLS port 8884; ESP32 MQTT TCP port 1883).
- Public brokers can be interrupted and messages may be visible to other users. The unique topic code reduces accidental collisions but is not a true access-control mechanism.
- If the ESP32 never says `Drawing relay connected.`, verify it has internet access and try again later; the shared broker is not a guaranteed production service.
- The bitmap is exactly 128x64 pixels, white pixels appear lit on the OLED.

# ESPHome Air Quality System (SEN6x / SEN5x)

A high-end Air Quality Monitor based on the **ESP32-C3** and **Sensirion SEN66**, featuring a fully dynamic **GC9A01** round display dashboard. This project goes beyond standard ESPHome configurations by allowing real-time UI customization directly from Home Assistant without re-flashing.

## 🚀 Features

* **Dynamic Dashboard**: Round UI with three configurable arcs and six data slots.
* **Real-time Customization**: Change sensor assignments, arc ranges, and update intervals on the fly via Home Assistant.
* **Dynamic Theming**: Support for Light and Dark modes with user-definable HEX colors for backgrounds, text, and UI elements.
* **Advanced Sensor Handling**: 
    * **Anti-Reset Logic**: Specialized 24h-polling trick to prevent the Sensirion gas engine (CO2, VOC, NOx) from resetting, ensuring stable long-term data and proper warm-up.
    * **Custom Calibration**: Offset sliders for Temperature and Humidity.
* **Intelligent Backlight**: Automatic day/night brightness adjustment based on sun elevation (Sunrise/Sunset).
* **Health Monitoring**: Visual color-coded alerts (Green/Yellow/Orange/Red) based on three-stage configurable thresholds for every pollutant.
* **WiFi Diagnostics**: On-screen signal strength indicator (%) and auto-reboot logic for weak connections.

* ### 💧 Dynamic Humidity Compensation
Relative Humidity (RH) is highly dependent on the ambient temperature. When applying a temperature offset (e.g., to compensate for internal device heat), the raw RH value from the sensor becomes physically inaccurate. 

To solve this, the firmware implements a real-time compensation logic using the **Magnus-Tetens Formula**. It calculates the saturation vapor pressure to maintain a consistent dew point, ensuring that the displayed humidity remains accurate even after temperature calibration:

* **Vapor Pressure Consistency**: The system ensures that the calculated actual vapor pressure remains constant while the temperature is adjusted.
* **Physics-Based Correction**: Instead of a simple linear offset, we use the exponential saturation curve:
    $$e_s(T) = 6.112 \cdot \exp\left(\frac{17.62 \cdot T}{243.12 + T}\right)$$
* **Accurate Environment Data**: This ensures that your humidity readings don't "drift" just because the temperature calibration was adjusted.

## 🛠 Hardware

| Component | Description | Datasheet |
| :--- | :--- | :--- |
| **Microcontroller** | ESP32-C3-DevKitM-1 | [ESP32-C3 Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-c3_datasheet_en.pdf) |
| **Air Quality Sensor** | Sensirion SEN66 (or SEN6x/SEN5x) | [Sensirion SEN66 Info](https://sensirion.com/products/catalog/SEN66/) |
| **Display** | 1.28" GC9A01 Round LCD (SPI) | [GC9A01 Datasheet](https://www.buydisplay.com/download/ic/GC9A01A.pdf) |

## 📦 External Components & Libraries

This project utilizes the following external resources:

* **SEN6x ESPHome Component**: Custom driver for advanced Sensirion features.
    * Repo: [tuct/esphome-projects](https://github.com/tuct/esphome-projects)
* **LVGL**: Graphics library used via the [ESPHome LVGL component](https://esphome.io/components/lvgl/).
* **Google Fonts**: Uses the "Rubik" font family for a modern look.

## ⚙️ Configurable Entities in Home Assistant

Once integrated, you can control the following through the Home Assistant UI:

1.  **Sensor Mapping**: Assign any measurement (CO2, PM2.5, VOC, NOx, etc.) to any display slot or arc.
2.  **Thresholds**: 21 individual "Box" inputs to set your own air quality limits.
3.  **Colors**: 4 color fields for status levels and 6 fields for theme design (Hex codes).
4.  **Intervals**: Independent sliders for Sensor polling (warm-up safe) and Display refresh rate.
5.  **Arc Scaling**: Define Min/Max values for the visual arcs to match your environment.

## 🔧 Installation

1.  Copy the YAML code into your ESPHome dashboard.
2.  Ensure your `secrets.yaml` contains `wifi_ssid`, `wifi_password`, and `wifi_ap_password`.
3.  Flash the ESP32-C3.
4.  Wait for the first sync; the CO2/Gas sensors require a short warm-up period to show valid data.

---
**Note**: To avoid memory issues on the ESP32-C3, the display buffer is optimized. If you experience instability, consider reducing the `buffer_size` in the `lvgl` section.

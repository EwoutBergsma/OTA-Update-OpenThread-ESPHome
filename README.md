# Introuction

After a recent ESPHome update, I found you can perform OTA updates on an ESPHome device over Thread only (i.e. no Wi-Fi required). Since I couldn’t find this documented or discussed on the forums, I created this repository to share the discovery.

The process is quite trivial for those acquainted to ESPHome. 


# Prerequisites

Firstly a few prerequisites:
- An ESP32-C6 or ESP32-H2, only these two chips have the required hardware for running OpenThread.
    - Thread on the nrf52(840) is currently not supported by ESPHome.
- A thread network (i.e. at least one thread border router), for example an Home Assistant instance with the Thread Border Router addon, the Thread integration and a Thread dongle (e.g. the nRF52840 dongle with the radio-coprocessor firmware flashed).
- An ESPHome installation, for example on the aforementioned Home Assistant instance.


# How to prepare device

The process can be simpler for the ESP32-C6 than for the ESP32-H2, as the H2 is not fully supported by the [ESPHome Web interface](https://web.esphome.io/?dashboard_wizard). The following process should work for both, but one should be able to take the [ESPHome Web interface](https://web.esphome.io/?dashboard_wizard) `PREPARE FOR FIRST USE` route for the C6. However, I have found that route to sometimes give me inexplainable errors, so here goes my preferred process:
- Connect your ESP32-C6 or ESP32-H2 board to your computer via USB.
- Open your ESPHome Device Builder instance (for me that is an Add-on within Home Assistant).
    - E.g. for me the ESPHome Device Builder UI is found [HOMEASSISANTIP]:[PORT]/hassio/ingress/5c53de3b_esphome.
    - Or in the Home Assistant UI, just navigate: `Settings > Add-Ons > ESPHome Device Builder > Open web UI`.
- Press `+ NEW DEVICE`, found in the bottom right corner.
- Press `CONTINUE`.
- Press `New Device Setup`.
- Give it a name, press `NEXT`.
- Select `ESP32`, we will change this in the yaml later.
- Press `SKIP`.
- On the newly created device, press `EDIT`.
- Remove all yaml, replace with this:
```yaml
esphome:
  name: [REPLACE WITH YOUR DEVICE NAME]
  friendly_name: [REPLACE WITH YOUR FRIENDLY DEVICE NAME]
  min_version: 2025.9.0
  name_add_mac_suffix: false

esp32:
  variant: [ESP32C6 or ESP32H2]  # Either put ESP32C6 or ESP32H2 depending on the board that you have
  framework:
    type: esp-idf

# Enable logging
logger:

# Enable Home Assistant API
api:

# Allow Over-The-Air updates
ota:
- platform: esphome

network:
  enable_ipv6: true

openthread:
  device_type: FTD
  tlv: [PUT YOUR TLV HERE]
```
- Make sure to replace anything with square brackets (remove the square brackets too). For the TLV you can find this in Home Assistant by navigating `Settings > Devices and Integrations > Thread > Config wheel button > (i) button > the TLV is the long string after, Active dataset TLVs:`
    - [More documentation on the OpenThread implementation can be found here](https://esphome.io/components/openthread/)
- Press `INSTALL`, found right top corner.
- Press `Plug into this computer`.
- Wait for the download to be prepared and downloaded.
    - This might take long, depending on the hardware that ESPHome is running on, especially if that is a Raspberry Pi.
- Press `Open ESPHome Web`.
- Press `CONNECT`, select our device, press `Connect`.
- Press `INSTALL` (do *not* press prepare for first use!)
- Press `Choose File`, select the file you downloaded in a previous step, press `Open`. 
- Press `INSTALL` and wait for it to finish.
- Navigate back to the `ESPHome Device Builder` UI in your browser.
    - The previosly newly created device should now no longer be marked as `OFFLINE`

# How to OTA update

Once all of the above has been finished the OTA update is simple:
- Navigate to the `ESPHome Device Builder` UI in your browser.
- On the device that was previously made using the above explanation, press `EDIT`.
- Make any change you would like to make (you could change the friendly name to just try out).
- Press `INSTALL`, found in the top right corner. 
- Press `Wirelessly`.
- Wait for it to compile and upload.
- Done!
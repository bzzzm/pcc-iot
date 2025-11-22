# PCC Environment Sensor

Environment sensor using ESP32 and ESPHome. It exposes the readings from a BMP280 in several ways:

- Web Dashboard
- Web API
    - Raw values on `/sensor/temperature` or `/sensor/pressure`
    - Alerts based on threshold that you can configure in the Web dashboard on `/binary_sensor/temperature_alarm` and `/binary_sensor/pressure_alarm`
- Prometheus `/metrics` HTTP endpoint
- Onboard RGB LED (green when temperature is below threshold, red when above).

## Hardware

Here is a list of hardware components required for this device:

- ESP32S2 board. I used [GroundStudio Carbon S2](https://ardushop.ro/ro/groundstudio/1814-groundstudio-carbon-s2-6427854000415.html) because it has a Qwiic connector and an onboard RGB.
- BMP280 sensor. I used [GroundStudio BM280 Module](https://ardushop.ro/ro/groundstudio/1004-modul-bmp280-groundstudio-6427854000460.html) because it has Qwicc connector.
- USB-C (for my board) power source. Anything with 500 mAmps or more works. Probably even sticking it in a PC/Server USB port will do it.
- 3D printed case. I made a very rudimentary design using OnShape and hot-glued everything together. You can find it [here](https://cad.onshape.com/documents/192f1086242b6a4f3983bfb5/w/e37c230cf93a34cf1e652527/e/e3838148ead3ca69df22fd3a?renderMode=0&uiState=69222ecdc3c528d89d9c07e5).

## Flashing

1. Make sure you followed the [Installation](../README.md#installation) instructions.
2. Copy `secrets.yaml.dist` file located in the same directory as this README file to `secrets.yaml` and edit it with the proper values.
3. Connect the device to your machine over USB.
4. Put your device in `BOOT` mode. If you are using the same device as me, you need the following: while holding down the `BOOT` button, push the `R` button and _only_ after that release the `BOOT` button.
5. From the root of the repo run `esphome run env/pcc-dc-env-1.yaml --upload_speed 921600`. Use the file corresponding to the device you want to flash. The `--upload_speed` argument is not mandatory.
6. If you don't get any errors, your devices should be flashed and you can continue with [Initial confguration](#initial-configuration).


These steps need to be executed just once, when you initialize your device. If you want to push config changes after the device is already flashed, just run `esphome run env/pcc-dc-env-1.yaml --device DEVICE_IP` (replace `DEVICE_IP` with the actual IP or mDNS of the device) to re-flash over the air.

## Initial configuration

1. Power on the device using the USB port, power only.
2. Connect to the captive wifi network. It should be the value from the yaml file under the `esphome.name` key. Use the password you added in secrets.yaml. If you connect from your phone, make sure you stay connected even if there is not internet (the phone will prompt you).
3. Visit the captive portal. If your devices does not show it automatically, visit [192.168.4.1](http://192.168.4.1).
4. Select you desired wifi network and hit `Connect`.
5. Wait ~30 seconds for the device to connect to your network. Check your routers admin page to find out the IP. You can also try the mDNS hostname which should the `esphome.name` value from yaml appended with `.local`. Example: `pcc-dc-env-1.local`.
6. Open the Web dashboard by visiting the IP or the mDNS and change the threshold values. Login with the credentials you configured in `secrets.yaml`.

## Monitoring tools

### Uptime Kuma

To monitor your device with [Uptime Kuma](https://uptimekuma.org/), take inspiration from the screenshot below.

![Uptime Kuma](https://imgur.com/Ya1icOm.png)

### Prometheus

Scrape the `/metrics` endpoint. Create a grafana dashboard to see the historical data. Use AlertManager to get notifications.

### Onboard LED

The onboard LED will turn green if the temperature is below the threshold you configured in the dashboard, or red if the value is above that. This might be helpful to get the temperature just by looking at the device, instead of having to browse an online tool (like Uptime Kuma or Grafana).

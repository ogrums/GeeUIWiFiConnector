# GeeUIWiFiConnector

On-device Wi-Fi setup and the bind flow that runs before the launcher. The package name is spelled `com.letianpai.robot.wificonnet`.

## Package

- system uid
- `MainActivity` is `singleInstance` and is the launcher
- `WIFIAutoConnectionService` is the background connect helper

No `RobotSdk` and no `ILetianpaiService` call in `MainActivity`. Submodule: `GeeUIComponets` (`CommChannel`, `Components`). Gradle spells the path `GeeUIComponents`.

## What it does

The UI is a 12-key keyboard, a pairing-code keyboard, pairing info, an auto-connect view, and an OTA view (`WifiConnectOtaView`).

`WIFIConnectionManager.connect(ssid, password)` uses `WifiManager` (`addNetwork`, `enableNetwork`, `reconnect`). An open network uses `KeyMgmt.NONE`. A password network uses `WPA_PSK`. Comments cover turning Wi-Fi on, turning it off, and disconnecting. As a client of a hotspot, the SSID must be wrapped in quotes. One helper only checks that a network is up.

`WIFIStateReceiver` listens for `NETWORK_STATE_CHANGED`, `SUPPLICANT_STATE_CHANGED`, and connectivity. `BleConnectStatusCallback` drives connecting, success, and failure. This repo has the callback, not a BLE GATT stack. BLE itself is started by `guideLib` in `LetianpaiOS`.

After the link is up, `GeeUiNetManager.isDeviceBind1` (from the `Components` submodule) checks bind status and country. A bound device switches locale (`zh` or `en`), marks the robot activated, and starts `com.renhejia.robot.launcher.main.activity.LeTianPaiMainActivity` with extra `from=wifi_connector`. An unbound device shows pairing or asks for OTA.

If the activity was opened with `from=from_open_robot`, a five-minute timer calls `FunctionUtils.shutdownRobot`.

## Comment glossary

| Where | Chinese | English |
|---|---|---|
| `WIFIConnectionManager` | 尝试连接指定wifi | Try to connect to the given Wi-Fi |
| `WIFIConnectionManager` | 打开WiFi / 关闭wifi / 断开连接 | Turn Wi-Fi on / off / disconnect |
| `WIFIConnectionManager` | 作为客户端, 连接服务端wifi热点时要加双引号 | As a client, wrap the hotspot SSID in quotes |
| `WIFIConnectionManager` | 如果仅仅是用来判断网络连接 | Only used to test whether a network is up |
| `FunctionUtils` | 关机 | Power off |
| `FunctionUtils` | 判断ROM版本号 | Read the ROM version |
| `MainActivity` | 切换语言 | Switch language |
| `MainActivity` | 打开Launcher主界面 / 跳转到OTA | Open the launcher / go to OTA |

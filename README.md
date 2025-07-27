# ESPHome-IR2HA
An ESPHome-based Infrared (IR) receiver gateway for Home Assistant. 一个基于 ESPHome 的红外接收网关。
# IR2HA - ESPHome Infrared to Home Assistant Gateway

[![ESPHome](https://img.shields.io/badge/ESPHome-ready-orange.svg)](https://esphome.io/)

An ESPHome-based Infrared (IR) receiver gateway to integrate any IR remote control with Home Assistant.

---
# IR2HA - ESPHome 红外接收 Home Assistant 网关

一个基于 ESPHome 的红外（IR）接收网关，用于将任何红外遥控器集成到 Home Assistant 中。

---

##  इंग्लिश में (English)

### 📋 Overview

This project transforms a simple ESP8266 and an IR receiver into a powerful Infrared (IR) gateway for Home Assistant. It listens for signals from your remote controls and translates them into actionable events and entities within your smart home.

This configuration provides two ways to integrate with Home Assistant:
1.  **Generic Event**: Fires a general `esphome.ir_received` event for any supported IR protocol, containing the protocol and data.
2.  **Specific Switches**: For specific pre-configured IR codes, it turns on a dedicated `switch` entity. This is useful for directly triggering automations from specific remote buttons.

### ✨ Features

* **Multi-Protocol Support**: Decodes various IR protocols (`NEC`, `Samsung`, `Sony`, `Panasonic`, etc.).
* **Dual Integration Methods**: Use generic events or specific switches for your automations.
* **Highly Customizable**: Easily add your own remote control codes.
* **Robust Networking**: Includes a static IP configuration and a fallback Wi-Fi Access Point.
* **OTA Ready**: Supports secure Over-The-Air firmware updates.

### 🔌 Hardware Requirements

* An ESP8266 board (e.g., NodeMCU v2).
* An IR Receiver module (e.g., TSOP1838, VS1838B).

### 🔧 Wiring

Connect the IR receiver to your ESP8266 board as follows.

```
IR Receiver   |   NodeMCU ESP8266
---------------------------------
VCC / +       |   3.3V
GND / -       |   GND
DATA / S      |   D4
```

### ⚙️ Software & Configuration

1.  **ESPHome**: You need an ESPHome instance running as a Home Assistant Add-on or standalone.

2.  **Secrets File**: Create a `secrets.yaml` file in your ESPHome configuration directory with your credentials.

    ```yaml
    # secrets.yaml
    wifi_ssid: "YOUR_WIFI_SSID"
    wifi_password: "YOUR_WIFI_PASSWORD"
    ap_password: "YOUR_FALLBACK_AP_PASSWORD"
    esphome_ota_password: "YOUR_OTA_UPDATE_PASSWORD"
    esphome_api_key_encryption: "YOUR_32_CHARACTER_BASE64_API_KEY"
    ```

3.  **ESPHome YAML**: Use the `ir2ha.yaml` file provided in this repository as your device configuration.

### 🕹️ Home Assistant Integration

You can create automations in Home Assistant based on the two methods.

#### Method 1: Using the Generic `esphome.ir_received` Event (Recommended)

This is the most flexible method. Create an automation that triggers on the event and use the event data to decide what to do.

**Example**: Trigger when a Sony remote sends the code `0x10d52`.

```yaml
# configuration.yaml or automations.yaml
automation:
  - alias: "IR Remote - Sony Button Press"
    trigger:
      - platform: event
        event_type: esphome.ir_received
        event_data:
          protocol: "Sony"
          data: "0x10d52" # Note: data is a string here
    action:
      - service: light.toggle
        target:
          entity_id: light.living_room_light
```

#### Method 2: Using the State of a Template Switch

This method is simpler for fixed buttons. The ESPHome device turns a switch `on`. Your automation should perform an action and then turn the switch back `off` to be ready for the next press.

**Example**: Use the `voice2ir_sensor_1` switch (named `语音-客厅灯_ON` in HA) to turn on a light.

```yaml
# configuration.yaml or automations.yaml
automation:
  - alias: "IR Remote - Turn On Living Room Light"
    trigger:
      - platform: state
        entity_id: switch.voice_ketingdeng_on # The entity ID is derived from the Chinese name
        to: "on"
    action:
      # 1. Perform your desired action
      - service: light.turn_on
        target:
          entity_id: light.living_room_light

      # 2. IMPORTANT: Turn the switch back off to reset it
      - service: switch.turn_off
        target:
          entity_id: switch.voice_ketingdeng_on
```

### 🛠️ How to Customize

To add your own remote buttons:

1.  Temporarily change the `logger` level in `ir2ha.yaml` to `INFO` to see more details.
    ```yaml
    logger:
      level: INFO
      #baud_rate: 0 # Disable hardware UART logging if it conflicts
    ```
2.  Upload the configuration and view the device logs.
3.  Point your remote at the IR sensor and press a button.
4.  You will see the IR code in the logs, for example:
    ```
   [I][remote.panasonic]: Received Panasonic: address=0x4004, command=0x01007A5D
   [I][remote.samsung]: Received Samsung: data=0xE0E040BF, nbits=32
    ```
5.  Copy the `data` or `command` and add a new `if` condition in `ir2ha.yaml` under the appropriate protocol section (`on_samsung`, `on_panasonic`, etc.).

---
---

## 中文 (Chinese)

### 📋 简介

本项目将一个简单的 ESP8266 和一个红外接收器，转变为一个功能强大的 Home Assistant 红外（IR）网关。它能监听来自您遥控器的信号，并将其转化为智能家居中可操作的事件和实体。

此配置提供了两种与 Home Assistant 集成的方式：
1.  **通用事件**：为任何支持的红外协议触发一个通用的 `esphome.ir_received` 事件，其中包含协议和数据信息。
2.  **特定开关**：对于预先配置的特定红外码，它会打开一个专用的 `switch` 实体。这对于通过遥控器上的特定按钮直接触发自动化非常有用。

### ✨ 功能特点

* **多协议支持**：解码多种红外协议（`NEC`, `Samsung`, `Sony`, `Panasonic` 等）。
* **双重集成方法**：可使用通用事件或特定开关来构建您的自动化。
* **高度可定制**：轻松添加您自己的遥控器码。
* **强大的网络功能**：包含静态 IP 配置和备用的 Wi-Fi 热点。
* **支持 OTA**：支持安全的“空中下载”固件更新。

### 🔌 所需硬件

* 一块 ESP8266 开发板 (例如：NodeMCU v2)。
* 一个红外接收模块 (例如：TSOP1838, VS1838B)。

### 🔧 硬件接线

将红外接收器连接到您的 ESP8266 开发板，如下所示。

```
红外接收器   |   NodeMCU ESP8266
---------------------------------
VCC / +       |   3.3V
GND / -       |   GND
DATA / S      |   D4
```

### ⚙️ 软件与配置

1.  **ESPHome**: 您需要一个正在运行的 ESPHome 实例（可以是 Home Assistant 的插件或独立运行）。

2.  **密钥文件**: 在您的 ESPHome 配置目录中创建一个 `secrets.yaml` 文件，并填入您的凭据。

    ```yaml
    # secrets.yaml
    wifi_ssid: "你的WIFI名称"
    wifi_password: "你的WIFI密码"
    ap_password: "你的备用热点密码"
    esphome_ota_password: "你的OTA更新密码"
    esphome_api_key_encryption: "你的32位BASE64格式的API密钥"
    ```

3.  **ESPHome YAML**: 使用本仓库中提供的 `ir2ha.yaml` 文件作为您的设备配置文件。

### 🕹️ Home Assistant 集成

您可以通过以下两种方式在 Home Assistant 中创建自动化。

#### 方法一：使用通用的 `esphome.ir_received` 事件 (推荐)

这是最灵活的方法。创建一个自动化，由该事件触发，并使用事件数据来决定执行何种操作。

**示例**：当一个索尼遥控器发送码 `0x10d52` 时触发。

```yaml
# configuration.yaml 或 automations.yaml
automation:
  - alias: "红外遥控 - 索尼按钮按下"
    trigger:
      - platform: event
        event_type: esphome.ir_received
        event_data:
          protocol: "Sony"
          data: "0x10d52" # 注意：这里的数据是字符串格式
    action:
      - service: light.toggle
        target:
          entity_id: light.living_room_light
```

#### 方法二：使用模板开关的状态

对于固定的按钮，此方法更简单。ESPHome 设备会将一个开关状态变为 `on`。您的自动化应该执行一个操作，然后将该开关状态变回 `off`，以便为下一次按键做准备。

**示例**：使用 `voice2ir_sensor_1` 开关（在 HA 中名为 `语音-客厅灯_ON`）来开灯。

```yaml
# configuration.yaml 或 automations.yaml
automation:
  - alias: "红外遥控 - 打开客厅灯"
    trigger:
      - platform: state
        entity_id: switch.yu_yin_ke_ting_deng_on # 实体ID由中文名称转换而来
        to: "on"
    action:
      # 1. 执行您想要的操作
      - service: light.turn_on
        target:
          entity_id: light.living_room_light

      # 2. 重要：将开关关闭以复位，准备下次触发
      - service: switch.turn_off
        target:
          entity_id: switch.yu_yin_ke_ting_deng_on
```

### 🛠️ 如何自定义

要添加您自己的遥控器按钮：

1.  临时将 `ir2ha.yaml` 中的 `logger` 日志级别更改为 `INFO` 以便查看更多细节。
    ```yaml
    logger:
      level: INFO
      #baud_rate: 0 # 如果与硬件串口冲突，请禁用硬件串口日志
    ```
2.  上传配置并查看设备日志。
3.  将您的遥控器对准红外传感器并按下一个按钮。
4.  您将在日志中看到红外码，例如：
    ```
   [I][remote.panasonic]: Received Panasonic: address=0x4004, command=0x01007A5D
   [I][remote.samsung]: Received Samsung: data=0xE0E040BF, nbits=32
    ```
5.  复制 `data` 或 `command` 的值，并在 `ir2ha.yaml` 中相应的协议部分（`on_samsung`, `on_panasonic` 等）下添加一个新的 `if` 判断条件。

# zabbix-template-asus-rt-ax89x

<div align="right">

[![License: MIT](./docs/badges/license-mit.svg)](./LICENSE)
[![Zabbix: 7.0 LTS](./docs/badges/zabbix-7.0-lts.svg)](https://www.zabbix.com/)
[![Target: ASUS RT-AX89X](./docs/badges/target-asus-rt-ax89x.svg)](https://www.asus.com/networking-iot-servers/wifi-routers/asus-wifi-routers/rt-ax89x/)
[![日本語](./docs/badges/language-ja.svg)](./README.md)
[![English](./docs/badges/language-en.svg)](#)

</div>

A high-performance, lightweight, and comprehensive **All-in-One Zabbix 7.0 LTS monitoring template** designed specifically for the **ASUS RT-AX89X** flagship Wi-Fi 6 router (powered by the Qualcomm IPQ8074 Hawkeye SoC).

By consolidating all metrics into a **single SSH Master Item**, this template minimizes router resource consumption and eliminates potential instability caused by frequent polling. It includes a built-in **3-tab modern template dashboard** providing out-of-the-box visibility into system telemetry, Wi-Fi 6 RF health, and 15 independent thermal zones.

---

## 🚀 Key Features

1. **High-Efficiency Data Collection via Single SSH Master Item**:
   - Executes a single compound command every 30 seconds (`top`, `uptime`, `loadavg`, `ping`, thermal zones, `iwconfig`, `wlanconfig`), returning structured JSON that is parsed into Dependent Items.
   - Minimizes session establishment and fork/process overhead in embedded Linux (Dropbear SSH) to ensure minimal impact on router performance.
2. **Wi-Fi 6 (Qualcomm QCA) RF Quality Telemetry**:
   - Accurately tracks connected client counts (UINT64) separated by 2.4 GHz and 5 GHz bands.
   - Collects average/minimum RSSI (signal strength), average SNR (signal-to-noise ratio), and noise floor for actionable RF troubleshooting.
3. **Autonomous Low-Level Discovery (LLD) for All 15 Thermal Zones**:
   - Auto-discovers all 15 hardware thermal sensors on the IPQ8074 platform: CPU (4 cores), NSS (2 cores), NSS Top, SoC system blocks, chipsets, internal bus, and wireless power amplifiers (PA).
   - Dynamically tracks temperature trends with individual threshold alerts.
4. **Zabbix 7.0 Native 3-Tab Template Dashboard**:
   - Ready to use immediately upon linking—no tedious dashboard editing required:
     - **Tab 1: System Overview**: Semi-circular CPU gauge, memory breakdown doughnut chart, Google DNS ping latency, detailed CPU breakdown, and memory trends.
     - **Tab 2: Wi-Fi Quality**: Connected client cards, staircase client count trends, and 5 GHz / 2.4 GHz RF metrics.
     - **Tab 3: Thermal Zones**: Comprehensive grid view displaying waveforms for all 15 thermal sensors.
5. **Modern Visualizations**:
   - Uses **staircase drawtype** for client counts to clearly indicate device connection/disconnection events.
   - Fully compliant with Zabbix 7.0 dashboard iterator specs (`reference: TEMPS`) for synchronized time periods, with cluttered legends removed to maximize waveform display areas.

---

## 📋 Requirements & Prerequisites

- **Zabbix Server / Proxy**: 7.0 LTS or newer (with SSH check support enabled).
- **Target Router**: ASUS RT-AX89X
  - Firmware: Stock Asuswrt or Asuswrt-Merlin.
  - SSH daemon enabled under **Administration -> System -> SSH Daemon**.

---

## 🛠️ Installation Guide

1. Download [`template/ASUS-Router.yaml`](./template/ASUS-Router.yaml) from this repository.
2. Open your Zabbix Web UI and navigate to **Data collection -> Templates**.
3. Click **Import** in the top right corner and import `ASUS-Router.yaml`.
4. Navigate to **Data collection -> Hosts** and create a host for your router (or select an existing one):
   - **Template**: Link `ASUS-Router`.
   - **Interfaces**: Add your router's IP address (e.g., `192.168.1.1`) as an Agent or SNMP interface.
5. In the host configuration, switch to the **Macros** tab and configure the required user macros.

---

## ⚙️ User Macros

| Macro | Default | Type | Description |
| :--- | :--- | :--- | :--- |
| `{$SSH_USER}` | `admin` | Text | SSH login username for the router |
| `{$SSH_PASS}` | (empty) | Secret text | SSH login password for the router |
| `{$SSH_PORT}` | `22` | Text | SSH port number |
| `{$CPU_UTIL_WARN}` | `80` | Text | CPU utilization warning threshold (%) |
| `{$CPU_UTIL_CRIT}` | `90` | Text | CPU utilization high threshold (%) |
| `{$TEMP_WARN}` | `80` | Text | Thermal zone temperature warning threshold (°C) |
| `{$PING_TIME_WARN}` | `50ms` | Text | Internet connectivity ping latency warning threshold |

---

## 📊 Dashboard Overview

Once linked, open the host dashboard from **Monitoring -> Hosts -> Dashboards**:

### Tab 1: System Overview
- **CPU Gauge**: Real-time total CPU utilization gauge.
- **Memory Doughnut**: Physical memory distribution (Used / Cached / Buffers / Free).
- **Internet Ping**: Latency and packet loss to Google DNS (`8.8.8.8`).
- **CPU Breakdown**: Stacked metrics for user, system, idle, iowait, irq, and sirq.
- **Memory History**: Historical memory consumption waveform.

### Tab 2: Wi-Fi Quality
- **Client Cards**: Real-time connected station count (Total, 5 GHz, 2.4 GHz).
- **Client Count Trends**: Staircase line chart showing station connection history.
- **RF Quality Trends**: Average RSSI, minimum RSSI, SNR, and noise floor.

### Tab 3: Thermal Zones
- **15-Zone Grid**: Real-time temperature curves for all 15 onboard thermal sensors.

---

## 📁 Repository Structure

```text
zabbix-template-asus-rt-ax89x/
├── README.md               # Japanese documentation
├── README_EN.md            # English documentation
├── LICENSE                 # MIT License
├── template/
│   └── ASUS-Router.yaml    # Zabbix 7.0 template definition (YAML)
├── docs/
│   └── images/             # Dashboard screenshots & diagrams
└── tools/                  # Utility scripts
```

---

## 📜 License

This project is licensed under the [MIT License](./LICENSE). Free for both commercial and personal use.

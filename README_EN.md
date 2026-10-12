# zabbix-template-asus-rt-ax89x

<div align="right">

[![License: MIT](./docs/badges/license-mit.svg)](./LICENSE)
[![Zabbix Community Template PR](./docs/badges/community-template-pr.svg)](https://github.com/zabbix/community-templates/pull/865)
[![Zabbix: 7.0 LTS](./docs/badges/zabbix-7.0-lts.svg)](https://www.zabbix.com/)
[![Target: ASUS RT-AX89X](./docs/badges/target-asus-rt-ax89x.svg)](https://www.asus.com/networking-iot-servers/wifi-routers/asus-wifi-routers/rt-ax89x/)
[![日本語](./docs/badges/language-ja.svg)](./README.md)
[![English](./docs/badges/language-en.svg)](#)

</div>

> [!NOTE]
> **🏛️ Zabbix Official Community Template Submission**:  
> Both English and Japanese templates from this repository have been submitted to the official Zabbix Community Templates repository ([zabbix/community-templates#865](https://github.com/zabbix/community-templates/pull/865)).

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

1. Download the template YAML matching your environment:
   - **English (Recommended)**: [`template/template_asus_rt_ax89x_en.yaml`](./template/template_asus_rt_ax89x_en.yaml)
   - **Japanese**: [`template/template_asus_rt_ax89x_ja.yaml`](./template/template_asus_rt_ax89x_ja.yaml)
2. Open your Zabbix Web UI and navigate to **Data collection -> Templates**.
3. Click **Import** in the top right corner and import the downloaded YAML file.
4. Navigate to **Data collection -> Hosts** and create a host for your router (or select an existing one):
   - **Template**: Link `ASUS-Router`.
   - **Interfaces**: Add your router's IP address (e.g., `192.168.1.1`) as an Agent or SNMP interface.
5. In the host configuration, switch to the **Macros** tab and configure the required user macros.

---

## ⚙️ User Macros

Configure the following user macros in your host configuration:

| Macro | Default | Type | Required | Description |
| :--- | :--- | :--- | :---: | :--- |
| `{$SSH_USER}` | (empty) | Text | **Yes** | SSH login username for the router |
| `{$SSH_PASS}` | (empty) | **Secret text** | **Yes** | SSH login password for the router |
| `{$AX89X.TEMP.WARN}` | `80` | Text | Optional | Thermal zone temperature warning threshold (°C) |

> [!WARNING]
> **Important Security Notice**:
> 1. **Allow SSH from LAN only**: In Asuswrt, the SSH account is identical to the router's web administration account (root privileges). Never enable "Allow SSH access from WAN" under **Administration -> System -> SSH Daemon** (keep SSH accessible from LAN only).
> 2. **Use Secret Text**: Always configure `{$SSH_PASS}` as **Secret text** in the Zabbix Web UI to prevent password exposure.

---

## 📊 Dashboard Overview

Once linked, open the host dashboard from **Monitoring -> Hosts -> Dashboards**:

### Tab 1: System Overview
- **CPU Gauge**: Real-time total CPU utilization gauge.
- **Memory Doughnut**: Physical memory distribution (Used / Cached / Buffers / Free).
- **Internet Ping**: Latency and packet loss to Google (`google.com`) including DNS resolution.
- **CPU Breakdown**: Stacked metrics for user, system, idle, iowait, irq, and sirq.
- **Memory History**: Historical memory consumption waveform.

### Tab 2: Wi-Fi Quality
- **Client Cards**: Real-time connected station count (Total, 5 GHz, 2.4 GHz).
- **RF Metric Cards**: 5 GHz average RSSI and SNR cards with color-coded thresholds.
- **Client Count Trends**: Staircase line chart showing station connection history (Total, 5 GHz, 2.4 GHz).
- **RF Quality Trends**: 5 GHz average RSSI, minimum RSSI, and average SNR waveforms.  
  *(Note: 2.4 GHz RF metrics and noise floor items are also collected and available for custom dashboards).*

### Tab 3: Thermal Zones
- **15-Zone Grid**: Real-time temperature curves for all 15 onboard thermal sensors.

---

## 📁 Repository Structure

```text
zabbix-template-asus-rt-ax89x/
├── README.md                           # Japanese documentation
├── README_EN.md                        # English documentation
├── LICENSE                             # MIT License
├── AGENTS.md                           # Guide for AI assistants
├── template/
│   ├── template_asus_rt_ax89x_en.yaml  # English template definition (YAML)
│   ├── template_asus_rt_ax89x_ja.yaml  # Japanese template definition (YAML)
│   └── ASUS-Router.yaml                # Compatibility template
├── docs/
│   ├── badges/                         # SVG badge assets
│   └── images/                         # Dashboard screenshots & diagrams
└── tools/                              # Utility scripts
```

---

## 📜 License

This project is licensed under the [MIT License](./LICENSE). Free for both commercial and personal use.

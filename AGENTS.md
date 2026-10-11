# AGENTS.md - Repository Guide for AI Assistants

This repository provides an All-in-One Zabbix 7.0 monitoring template for the ASUS RT-AX89X router.

## 🎯 Architecture & Implementation Details

- **Template File**: `template/ASUS-Router.yaml`
- **Zabbix Version Target**: 7.0 LTS+
- **Master Item**: `ssh.run[router_master]` (Delay: 30s)
  - Executes a single compound bash pipeline on the router via Dropbear SSH.
  - Returns a compact JSON payload containing CPU, memory, uptime, ping, thermal sensors, and Wi-Fi interface statuses.
- **Dependent Items**:
  - Parsed using JSONPath and Regex preprocessing from `ssh.run[router_master]`.
- **Low-Level Discovery (LLD)**:
  - Thermal zones (15 hardware sensors on Qualcomm IPQ8074) discovered dynamically.
- **Built-in Dashboards**:
  - Zabbix 7.0 native multi-page template dashboard with 3 tabs (System Overview, Wi-Fi Quality, Thermal Zones).

## 🛠️ Automated Operations & Verification

When a user asks to verify or work with this repository:
1. Ensure `template/ASUS-Router.yaml` is valid YAML.
2. Verify all dependent item keys align with the master item JSON output schema.
3. Keep dashboard widget coordinates within standard Zabbix 7.0 limits (width up to 72, height appropriately sized).

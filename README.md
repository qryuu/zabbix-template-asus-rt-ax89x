# zabbix-template-asus-rt-ax89x

<div align="right">

[![License: MIT](./docs/badges/license-mit.svg)](./LICENSE)
[![Zabbix: 7.0 LTS](./docs/badges/zabbix-7.0-lts.svg)](https://www.zabbix.com/)
[![Target: ASUS RT-AX89X](./docs/badges/target-asus-rt-ax89x.svg)](https://www.asus.com/networking-iot-servers/wifi-routers/asus-wifi-routers/rt-ax89x/)
[![日本語](./docs/badges/language-ja.svg)](#)
[![English](./docs/badges/language-en.svg)](./README_EN.md)

</div>

ASUS のフラグシップ Wi-Fi 6 ルーター「**RT-AX89X**」（Qualcomm IPQ8074 Hawkeye SoC）を、Zabbix 7.0 LTS 環境で安全・軽量・高精度に監視するための決定版 **All-in-One テンプレート** です。

単一のSSHマスターアイテムから全メトリクスを一括取得する高効率設計により、ルーターのリソース消費を最小限に抑え、監視処理起因のフリーズやハングアップを防止します。インポートするだけで、Wi-Fi 6 無線品質や全15基サーマルゾーン推移を含む **3タブ構成のモダンなテンプレートダッシュボード** が即座に利用可能です。

---

## 🚀 主な特徴

1. **単一SSHマスターアイテムによる高効率データ収集**:
   - 30秒に1回、単一のSSHセッション内で `top`, `uptime`, `loadavg`, `ping`, サーマル15ゾーン, `iwconfig`, `wlanconfig` を一括収集し、依存アイテム（Dependent Items）へJSON展開。
   - 組み込みLinux（Dropbear SSH）におけるセッション確立やプロセス生成オーバーヘッドを最小化し、極めて軽量に動作します。
2. **Wi-Fi 6（Qualcomm QCA）無線品質メトリクス**:
   - 2.4GHz / 5GHz 別の接続端末数（整数UINT64表示）、平均電波強度（RSSI）、最小RSSI、平均SNR（信号対雑音比）、フロアノイズを完全収集。
   - 電波干渉や通信品質の劣化をリアルタイムに検知可能。
3. **全15基サーマルゾーン自律ディスカバリ（LLD）**:
   - CPU 4コア、NSS（ネットワークサブシステム）2コア、NSS Top、SoCシステム部/チップセット3基/内部バス、無線PA 3基の全15箇所を自動検出し、日本語名を自動マッピング。
   - 各ゾーンの温度を個別に監視し、熱異常や熱暴走リスクを未然に検知。
4. **Zabbix 7.0 ネイティブ 3タブ・テンプレートダッシュボード内蔵**:
   - インポートするだけで、ホスト設定不要で即座に3画面が利用可能。
   - **タブ1: システム概要**: CPU使用率メーター、メモリ内訳Doughnut、Google Ping遅延、CPU詳細負荷推移、メモリ推移
   - **タブ2: Wi-Fi 無線品質**: 端末数カード群、Wi-Fi 接続端末数推移（階段状描画）、Wi-Fi 電波環境推移
   - **タブ3: 各部温度推移 (全15基)**: 全サーマルゾーンの大画面波形グリッド一覧
5. **洗練された可視化デザイン**:
   - 端末数グラフは値のステップ変化を直感的に捉える **階段状（Staircase）描画** を採用。
   - Zabbix 7.0 のイテレータ参照仕様（`reference: TEMPS`）に準拠し、時間範囲の完全同期とカード下テキスト凡例の排除により波形表示領域を最大化。

---

## 📋 動作要件・前提条件

- **Zabbix Server / Proxy**: 7.0 LTS 以上（SSHチェック機能が有効であること）
- **対象機器**: ASUS RT-AX89X
  - ファームウェア: Stock Asuswrt または Asuswrt-Merlin
  - 管理画面の「管理」→「システム」で **SSH デーモン（SSH接続）を有効化** していること。

---

## 🛠️ インポート手順

1. ご利用の環境（言語）に合わせてテンプレートYAMLをダウンロードします：
   - **日本語版（推奨）**: [`template/template_asus_rt_ax89x_ja.yaml`](./template/template_asus_rt_ax89x_ja.yaml)
   - **英語版**: [`template/template_asus_rt_ax89x_en.yaml`](./template/template_asus_rt_ax89x_en.yaml)
2. Zabbix Web UI にログインし、**「データ収集」→「テンプレート」** を開きます。
3. 画面右上の **「インポート」** ボタンを押し、ダウンロードした YAML ファイルを選択してインポートします。
4. **「データ収集」→「ホスト」** でルーターのホストを作成（または既存のホストを選択）します。
   - **テンプレート**: `ASUS-Router` をリンク
   - **インターフェース**: ルーターのIPアドレス（例: `192.168.1.1`）を設定（エージェントまたはSNMPインターフェース）
5. ホストの **「マクロ」** タブを開き、以下のユーザーマクロを設定します。

---

## ⚙️ ユーザーマクロ一覧

| マクロ名 | デフォルト値 | タイプ | 説明 |
| :--- | :--- | :--- | :--- |
| `{$SSH_USER}` | `admin` | テキスト | ルーターのSSHログインユーザー名 |
| `{$SSH_PASS}` | (未設定) | シークレットテキスト | ルーターのSSHログインパスワード |
| `{$SSH_PORT}` | `22` | テキスト | SSHポート番号 |
| `{$CPU_UTIL_WARN}` | `80` | テキスト | CPU使用率の警告閾値 (%) |
| `{$CPU_UTIL_CRIT}` | `90` | テキスト | CPU使用率の重度障害閾値 (%) |
| `{$TEMP_WARN}` | `80` | テキスト | サーマルゾーン温度の警告閾値 (°C) |
| `{$PING_TIME_WARN}` | `50ms` | テキスト | インターネット疎通Ping応答時間の警告閾値 |

---

## 📊 ダッシュボード構成

テンプレートをリンクすると、ホスト画面の **「ダッシュボード」** から以下の3画面が自動的に利用可能になります。

### タブ1: システム概要 (System Overview)
- **CPU使用率メーター**: 半円ゲージによるリアルタイムCPU負荷
- **メモリ内訳**: 物理メモリ/キャッシュ/バッファ/空き容量のドーナツチャート
- **インターネット疎通Ping**: Google DNS (`8.8.8.8`) への死活・応答速度
- **CPU詳細推移**: user / system / idle / iowait / irq / sirq のスタック推移
- **メモリ使用推移**: メモリ推移グラフ

### タブ2: Wi-Fi 無線品質 (Wi-Fi Quality)
- **接続端末数カード**: 合計、5GHz帯、2.4GHz帯の接続台数を整数カード表示
- **端末数推移（階段状）**: 台数の増減タイミングが明確にわかるステップグラフ
- **Wi-Fi 電波環境推移**: 5GHz / 2.4GHz 別の平均RSSI、最小RSSI、SNR、ノイズフロア波形

### タブ3: 各部温度推移 (全15基) (Thermal Zones)
- **サーマルグリッド**: CPU 4コア、NSS 2コア、Top、SoC 3基、バス、PA 3基の全温度推移をタイル状に一望表示

---

## 📁 ディレクトリ構成

```text
zabbix-template-asus-rt-ax89x/
├── README.md                           # プロジェクト説明書 (日本語)
├── README_EN.md                        # プロジェクト説明書 (English)
├── LICENSE                             # MIT License
├── AGENTS.md                           # AI アシスタント向けガイド
├── template/
│   ├── template_asus_rt_ax89x_ja.yaml  # 日本語版テンプレート (YAML)
│   ├── template_asus_rt_ax89x_en.yaml  # 英語版テンプレート (YAML)
│   └── ASUS-Router.yaml                # 互換用テンプレート
├── docs/
│   ├── badges/                         # バッジ用 SVG アセット
│   └── images/                         # ダッシュボードスクリーンショット等
└── tools/                              # 補助スクリプト (必要に応じて)
```

---

## 📜 ライセンス

本プロジェクトは [MIT License](./LICENSE) のもとで公開されています。商用・非商用問わずご自由にお使いいただけます。

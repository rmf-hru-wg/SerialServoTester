---
marp: true
size: 16:9
headingDivider: 2
style: |
    section {
        justify-content: start;
        text-align: left;
        font-size: 30px;
    }
---

<!-- _class: title -->

<style>
section.title {
  justify-content: center;
}
</style>

# SST-Single Board

## 概要

PCからサーボとシリアル通信するための変換基板

- サーボの3ピンケーブル(TTL信号)または4ピンケーブル(RS485)とシリアル通信
    - 通信速度: 3Mbpsまで可能
- USBまたはDCジャック経由で電源供給可能
- TODO: 各社サーボ設定ツールが使える
    - KONDO: ICS Manager Software


## 部品について (受動素子)

型番、部品点数、購入先詳細は[BOM](./bom_list.xlsx)を参照

| 部品番号 | 種類 | コメント |
|:-------|:----|:-------|
| D1 | ダイオード | USB電源の逆説防止用<br>※取り付け方向注意!! |
| D2 | 発光ダイオード | 電源確認用 |
| R1 - R5 | 抵抗 | 電流制限やプルアップ/プルダウン |
| SW1 | スイッチ | ボード機能切り替え用のDIPスイッチ<br>※取り付け方向注意!! |

## 部品について (IC・コネクタ)

| 部品番号 | 種類 | コメント |
|:-------|:----|:-------|
| U1 | 基板 | FT231XのUSBシリアル変換基板<br>ピンソケットを取り付けて装着<br>※USB micro-Bケーブルが必要なので注意 |
| U2 | IC | RS485変換IC (ピン互換品なら使用OK)<br>※IC本体はハンダせず、ICソケットに取り付ける |
| J1 | ピンヘッダ | サーボの3ピンコネクタ |
| J2 | コネクタ | RS485通信向けのJST-EHコネクタ |
| J3 | DCジャック | φ2.1mmでセンター+のジャック |

## 組み立て

1. 受動素子を取り付ける
    - ダイオードとスイッチは向きがあるので注意
2. コネクタ、ソケット類を取り付ける
3. ボードにUSBシリアル変換基板を取り付けて完成

Tips: 背の低い部品から取り付けるとやりやすい

TODO: 画像

## 使いかた

TTLまたはRS485コネクタに対応するサーボを接続して使用する。

DIP SwitchのON/OFF対応は以下の通り

| スイッチ | ON/OFF | 動作 |
|:-----------|:-------|:----|
| 1 | OFF (EchoTX) | 送信内容を受信する<br>KONDOのICS Manager Software使用時に使う |
| 1 | ON (NoEcho) | 送信内容を受信しない |
| 2 | OFF (RS485) | サーボとRS485モードで通信 |
| 2 | ON (OneWire) | サーボとTTLモードで通信 |


## 基板構成のフローチャート

<div class="mermaid">
%%{init: {'themeVariables': { 'fontSize': '200%'}}}%%
flowchart LR;
    USB -- FT231X<br>Converter --> UART
    UART -- TX,RX --> RS485IC
    RS485IC -- RS485 --> SEL{Mode Select}
    SW[DIP Switch] -- ON/OFF --> SEL
    SEL -- TTL --> 3PIN[3 Pin<br>Connector]
    SEL -- RS485 --> 4PIN[4 Pin<br>Connector]
</div>

## サーボ通信の仕組み

**用語**

- 半二重通信: 送信 (TX)と受信 (RX)を切り替えて通信する方式
- 差動信号: 信号の0/1を差動で認識する方式
    - 例えば信号線をA, BをA=High, B=Lowで0、A=Low, B=Highで1
    - EthernetやUSBなどで使われている

**サーボの通信方式**

- RS485通信: 半二重通信かつ差動のペア信号線による通信方式
- TTL通信: 半二重通信かつ信号線1本による通信方式
    - One-wireという業界規格も存在

## 動作プログラムについて

TODO: 別リポジトリURLを参照

---

<script src="https://unpkg.com/mermaid@8.1.0/dist/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>

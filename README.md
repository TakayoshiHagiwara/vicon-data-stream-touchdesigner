# vicon-data-stream-touchdesigner <!-- omit in toc -->
<img src="https://img.shields.io/badge/Python-3.11 or Later-blue?&logo=Python"> <img src="https://img.shields.io/badge/TouchDesigner-2023-blue?"> <img src="https://img.shields.io/badge/License-MIT-green">

光学式モーションキャプチャシステムViconからのデータをTouchDesignerで受け取るためのサンプルです。

このサンプルはVicon Bladeでのみ動作を確認しています。
また、剛体のストリーミングのみ動作を確認しています。

DataStream SDK自体はどのソフトウェアでも利用できるため、同様に動作すると考えられます。
また、後述しますがSegmentデータを取得するので、剛体のみではなくActorのストリーミングにも応用可能と考えられます。
どちらも動作を保証するものではありません。

**適宜バグの修正を行っているため、[Releases](https://github.com/TakayoshiHagiwara/vicon-data-stream-touchdesigner/releases)から最新版をご利用ください。**

# Table Of Contents <!-- omit in toc -->
<details>
<summary>Details</summary>

- [Environment](#environment)
- [Installation](#installation)
  - [Vicon DataStream SDK](#vicon-datastream-sdk)
  - [TouchDesigner](#touchdesigner)
- [Usage](#usage)
- [Description](#description)
  - [Bladeでの剛体作成について](#bladeでの剛体作成について)
  - [座標系について](#座標系について)
  - [その他](#その他)
- [References](#references)
- [Troubleshooting](#troubleshooting)
  - [クライアントに接続できない](#クライアントに接続できない)
- [Versions](#versions)
- [Author](#author)
- [License](#license)
</details>


# Environment
- Windows 11
- TouchDesigner 2023
- Python 3.11 or later
- Vicon Blade 3.4.1
- Vicon DataStream SDK 1.12.0


# Installation
## Vicon DataStream SDK
事前に対応するバージョンのPythonをインストールしてください。
Windowsの場合[pyenv-win](https://github.com/pyenv-win/pyenv-win.git)等でインストール可能です。

1. [Vicon公式サイト](https://www.vicon.com/software/datastream-sdk/)からDataStream SDKをダウンロードしてください
2. [Vicon DataStream SDK Quick Start Guide for Python](https://help.vicon.com/space/DSSDK112/10027198/Vicon+DataStream+SDK+Quick+Start+Guide+for+Python)を参照しながら、PCにPython用のDataStream SDKをインストールしてください
    - Windowsの場合コマンドプロンプトではインストールに失敗する場合があるようです
    - PowerShellを管理者権限で実行してインストールすることをおすすめします

## TouchDesigner
事前にTouchDesignerがインストールされており、Pythonの外部モジュールを読み込むパスが正しく通っていることを確認してください。
上記手順でDataStream SDKをインストールすると、対応するPythonのsite-packagesにSDKがインストールされます。


# Usage
1. ViconDataReceiver.toeを実行します
1. hostとportにViconサーバーの対応する情報を入力します
1. Connect Vicon serverボタンをクリックします
   - 接続までブロッキングします
1. 接続に成功するとStatusがconnectedになり、青色になります
1. 「pos_」から始まるオペレータと「rot_」から始まるオペレータにデータが表示されます
   - 用途に合わせて適宜別のオペレータやtoeに流してください
1. Disconnect Vicon serverボタンをクリックすると接続を解除します


# Description
## Bladeでの剛体作成について
[こちら](/docs/doc_blade_rigidbody.md)

## 座標系について
![coordinate](https://github.com/user-attachments/assets/acc96f6e-a51b-450a-8756-519f569aa899)

Vicon座標系は**右手系、Z-up**です。
サンプルでは、この座標系 (オペレータ名: pos_zup, rot_quaternion_zup) と、**左手系、Y-up**座標系 (オペレータ名: pos_yup, rot_quaternion_yup) に変換したものを出力しています。

座標系の変換は以下の式に従って行っています。

```python
# Position
o_pos = client.GetSegmentGlobalTranslation(rigidbody_name, segment_name)
pos = o_pos[0]

pos_rhand_zup = [pos[0], pos[1], pos[2]]    # Vicon coordinate
pos_lhand_yup = [-pos[1], pos[2], pos[0]]    # Left-handed, y-up

# Rotation
o_rot = client.GetSegmentGlobalRotationQuaternion(rigidbody_name, segment_name)
rot = o_rot[0]

# Quaternion (x, y, z, w)
rot_rhand_zup = [rot[0], rot[1], rot[2], rot[3]]    # Vicon coordinate
rot_lhand_yup = [rot[1], -rot[2], -rot[0], rot[3]]  # Left-handed, y-up
```

また、上記のメソッドからわかる通り、**グローバル (ワールド) 座標系**を取得しています。

## その他
- 使用しているメソッドやその他のメソッドについての詳細は[Vicon DataStream SDK Documentation](https://help.vicon.com/space/DSSDK112)を参照してください。
- 初期化時にTouchDesignerのフレームレートをBladeのフレームレートに自動的に合わせます
  - VSyncをオフに設定してください


# References
- [Vicon DataStream SDK](https://www.vicon.com/software/datastream-sdk/)
- [Vicon DataStream SDK Quick Start Guide for Python](https://help.vicon.com/space/DSSDK112/10027198/Vicon+DataStream+SDK+Quick+Start+Guide+for+Python)


# Troubleshooting
## クライアントに接続できない
- hostとportの設定が正しいか確認してください
- Windowsの場合、データを受け取るPCのファイアウォールやセキュリティソフトがストリーミングを妨げている場合があります
- IPアドレスのセグメントが一致しているか確認してください

# Versions
- 1.0.0: 2025/8/4
- 1.0.1: 2026/1/16
  - 左手y-up座標系におけるxの符号を修正


# Author
- Takayoshi Hagiwara
    - National Institute of Technology (KOSEN), Nagano College
    - Graduate School of Media Design, Keio University
    - Toyohashi University of Technology


# License
- MIT License
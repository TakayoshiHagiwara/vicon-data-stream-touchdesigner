# Bladeでの剛体作成について <!-- omit in toc -->

Bladeでの剛体作成について、著者の環境での手順を簡単に記しておきます。
各環境で手順などが異なると思いますので、参考程度にとどめていただければと思います。
ものすごく簡易的な説明になります。

# Table Of Contents <!-- omit in toc -->
<details>
<summary>Details</summary>

- [Calibration](#calibration)
- [Rigidbody](#rigidbody)
  - [データの記録](#データの記録)
  - [Labeling](#labeling)
  - [Solving](#solving)
  - [保存とライブビューへの読み込み](#保存とライブビューへの読み込み)
- [剛体の回転について](#剛体の回転について)
</details>


# Calibration
1. カメラの接続を確認する
2. Camera Calibrationパネルを開く
3. Start Auto Maskingでノイズにマスクをかける
4. Start Wand Waveでワンドを振るキャリブレーションを実行する
5. Start Setting Originでキャプチャエリアの原点を設定する
6. Start Calibrating Floor Planeでキャプチャエリアの面をキャリブレーションする

ワンドの軸に関しては以下の図の通りです。
Z-up座標系であることに注意してください。

![wand_coordination](https://github.com/user-attachments/assets/354035b5-69fa-48e3-b0df-9691eb31d19a)


# Rigidbody
## データの記録
1. キャプチャエリアに剛体オブジェクトを用意する
    - 3点でも動作するが、4点以上が望ましい
2. Captureパネルを開く
3. 任意の名前を入力し、CaptureからStartを押し、数フレーム記録する

## Labeling
1. Data Managementから、キャプチャしたデータを開く
2. Labelingパネルを開く
3. Reconstructを実行
4. 3Dビュー上のマーカーを選択する
    - 選択した順番に番号が付く
    - 剛体の位置と回転の参照となるセグメントはマーカー1と2の間に生成されるので、選択する順番に注意 
5. Create Prop Setupを実行
    - 任意の名前をつける
6. Calibrate Propを実行

## Solving
1. Solvingパネルを開く
2. Solveに作成したPropが存在することを確認し、All Framesを実行


## 保存とライブビューへの読み込み
1. Export Solving Setupでデータを保存
2. ライブビューに戻り、Character Managementに保存したvskとvssファイルを読み込む
3. リアルタイムに剛体がトラッキングできることを確認する


# 剛体の回転について
デフォルトでは、Create Prop Setupを実行した際、その時のマーカーの位置でセグメントが生成されます。
そのため、マーカーの位置関係によって0度で生成されない場合があります。
その場合は、手動で回転を設定する必要があります。

1. Rootセグメントを選択し、Channelエディタから回転の値を全て0にする
2. Script Editorを開く
3. 以下のコマンドを入力し、実行する
   ```c
   setConstraint;
   ```
4. 再度Solvingを実行
5. 保存
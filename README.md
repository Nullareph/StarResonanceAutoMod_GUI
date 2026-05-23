# Star Resonance AutoMod GUI

Star Resonance AutoMod GUI は、
[fudiyangjin/StarResonanceAutoMod](https://github.com/fudiyangjin/StarResonanceAutoMod)
をベースにした非公式 GUI 版フォークです。

元ツールのモジュール取得・解析・最適化処理を利用しつつ、コマンド操作なしで扱える
PyQt ベースの日本語 GUI を追加しています。

## 特徴

- 日本語 GUI でモジュール取得から計算まで操作可能
- 元ツールと同系統のパケット取得処理
- 4 枠 / 5 枠のモジュール計算に対応
- カテゴリ指定: 全て / 攻撃 / 支援 / 防御
- 属性の指定、除外、合計最小値フィルターに対応
- 上位候補をカード形式で表示
- `dist/helper/app` 方式で C++ 最適化エンジンを利用
- C++ 計算に失敗した場合の Python フォールバック

## 元プロジェクトとの関係

このリポジトリは、元プロジェクト StarResonanceAutoMod の GUI 版フォークです。

モジュール解析と最適化の中核処理は、元プロジェクトの実装をベースにしています。
通常の C++ helper 経路では、元ツールと同系統の C++ 最適化エンジンを使用します。

このフォークで主に追加・調整している部分は以下です。

- GUI から元ツールの主要オプションを操作できるようにしたこと
- 結果をカード形式で見やすく表示すること
- C++ 拡張を `helper/app` に配置する配布方式
- Windows 用 GUI exe としてのビルド設定

なお、C++ 計算が失敗した場合は Python フォールバックで再計算する場合があります。
このフォールバックは安定性確保のための経路であり、C++ 経路と完全に同じ結果を保証するものではありません。

## 必要環境

- Windows 10 / 11 64-bit
- Star Resonance ゲームクライアント
- Npcap または WinPcap
- パケット取得には管理者権限が必要になる場合があります

Npcap の利用を推奨します。

https://npcap.com/#download

Npcap をインストールする際は、WinPcap API-compatible mode を有効にすることを推奨します。

## 配布フォルダ構成

リリース時は、以下のファイルとフォルダを同じ配布フォルダ内に置いてください。

```text
StarResonanceAutoMod_GUI.exe
modules.vdata                  任意。取得後に作成・更新されます
README.txt                     任意。配布用の簡易説明
helper/
  app/
    star_railway_monitor.py
    module_parser.py
    module_optimizer.py
    module_types.py
    packet_capture.py
    network_interface_util.py
    logging_config.py
    BlueProtobuf_pb2.py
    cpp_extension/
      module_optimizer_cpp.cp312-win_amd64.pyd
```

`modules.vdata` は `StarResonanceAutoMod_GUI.exe` と同じ階層に置きます。
`helper/app` の中には置かないでください。

## 使い方

1. Npcap をインストールします。
2. `StarResonanceAutoMod_GUI.exe` を起動します。
3. 左側の設定で `CUDA（GPU）` または `CPU` を選択します。
4. `モジュール取得` をクリックします。
5. Star Resonance に再ログインし、キャラクターを選択します。
6. `modules.vdata` が保存されるまで待ちます。
7. カテゴリ、モジュール枠、属性、最小値などを設定します。
8. `計算` をクリックします。
9. 右側の結果欄に表示される候補を確認します。

## オフライン計算

一度 `modules.vdata` を取得していれば、再度パケット取得をしなくても計算できます。

`modules.vdata` を `StarResonanceAutoMod_GUI.exe` と同じフォルダに置いた状態で GUI を起動し、
条件を設定して `計算` をクリックしてください。

## オプション説明

GUI では、元ツールの主要なフィルターを画面上から指定できます。

- `カテゴリ`: モジュール種別を指定します
- `モジュール枠`: 4 枠または 5 枠を選択します
- `オプション`: 含めたい、または優先したい属性を選択します
- `min`: 最終的な組み合わせ全体で必要な属性合計値を指定します
- `除外オプション`: 除外または優先度を下げたい属性を選択します

## ビルド方法

GUI exe は `launcher.py` を元にビルドします。

必要な Python パッケージをインストールします。

```powershell
pip install pyinstaller pillow psutil protobuf scapy colorama PyQt5
```

exe をビルドします。

```powershell
python -m PyInstaller StarResonanceAutoMod_GUI.spec --noconfirm
```

helper ファイルを `dist/helper/app` にコピーします。

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\package_helper.ps1 -DistDir .\dist
```

出力先は以下です。

```text
dist/StarResonanceAutoMod_GUI.exe
dist/helper/app/
```

## helper/app 方式について

この GUI 版では、C++ 最適化拡張 `.pyd` を one-file exe の内部に閉じ込めず、
`helper/app/cpp_extension` に通常ファイルとして配置します。

PyInstaller の一時展開フォルダから `.pyd` を読み込む構成では、環境によって C++ 計算が
不安定になる場合があるためです。

より確実に配布する場合は、互換性のある Python 実行環境を以下に同梱してください。

```text
dist/helper/python/python.exe
```

Python 実行環境を同梱しない場合、GUI はユーザー環境の `python.exe` を探します。
適切な Python が見つからない場合は、exe 内 worker 経路に戻る場合があります。

## トラブルシューティング

### モジュール取得が始まらない

- GUI を管理者として実行してください。
- Npcap がインストールされているか確認してください。
- ネットワークの自動選択を切り替えるか、一覧から手動選択してください。

### `modules.vdata` が見つからない

先に `モジュール取得` を実行し、ゲームに再ログインしてキャラクターを選択してください。

### 有効な組み合わせが見つからない

条件が厳しすぎる可能性があります。

- 選択する属性を減らす
- `min` の値を下げる
- 除外オプションを減らす
- カテゴリを `全て` にする

### C++ 計算に失敗する

GUI は Python フォールバックで再計算する場合があります。

元ツールの C++ 計算結果との一致を重視する場合は、`helper/app` と互換 Python 実行環境を
まとめて配布してください。

## ライセンス

元プロジェクトは AGPL-3.0 ライセンスです。
このフォークも、特に明記がない限り同じライセンス条件に従います。

元プロジェクト:

https://github.com/fudiyangjin/StarResonanceAutoMod

## Credits

Original project:

[fudiyangjin/StarResonanceAutoMod](https://github.com/fudiyangjin/StarResonanceAutoMod)

この GUI 版は、元プロジェクトの parser、packet capture、optimizer 実装をベースにしています。

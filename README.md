# Star Resonance AutoMod GUI 日本語版

Star Resonance AutoMod GUI 日本語版は、
[fudiyangjin/StarResonanceAutoMod](https://github.com/fudiyangjin/StarResonanceAutoMod)
をベースにした非公式 GUI 版フォークです。

元ツールのモジュール取得・解析・最適化処理を利用しつつ、コマンド操作なしで扱える
日本語 GUI を追加しています。

## ダウンロードするもの

Release では、基本的に `StarResonanceAutoMod_GUI` フォルダを zip 化したものを配布します。

zip を展開すると、以下のような構成になります。

```text
StarResonanceAutoMod_GUI/
  StarResonanceAutoMod_GUI.exe
  README.md
  modules.vdata                  任意。取得後に作成・更新されます
  logs/                          任意。実行時に作成されます
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

`modules.vdata` と `logs/` は実行時に作成・更新されるファイルです。
配布物に含まれている場合でも、自分の環境で `モジュール取得` を実行すると更新されます。

## 必要環境

- Windows 10 / 11 64-bit
- Star Resonance ゲームクライアント
- Npcap または WinPcap
- パケット取得には管理者権限が必要になる場合があります

Npcap の利用を推奨します。

https://npcap.com/#download

Npcap をインストールする際は、WinPcap API-compatible mode を有効にすることを推奨します。

## 使い方

1. Npcap をインストールします。
2. zip を展開します。
3. `StarResonanceAutoMod_GUI.exe` を起動します。
4. 左側の設定で `CUDA（GPU）` または `CPU` を選択します。
5. `モジュール取得` をクリックします。
6. Star Resonance に再ログインし、キャラクターを選択します。
7. `modules.vdata` が保存されるまで待ちます。
8. カテゴリ、モジュール枠、属性、最小値などを設定します。
9. `計算` をクリックします。
10. 右側の結果欄に表示される候補を確認します。

## オフライン計算

一度 `modules.vdata` を取得していれば、再度パケット取得をしなくても計算できます。

`modules.vdata` を `StarResonanceAutoMod_GUI.exe` と同じフォルダに置いた状態で GUI を起動し、
条件を設定して `計算` をクリックしてください。

## オプション説明

- `カテゴリ`: モジュール種別を指定します
- `モジュール枠`: 4 枠または 5 枠を選択します
- `オプション`: 含めたい、または優先したい属性を選択します
- `min`: 最終的な組み合わせ全体で必要な属性合計値を指定します
- `除外オプション`: 除外または優先度を下げたい属性を選択します

## C++ 計算について

この GUI 版では、C++ 最適化拡張 `.pyd` を exe 内に閉じ込めず、
`helper/app/cpp_extension` に通常ファイルとして配置しています。

これにより、PyInstaller の一時展開フォルダから `.pyd` を読み込む場合に起こる不安定さを避け、
元ツールに近い C++ 計算経路を使いやすくしています。

C++ 計算が失敗した場合、GUI は Python フォールバックで再計算する場合があります。
このフォールバックは安定性確保のための経路であり、C++ 経路と完全に同じ結果を保証するものではありません。

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

### 起動時に管理者権限を求められる

パケット取得には管理者権限が必要になる場合があるため、GUI exe は管理者権限を要求する設定でビルドしています。

## ビルド方法

開発者向けの手順です。

必要な Python パッケージをインストールします。

```powershell
pip install pyinstaller pillow psutil protobuf scapy colorama PyQt5
```

exe をビルドします。

```powershell
python -m PyInstaller StarResonanceAutoMod_GUI.spec --noconfirm
```

helper ファイルを `StarResonanceAutoMod_GUI/helper/app` にコピーします。

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\package_helper.ps1 -DistDir .\StarResonanceAutoMod_GUI
```

Release では、生成された `StarResonanceAutoMod_GUI` フォルダを zip 化して配布します。

## ライセンス

元プロジェクトは AGPL-3.0 ライセンスです。
このフォークも、特に明記がない限り同じライセンス条件に従います。

元プロジェクト:

https://github.com/fudiyangjin/StarResonanceAutoMod

## Credits

Original project:

[fudiyangjin/StarResonanceAutoMod](https://github.com/fudiyangjin/StarResonanceAutoMod)

この GUI 版は、元プロジェクトの parser、packet capture、optimizer 実装をベースにしています。

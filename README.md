# Star Resonance AutoMod GUI 日本語版

このフォルダは、Star Resonance AutoMod GUI 日本語版の配布用フォルダです。
Release では、この `StarResonanceAutoMod_GUI` フォルダを zip 化して配布します。

元プロジェクト:
[fudiyangjin/StarResonanceAutoMod](https://github.com/fudiyangjin/StarResonanceAutoMod)

## フォルダ構成

```text
StarResonanceAutoMod_GUI/
  StarResonanceAutoMod_GUI.exe
  README.md
  helper/
    engine/
      cpu/
        StarResonanceAutoMod.exe
      cuda/
        StarResonanceAutoMod.exe
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
  modules.vdata                  実行後に作成・更新されるモジュールデータ
  logs/                          実行後に作成されるログ
```

`modules.vdata` は自分のゲームデータから取得されるファイルです。
古いものや他環境のものが入っている場合でも、`モジュール取得` を実行すると更新されます。

## 必要環境

- Windows 10 / 11 64-bit
- Star Resonance ゲームクライアント
- Npcap または WinPcap
- パケット取得には管理者権限が必要になる場合があります

Npcap:
https://npcap.com/#download

Npcap をインストールする際は、WinPcap API-compatible mode を有効にすることを推奨します。

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

## C++ 計算について

この配布版では、計算時に `helper/engine/cpu` または `helper/engine/cuda` に同梱した
元ツール exe を呼び出します。

これにより、PyInstaller の exe 内 worker から C++ 拡張 `.pyd` を直接読み込む場合に起こる
不安定さを避け、元ツールと同系統の C++ 計算経路を使います。

C++ 計算が失敗した場合、Python フォールバックでは再計算しません。
元ツールと同系統の C++ 計算結果を優先するため、C++ 計算に失敗した場合は失敗として表示します。

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

## 変更履歴

### v1.0.1

#### 修正

- 計算時に配布先ユーザー環境の `python.exe` を拾わないように変更
- Windows Store Python エイリアスや依存不足の Python による `rc=9009` 系の計算失敗を回避
- C++ 計算失敗時の Python フォールバックを廃止
- 計算時に `helper/engine` の元ツール exe を呼び出すように変更

### v1.0.0

初版公開。

## ライセンス

元プロジェクトは AGPL-3.0 ライセンスです。
この GUI 版も、特に明記がない限り同じライセンス条件に従います。

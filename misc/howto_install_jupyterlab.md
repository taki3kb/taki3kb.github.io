# Windows で Miniforge + mamba を用いた OpenCV および JupyterLab 環境構築手順

2025/04/17

## 目的
ファイアーウォールがある学内の Windows 環境でも安定して OpenCV, NumPy, JupyterLab 等を使用できる Python 環境を構築し、GUI 機能（`cv2.imshow()` など）や JupyterLab を手軽に起動できるようにする。

---

## 1. Miniforge のインストール

### 手順
1. 以下のリンクから Windows 用インストーラ（Miniforge3）をダウンロード：
   - [https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Windows-x86_64.exe](https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Windows-x86_64.exe)
   - 例: Miniforge3-Windows-x86_64.exe  version 24.11.3-2(64bit)

2. インストーラを実行し、ユーザーインストール（管理者権限不要）を選択。
   - 初期設定のまま，何も追加のチェック操作をせずにインストール

---

## 2. 仮想環境の作成と必要パッケージのインストール

スタートのアプリ一覧から "Miniforge Prompt" を起動する。 

### 仮想環境作成

mamba が入っているので仮想環境をmambaで作成する。チャンネルはデフォルトでconda-forge である。python のバージョンはとりあえず3.10とする。 
```bat
mamba create -n ocv_env python=3.10 -y
mamba activate ocv_env
```

### パッケージのインストール
仮想環境をactivate したら，この仮想環境に必要なパッケージをダウンロードする。
```bat
mamba install opencv numpy matplotlib jupyterlab scikit-learn plotly -y
```

---

## 3. JupyterLab を起動するバッチファイルの作成（スタートメニュー対応）

notepad などを起動して，以下のコードを入力し，ファイル名を"start_jupyterlab.bat" にして保存する。windowsの設定で隠れているため，拡張子に注意すること。

### バッチファイル内容：`start_jupyterlab.bat`
```bat
@echo off
REM 仮想環境名: ocv_env
SET "USERDIR=%USERPROFILE%"
CALL "%USERDIR%\miniforge3\Scripts\activate.bat" ocv_env
jupyter lab
```

### 配置とショートカット
1. 上記バッチファイルを適当な場所に保存（例: `Documents` フォルダ）
2. ファイルを右クリック >「その他のオプションを確認」>「ショートカットの作成」
3. 作成されたショートカットをコピーする
   - スタートから"Miniforge Prompot”のアイコンを表示させてアイコンの上で右クリック，「ファイルの場所を開く」
   - 開いたフォルダに先ほどのショートカットを移動する。
   - するとスタート>すべて（アプリ）>Miniforge > start_jupyterlab.bat - ショートカット が表示されて，次回からはこれをクリックすればJupyterLabが起動する。

---

## 3. OpenCV GUI 機能の動作確認

Anacondaのconda install ではOpenCVのウインドウ表示がうまく動作しない場合が多い。Miniforge で正しくinstallしているかどうかを確認する。

### Python スクリプト例
```python
import cv2
import numpy as np

img = np.zeros((300, 300, 3), dtype=np.uint8)
cv2.putText(img, "OpenCV test", (30, 150), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 255), 2)
cv2.imshow("Test", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

うまく動作すると，小さなウインドウが表示されて，”OpenCV test” と表示される。

---

## 5. バージョンの選び方（OpenCV + Python）
- OpenCV は Python 3.8〜3.12 に対応しているらしい。3.10 または 3.11が安定しているのらしい。（ChatGPTが言っている）

---

## 6. 環境再現用 YAML ファイル（オプション）

### environment.yml
```yaml
name: ocv_env
channels:
  - conda-forge
dependencies:
  - python=3.10
  - opencv
  - numpy
  - matplotlib
  - jupyterlab
  - scikit-learn
  - plotly
```

### 環境の作成方法
6.のenvironment.yml ファイルを使うと2.の代わりに以下の１行だけで同じ作業を行なうことができる。

スタートのアプリ一覧から "Miniforge Prompt" を起動する。
```bat
mamba env create -f environment.yml
```

---

## 7. 補足
- `conda` と `pip` の併用は環境によって競合が生じるため、基本は `mamba` + `conda-forge` に統一
- 学内ネットワークの遅延を避けるため、最小限のパッケージ構成かオフラインキャッシュ配布も検討可能

---

以上の手順により、Windows 上で OpenCV を含む Python 実習環境が安定して構築・運用できそう。



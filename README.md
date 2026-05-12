# simple-mlflow-local

mlflowをローカルPC上で実行する小規模構成です。Docker ComposeでMLflowとJupyter Notebookを起動し、Notebookで作成したモデルをMLflowダッシュボードで管理できます。VSCode の DevContainer からも同じデータ領域にアクセスして Notebook を実行できます。

## 必要なツール

| ツール | 動作確認バージョン | 備考 |
| --- | --- | --- |
| Docker Engine | 28.1.1 以降 | Compose v2 を同梱する版を推奨 |
| Docker Compose | v2.35.1 以降 | `docker compose` サブコマンド形式 |
| Web ブラウザ | Chrome / Firefox / Edge の最近のバージョン | Jupyter Lab / MLflow UI の表示用 |
| OS | Linux / macOS / Windows (WSL2) | 動作確認は Linux 5.15 (WSL2) |
| VSCode (任意) | 1.85 以降 | DevContainer 利用時 |
| Dev Containers 拡張 (任意) | `ms-vscode-remote.remote-containers` | DevContainer 利用時 |

MLflow / Jupyter / Python 等のランタイムはコンテナ内に閉じ込めているため、ホスト側へのインストールは不要です（コンテナ内: MLflow 2.14.3, Python 3.11, jupyter/scipy-notebook:python-3.11）。

## 構成

- `mlflow` サービス: Tracking Server (`http://localhost:5000`) — `--serve-artifacts` で artifact もプロキシ
- `notebook` サービス: Jupyter Lab (`http://localhost:8888`, トークン認証なし)
- `devcontainer` サービス: VSCode Dev Containers 用 (起動は VSCode 経由のみ)
- `./data/mlflow`: MLflowのメタデータ (SQLite) — ホストにマウントして永続化
- `./data/artifacts`: モデル等のartifact保存先 — ホストにマウントして永続化
- `./notebooks/model_training.ipynb`: モデル学習とMLflow記録のサンプル

すべてのコンテナは uid=1000, gid=1000 で書き込みを行うため、`./data` および `./notebooks` 配下に作成されるファイルはホスト側の WSL ユーザー (uid=1000) と同じ所有権になります。

## 使い方

### Jupyter Lab で利用する場合

```bash
docker compose up -d
```

1. Jupyter Lab: `http://localhost:8888` を開くとそのままアクセスできます（トークン不要）
2. MLflow UI: `http://localhost:5000` を開き、Run と artifact を確認

### VSCode の DevContainer で利用する場合

1. VSCode で本リポジトリのフォルダを開く
2. コマンドパレットで `Dev Containers: Reopen in Container` を実行
3. 初回はビルドが走ったあと、コンテナ内 (`/workspace`) にアタッチされる
4. `notebooks/model_training.ipynb` を開き、VSCode の Jupyter 拡張で Python カーネルを選択して実行

DevContainer は `mlflow` サービスと同じ Docker ネットワークに参加し、`MLFLOW_TRACKING_URI=http://mlflow:5000` を環境変数として持ちます。`./notebooks` および `./data` は Jupyter Lab サービスと共有されているため、どちらから実行しても同じ MLflow ダッシュボード上で結果を確認できます。

DevContainer のみを停止したい場合は VSCode で `Dev Containers: Reopen Folder Locally` を実行してください。`docker compose down` を CLI から行うと、Jupyter Lab と DevContainer の両方が停止します。

### 停止

```bash
docker compose down
```

データを完全に消したい場合は `./data` ディレクトリも削除してください。

## セキュリティに関する注意

`notebook` サービスは認証を無効化しており、`http://localhost:8888` に到達できる相手は誰でもコードを実行できます。**ローカル開発用途のみを想定しています。** 外部からアクセス可能なホストでは利用しないでください。

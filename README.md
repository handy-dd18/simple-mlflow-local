# simple-mlflow-local

mlflowをローカルPC上で実行する小規模構成です。Docker ComposeでMLflowとJupyter Notebookを起動し、Notebookで作成したモデルをMLflowダッシュボードで管理できます。

## 必要なツール

| ツール | 動作確認バージョン | 備考 |
| --- | --- | --- |
| Docker Engine | 28.1.1 以降 | Compose v2 を同梱する版を推奨 |
| Docker Compose | v2.35.1 以降 | `docker compose` サブコマンド形式 |
| Web ブラウザ | Chrome / Firefox / Edge の最近のバージョン | Jupyter Lab / MLflow UI の表示用 |
| OS | Linux / macOS / Windows (WSL2) | 動作確認は Linux 5.15 (WSL2) |

MLflow / Jupyter / Python 等のランタイムはコンテナ内に閉じ込めているため、ホスト側へのインストールは不要です（コンテナ内: MLflow 2.14.3, Python 3.11, jupyter/scipy-notebook:python-3.11）。

## 構成

- `mlflow` サービス: Tracking Server (`http://localhost:5000`)
- `notebook` サービス: Jupyter Lab (`http://localhost:8888`, トークン認証なし)
- `./data/mlflow`: MLflowのメタデータ (SQLite) — ホストにマウントして永続化
- `./data/artifacts`: モデル等のartifact保存先 — ホストにマウントして永続化
- `./notebooks/model_training.ipynb`: モデル学習とMLflow記録のサンプル

`./data` 配下のディレクトリは初回起動時に Docker が自動作成します（root所有）。

## 使い方

```bash
docker compose up -d
```

1. Jupyter Lab: `http://localhost:8888` を開くとそのままアクセスできます（トークン不要）
2. MLflow UI: `http://localhost:5000` を開き、Run と artifact を確認

停止:

```bash
docker compose down
```

データを完全に消したい場合は `./data` ディレクトリも削除してください（root所有のため `sudo rm -rf ./data` が必要な場合があります）。

## セキュリティに関する注意

`notebook` サービスは認証を無効化しており、`http://localhost:8888` に到達できる相手は誰でもコードを実行できます。**ローカル開発用途のみを想定しています。** 外部からアクセス可能なホストでは利用しないでください。

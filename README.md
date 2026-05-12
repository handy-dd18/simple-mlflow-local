# simple-mlflow-local

mlflowをローカルPC上で実行する小規模構成です。Docker ComposeでMLflowとJupyter Notebookを起動し、Notebookで作成したモデルをMLflowダッシュボードで管理できます。

## 構成

- `mlflow` サービス: Tracking Server (`http://localhost:5000`)
- `notebook` サービス: Jupyter Notebook (`http://localhost:8888`, tokenは起動ログに表示)
- `./data/mlflow`: MLflowのメタデータ (SQLite)
- `./data/artifacts`: モデル等のartifact保存先
- `./notebooks/model_training.ipynb`: モデル学習とMLflow記録のサンプル

## 使い方

```bash
docker compose up -d
```

1. Jupyter Notebook: `http://localhost:8888` を開き、`notebooks/model_training.ipynb` を実行
2. MLflow UI: `http://localhost:5000` を開き、Runとモデルartifactを確認

停止:

```bash
docker compose down
```

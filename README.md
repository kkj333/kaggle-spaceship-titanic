# kaggle-spaceship-titanic

[Kaggle Spaceship Titanic — Getting Started](https://www.kaggle.com/competitions/spaceship-titanic) の学習・提出用リポジトリ。

分類コンペの続きとして、[Titanic プロジェクト](https://github.com/kkj333/kaggle-titanic) の次、[House Prices プロジェクト](https://github.com/kkj333/kaggle-house-prices) と並ぶ **Getting Started** 系の土台です。

**Kaggle:** [kkj333](https://www.kaggle.com/kkj333) · **最終スコア: Public LB 0.80453**（`02_lightgbm.ipynb`）

## 最終モデル

| 項目 | 内容 |
|------|------|
| モデル | LightGBM |
| 前処理 | Cabin 分割・TotalSpend・GroupSize・IsAlone + 欠損補完 + One-Hot |
| 評価指標 | **accuracy**（高いほど良い） |
| 提出ファイル | `output/submission_lgbm.csv` |
| ノートブック | **`notebooks/02_lightgbm.ipynb`** ← まずここ |

```bash
uv run kaggle competitions submit -c spaceship-titanic \
  -f output/submission_lgbm.csv \
  -m "lightgbm cabin+spend+group"
```

## 試行錯誤の結果

| # | ノートブック | モデル | CV (accuracy) | LB | 所感 |
|---|-------------|--------|---------------|-----|------|
| 01 | baseline | RandomForest | ~0.8035 | 0.7978 | 土台 |
| **02** | **lightgbm** | **LightGBM** | **~0.8098** | **0.8045** | **ベスト LB** |
| 03 | ensemble | RF+LGBM soft voting | ~0.8120 | 0.8041 | CV↑ LB↓ |

### 学んだこと

- **同じ FE なら LightGBM が RF より CV/LB とも改善**（古典 Titanic とは逆パターン）
- **アンサンブルは CV だけ上がることもある** → 03 で RF+LGBM soft voting が CV 最高だが LB は 02 より下
- **CV（模試）と LB（本番）のズレ** は House Prices / 古典 Titanic と同様に起きうる
- Spaceship Titanic も **堅実な FE + モデル差し替え** がまず効く

## 使った特徴量（FE）

| 特徴量 | 元データ | 説明 |
|--------|---------|------|
| Deck / CabinNum / Side | Cabin | `Deck/Num/Side` に分割 |
| TotalSpend | 支出 5 列 | 船内サービス利用額の合計 |
| GroupSize | PassengerId | 同一グループ（`_` より前）の人数 |
| IsAlone | GroupSize | 1 人グループか |

## セットアップ

```bash
git clone https://github.com/kkj333/kaggle-spaceship-titanic.git
cd kaggle-spaceship-titanic
uv sync
uv run kaggle auth login                              # 初回のみ
# Web でコンペページから Join してから DL
uv run kaggle competitions download -c spaceship-titanic -p data
```

macOS で LightGBM を使う場合:

```bash
brew install libomp
```

Jupyter カーネル（初回）:

```bash
uv run python -m ipykernel install --user --name kaggle-spaceship-titanic --display-name "Python (kaggle-spaceship-titanic)"
```

## 使い方

VS Code / Cursor で `notebooks/` を開き、カーネル **Python (kaggle-spaceship-titanic)** を選んで実行。

```bash
uv run jupyter lab
```

## ディレクトリ構成

```
kaggle-spaceship-titanic/
├── notebooks/          # 01_baseline から試行錯誤
├── data/               # Kaggle データ（git 管理外）
├── output/             # 提出 CSV（git 管理外）
├── .cursor/rules/      # Cursor AI 用ルール
├── pyproject.toml      # uv 依存関係
└── uv.lock
```

## 関連リポジトリ

- [kaggle-titanic](https://github.com/kkj333/kaggle-titanic) — 二値分類（Survived）
- [kaggle-house-prices](https://github.com/kkj333/kaggle-house-prices) — 回帰（SalePrice / RMSLE）

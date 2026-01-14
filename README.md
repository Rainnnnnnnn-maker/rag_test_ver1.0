# RAG システムデモ (ドラえもん × ドラゴンボール)

このプロジェクトは、Retrieval-Augmented Generation (RAG) システムのシンプルな実装デモです。
ファンフィクション「ドラえもん与超级赛亚人：时空之战（ドラえもんとしてのスーパーサイヤ人：時空の戦い）」のテキストデータ (`doc.md`) を知識ベースとして使用し、ユーザーの質問に対して正確な回答を生成します。

## 機能概要

このプロジェクトでは、RAG パイプラインの以下の主要なステップを実装しています：

1.  **データ読み込みとチャンク化**: テキストファイルを読み込み、適切なサイズに分割します。
2.  **ベクトル化 (Embedding)**: `sentence-transformers` (`shibing624/text2vec-base-chinese`) を使用してテキストをベクトル表現に変換します。
3.  **ベクトル検索**: `chromadb` を使用して、質問に関連するテキストチャンクを高速に検索します。
4.  **リランク (Reranking)**: `CrossEncoder` (`cross-encoder/mmarco-mMiniLMv2-L12-H384-v1`) を使用して、検索結果の関連度を再評価し、精度を向上させます。
5.  **回答生成**: Google Gemini API (`google-genai`) を使用して、検索されたコンテキストに基づき自然な回答を生成します。

## 必要条件

- Python 3.x
- [uv](https://docs.astral.sh/uv/) (高速なPythonパッケージマネージャー)
- Jupyter Lab
- Google Gemini API Key

## インストール方法

### 1. ツールと依存関係のインストール

まず、システムに `uv` と `Jupyter` がインストールされていることを確認してください。まだの場合は以下のリンクを参照してください：

- **uv**: [インストールガイド](https://docs.astral.sh/uv/getting-started/installation/)
- **Jupyter**: [インストールガイド](https://jupyter.org/install)

次に、プロジェクトのルートディレクトリで以下のコマンドを実行し、Pythonの依存ライブラリをインストールします：

```bash
uv add sentence_transformers chromadb google-genai python-dotenv
```

### 2. 環境設定

プロジェクトのルートディレクトリに `.env` という名前のファイルを作成し、Google Gemini の API キーを設定してください：

```env
GEMINI_API_KEY=your_api_key_here
```

※ `your_api_key_here` の部分をご自身の API キーに置き換えてください。キーをお持ちでない場合は [Google AI Studio](https://aistudio.google.com/apikey) から取得できます。

## 使用方法

以下のコマンドで Jupyter Lab を起動します：

```bash
uv run --with jupyter jupyter lab
```

ブラウザが開いたら、`main.ipynb` を開いてセルを順番に実行してください。

### ノートブック (`main.ipynb`) の流れ

1.  **環境変数の読み込み**: `.env` ファイルから API キーを読み込みます。
2.  **ドキュメントの準備**: `doc.md` を読み込み、段落ごとに分割して表示します。
3.  **Embedding**: テキストチャンクをベクトル化します。
4.  **データベースへの保存**: ベクトルデータを ChromaDB に保存します。
5.  **検索 (Retrieval)**: 質問（例：「ドラえもんが使用した3つの秘密道具は何ですか？」）に対する関連ドキュメントを検索します。
6.  **リランク (Reranking)**: 検索結果をより正確な順序に並べ替えます。
7.  **生成 (Generation)**: 検索された情報をコンテキストとして Gemini に渡し、最終的な回答を生成します。

## ファイル構成

- `main.ipynb`: RAG システムのメインロジックを含む Jupyter Notebook。
- `doc.md`: 知識ベースとなるテキストデータ（物語）。
- `README.md`: 中国語版の簡易説明書。
- `README_JP.md`: このファイル（日本語版の詳細説明書）。
- `.env`: API キーなどの環境変数を設定するファイル（各自作成）。
- `pyproject.toml` / `uv.lock`: 依存関係管理ファイル。

## サンプル

**質問**: 「ドラえもんが使用した3つの秘密道具は何ですか？」

**回答例**:
> ドラえもんが使用した3つの秘密道具は以下の通りです：
> 1.  **複製マント（复制斗篷）**: 一時的にスーパーパワーを与えることができます。
> 2.  **時間停止ウォッチ（时间停止手表）**: 時間を5秒間止めることができます。
> 3.  **精神と時の部屋・ポータブル版（精神与时光屋便携版）**: 現実時間の1分間で1年分の修行を行うことができます。

---
作成日: 2026-01-14

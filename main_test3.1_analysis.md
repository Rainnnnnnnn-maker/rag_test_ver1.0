# main_test3.1.py 分析と変更点レポート

## 概要
`main_test3.1.py` は、`main_test3.py` をベースに、テキスト分割ロジックを **LangChain の RecursiveCharacterTextSplitter** に置き換えたバージョンです。これにより、日本語の文章構造（段落、文、句読点）を意識した、より自然で意味の通るチャンク分割が可能になります。

## 主な変更点

### 1. ライブラリの導入
`langchain.text_splitter` モジュールから `RecursiveCharacterTextSplitter` をインポートしました。

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter
```

### 2. テキスト分割関数の刷新 (`split_text`)

**変更前 (main_test3.py)**:
単純な文字数カウントによるスライス。
```python
def split_text(text):
    chunk_size = 200
    overlap = 50
    # ... (whileループで単純分割)
```

**変更後 (main_test3.1.py)**:
再帰的分割ロジックの適用。
```python
def split_text(text):
    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=200,
        chunk_overlap=50,
        separators=["\n\n", "\n", "。", "、", " ", ""], # 日本語向けの優先順位設定
        length_function=len,
    )
    chunks = text_splitter.split_text(text)
    return chunks
```

#### 設定の詳細解説
*   **`chunk_size=200`**: ターゲットとする最大文字数です。
*   **`chunk_overlap=50`**: 前後のチャンクで重複させる文字数です。文脈の連続性を保ちます。
*   **`separators`**: 分割に使用する区切り文字の優先順位リストです。
    1.  `\n\n` (段落): 最も優先。大きな意味の塊を維持します。
    2.  `\n` (改行): 次に優先。
    3.  `。` (句点): 文の終わり。
    4.  `、` (読点): 文の途中。
    5.  `" "` (スペース): 単語の区切り（英語など）。
    6.  `""` (文字単位): 最終手段。

### 3. UI表示の微修正
*   `st.set_page_config` のタイトルを `Local LLM Chat (Recursive)` に変更し、識別しやすくしました。
*   インデックス作成完了時に、チャンク数を表示するようにしました（デバッグや確認用）。

## 期待される効果

1.  **検索精度の向上**: 文の意味が途中で途切れることが減るため、ベクトル化された際の意味表現がより正確になります。
2.  **回答品質の向上**: LLMに渡されるコンテキスト（参考情報）が自然な日本語の文章となるため、LLMが内容を理解しやすくなり、より的確な回答が生成されやすくなります。
3.  **ノイズの低減**: 単語の途中や文脈無視の分割によるノイズが減ります。

## 実行方法

```bash
streamlit run main_test3.1.py
```
※ 事前に `uv pip install langchain` 等でLangChainライブラリのインストールが必要です。

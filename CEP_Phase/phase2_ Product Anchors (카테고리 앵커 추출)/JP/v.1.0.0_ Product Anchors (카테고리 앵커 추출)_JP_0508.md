<!-- v.1.0.0_cep_JP_0508.md (updated 2026-05-08) -->

## Phase 2 — Product Anchors (カテゴリアンカー抽出)

### 目的

基本情報の事前調査結果をもとに、**ベクトル検索用のカテゴリキーワード**を抽出します。  
以降のステップでのキーワード検索において、製品カテゴリの文脈として活用されます。

### 入力変数

| 変数                         | 説明                          | 例                |
| ---------------------------- | ----------------------------- | ----------------- |
| `{{product_name}}`           | 製品名                        | `갤럭시 S25`      |
| `{{country}}`                | 国コード                      | `kr`              |
| `{{response_language}}`      | 出力言語 (ko/en/ja)           | `ko`              |
| `{{basic_research_summary}}` | 基本情報の事前調査結果の要約  | (マークダウンテキスト) |

### 出力形式

JSON オブジェクト

```json
{
  "category": ["스마트폰", "안드로이드폰", "플래그십폰"]
}
```

### リクエストモデルおよびパラメータ

| パラメータ | 設定値                                                                                      |
| :-------- | :------------------------------------------------------------------------------------------ |
| model     | 'gpt-5.4-nano'                                                                              |
| input     | buildProductAnchorsPrompt({ productName, country, responseLanguage, basicResearchSummary }) |
| text      | { format: { type: 'text' } }                                                                |
| reasoning | { effort: 'none' }                                                                          |

---

### Prompt テンプレート

```
あなたはベクトル検索のための機械可読な product anchors を生成しています。

制約:
- すべての文字列は必ず {{response_language}} で記述してください。

コンテキスト:
- productName: {{product_name}}
- country: {{country}}

参照する調査資料 (Basic Research、前処理済みセクション要約):
{{basic_research_summary}}

スキーマ (厳密に準拠):
{
  "category": ["..."]
}

ルール:
- category: 2〜4個の一般的/汎用的な名詞または短い名詞句 (ブランド名/モデル名は不可)。
- 重複を除去し、可能な限り広範 → 具体の順に並べ替えます。
- カテゴリは単一の機能や特定の観点ではなく、製品全体を代表するものでなければなりません。

それでは JSON のみを出力してください。
```

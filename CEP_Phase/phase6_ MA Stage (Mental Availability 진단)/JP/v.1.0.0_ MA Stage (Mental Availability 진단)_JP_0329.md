<!-- v.1.0.0_cep_JP_0329.md (updated 2026-03-29) -->

## Phase 6 — MA Stage (Mental Availability 診断)

### 目的

AI Overview の応答から特定ブランドの **Mental Availability(MA)を6段階**で診断します。  
同一 CEP に対する複数の AIO ドキュメントをバッチで評価します。

### MA Stage 6段階の定義

| Stage              | 説明                                  |
| ------------------ | ------------------------------------- |
| `Absent`           | ブランドもカテゴリも言及なし         |
| `Category Only`    | カテゴリは言及されたがブランドはなし |
| `Competitor Owned` | 競合のみ言及、分析対象ブランドなし  |
| `Mentioned`        | 候補群に含まれるが比重が低い     |
| `Compared`         | 長所短所の比較文脈で登場             |
| `Recommended`      | 当該 CEP で優先的に推奨される              |

### 入力変数

| 変数                    | 説明                        | 例                      |
| ----------------------- | --------------------------- | ------------------------- |
| `{{brand_name}}`        | 分析対象ブランド名          | `로보락 S8`               |
| `{{cep_trigger_cue}}`   | CEP 購買文脈               | `아파트 1인 가구 청소 시` |
| `{{nano_intent}}`       | 詳細な目的                   | `빠른 청소`               |
| `{{kbf}}`               | 主要購買要因（KBF）              | `자동 먼지 비움 기능`     |
| `{{aio_documents}}`     | AIO ドキュメント配列（id + テキスト） | （下記フォーマット参照）          |
| `{{document_count}}`    | AIO ドキュメント数               | `5`                       |
| `{{response_language}}` | 根拠の記述言語              | `Korean`                  |

#### AIO ドキュメントフォーマット（`{{aio_documents}}`）

```
### Document 1 (ID: "doc-abc123")

```

[AI Overview 応答テキスト — 最大1000字]

```

### Document 2 (ID: "doc-def456")

```

[AI Overview 応答テキスト]

```

```

### 出力フォーマット

JSON 配列（正確に `{{document_count}}`個）

```json
[
  {
    "id": "doc-abc123",
    "ma_stage": "Mentioned",
    "rationale": "분석 대상 브랜드가 후보군에 포함되어 있으나 비중이 낮음."
  }
]
```

### リクエストモデルおよびパラメータ

| フィールド              | 値                                            |
| :---------------- | :-------------------------------------------- |
| model             | gpt-5.4-nano                                  |
| input             | systemPrompt + "nn" + userInput（単一文字列） |
| text              | { format: { type: 'text' } }                  |
| reasoning         | { effort: 'none' }                            |
| max_output_tokens | 2048                                          |

---

### Prompt テンプレート

````
# 役割
あなたはAI Overview応答からブランドのMental Availabilityを診断する専門家です。
あなたの任務は、与えられた購買文脈（CEP）の中で特定のブランドが各AI応答にどれほど際立って登場するかを評価することです。
複数のAI Overview文書を受け取り、それぞれを独立して判断しなければなりません。

# MA Stage (6段階)

1. **Absent**: ブランドも製品カテゴリも言及されない（カテゴリ未認識）
2. **Category Only**: 製品カテゴリは言及されるが、ブランドは登場しない
3. **Competitor Owned**: 競合のみが言及され、分析対象ブランドは登場しない
4. **Mentioned**: 候補群には含まれるが存在感が低い
5. **Compared**: 強み/弱みが説明され、比較文脈に登場する
6. **Recommended**: 与えられたCEPに対して優先的に推奨される

# 判断原則
- 分析対象ブランドが登場するか、どのような文脈で登場するかを確認する
- **Product(Brand) matching**: 一般的に同一製品（ブランド）と認識される派生形は有効なものとして扱う
- CEP、Nano Intent、KBFとの関連性を考慮する
- 同じ言及でも文脈によって異なる段階にマッピングされうる
- 不確実な場合は、より低い（保守的な）段階を選択する

# 独立評価 (CRITICAL)
- 各AI Overview文書を**完全に独立して**評価してください。
- ある文書に対する判断が、他の文書の判断に影響を与えてはなりません。
- すべての文書に同一の基準を一貫して適用してください。
- いかなる文書もスキップしないでください。すべての入力文書は対応する出力項目を持たなければなりません。

# 境界ケースガイド

| 境界 | → 低い段階 | → 高い段階 |
|----------|--------------|----------------|
| Category Only / Competitor Owned | ブランドが全く言及されない | 競合ブランドが一つでも言及される |
| Mentioned / Compared | 評価のない単純な列挙 | ブランドに対する強み/弱み/属性が一つでも記述される |
| Compared / Recommended | 選好のない長所短所 | 与えられたCEPに対する明示的な推奨 |

特殊ケース:
- **Negative mention**: Mentionedとみなす。比較の詳細があれば → Compared。決してRecommendedにはしない
- **CEP-irrelevant mention**: 深さに関わらずMentionedを上限とする

# 出力形式

正確に**{{document_count}}**個の要素（入力文書ごとに一つ）を持つ有効なJSON**配列**のみを返してください。
各要素は正確に三つのキーを持たなければなりません:
- **id**: 文書ID（入力文書のIDと正確に一致すること）
- **ma_stage**: "Absent" | "Category Only" | "Competitor Owned" | "Mentioned" | "Compared" | "Recommended" のいずれか
- **rationale**: 判断を説明する1〜2文（下記で指定された言語で）

# 出力ルール (STRICT, JSON-ONLY)
- 単一の有効なJSON**配列**のみを返してください。
- 配列は正確に**{{document_count}}**個の要素を含まなければなりません。
- 各要素の**id**は、対応する入力文書のIDと正確に一致しなければなりません。
- JSONをMarkdownコードフェンスで囲まないでください（no ```）。
- JSONの外部にいかなる散文、説明、見出しも追加しないでください。
- すべてのJSONキーと文字列値にダブルクォートを使用してください。

# 言語
- **rationale**フィールドは**{{response_language}}**で記述してください

# 入力

- **分析対象ブランド**: {{brand_name}}
- **CEP (購買文脈)**: {{cep_trigger_cue}}
- **Nano Intent (具体的な目的)**: {{nano_intent}}
- **KBF (主要購買要因)**: {{kbf}}

## AI Overview 応答 ({{document_count}} documents)

{{aio_documents}}

上記の各AI Overview文書を独立して分析し、それぞれのMA StageをJSON配列として出力してください。
```
````

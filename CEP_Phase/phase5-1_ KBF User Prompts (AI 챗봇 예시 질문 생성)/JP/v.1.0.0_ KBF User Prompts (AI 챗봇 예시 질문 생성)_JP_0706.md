<!-- v.1.0.0_cep_JP_0706.md (updated 2026-07-06) -->

## 8. Phase 5-1 — KBF User Prompts (AIチャットボット向け質問例の生成)

### 目的

CEP、nano-intent、KBFのメタデータをもとに、実際のユーザーがAIチャットボットに入力しそうな**自然な質問9個（KBFごとに3個）**を生成します。

### 入力変数

| 変数                          | 説明                                                                 | 例                                |
| ----------------------------- | -------------------------------------------------------------------- | --------------------------------- |
| `{{cep}}`                     | CEPの状況 (`card.cep`)                                               | `아침 샤워 후 머리 빠질 때`       |
| `{{nano_intent}}`             | nano-intent (`card.nano_intent`)                                    | `탈모 초기 자가 진단`             |
| `{{kbf}}`                     | カードのすべてのKBFを `", "` で連結した文字列 (`", ".join(kbf_list)`) | `약산성 pH 제품, 무향, 저자극`    |
| `{{response_language_label}}` | 応答言語ラベル（locale KR/JP/US基準、デフォルトはKorean）             | `Korean` / `Japanese` / `English` |

### 出力形式

JSON配列（正確に9個 — KBFごとに3個 × 3 KBF）

```json
[
  "질문1",
  "질문2",
  "질문3",
  "질문4",
  "질문5",
  "질문6",
  "질문7",
  "질문8",
  "질문9"
]
```

### リクエストモデルおよびパラメータ

| パラメータ | 設定値                                       | 備考                                     |
| --------- | -------------------------------------------- | ---------------------------------------- |
| model     | `'gpt-5.4-nano'`                             | 固定                                     |
| input     | `buildKbfUserPromptsPrompt(...)` の結果文字列 | CEP / Nano Intent / KBF / 出力言語を反映 |
| text      | `{ format: { type: 'text' } }`               | 通常テキスト出力                         |
| reasoning | `{ effort: 'none' }`                         | 推論effortを最小化                       |

### コード位置

- 定数: `KBF_USER_PROMPTS_TEMPLATE`
- コード位置: `app/module/clients/cep_prompts.py:963`
- 呼び出しAPI: `POST create_kbf_user_prompts` · `cep_finder.py:683`

---

### Prompt テンプレート

```
以下のCEP (Category Entry Point)、Nano Intent、KBF (Key Buying Factors)を活用して、実際のユーザーがAIチャットボット（ChatGPT、Gemini、Perplexityなど）に尋ねそうな自然な質問を生成してください。
AI検索で頻繁に見られる、購買意図の高い複合型プロンプトに焦点を当ててください。
トーン：頼れる友人と会話するように、親しみやすく、カジュアルで、話しかけやすいスタイルで書いてください。
正確に9個の項目（KBFごとに質問3個）を、追加の説明なしでリスト形式で提供してください。

Input:
- CEP (Category Entry Point): {{cep}}
- Nano Intent: {{nano_intent}}
- KBFs (Key Buying Factors): {{kbf}}

Output language: {{response_language_label}}

応答形式（JSON配列のみを出力し、それ以外は何も出力しないでください）：
["質問1", "質問2", "質問3", "質問4", "質問5", "質問6", "質問7", "質問8", "質問9"]
```

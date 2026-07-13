<!-- v.1.1.0_cep_JP_0710.md (updated 2026-07-10) — grounding 改訂: 入力を Phase 3.5 の証拠ユニットに置換 -->

## Phase 4 — CEP Trigger Extraction (CEP 状況・nano-intent 生成) — v1.1.0 (Grounded)

### v1.0.0 との変更点

| 項目      | v1.0.0（現行）                                       | v1.1.0（本文書）                                                             |
| --------- | ---------------------------------------------------- | ---------------------------------------------------------------------------- |
| 入力      | Phase 3 の散文マークダウン（`product_research_sections`） | **Phase 3.5 の証拠ユニット JSON**（`evidence_units`）                        |
| Task      | 散文から状況を抽出（自由生成）                        | **証拠ユニットの選択・結合によるシーン組み立て**（ユニットの quote 外の表現生成を禁止） |
| 根拠の連結 | `evidence`（文）+ `section_refs`（セクション番号）    | `evidence_unit_ids` + `source_ref` + **RTB（verbatim の再引用）**            |
| 出力      | `{situation, nanoIntents×3, evidence, section_refs}` | `{situation, w7, nanoIntents×3, kbf_hints, rtb, source_ref, evidence_unit_ids}` |
| 維持      | —                                                    | 多様性制約・7W soft quota・数量制約（正確に N 個）・JSON-only ルールをすべて維持 |

### 目的

Phase 3.5 の証拠ユニットから、**具体的な CEP 状況と nano-intent を組み立て**ます。
v1.0.0 とは異なり自由生成ではなく **ユニットの選択・結合**であるため、カードのすべての具体語（時間・場所・発話・行動）が引用へと逆追跡できます。

### コアコンセプト

- **CEP / nano-intent / 7W's Framework**: v1.0.0 と同一
- **組み立て（assembly）**: 同じ `source_ref` 内のユニット 1〜3 個を結合して 1 つのシーン文を作ること。異なるセクションのユニットの結合は禁止（文脈の合成を防止）
- **具体語対応の原則**: カードに登場するすべての時間・場所・発話・行動の表現は、結合したユニットの `quote` の中に実在していなければならない
- **4 大ハルシネーション類型の禁止**（Phase 3.5 文書と同一定義）: ① 時間の断定（Time assertion）② 存在しない場所（Invented place）③ 偽の引用（Fake quotation）④ 存在しない行動（Invented action）

### 入力変数

| 変数                      | 説明                                     | 例                 |
| ------------------------- | ---------------------------------------- | ------------------ |
| `{{product_name}}`        | 製品名                                   | `버티컬 마우스`    |
| `{{country}}`             | 国コード                                 | `kr`               |
| `{{evidence_units}}`      | **Phase 3.5 の出力 JSON（証拠ユニット配列）** | (JSON)             |
| `{{requested_count}}`     | 抽出する CEP 状況の個数                  | `10`               |
| `{{category}}`            | ［任意］製品カテゴリー                   | `마우스, 입력장치` |
| `{{existing_situations}}` | ［任意］既存の CEP リスト（重複防止用）  | （既に生成済みのリスト） |

### 出力形式

JSON 配列（正確に `{{requested_count}}` 個）

```json
[
  {
    "situation": "재택근무로 혼자 오래 작업하면서 목·어깨 불편을 먼저 느끼고, 소파·침대 옆에서 노트북을 쓰다 손목이 몸쪽으로 꺾이는 게 반복돼 '마우스까지 인체공학적으로 못 맞춘다'고 느껴 교체를 떠올리는 순간",
    "w7": {
      "why": "재택 전환으로 노트북 중심 작업이 길어짐",
      "where": "소파·침대 옆(팔이 자연스럽게 내려가지 않는 자세)",
      "while_": "목·어깨에 이어 손목이 몸쪽으로 꺾이는 느낌이 반복됨",
      "how_feeling": "'마우스까지 인체공학적으로 못 맞춘다'는 답답함"
    },
    "nanoIntents": [
      "노트북 중심 자세에서 손목이 꺾이지 않는 그립으로 바꾸기",
      "목·어깨-손목 부담을 함께 줄이기",
      "책상 전체가 아니라 손에 닿는 기기부터 바꾸기"
    ],
    "kbf_hints": ["손목을 덜 꺾이게 하는 수직(핸드셰이크) 그립 각도"],
    "rtb": "\"소파나 침대 옆에 노트북을 두고 업무를 보다 보니, 손목이 몸쪽으로 꺾이는 느낌이 반복돼요\" (hankyung)",
    "source_ref": "§1",
    "evidence_unit_ids": [1, 2]
  }
]
```

### リクエストモデルおよびパラメータ

v1.0.0 と同一（gpt-5.4-nano・tools []・reasoning none・max_output_tokens 32768）。

---

### Prompt テンプレート

````
# Role
あなたは Category Entry Point（CEP）の特定を専門とする消費者行動アナリストです。
あなたは状況を捏造しません。事前検証済みの証拠ユニットから CEP 状況を**組み立て**、出力のすべての具体的なディテールが verbatim の引用へと逆追跡できるようにします。

{{category_section}}
# Evidence Units（事前検証済み・verbatim 根拠付き）
各ユニットには、消費者リサーチから取得した verbatim の引用と、その引用が直接裏付ける w7 フィールド・nano-intent 候補・KBF ヒントが含まれています。

{{evidence_units}}

# CEP Definition

CEP (Category Entry Point): 状況/トリガー
CEP とは、消費者が特定の製品やサービスを必要としたり購入を検討したりするようになる、具体的な状況・文脈・きっかけを指します。例えば「喉が渇いたとき」「映画館にいるとき」「友人へのプレゼントを買う必要があるとき」がまさにそうした状況です。

# Task

{{task_section}}

**証拠ユニットを選択・結合**して、正確に {{requested_count}} 個の CEP 状況を組み立ててください:

1. 同じ `source_ref`（同じセクション）を共有するユニットを 1〜3 個選んでください。異なるセクションのユニットを結合しないでください — それはどの消費者も語っていない文脈を捏造することになります。
2. 選択したユニットの quote と w7 フィールドに存在する表現のみを用いて、`situation` を 1 つの自然なシーン文として記述してください。
3. ユニットの w7 フィールドをカードの `w7` に統合してください（ユニットが実際に提供するフィールドのみ — 欠けている次元を埋めないでください）。
4. ユニットの `nano_intent_candidates` を起点として、正確に 3 個の `nanoIntents` を記述してください；自然さのために言い換えることはできますが、新たな具体情報を導入してはいけません。
5. ユニットの `kbf_hints` を（重複を除去して）そのまま引き継いでください。
6. `rtb` は選択したユニットの中で最も強力な quote に設定し（verbatim、引用符付き、source タグ付き）、`source_ref` は共有セクションに、`evidence_unit_ids` は選択したユニット id に設定してください。

# Grounding Constraint (CRITICAL)

`situation` のすべての具体的なディテール — 時間表現・場所・引用された発話・行動 — は、選択したユニットの quote に必ず登場していなければなりません。
以下は禁止です:
- **Time assertion**: quote に存在しない期間/タイミング（例: 「첫 주」「주말」の追加）。
- **Invented place**: quote に存在しない場所（例: 「도서관」の追加）。
- **Fake quotation**: 引用符で囲んだ言い換え。引用する場合は、ユニットの quote から verbatim でコピーしてください。
- **Invented action**: quote に存在しない行動（例: 「다시 검색」の追加）。
シーンが乏しく感じられても、乏しいままにしてください — 根拠に乏しくても裏付けのあるカードの方が、豊かに捏造されたカードより優れています。

# Prioritize Natural, Real-World Situations

- 普通の人々が日常生活で実際に思い浮かべるような状況を記述してください
- わざとらしい、あるいは過度に複雑で不自然に感じられる状況を避けてください
- 日常で現実的に起こり得る、具体的で自然な状況に焦点を当ててください

# Diversity Constraint — Each Situation Must Be Independent

- 各状況は、明確に異なるペルソナ・文脈・人生の瞬間を表さなければなりません。
- 同じ根底のトリガーを単に言い換えただけの状況を生成しないでください。
- 1 つのセクションを繰り返し再利用するよりも、多くの異なるセクション（source_ref）を幅広くカバーすることを優先してください。
- 出力を確定する前に、すべての状況をまとめて見直し、いずれも冗長だったり過度に似通っていたりしないことを確認してください。

**Self-check: 各状況のペアについて「別の人物が主役になり得るか、あるいは設定・活動が根本的に異なるか?」を問うてください。答えが NO なら、いずれか一方を差し替えなければなりません。**

# 7W Coverage (Soft Quota) — Make Diversity Real

{{coverage_section}}

# Format

各状況: { "situation": "...", "w7": { ... }, "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"], "kbf_hints": ["..."], "rtb": "...", "source_ref": "§N", "evidence_unit_ids": [<unit_id>, ...] }

**7W's Framework** — `w7` オブジェクトの次元（ユニットが裏付けるものだけを埋めてください）:
- **Why** (必要/動機) / **When** (機会/時間) / **Where** (場所/文脈) / **While** (並行活動) / **With Whom** (社会的文脈) / **With What** (補完製品) / **hoW Feeling** (感情状態)
- JSON keys: why / when / where / while_ / with_whom / with_what / how_feeling

**Nano Intent (nanoIntents 配列、正確に 3 項目)**: 同じ CEP 内でも消費者ごとに異なる具体的な目的（= Why 次元）。
- 製品名やカテゴリーを繰り返さないでください
- 「必要だ」「切らした」のような一般的な表現を避けてください
- 状況ごとに正確に 3 個の nanoIntents を出力してください

{{evidence_section}}

# Language

**{{response_language}} で有効な JSON 形式で応答してください。**
`rtb` は元の引用の言語を verbatim で保持しなければなりません。

# Quantity Constraint (CRITICAL)

正確に {{requested_count}} 個の CEP オブジェクトを返さなければなりません。
トップレベルの JSON 配列の長さは正確に {{requested_count}} でなければなりません。
証拠ユニットが {{requested_count}} 個の十分に区別される状況を裏付けられない場合でも、依然として {{requested_count}} 個を返してください — ただしディテールを捏造するのではなく、使用頻度の低いセクションからより乏しいカードを作ってください。

---

# OUTPUT RULES (STRICT, JSON-ONLY)
- 有効な JSON 値を 1 つだけ返してください。
- JSON を Markdown のコードフェンスで囲まないでください（``` は禁止）。
- JSON の外部に、いかなる散文・説明・見出し・箇条書き/番号付きリストも追加しないでください。
- 末尾のカンマを追加しないでください。
- すべての JSON キーと文字列値にダブルクォートを使用してください。
- トップレベルの JSON は、必ず長さが正確に {{requested_count}} の配列でなければなりません。
````

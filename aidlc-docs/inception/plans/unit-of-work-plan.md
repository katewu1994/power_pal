# PowerPal Unit of Work Plan

**ドキュメント種別**: Units Generation Plan
**ステータス**: Completed
**最終更新**: 2026-05-11
**関連文書**: `../application-design/unit-of-work.md`, `../application-design/unit-of-work-dependency.md`, `../application-design/unit-of-work-story-map.md`

---

## 1. 目的

Units Generation ステージで確認した論点と、その回答を残す。Workflow Planning は本書の回答を前提として実行する。

---

## 2. 計画論点と回答

### Q1. ユニットは何を基準に切るか

[Answer]: ユニットはユーザー画面やエピックではなく、責務境界と依存安定性で切る。`stories.md` の 1 ストーリーを 1 ユニットへ対応させない。

### Q2. ユーザーストーリーとの関係はどう整理するか

[Answer]: ストーリーは価値の記述、ユニットは実装責務の記述とみなし、`unit-of-work-story-map.md` では many-to-many の対応表だけを持つ。受入条件の再記述は行わない。

### Q3. 並列開発のために最初に固定すべき領域は何か

[Answer]: U1, U2, U3, U4, U6 を Foundation として先に固定する。ここが安定すると、U5 と U7 を headless に進められ、UI 側の stub 依存が減る。

### Q4. 判定ロジック、文言、通知出し分けは同一ユニットにまとめるべきか

[Answer]: まとめない。U5 を「何を提案するか」、U6 を「どう言うか」、U7 を「いつ・どの強さで出すか」に分離する。これにより Quiet/Stealth やキャラクタートーンの調整が局所化される。

### Q5. Renderer 着手前に固定すべき契約は何か

[Answer]: U10 の preload API、IPC payload 型、subscription 方式、Renderer view model 型を先に固定する。U8/U9 は domain module を直接 import しない。

### Q6. platform unit はどう扱うか

[Answer]: U4 と U10 は単独のユーザー価値よりもシステム安定性を担う基盤ユニットとして扱う。ストーリーに primary owner がなくても、独立した Construction 対象として残す。

### Q7. Units Generation 完了の判定条件は何か

[Answer]: `unit-of-work.md`, `unit-of-work-dependency.md`, `unit-of-work-story-map.md`, `unit-of-work-plan.md` の 4 文書が揃い、ユニット一覧、依存、ストーリー対応、実装順が相互整合していること。

---

## 3. 実装ウェーブ案

| Wave | 対象 | 狙い | 次ウェーブへ渡すもの |
|---|---|---|---|
| Wave 1 | U1, U2, U3, U4, U6 | 基盤契約の固定 | battery/state 型、visibility 型、meeting 型、repo 契約、tone API |
| Wave 2 | U5, U7 | headless な判定と通知編成 | 朝判定、予備充電、抽選、Quiet/Stealth、再通知仕様 |
| Wave 3 | U10 | Main と Renderer の接続面を閉じ込める | preload API、IPC names、view model 契約 |
| Wave 4 | U8, U9 | UI surface の並列実装 | 常駐キャラクター、モーダル群、設定、オンボーディング |
| Wave 5 | Integration | デモ導線と build/test | MVP Core end-to-end シナリオ、mock fallback、Quiet 復帰確認 |

---

## 4. Workflow Planning へ持ち越す論点

### 4.1 Construction 設計で具体化する項目

[Answer]: per-unit の Functional Design、テスト観点、NFR の割当、ファイル粒度の実装順は Workflow Planning とその後続ステージで具体化する。

### 4.2 G5 で承認対象にする内容

[Answer]: 実装順、並列化方針、各 wave の exit criteria、Construction 着手条件、mock fallback を含むデモ成立条件を `execution-plan.md` にまとめて承認を得る。

### 4.3 現時点で開いたままの事項

[Answer]: Phase 2 以降の跨日記憶具体ルール、多端末同期、最終キャラクターアート、デモ台本文言は残タスクだが、MVP Core のユニット分解自体はブロックしない。

---

## 5. Units Generation 完了宣言

[Answer]: 本ステージでは「ユーザー価値の定義」と「技術責務の分解」を分離した。次ステージは `execution-plan.md` を正式化する Workflow Planning である。

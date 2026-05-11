# PowerPal Unit of Work Dependency

**ドキュメント種別**: Unit Dependency Matrix
**ステータス**: Finalized for Workflow Planning
**最終更新**: 2026-05-11
**関連文書**: `unit-of-work.md`, `unit-of-work-story-map.md`, `../plans/unit-of-work-plan.md`

---

## 1. 目的

本書は、`unit-of-work.md` で定義したユニット間の依存順序を明示し、並列実装可能な境界を固定する。

- 矢印は「ランタイム依存」かつ「先に契約を固定したい依存」を意味する
- user story の実行順ではなく、**Construction 上のビルド順**を表す
- UI は domain へ直接依存せず、必ず `U10 ipc-bridge` を経由する

---

## 2. レイヤ構造

| レイヤ | ユニット | 役割 |
|---|---|---|
| Foundation | U1, U2, U3, U4, U6 | 計算、外部シグナル、外部データ、永続化、文言テンプレート |
| Orchestration | U5, U7 | Foundation を束ねてユーザー介入の判定と編成を行う |
| Boundary | U10 | Main と Renderer の安全な接続面 |
| Renderer | U8, U9 | 常駐キャラクターと各種 UI surface |

---

## 3. 依存マトリクス

凡例:

- `●`: 行ユニットが列ユニットに依存する
- 空欄: 直接依存なし

| From \\ To | U1 | U2 | U3 | U4 | U5 | U6 | U7 | U8 | U9 | U10 |
|---|---|---|---|---|---|---|---|---|---|---|
| U1 `energy-domain` |  |  |  |  |  |  |  |  |  |  |
| U2 `activity-visibility` |  |  |  |  |  |  |  |  |  |  |
| U3 `calendar-context` |  |  |  |  |  |  |  |  |  |  |
| U4 `local-data-platform` |  |  |  |  |  |  |  |  |  |  |
| U5 `decisioning-orchestrator` | ● | ● | ● | ● |  |  |  |  |  |  |
| U6 `tone-engine` |  |  |  |  |  |  |  |  |  |  |
| U7 `intervention-scheduler` |  | ● |  | ● | ● | ● |  |  |  |  |
| U8 `character-shell-ui` |  |  |  |  |  |  |  |  |  | ● |
| U9 `interaction-ui` |  |  |  |  |  |  |  |  |  | ● |
| U10 `ipc-bridge` | ● | ● | ● | ● | ● | ● | ● |  |  |  |

---

## 4. 重要依存チェーン

| シナリオ | 依存チェーン | 意味 |
|---|---|---|
| 朝の初期判定 | U3 → U5 → U6 → U10 → U8 | 予定と天気を元に判定し、文言付きで常駐 UI へ出す |
| 予備充電通知 | U3 → U5 → U7 → U10 → U9 | 連続会議検知から通知編成を経てモーダルへ渡す |
| Quiet/Stealth | U2 → U7 → U10 → U8/U9 | 人前シグナルが UI surface の強度に反映される |
| 充電抽選 | U5 → U6 → U10 → U9 | 抽選結果は main で決め、Renderer は表示のみ行う |
| 設定反映 | U9 → U10 → U4, U2, U7 | 画面から変更した設定が保存され、閾値や通知強度へ波及する |

---

## 5. 並列化ルール

| ルール | 内容 |
|---|---|
| R1 | U1, U2, U3, U4, U6 は互いに直接依存しないため同時に着手できる |
| R2 | U5 は Foundation の契約が揃えば UI を待たずに完成できる |
| R3 | U7 は U5 と U6 の出力契約が固定されれば headless で検証できる |
| R4 | U8/U9 は U10 の IPC 契約確定後に並列実装できる |
| R5 | U8/U9 から U1-U7 へ直接 import しない。依存が発生した時点で設計逸脱とみなす |

---

## 6. 建設順の推奨

| 順序 | ユニット | 理由 |
|---|---|---|
| 1 | U1, U2, U3, U4, U6 | もっとも下流の依存を持つ基盤群。後工程の stub 削減に効く |
| 2 | U5 | 朝判定、抽選、予備充電など複数ストーリーの中核判定を固定する |
| 3 | U7 | 通知制御と Quiet/Stealth の振る舞いを固める |
| 4 | U10 | Main 契約を閉じ込め、Renderer 実装に渡す |
| 5 | U8, U9 | UI を並列で実装し、同一 IPC 契約に載せる |
| 6 | Integration | Build/Test、デモシナリオ、mock fallback、Quiet 復帰を結合確認する |

---

## 7. 下流へ先渡しすべき固定契約

| Unit | 先に固定すべき契約 |
|---|---|
| U1 | `BatterySnapshot`, `ChargeAction`, battery tier 判定 |
| U2 | `VisibilitySignals`, `DisplayMode`, continuous work counter |
| U3 | `MeetingSummary`, `ContinuousMeetingBlock`, mock fallback API |
| U4 | 設定スキーマ、ログ保存 API、token store interface |
| U5 | `MorningAssessment`, `PreChargeSuggestion`, `ChargeChoice`, `DailySummaryModel` |
| U7 | `PendingNotification`, defer 仕様、response logging 契約 |
| U10 | preload API 名、subscription 方式、Renderer view model 型 |

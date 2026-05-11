# PowerPal Unit of Work

**ドキュメント種別**: Unit Definition Artifact
**ステータス**: Finalized for Workflow Planning
**最終更新**: 2026-05-11
**関連文書**: `unit-of-work-dependency.md`, `unit-of-work-story-map.md`, `../plans/unit-of-work-plan.md`, `../user-stories/stories.md`

---

## 1. 目的

本書は、PowerPal を Construction フェーズへ渡すための**技術的な作業分割**を定義する。

- `stories.md` はユーザー価値、期待行動、受入条件を定義する
- `unit-of-work.md` は実装責務、所有境界、依存関係、完了条件を定義する
- 対応関係は **1:1 ではなく many-to-many** とする

したがって、ユニットは「1 つのストーリーをそのまま実装する箱」ではなく、複数ストーリーを横断して再利用される**技術的な責務のまとまり**として切る。

---

## 2. 分解原則

| 原則 | 内容 |
|---|---|
| P1 | ユニットはユーザー画面ではなく、**責務境界**で切る |
| P2 | Main process が判定・計算・外部連携を持ち、Renderer は表示と入力に限定する |
| P3 | 文言生成、通知制御、表示ポリシーは独立ユニットとし、判定ロジックと混在させない |
| P4 | `persistence` や `ipc-bridge` のような**基盤ユニット**は、単独のユーザーストーリーを持たなくても正当な作業単位とみなす |
| P5 | カレンダー詳細の非永続化、外部書き込み禁止、Quiet/Stealth 優先などの不変条件は、責務を持つユニットに直接割り当てる |
| P6 | 各ユニットは downstream が依存できる**明示的な公開契約**と、完了判定できる DoD を持つ |

---

## 3. 正式ユニット一覧

| Unit ID | 名称 | 主要責務 | 主な配置候補 | 依存 |
|---|---|---|---|---|
| U1 | `energy-domain` | 電池残量の状態遷移、消耗・回復計算、手動補正ルール | `electron/main/domain/battery/`, `shared/types/battery.ts` | （なし） |
| U2 | `activity-visibility` | アクティブ作業検知、連続作業計測、人前シグナル正規化、DisplayMode 判定 | `electron/main/domain/visibility/` | （なし） |
| U3 | `calendar-context` | Google Calendar read-only 連携、mock fallback、連続会議ブロック抽出、天気入力取得 | `electron/main/adapter/`, `electron/main/domain/precharge/` | （なし） |
| U4 | `local-data-platform` | SQLite スキーマ、リポジトリ、設定保存、日次ログ、セキュアトークン保管 | `electron/main/persistence/`, `electron/main/security/` | （なし） |
| U5 | `decisioning-orchestrator` | 朝判定、予備充電計画、抽選、強い介入の判定、退勤集計モデル生成 | `electron/main/domain/morning/`, `lottery/`, `precharge/` | U1, U2, U3, U4 |
| U6 | `tone-engine` | シーン別文言、トーン変化、口調テンプレート、強度別コピー選択 | `electron/main/domain/tone/` | （なし） |
| U7 | `intervention-scheduler` | rate limit、dedupe、defer、再通知、Quiet/Stealth 適用後の通知編成 | `electron/main/domain/notification/` | U2, U4, U5, U6 |
| U8 | `character-shell-ui` | 常駐キャラクター、ドラッグ、小型化、トレイ、電池残量の常時表示 | `electron/main/window-manager.ts`, `src/components/character/` | U10 |
| U9 | `interaction-ui` | 予備充電、抽選、Quiet バナー、強い介入、退勤サマリー、初回セットアップ、設定画面 | `src/components/cards/`, `modal/`, `settings/` | U10 |
| U10 | `ipc-bridge` | preload、typed IPC 契約、Renderer 向け view model 変換、Node API 隔離 | `electron/main/ipc/`, `electron/preload/`, `shared/types/ipc.ts` | U1, U2, U3, U4, U5, U6, U7 |

---

## 4. ユニット詳細

### U1 `energy-domain`

- `責務`: `BatteryEngine` の状態遷移、アクティブ時間ベースの消耗、充電アクションによる回復、手動補正時の clamp と tier 再計算
- `公開契約`: `BatterySnapshot`, `TickContext`, `ChargeAction`, `adjustManual()`, `recover()`, `tick()`
- `非責務`: 会議検知、通知表示、文言生成、Renderer 描画
- `完了条件`: 0-100 範囲保証、無操作時非消耗、回復テーブル固定、手動補正が永続化経路へ渡せる

### U2 `activity-visibility`

- `責務`: OS シグナル取得、連続作業時間計測、アイドル判定、`manualHumanMode` を含む `VisibilitySignals` の正規化、`DisplayMode` 決定
- `公開契約`: `VisibilitySignals`, `DisplayMode`, `getSignals()`, `getMode()`
- `非責務`: 通知キュー、文言、会議前提案の内容決定
- `完了条件`: 高信頼シグナルのみ自動判定対象、判定不確実時は `quiet` 側へ倒す、手動トグルで必ず上書き可能

### U3 `calendar-context`

- `責務`: OAuth 2.0 read-only、予定取得、`MeetingSummary` 変換、連続会議ブロック抽出、mock JSON fallback、朝判定用天気入力取得
- `公開契約`: `CalendarAdapter`, `MeetingSummary`, `ContinuousMeetingBlock`, `WeatherInput`
- `非責務`: カレンダータイトル保存、予定変更、通知出し分け
- `完了条件`: `calendar.readonly` のみ使用、自己のみ予定と終日予定を除外、オフライン時も mock 経由でコア導線が成立

### U4 `local-data-platform`

- `責務`: DB 初期化、マイグレーション、設定・日次ログ・充電ログ・通知応答ログの保存、OAuth トークンのセキュア保管
- `公開契約`: `SettingsRepo`, `DailyLogRepo`, `ChargeActionLogRepo`, `NotificationLogRepo`, `TokenStore`
- `非責務`: UI 制御、通知タイミング、ストーリー文言
- `完了条件`: カレンダー詳細非保存、再起動後の設定復元、マイグレーション再実行安全、Keychain 経路が分離されている

### U5 `decisioning-orchestrator`

- `責務`: `MorningJudge`, `PreChargePlanner`, `LotteryService`, `ForceChargeEvaluator`, `SummaryAggregator` を束ね、UI 非依存の意思決定結果を返す
- `公開契約`: `MorningAssessment`, `PreChargeSuggestion`, `ChargeChoice`, `ForceChargeDecision`, `DailySummaryModel`
- `非責務`: 文言テンプレート選択、通知の rate limit、画面描画
- `完了条件`: 判定結果は semantic な view model として出力し、文言埋め込みなしでも downstream が利用できる

### U6 `tone-engine`

- `責務`: 朝判定、予備充電、抽選、Quiet 復帰、退勤サマリー向けの台詞テンプレートとバリエーション管理
- `公開契約`: `morningLine()`, `preChargeLine()`, `lotteryLine()`, `quietRecoveryLine()`, `summaryLine()`
- `非責務`: 条件判定、通知抑制、UI アニメーション制御
- `完了条件`: 分析的説明に寄りすぎない、表示強度と口調が矛盾しない、Phase 2 の口調変化を後付け可能

### U7 `intervention-scheduler`

- `責務`: 通知編成、重複排除、1 時間 2 回までの proactive 制御、90 分 1 回までの強い介入制御、defer 1 件保持、Quiet 解除後の再通知
- `公開契約`: `enqueue()`, `markResponded()`, `flushDeferred()`, `PendingNotification`
- `非責務`: 生の OS シグナル取得、朝判定の数値計算、Renderer コンポーネント
- `完了条件`: `DisplayMode` を必ず経由し、同一意図の 30 分以内再送禁止、抑制通知は最重要 1 件だけ戻す

### U8 `character-shell-ui`

- `責務`: 透明ウィンドウ、最前面表示、ドラッグ移動、小型化、電池表示、トレイ統合、軽量アニメーション
- `公開契約`: `CharacterViewModel` を受けて描画する Renderer surface
- `非責務`: 電池計算、抽選ロジック、設定保存
- `完了条件`: Main から受けた完成済み state のみ表示し、Renderer 側で判定を再実装しない

### U9 `interaction-ui`

- `責務`: 予備充電 UI、抽選 UI、Quiet 復帰バナー、強い介入カード、退勤サマリー、初回セットアップ、設定画面
- `公開契約`: 各 surface の action payload と、`U10` が提供する typed command 呼び出し
- `非責務`: ビジネスルール、通知抑制判断、トークン保存
- `完了条件`: キーボード操作可能、強い介入 UI は ESC で閉じられる、`settings` と `onboarding` が同一設定契約を共有する

### U10 `ipc-bridge`

- `責務`: preload での `contextBridge` 公開、Main↔Renderer の typed IPC、view model 変換、セキュアな境界維持
- `公開契約`: `window.powerPal.*` API、IPC payload 型、Renderer subscription 契約
- `非責務`: domain 判定ロジック、実際の画面レイアウト
- `完了条件`: `contextIsolation: true` 前提、Node API の生渡し禁止、U8/U9 がこのユニットだけを通じて Main と通信する

---

## 5. 実装ウェーブ

| Wave | 対象ユニット | 狙い | 完了条件 |
|---|---|---|---|
| Wave 1 | U1, U2, U3, U4, U6 | 基盤ロジックと外部境界を固定する | domain 型と repository 契約が安定し、mock で単体確認できる |
| Wave 2 | U5, U7 | 判定と通知編成を成立させる | 朝判定、予備充電、抽選、Quiet/Stealth 制御が headless で検証できる |
| Wave 3 | U10 | 安全な Renderer 接続面を先に確定する | typed IPC と view model 変換が固定され、Renderer 側の stub を不要にする |
| Wave 4 | U8, U9 | ユーザー接点を接続する | MVP Core の主要導線が UI から end-to-end で通る |
| Wave 5 | Integration | Build/Test とデモ導線調整 | 主要シナリオ、Quiet 動作、mock fallback が一貫して動く |

---

## 6. 補足

- U4 と U10 は**基盤ユニット**であり、単独のユーザー価値よりも downstream の安全性と並列開発性を担保する
- U5, U6, U7 を分離したのは、**判定内容**、**言い方**、**出し方**を独立に調整できるようにするため
- U8 と U9 を分離したのは、常駐キャラクターの軽量性と、モーダル系ワークフローの複雑さを分けるため

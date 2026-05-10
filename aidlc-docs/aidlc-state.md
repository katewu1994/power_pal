# AIDLC ワークフロー状態ファイル

**File**: `aidlc-docs/aidlc-state.md`
**Project**: PowerPal
**Methodology**: [AI-DLC](https://github.com/awslabs/aidlc-workflows)（AI-Driven Development Life Cycle）
**State Version**: 2.2
**Last Updated**: 2026-05-10T10:01:16Z

> 本ファイルは AIDLC ワークフロー進捗の **single source of truth** である。セッション再開時、エージェントは必ず最初に本ファイルを読み、Stage Progress テーブル中の最初の非 `COMPLETE` ステージを特定し、対応するプラン／アーティファクトファイルから再開すること。

---

## プロジェクト情報

| 項目 | 値 |
|---|---|
| プロジェクト名 | PowerPal |
| プロジェクト分類 | **Greenfield**（新規開発、既存コードベースなし） |
| アプリケーション分類 | デスクトップアプリケーション（Electron + React + TypeScript） |
| 起源 | ハッカソン応募作品（テーマ「人をダメにする」）。MVP → Phase 2 → Phase 3 のフェーズ展開を想定 |
| プライマリペルソナ | デスクワーク中心の会社員（在宅・ハイブリッド勤務、連続会議多め） |
| 対象プラットフォーム | macOS（MVP）／ Windows（Phase 2）／ Linux（Phase 3 以降検討） |
| 技術スタック | Electron, React, TypeScript, Vite, SQLite (better-sqlite3), Google Calendar API |
| 解析深度 | **Comprehensive**（製品ロードマップが存在し、詳細な Inception アーティファクトが既に生成されているため） |
| ワークスペースルート | `/` |
| ドキュメント配置 | `aidlc-docs/` |
| アプリケーションコード配置 | ワークスペースルート（`aidlc-docs/` 内には絶対に配置しない） |

---

## 現在のフェーズ／ステージ

| 項目 | 値 |
|---|---|
| Current Phase | 🔵 **INCEPTION** |
| Current Stage | **Application Design**（COMPLETE）→ 次は **Units Generation** |
| 直前完了ステージ | Application Design |
| 直前通過した承認ゲート | G4 — Application Design 完了確認（2026-05-10T07:40:00Z） |
| 待機中の承認 | （なし — ユーザーによる Units Generation 着手指示待ち） |
| 次アクション | **Units Generation** ステージの開始（`inception/units-generation.md`）。詳細は本ファイル末尾の「Next Action」節を参照 |

---

## ステージ進捗（Stage Progress）

### INCEPTION フェーズ（🔵）

| # | Phase | Stage | Status | 完了日時（UTC） | アーティファクト／プラン |
|---|---|---|---|---|---|
| 1 | INCEPTION | Workspace Detection | ✅ COMPLETE | 2026-05-10T06:35:00Z | `aidlc-docs/inception/workspace-detection/workspace-analysis.md` — Greenfield 確定 |
| 2 | INCEPTION | Reverse Engineering | ⏭️ SKIPPED | 2026-05-10T06:35:00Z | _スキップ理由: Greenfield プロジェクトのため、解析対象の既存コードなし_ |
| 3 | INCEPTION | Requirements Analysis | ✅ COMPLETE | 2026-05-10T07:05:00Z | `aidlc-docs/inception/requirements/requirements.md` |
| 4 | INCEPTION | User Stories | ✅ COMPLETE | 2026-05-10T07:25:00Z | `aidlc-docs/inception/user-stories/stories.md` + `personas.md` |
| 5 | INCEPTION | Application Design | ✅ COMPLETE | 2026-05-10T07:40:00Z | `aidlc-docs/inception/application-design/application-design.md` |
| 6 | INCEPTION | Units Generation | ⏳ PENDING | — | _次アクション_ — 出力先: `aidlc-docs/inception/application-design/unit-of-work.md` ほか |
| 7 | INCEPTION | Workflow Planning | ⏳ PENDING | — | _出力先: `aidlc-docs/inception/plans/execution-plan.md`（**Construction 着手前にユーザー承認 G5 が必須**）_ |

### CONSTRUCTION フェーズ（🟢）

各ユニットごとの per-unit ステージ（Functional Design → NFR Requirements → NFR Design → Infrastructure Design → Code Generation）は、Units Generation 完了後に確定する。現時点では未展開。

| # | Phase | Stage | Status |
|---|---|---|---|
| 8 | CONSTRUCTION | Functional Design（per unit） | ⏳ PENDING（per-unit テーブル未生成） |
| 9 | CONSTRUCTION | NFR Requirements（per unit） | ⏳ PENDING |
| 10 | CONSTRUCTION | NFR Design（per unit） | ⏳ PENDING |
| 11 | CONSTRUCTION | Infrastructure Design（per unit） | ⏳ PENDING |
| 12 | CONSTRUCTION | Code Generation（per unit、**two-step approval**） | ⏳ PENDING |
| 13 | CONSTRUCTION | Build and Test | ⏳ PENDING |

### OPERATIONS フェーズ（🟡）

| # | Phase | Stage | Status |
|---|---|---|---|
| 14 | OPERATIONS | （将来拡張のためのプレースホルダ） | ⏳ N/A |

**Status の意味**:

| 値 | 意味 |
|---|---|
| ✅ `COMPLETE` | ステージ完了、アーティファクト生成済み |
| 🔄 `IN_PROGRESS` | 現在実行中 |
| ⏳ `PENDING` | 未着手 |
| ⏭️ `SKIPPED` | 意図的にスキップ（理由を併記） |
| ❌ `FAILED` | バリデーション失敗、リワーク必要 |
| ⏸️ `BLOCKED` | ユーザー入力待ち、または外部依存待ち |

---

## 拡張ルール設定（Extension Configuration）

> 拡張ルール（セキュリティ、コンプライアンス、テスト等）は core workflow の上に重ねて適用される追加レイヤである。有効化された拡張は **blocking constraint** となり、各ステージ完了前に充足検証が必須となる。本セクションは各ステージ開始時に確認される。

| Extension | カテゴリ | Enabled | 決定日時 | 理由 |
|---|---|---|---|---|
| `security-baseline` | security | ✅ **true** | 2026-05-10T07:00:00Z | Google Calendar OAuth トークンを扱い、OS Keychain に保存する。明示的なプライバシー不変条件（「プロダクト憲法」）が存在する。ベースラインセキュリティルールが適用対象 |
| `property-based-testing` | testing | ❌ false | 2026-05-10T07:00:00Z | MVP はハッカソン規模であり、標準的な単体・結合テストで十分。Phase 2 で再評価 |

### `security-baseline` のプロジェクト固有バインディング

OWASP 準拠の汎用ルール（SECURITY-01 〜 SECURITY-15）を PowerPal のドメインに割り付ける。Code Generation ステージでは、対応する rule ID をコード中のコメントとして残すことを義務付ける（`code-generation.md` ルール準拠）。

| Rule ID | 汎用説明 | PowerPal バインディング |
|---|---|---|
| SECURITY-01 | 入力バリデーション | 電池残量手動調整（0-100 整数）、設定パネル各入力 |
| SECURITY-02 | 出力エンコーディング | キャラクター台詞は React 経由で描画（`dangerouslySetInnerHTML` 禁止） |
| SECURITY-03 | 構造化ログ・PII 非出力 | `~/.powerpal/logs/error.log`: error / warn のみ。カレンダータイトルや参加者名を出力禁止 |
| SECURITY-04 | 認証 / OAuth | Google OAuth 2.0 + PKCE。スコープは `calendar.readonly` のみ。リダイレクトは `127.0.0.1:<random>` のループバック |
| SECURITY-05 | 認可・最小権限 | Calendar Adapter は書き込み API を一切公開しない。Slack / Teams SDK は `package.json` に依存追加禁止 |
| SECURITY-06 | シークレット管理 | OAuth トークンは `keytar` 経由で OS Keychain（macOS Keychain / Windows Credential Manager）に保存 |
| SECURITY-07 | 通信のセキュリティ | 外部通信はすべて HTTPS（`accounts.google.com` / `googleapis.com` / `open-meteo.com`） |
| SECURITY-08 | セッション・分離 | Renderer: `contextIsolation: true`, `nodeIntegration: false`, `sandbox: true` |
| SECURITY-09 | エラーハンドリング | ユーザー向けエラー表示はサニタイズ済み。詳細スタックトレースはローカルログのみ |
| SECURITY-10 | 依存パッケージのセキュリティ | CI で `npm audit`。`googleapis` / `keytar` / `better-sqlite3` はバージョン pinning |
| SECURITY-11 | サプライチェーン | Phase 2: macOS notarization + Windows code signing（MVP は dev-signed のみ） |
| SECURITY-12 | データ最小化 | カレンダーイベントのタイトル・本文・参加者名は永続化禁止。`MeetingSummary` 型定義から該当フィールドを排除し、**型システムレベルで担保** |
| SECURITY-13 | 保存時暗号化 | OAuth トークンは OS Keychain（OS レベル暗号化）。SQLite は非暗号化（PII を保存しないため） |
| SECURITY-14 | プライバシー・バイ・デザイン | 「プロダクト憲法」: 外部書き込み API 禁止 / カメラ・スクリーンショット禁止 / チャット状態の自動変更禁止 |
| SECURITY-15 | フェイルセーフ既定値 | 隠身モード検知失敗時 → **隠身モード ON へフォールバック**（ユーザー側に倒す） |

---

## 承認ゲート（Approval Gates）

AIDLC は以下のチェックポイントで**ユーザーの明示的承認**を要求する。承認なしでは次フェーズ／ステージへ進行不可。

| Gate | ステージ | Status | 承認日時 |
|---|---|---|---|
| G1 | Workspace Detection 結果 | ✅ Approved | 2026-05-10T06:35:00Z |
| G2 | Requirements（`requirements.md`） | ✅ Approved | 2026-05-10T07:05:00Z |
| G3 | User Stories（`stories.md`） | ✅ Approved | 2026-05-10T07:25:00Z |
| G4 | Application Design（`application-design.md`） | ✅ Approved | 2026-05-10T07:40:00Z |
| G5 | Workflow Execution Plan（`execution-plan.md`） | ⏳ Pending | — |
| G6 | Code Plan（`code-plan.md`、ユニット単位、**two-step approval**） | ⏳ Pending | — |

**承認の運用ルール**: ユーザーは明示的に「承認」「進めて」「Continue」「OK, proceed」等の言葉で承認を表明する。暗黙的承認は無効。承認のタイムスタンプは確認文言を受領した瞬間を記録する。

---

## ファイル配置（AIDLC 標準化済み）

Inception アーティファクトは AIDLC 標準ディレクトリ構造へ再配置済みである。今後は以下の構成を正本として扱う。

| アーティファクト | 現在のパス |
|---|---|
| Requirements | `aidlc-docs/inception/requirements/requirements.md` |
| User Stories | `aidlc-docs/inception/user-stories/stories.md` |
| Personas | `aidlc-docs/inception/user-stories/personas.md` |
| Application Design | `aidlc-docs/inception/application-design/application-design.md` |
| Components | `aidlc-docs/inception/application-design/components.md` |
| Services | `aidlc-docs/inception/application-design/services.md` |
| Component Dependency | `aidlc-docs/inception/application-design/component-dependency.md` |
| Character Spec | `aidlc-docs/inception/design/character-spec.md` |

現行ファイル構成（今後更新される出力先を含む）:

```
aidlc-docs/
├── aidlc-state.md                                    ← 本ファイル
└── inception/
    ├── plans/
    │   ├── execution-plan.md                         ← Workflow Planning（G5、雛形作成済み）
    │   └── unit-of-work-plan.md                      ← Units Generation（雛形作成済み）
    ├── requirements/
    │   └── requirements.md                           ← 要件分析書の正本
    ├── user-stories/
    │   ├── stories.md                                ← ユーザーストーリーの正本
    │   └── personas.md                               ← ペルソナ抽出済み
    ├── design/
    │   └── character-spec.md                         ← キャラクター原案と状態表現の補助仕様
    └── application-design/
        ├── application-design.md                     ← アプリケーション設計書の正本
        ├── components.md                             ← コンポーネント構成抽出済み
        ├── services.md                               ← サービス責務抽出済み
        ├── component-dependency.md                   ← 依存関係抽出済み
        ├── unit-of-work.md                           ← Units Generation 出力先（雛形作成済み）
        ├── unit-of-work-dependency.md                ← Units Generation 出力先（雛形作成済み）
        └── unit-of-work-story-map.md                 ← Units Generation 出力先（雛形作成済み）
```

---

## 暫定ユニット分解（Units Generation ステージ向けドラフト）

> **状態**: Units Generation ステージでのレビュー用ドラフト。正式アーティファクトではない。本ステージ実行時に確認・精緻化され、本セクションは `unit-of-work.md` に置き換えられる。

PowerPal は単一の Electron アプリだが、Construction を効率化するため以下の独立開発可能なユニットに分解する案を提示する。並列化のために依存関係を明示する。

| Unit ID | 名称 | 責務 | 技術 | 依存 |
|---|---|---|---|---|
| U1 | `battery-engine` | 電池状態、消耗・回復ロジック、tick スケジューラ、触発条件評価 | Node.js（Main）+ TypeScript | （なし） |
| U2 | `activity-tracker` | OS レベルの活動検知（アイドル、前面アプリ、フルスクリーン） | Node.js + `powerMonitor`、OS 別ヘルパー | （なし） |
| U3 | `calendar-adapter` | Google Calendar OAuth、取得、連続会議検知 | Node.js + `googleapis` | （なし） |
| U4 | `persistence` | SQLite スキーマ、リポジトリ、マイグレーション | `better-sqlite3` | （なし） |
| U5 | `notification-scheduler` | rate limit、dedupe、defer 付き通知キュー | Node.js | U1, U2 |
| U6 | `tone-engine` | シーン別台詞テンプレート選択、キャラクタートーンガイド | Node.js（Main / shared types 併用） | （なし） |
| U7 | `ui-character` | 透明・最前面のキャラクターウィンドウ、表情、ドラッグ | React + Electron BrowserWindow | U6 |
| U8 | `ui-modals` | 予備充電 / 抽選 / Quiet 再通知 / 初回セットアップ / Stretch UI | React + IPC | U6, U7 |
| U9 | `ui-settings` | 設定パネルウィンドウ | React | U4 |
| U10 | `ipc-bridge` | Main↔Renderer の IPC 契約（`contextBridge` 経由） | Electron preload | U1, U2, U3, U4, U5 |

**ビルド順の提案**:

1. **Wave 1**（並列）: U1, U2, U3, U4, U6
2. **Wave 2**（並列）: U5（U1, U2 依存）、U10（U1〜U5 依存）
3. **Wave 3**（並列）: U7, U9（U6 / U4 依存）
4. **Wave 4**: U8（U6, U7, U10 依存）
5. **Wave 5**: Build and Test

---

## 引き継がれた未決事項

要件分析書（§11）および設計書（§15）に記載されていた未決事項。MVP 開始前に解決済みのものと、今後解決予定のものに分ける。

### ✅ Resolved（解決済み — 2026-05-10T08:30:00Z）

| ID | 項目 | 決定値 | 派生約束・補足 |
|---|---|---|---|
| TBD-D-03 | 連続会議の最低人数閾値 | **2 名以上** | 1on1 連続（マネージャー → スキップレベル → 部下面談）も検知対象に含める。間隔 ≤10 分の条件で誤検知を抑制する |
| TBD-D-04 | 強制充電モードの 1 日最大発動回数 | **5 回／日** | 追加制約：**各発動間に最低 60 分のクールダウン**を設置。連続 3 回スヌーズ後は当日無効化（US-06-02 既存ルール） |
| TBD-D-05 | 朝の判定における天気要因の重み | 雨 **-3%** ／ 雪 **-5%** ／ 晴・曇 **0** | キャラクターの口実としての flavor を主目的とするため数値は控えめ。MVP では二分判定（雨／非雨）+ 雪を別枠で扱う。気温・湿度等の細粒度判定は実装しない |
| TBD-D-06 | 予備充電通知タイミング | **15 分前**（要件書原値） | 設計書で提案した 20 分前は撤回。緊迫感を保つことが本機能の核心価値であるため。15 分でトイレ（5 分）+ 水（2 分）+ 近場コーヒー（10 分以内）を収める前提 |

これらの決定値は対応する Functional Design（U1, U3, U5）で参照される。2026-05-10 の要件書 / 設計書改訂で、予備充電タイミングと関連数値の整合は反映済み。

### ⏳ Open（今後解決予定）

| ID | 項目 | 解決時期 |
|---|---|---|
| TBD-01 | 跨日記憶を口調に反映する具体ルール | Phase 2（MVP 後） |
| TBD-02 | 多端末同期時のデータスキーマ・暗号化方式 | Phase 2 設計時 |
| TBD-D-01 | キャラクターアートの最終デザイン（現行ドラフト: `aidlc-docs/inception/design/character-spec.md`） | MVP コード生成前 |
| TBD-D-02 | デモ用スクリプト（60-90 秒）の最終文言 | デモ前日 |

---

## ワークフロー不変条件（Project-level Invariants）

以下の不変条件は **Construction 全ステージ**を通じて適用され、各アーティファクト検証ゲートで確認される。違反するアーティファクト／コードはステージ完了を**ブロック**する。

1. **プロダクト憲法（最優先）**: ユーザーの外部関係に干渉する機能・依存・設計を一切許可しない。具体的に以下を禁ずる：
   - Slack / Teams / Discord / Email SDK の `package.json` への追加
   - カレンダーの **書き込み**スコープ（`calendar.readonly` のみ可）
   - カメラ / マイク / スクリーンショット API の使用
   - 自動メッセージ送信・自動返信機能

2. **プライバシー不変条件**: カレンダーイベントのタイトル・本文・参加者名は永続化禁止。型システムでこれを担保（`shared/types/MeetingSummary` インタフェースに該当フィールドを定義しない）。

3. **隠身モード不変条件**: 通知・UI 表示は必ず `DisplayPolicy` を経由する。表示ポリシーを迂回した直接 `Notification` API 呼び出しは禁止。

4. **ユーザーオーバーライド不変条件**: 通知・モーダル・強い休憩提案 UI には必ずユーザーが制御可能な dismiss / mute / postpone 経路を設ける。ESC キーは強い休憩提案 UI を常に解除可能とする。

5. **ローカルファースト不変条件**: ユーザーデータはローカル保存のみ。テレメトリなし。MVP ではクラウド同期なし（Phase 2 のクラウド同期は opt-in + E2E 暗号化必須）。

---

## Next Action

**実行すべきステージ**: `Units Generation`（`inception/units-generation.md`）

**入力として読み込むファイル**:

- `aidlc-docs/inception/requirements/requirements.md`
- `aidlc-docs/inception/user-stories/personas.md`
- `aidlc-docs/inception/user-stories/stories.md`
- `aidlc-docs/inception/application-design/application-design.md`
- `aidlc-docs/inception/design/character-spec.md`
- 本 `aidlc-state.md`（特に「暫定ユニット分解」セクション）

**生成すべき出力**:

- `aidlc-docs/inception/application-design/unit-of-work.md` — 雛形あり。正式なユニット定義で置き換える
- `aidlc-docs/inception/application-design/unit-of-work-dependency.md` — 雛形あり。依存関係マトリクスで置き換える
- `aidlc-docs/inception/application-design/unit-of-work-story-map.md` — 雛形あり。ストーリーとユニットのマッピングで置き換える
- `aidlc-docs/inception/plans/unit-of-work-plan.md` — 雛形あり。`[Answer]:` タグ付きプランで置き換える

**次の承認ゲート**: G5（Workflow Planning 完了時）。Construction 着手前に必須。

**セッション再開時のプロンプトテンプレート**:

```
PowerPal プロジェクトの AIDLC セッションを再開します。
aidlc-docs/aidlc-state.md を読み、最初の非 COMPLETE ステージを特定し、
そこから再開してください。コンテキスト圧縮（context compaction）の
プロンプトが出ても絶対に承認しないでください。
```

---

## 次エージェント向けコンプライアンスチェックリスト

AIDLC 操作を実行する前に、以下を必ず確認すること：

- [ ] 本ファイル（`aidlc-state.md`）を最初に読んだか
- [ ] 共通ルールをロードしたか: `process-overview.md`, `session-continuity.md`, `content-validation.md`, `question-format-guide.md`
- [ ] 拡張ルール状態を上記表で確認したか — `security-baseline` 有効、`property-based-testing` 無効
- [ ] ワークフロー不変条件 5 項目をすべて理解しているか
- [ ] Units Generation 着手前にユーザー確認を取得したか

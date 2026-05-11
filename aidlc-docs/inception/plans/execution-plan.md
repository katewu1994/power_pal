# PowerPal Workflow Execution Plan

**ドキュメント種別**: Workflow Planning Artifact
**ステータス**: G5 Approved
**最終更新**: 2026-05-11
**関連文書**: `../application-design/unit-of-work.md`, `../application-design/unit-of-work-dependency.md`, `../application-design/unit-of-work-story-map.md`, `unit-of-work-plan.md`

---

## 1. 目的

本書は、Units Generation で確定した 10 ユニットを Construction フェーズへ渡すための実行計画である。

- Construction の着手順を固定する
- 各ユニットの成果物、完了条件、検証観点を明示する
- G5 承認対象を明確にする
- G6（ユニット単位の Code Plan 承認）へ進むための境界を定義する

---

## 2. 実行方針

| 方針 | 内容 |
|---|---|
| MVP Core 優先 | `requirements.md` の MVP Core を先に通し、Stretch は Core 完了後に着手する |
| headless first | 判定、通知、永続化、外部連携を UI より先に headless に検証する |
| mock fallback 必須 | Google Calendar 認証なしでもデモ導線が成立する状態を各段階で保つ |
| Renderer 境界固定 | UI は `U10 ipc-bridge` の typed API だけを通じて Main と通信する |
| G5 で Construction 方針を承認 | 本書の承認なしに Construction へ進まない |
| G6 は per-unit | 各ユニットの Code Plan は実装前に個別承認する |

---

## 3. Construction 前提

| ID | 前提 | 根拠 |
|---|---|---|
| A1 | アプリケーションコードはワークスペースルート配下に作る | `aidlc-state.md` の配置ルール |
| A2 | MVP は macOS 先行 | `requirements.md` NFR-11 |
| A3 | Electron + React + TypeScript + Vite を採用 | Application Design 既定 |
| A4 | Calendar は read-only のみ | プロダクト憲法、SECURITY-04/05 |
| A5 | カレンダー詳細は永続化しない | プライバシー不変条件、SECURITY-12 |
| A6 | 強い介入や大きな UI は DisplayPolicy 経由 | 隠身モード不変条件 |
| A7 | Stretch は Core 完了後の追加 wave | Requirements の優先度定義 |

---

## 4. 実行ウェーブ

| Wave | 対象 | 目的 | Exit Criteria |
|---|---|---|---|
| W0 | Bootstrap | Electron/Vite/TypeScript、テスト、ディレクトリ、品質コマンドの土台を作る | `npm` scripts、基本ビルド、型チェック、テストランナーが動く |
| W1 | U1, U2, U3, U4, U6 | domain と外部境界の契約を固定する | 型、repository interface、mock adapter、tone API が単体テスト可能 |
| W2 | U5, U7 | 判定と通知編成を headless に成立させる | 朝判定、予備充電、抽選、Quiet/defer が UI なしで検証できる |
| W3 | U10 | Main と Renderer の境界を固定する | preload API、IPC payload、subscription 契約が型で保護される |
| W4 | U8, U9 | MVP Core の UI surface を接続する | 常駐表示、朝判定、予備充電、抽選、Quiet、設定、初回セットアップが UI から通る |
| W5 | Integration | デモ導線と品質ゲートを通す | 90 秒デモ導線、mock fallback、Quiet 復帰、build/test が成功する |
| W6 | Stretch | 自動提案、強制充電、退勤サマリーを追加する | Core の安定性を壊さず ST-01/ST-02/ST-03 が通る |

---

## 5. ユニット別実行計画

| Unit | Wave | 主成果物 | 主要検証 | G6 Code Plan の焦点 |
|---|---|---|---|---|
| W0 `bootstrap` | W0 | `package.json`, Electron/Vite scaffold, test setup, folder skeleton | dev/build/typecheck/test が空構成で通る | 依存追加、scripts、セキュア Electron default |
| U1 `energy-domain` | W1 | battery 型、engine、回復テーブル、tier 判定 | clamp、無操作時非消耗、回復量、手動補正 | 電池計算の deterministic 化と境界値 |
| U2 `activity-visibility` | W1 | signal interface、idle/active counter、DisplayPolicy | manual override、quiet fallback、連続作業閾値 | OS 依存を薄く閉じ込める設計 |
| U3 `calendar-context` | W1 | Calendar adapter、mock adapter、MeetingSummary、continuous meeting detection | read-only scope、自己のみ/終日除外、mock fallback | OAuth と privacy-preserving event mapping |
| U4 `local-data-platform` | W1 | SQLite schema、repositories、migrations、TokenStore interface | 再起動復元、migration idempotency、非保存項目 | PII を保存しない schema と token 分離 |
| U6 `tone-engine` | W1 | tone templates、scene APIs、口調ルール | 分析的説明に寄りすぎない、scene ごとの文言 | domain 判定と文言の分離 |
| U5 `decisioning-orchestrator` | W2 | MorningJudge、PreChargePlanner、Lottery、ForceChargeEvaluator、SummaryAggregator | 朝判定、15 分前提案、再抽選上限、強制充電条件 | semantic view model と UI 非依存性 |
| U7 `intervention-scheduler` | W2 | notification queue、rate limit、dedupe、defer、response logging | 1h/2回、90m/1回、30m dedupe、defer 1 件 | DisplayPolicy 強制経由と再通知制御 |
| U10 `ipc-bridge` | W3 | preload API、IPC handlers、shared payload types | Node API 非公開、payload validation、subscription cleanup | contextIsolation と typed boundary |
| U8 `character-shell-ui` | W4 | character window、tray、drag、小型化、battery expression | Renderer 再判定なし、position persistence、軽量表示 | window lifecycle と view model 表示 |
| U9 `interaction-ui` | W4 | cards、modals、settings、onboarding | keyboard 操作、ESC、settings 反映、mock 導線 | Core UI surface と action payload |

---

## 6. Construction ステージ運用

各ユニットは以下の順序で進める。

| Step | 成果物 | 完了条件 |
|---|---|---|
| S1 Functional Design | ユニット別 `functional-design.md` | 入出力、状態遷移、例外、story map との関係が明確 |
| S2 NFR Requirements | ユニット別 `nfr-requirements.md` | security、privacy、performance、accessibility の該当要件が割り当て済み |
| S3 NFR Design | ユニット別 `nfr-design.md` | NFR を満たす具体設計とテスト観点がある |
| S4 Infrastructure Design | ユニット別 `infrastructure-design.md` | ファイル配置、依存パッケージ、adapter/mock 方針が確定 |
| S5 Code Plan | ユニット別 `code-plan.md` | 実装手順、ファイル単位、テスト計画があり、G6 承認対象になる |
| S6 Code Generation | コード、テスト、更新済み docs | G6 承認後に実装し、ユニット単位の検証が通る |

---

## 7. MVP Core 成立条件

MVP Core は以下を満たした時点で完了扱いにする。

| ID | 条件 | 対象ユニット |
|---|---|---|
| MC-01 | 電池キャラクターが常駐表示され、残量と tier が変化する | U1, U8, U10 |
| MC-02 | 初回起動時に勤務時間、Calendar 接続/スキップ、人前モード説明を通過できる | U3, U4, U9, U10 |
| MC-03 | 朝の初期判定が表示され、手動修正が保存される | U1, U3, U4, U5, U6, U8, U10 |
| MC-04 | `充電する` から抽選、再抽選、自由入力 fallback まで通る | U1, U4, U5, U6, U9, U10 |
| MC-05 | 連続会議ブロックの 15 分前に予備充電を提案できる | U3, U5, U6, U7, U9, U10 |
| MC-06 | Quiet/Stealth 中は大きな UI が抑制され、解除後に 1 件だけ控えめに戻る | U2, U6, U7, U8, U9, U10 |
| MC-07 | Google Calendar 認証なしでも mock モードで DS-02 のデモが成立する | U3, U5, U7, U8, U9, U10 |

---

## 8. Stretch 着手条件

Stretch は以下をすべて満たした後に開始する。

- MVP Core 成立条件 MC-01 から MC-07 が通っている
- build、typecheck、unit tests が成功している
- mock デモ導線が 90 秒以内に破綻しない
- Quiet/Stealth の抑制漏れがない
- G6 の追加 Code Plan が承認されている

Stretch の対象は `US-05-01`, `US-05-02`, `US-06-01`, `US-06-02`, `US-08-01`, `US-08-02` とする。

---

## 9. 検証計画

| レベル | 対象 | 方針 |
|---|---|---|
| Unit | U1, U5, U7 などの pure/domain logic | 境界値、rate limit、dedupe、抽選上限、判定条件を自動テスト |
| Adapter | U2, U3, U4 | OS/API/DB 依存を interface 越しに mock して検証 |
| Contract | U10 | IPC payload と preload API を型と runtime guard で確認 |
| UI | U8, U9 | React component と interaction flow を確認 |
| Integration | W5 | mock Calendar で朝判定、予備充電、Quiet 復帰、抽選を通す |
| Manual Demo | W5 | 60-90 秒のデモシナリオを実機で確認 |

---

## 10. NFR 割当

| NFR / Rule | 主担当 | 補助 |
|---|---|---|
| 外部関係へ介入しない | U3, U5, U7 | U10 |
| カレンダー詳細非保存 | U3, U4 | U10 |
| OAuth read-only | U3 | U4 |
| Renderer 分離 | U10 | U8, U9 |
| Quiet/Stealth 優先 | U2, U7 | U8, U9 |
| ユーザー解除導線 | U7, U9 | U10 |
| ローカルファースト | U4 | U3, U5 |
| 軽量性 | U2, U7, U8 | W5 |
| アクセシビリティ | U9 | U8 |
| 文言のキャラクター性 | U6 | U5, U7 |

---

## 11. 承認ゲート

| Gate | タイミング | 承認対象 | 未承認時の扱い |
|---|---|---|---|
| G5 | 本書レビュー後 | Construction の実行順、MVP Core 範囲、G6 運用、Stretch 着手条件 | Construction へ進まない |
| G6 | 各ユニットの Code Plan 完了後 | ファイル単位の実装計画、テスト計画、依存追加 | 該当ユニットの Code Generation へ進まない |

---

## 12. G5 レビュー観点

G5 で確認すべき事項は以下。

- MVP Core を W0-W5 で先に通す方針でよいか
- W0 bootstrap を Construction の最初に置く方針でよいか
- U5/U6/U7 を「判定」「文言」「通知編成」に分ける方針でよいか
- Stretch を Core 完了後に限定する方針でよいか
- mock fallback をデモ成立条件として必須にする方針でよいか
- G6 を unit ごとに実施する運用でよいか

---

## 13. 未決事項

| ID | 項目 | ブロック対象 | 対応方針 |
|---|---|---|---|
| TBD-D-01 | キャラクターアートの最終デザイン | U8 の最終 visual polish | MVP 実装は SVG ベースで進め、最終差し替え可能にする |
| TBD-D-02 | デモ用スクリプト最終文言 | W5 Manual Demo | W5 で 60-90 秒台本として確定する |
| TBD-01 | 跨日記憶の口調反映ルール | Phase 2 | MVP ではデータ蓄積のみ |
| TBD-02 | 多端末同期方式 | Phase 2 | MVP では対象外 |

---

## 14. Workflow Planning 完了

本書は 2026-05-11T10:44:37Z に G5 承認済み。

Workflow Planning は完了し、次は Construction の W0 bootstrap へ進む。

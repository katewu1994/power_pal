# PowerPal Unit of Work Story Map

**ドキュメント種別**: Story-to-Unit Mapping
**ステータス**: Finalized for Workflow Planning
**最終更新**: 2026-05-11
**関連文書**: `unit-of-work.md`, `../user-stories/stories.md`

---

## 1. 目的

本書は、ユーザーストーリーとユニットの関係を**対応表としてのみ**整理する。

- user story は「誰に、どんな価値を、どう受け入れるか」
- unit of work は「どの技術責務が、その価値のどこを担うか」
- したがって、各ストーリーをユニットへ**丸ごと写経しない**

---

## 2. マッピング方針

| 項目 | 方針 |
|---|---|
| `Primary Unit` | そのストーリーの中核責務を持つユニット |
| `Supporting Units` | 入力、永続化、文言、UI、境界を支えるユニット |
| many-to-many | 1 つのストーリーが複数ユニットにまたがること、1 つのユニットが複数ストーリーを支えることを許可する |
| platform unit | U4 と U10 は単独のユーザー価値ではなく、複数ストーリーの基盤として現れる |

---

## 3. ユニット側サマリー

| Unit | 主に支えるストーリー群 |
|---|---|
| U1 `energy-domain` | 朝判定手動修正、充電実行、強い介入判定、退勤集計 |
| U2 `activity-visibility` | Quiet/Stealth、人前モード、連続作業トリガ |
| U3 `calendar-context` | 朝判定、予備充電、会議後提案、初回セットアップの接続状態 |
| U4 `local-data-platform` | 設定保持、ログ保存、再通知履歴、跨日記憶の土台 |
| U5 `decisioning-orchestrator` | 朝判定、予備充電計画、抽選、強い介入、退勤集計 |
| U6 `tone-engine` | 朝判定、予備充電、抽選、Quiet 復帰、退勤コメント、Phase 2 の口調変化 |
| U7 `intervention-scheduler` | 予備充電通知、自動提案、強い介入、Quiet 復帰 |
| U8 `character-shell-ui` | 常駐キャラクター、小型化、残量表示 |
| U9 `interaction-ui` | モーダル、バナー、設定、オンボーディング、サマリー |
| U10 `ipc-bridge` | すべての Renderer 表示系ストーリー |

---

## 4. MVP Core ストーリーマップ

| Story ID | Primary Unit | Supporting Units | マッピング理由 |
|---|---|---|---|
| US-01-01 | U8 | U10 | 常駐キャラクターは shell UI の責務であり、Main との接続は IPC が支える |
| US-01-02 | U8 | U1, U10 | 一目で分かる残量表示は UI 表現だが、値の正しさは battery state に依存する |
| US-01-03 | U8 | U4, U10 | 小型化は shell UI の振る舞いで、ユーザー選好の保持は persistence が担う |
| US-02-01 | U5 | U1, U3, U4, U6, U10, U8 | 朝判定は decisioning が中核で、入力集約、文言、表示が周辺を構成する |
| US-02-02 | U1 | U4, U6, U10, U8 | 手動修正の中核は battery state 更新であり、履歴保存とリアクション表示が付随する |
| US-03-01 | U7 | U3, U5, U6, U9, U10 | 予備充電通知は scheduler が出し分けを担い、会議検知と提案内容が upstream となる |
| US-03-02 | U9 | U1, U4, U5, U10 | 実行フローは interaction UI が受け持ち、回復反映とログ保存が backend 側責務になる |
| US-03-03 | U7 | U4, U9, U10 | 見送りと再通知は scheduler の責務で、UI は操作入口、DB は履歴保存を担う |
| US-04-01 | U5 | U4, U6, U9, U10 | 抽選の本質は decisioning であり、UI は結果提示と操作受付のみ行う |
| US-04-02 | U5 | U4, U9, U10 | 再抽選回数制御は domain ルールであり、保存と UI 更新が周辺責務になる |
| US-04-03 | U9 | U4, U6, U10 | 自由入力は UI 導線が主で、ログ化とリアクション文言が支援責務になる |
| US-07-01 | U7 | U2, U8, U9, U10 | 人前で静かにするのは scheduler/policy の責務で、signal 検知と UI 反映が支える |
| US-07-02 | U7 | U6, U9, U10 | 抑制通知の復帰制御は scheduler が持ち、控えめな表現は tone と UI が担う |
| US-09-01 | U9 | U2, U4, U10 | 設定変更の入口は settings UI で、実際の閾値適用と保持は backend 側が担う |
| US-09-02 | U9 | U4, U7, U10 | 通知強度の設定は UI 起点だが、影響先は scheduler と persistence である |
| US-09-03 | U9 | U2, U7, U10 | 手動人前モードは UI から指示し、signal と表示制御へ反映させる |
| US-09-04 | U9 | U3, U4, U10 | オンボーディングは interaction UI が主、接続状態と設定保存が支援する |

---

## 5. Stretch ストーリーマップ

| Story ID | Primary Unit | Supporting Units | マッピング理由 |
|---|---|---|---|
| US-05-01 | U7 | U2, U5, U6, U9, U10 | 連続作業後の提案は scheduler が出すが、トリガ判定と文言は upstream に分かれる |
| US-05-02 | U7 | U3, U5, U6, U9, U10 | 会議後提案は calendar context と decisioning を受けて scheduler が通知面を担う |
| US-06-01 | U7 | U1, U2, U5, U6, U9, U10 | 強い介入は scheduler が最終責務を持ち、発動条件は battery と signal と decisioning に分散する |
| US-06-02 | U7 | U4, U9, U10 | 再通知制御は scheduler の責務で、UI は操作、DB は履歴保持を担う |
| US-08-01 | U9 | U1, U4, U5, U6, U10 | 退勤サマリーは interaction UI が表示面を持ち、集計とコメント生成が backend から供給される |
| US-08-02 | U9 | U4, U10 | 閉じる挙動は UI 責務で、確定保存が persistence 側責務になる |

---

## 6. Phase 2 ストーリーマップ

| Story ID | Primary Unit | Supporting Units | マッピング理由 |
|---|---|---|---|
| US-10-01 | U6 | U4, U5, U10, U8, U9 | 跨日記憶の表出は tone 側が主で、履歴保持と表示タイミングが周辺責務となる |
| US-11-01 | U9 | U4, U10 | 週次振り返りは UI 中心の機能で、履歴集計基盤が支える |
| US-12-01 | U9 | U4, U5, U10 | カスタムメニュー編集は UI と保存が主で、抽選ロジックへの反映が decisioning に入る |

---

## 7. 補足

- U4 `local-data-platform` と U10 `ipc-bridge` は、ほぼすべての UI ストーリーに支援ユニットとして登場する
- U5 `decisioning-orchestrator` は「何を提案するか」を持ち、U7 `intervention-scheduler` は「いつ・どの強さで出すか」を持つ
- この分離により、ストーリーをそのままユニットへ変換せずに、責務ごとの変更影響範囲を小さく保てる

# PowerPal

AWS Summit Japan 2026 AI-DLC ハッカソン 参加作品

- Team: サボりマスター
- Product: PowerPal
- Status: Hackathon MVP の企画・設計ドキュメントを収録中

> 休む判断まで、手放そう。退勤時に、まだ少し余力を残すために。

PowerPal は、会議密度や作業状況からデスクワーカーの消耗を推定し、休憩の判断と充電行動の提案を電池キャラクターが肩代わりするデスクトップ常駐アシスタントです。

一般的な休憩リマインダーのように自己管理を求めるのではなく、「休むかどうか」「何をして休むか」「今どれくらい消耗しているか」を、キャラクターに少し越権的に委ねる体験をつくります。

## 1 Minute Overview

- 連続会議の前に、消耗を予測して先回りで「予備充電」を提案します。
- 休憩内容はユーザーに考えさせず、電池君がその場で充電メニューを抽選します。
- 会議中や画面共有中は静かに隠れ、低電量時だけ少し強めに介入します。

## Demo

1. 朝の初期電池判定
   当日の予定や前日の状態を見て、その日の残量をひと目で伝える。
2. 連続会議前の予備充電
   会議が詰まる前に、短い回復行動を先回りで提案する。
3. 考えなくていい充電抽選
   ユーザーが迷う前に、その場で実行しやすい充電メニューを決める。
4. 強制充電モードと隠身モード
   本当に消耗しているときだけ強めに促し、人前では邪魔をしない。

## Boundary

- ユーザー本人の身体と注意力には越権的に関わる。
- 会議・同僚・上司・顧客など、職場の人間関係には介入しない。
- カレンダー連携は read-only 前提で、個人データはローカルファーストで扱う。

## Repository

- 現在のリポジトリは、Hackathon MVP Core / Stretch の要件整理と設計資料を中心に構成しています。
- 初見の方は、要件 -> サービス設計 -> ユニット分解 -> 実行計画の順で読むと全体像を追えます。

## Docs

1. [要件分析](aidlc-docs/inception/requirements/requirements.md)
2. [サービス設計](aidlc-docs/inception/application-design/services.md)
3. [ユニット分解](aidlc-docs/inception/application-design/unit-of-work.md)
4. [実行計画](aidlc-docs/inception/plans/execution-plan.md)
5. [キャラクター素材](aidlc-docs/assets/character)

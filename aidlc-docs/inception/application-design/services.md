# PowerPal サービス設計

**ドキュメント種別**: Service Responsibilities
**バージョン**: 1.1
**最終更新**: 2026-05-10
**関連文書**: `application-design.md`, `components.md`

---

## 1. 目的

PowerPal の主要サービス責務を、インタフェース中心に整理する。

---

## 2. 主要サービス

### 2.1 BatteryEngine

```typescript
interface BatteryEngine {
  getCurrent(): BatterySnapshot;
  tick(input: TickContext): BatterySnapshot;
  recover(action: ChargeAction): BatterySnapshot;
  adjustManual(value: number): BatterySnapshot;
}
```

責務:

- アクティブ作業分のみ電池を減算
- 回復テーブルに従って電池を加算
- tier 変化を通知

### 2.2 MorningJudge

```typescript
interface MorningJudge {
  evaluate(input: MorningInput): MorningResult;
}
```

責務:

- 前日履歴、当日予定、天気を使って初期電池を決定
- 数値は deterministic、文言だけをバリエーション化

### 2.3 CalendarAdapter

```typescript
interface CalendarAdapter {
  authenticate(): Promise<void>;
  getEvents(from: Date, to: Date): Promise<MeetingSummary[]>;
  detectContinuousMeetings(events: MeetingSummary[]): ContinuousMeetingBlock[];
}
```

責務:

- Google Calendar read-only 取得
- `MeetingSummary` へ変換し、生イベントを永続化しない
- mock へのフォールバックを提供

### 2.4 VisibilityDetector

```typescript
interface VisibilityDetector {
  getSignals(): Promise<VisibilitySignals>;
}
```

責務:

- `manualHumanMode`
- `meetingAppForeground`
- `fullscreenPresentation`
- `cameraInUse`

の各シグナルを返す。判定ロジック自体は持たない。

### 2.5 DisplayPolicy

```typescript
interface DisplayPolicy {
  getMode(signals: VisibilitySignals): DisplayMode;
  allow(surface: SurfaceKind, mode: DisplayMode): boolean;
}
```

責務:

- `normal / quiet / stealth` の 3 状態へ射影
- シグナル不確実時は `quiet` に倒す
- 全通知停止ではなく、強い surface だけ抑制する

### 2.6 NotificationScheduler

```typescript
interface NotificationScheduler {
  enqueue(notification: PendingNotification): EnqueueResult;
  markResponded(id: string, response: UserResponse): void;
  flushDeferred(mode: DisplayMode): PendingNotification[];
}
```

責務:

- rate limit
- dedupe
- defer 1 件保持
- response logging

### 2.7 ToneEngine

```typescript
interface ToneEngine {
  morningLine(input: MorningInput, battery: number): string;
  preChargeLine(context: PreChargeContext): string;
  lotteryLine(result: ChargeMenu): string;
  quietRecoveryLine(notification: PendingNotification): string;
}
```

責務:

- Main 側で完成済み文言を生成
- 口調と表示強度の矛盾を防ぐ

---

## 3. 依存関係の原則

- `BatteryEngine` は UI へ依存しない
- `ToneEngine` は Renderer に置かない
- `NotificationScheduler` は `DisplayPolicy` を経由せずに表示しない
- `CalendarAdapter` は read-only API のみを公開する

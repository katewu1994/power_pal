# PowerPal コンポーネント依存関係

**ドキュメント種別**: Component Dependency Overview
**バージョン**: 1.1
**最終更新**: 2026-05-10
**関連文書**: `application-design.md`, `components.md`, `services.md`

---

## 1. 依存関係マップ

```text
CalendarAdapter ───────┐
WeatherAdapter ────────┤
                       ▼
                 MorningJudge ───────┐
                                     │
ActivityTracker ───────┐             │
VisibilityDetector ────┼─> DisplayPolicy ──┐
SettingsRepo ──────────┘                   │
                                           ▼
BatteryEngine ───────────┐          NotificationScheduler ──> IPC / Renderer
ChargeMenuRepo ──────────┼─> Lottery ────────────────────────┘
MeetingBlockRepo ────────┘

ToneEngine <──────── MorningJudge / Lottery / PreChargePlanner / NotificationScheduler
Persistence <────── all Main domain services
```

---

## 2. 依存原則

- Renderer は Main domain へ直接依存しない
- ToneEngine は `MorningJudge` や `PreChargePlanner` から利用されるが、逆依存しない
- NotificationScheduler は `DisplayPolicy` の判定なしに表示しない
- VisibilityDetector は signal provider、DisplayPolicy は decision maker として分離する

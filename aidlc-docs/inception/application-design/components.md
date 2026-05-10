# PowerPal コンポーネント構成

**ドキュメント種別**: Component Structure
**バージョン**: 1.1
**最終更新**: 2026-05-10
**関連文書**: `application-design.md`, `services.md`, `component-dependency.md`

---

## 1. 目的

PowerPal の主要コンポーネント配置を、実装単位として把握しやすい形で要約する。

---

## 2. ディレクトリ構造

```text
powerpal/
├── electron/
│   ├── main/
│   │   ├── index.ts
│   │   ├── window-manager.ts
│   │   ├── tray.ts
│   │   ├── ipc/
│   │   ├── domain/
│   │   │   ├── battery/
│   │   │   ├── morning/
│   │   │   ├── lottery/
│   │   │   ├── precharge/
│   │   │   ├── tone/
│   │   │   ├── visibility/
│   │   │   └── notification/
│   │   ├── adapter/
│   │   ├── persistence/
│   │   └── security/
│   └── preload/
│       └── index.ts
├── src/
│   ├── components/
│   │   ├── character/
│   │   ├── cards/
│   │   ├── modal/
│   │   └── settings/
│   ├── stores/
│   ├── hooks/
│   └── theme/
└── shared/
    └── types/
```

---

## 3. コンポーネント方針

### 3.1 Main 側

- `domain/battery`: 電池計算
- `domain/morning`: 朝の初期判定
- `domain/lottery`: 充電抽選
- `domain/precharge`: 連続会議前の提案計画
- `domain/tone`: 文言生成
- `domain/visibility`: 人前シグナル検知と表示モード判定
- `domain/notification`: 節流・重複排除・defer

### 3.2 Renderer 側

- `character`: 電池君の見た目
- `cards`: 朝判定、予備充電、Quiet 再通知、強い休憩提案
- `modal`: 抽選、サマリー、初回セットアップ
- `settings`: 閾値、人前モード、通知強度

### 3.3 Shared

- `shared/types`: IPC payload、設定、通知、電池状態

---

## 4. 設計上の注意

- `tone` は Renderer に置かない
- Renderer は Main から渡された完成済み文言を表示する
- 人前検知は `visibility detector` と `display policy` に分離する

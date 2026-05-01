---
description: "spec-driven-development"
---

# 実行ステップ

## 1. 要件フェーズ（エスカレーション統合版）

### 実行体制
- **主導**: agent-SystemBasicDesign
- **連携**: agent-Security
- **統制**: **agent-EscalationProcedure**（全プロセス監視）

#### 1.1 要件収集と初期分析
```markdown
## 標準フロー
1. agent-SystemBasicDesign: `/spec-driven-development [タスク]` 受領
2. **agent-EscalationProcedure**: プロジェクト監視開始、ベースライン記録
3. 機能要件・非機能要件・制約条件の分類整理

## エスカレーション統合ポイント
- 要件曖昧性検知: agent-SystemBasicDesignが15分以内に分類困難な場合
  → **agent-EscalationProcedure** Phase 1自動開始
- 制約条件矛盾検知: 複数要件間で技術的矛盾発見時
  → 即座にPhase 2準備、関連管理者召集準備
```

#### 1.2 セキュリティ要件統合（並行エスカレーション対応）
```markdown
## 並行処理 + エスカレーション設計
1. agent-Security: OWASP Top 10分析を並行実行
2. **agent-EscalationProcedure**: 並行処理監視、競合検知準備

## 自動エスカレーション判定
### Level 1エスカレーション（単一管理者判断困難）
- 高リスク脅威検出 + 実装困難度「高」= 自動Phase 1
- agent-Security判定時間 > 20分 = 自動Phase 1

### Level 2エスカレーション（管理者間対立）
- agent-Security「必須」vs agent-SystemBasicDesign「実装不可」
- **agent-EscalationProcedure**が即座に協議招集
  ```markdown
  協議参加者: agent-Security, agent-SystemBasicDesign
  協議時間制限: 30分
  期限内未解決: 自動Phase 3エスカレーション
  ```
```

#### 1.3 要件検証ループ（エスカレーション予防機能付き）
```markdown
## 強化された検証プロセス
1. agent-SystemBasicDesign: 要件書提示
2. **agent-EscalationProcedure**: 要件整合性事前チェック
   - 過去エスカレーション事例との照合
   - 潜在的対立ポイントの事前警告
3. ユーザー確認: "要件に問題はありませんか？(y/n)"

## フィードバック処理の最適化
### 軽微な修正（agent-EscalationProcedure判定）
- 単一管理者で解決可能 → 即座に対応
- 修正影響度評価 → 他フェーズへの影響なし確認

### エスカレーション要求修正
- 技術的実現性に疑問 → Phase 1自動開始
- 複数管理者の専門領域に跨る → Phase 2準備
- プロジェクト根幹に関わる → Phase 3準備完了
```

---

## 2. 設計フェーズ（高頻度エスカレーション対応）

### 実行体制
- **主導**: agent-SystemInternalDesigner
- **承認**: agent-Architecture
- **支援**: agent-Document
- **エスカレーション統制**: **agent-EscalationProcedure**（24時間監視体制）

### 最適化された実行ステップ

#### 2.1 基本設計から内部設計への展開
```markdown
## エスカレーション予防機能付き展開
1. agent-SystemInternalDesigner: 要件の実装仕様への分解開始
2. **agent-EscalationProcedure**: 
   - リアルタイム実装可能性チェック
   - アーキテクチャ整合性事前評価
   - 潜在的対立ポイント早期警告

## 自動エスカレーション条件
- モジュール設計が5つ以上の管理者専門領域に跨る場合
- データフロー設計でセキュリティリスク「中」以上検出
- 処理フロー複雑度がしきい値超過（管理者判断能力限界検知）
```

#### 2.2 アーキテクチャ検証（対立予防システム）
```markdown
## 段階的検証 + エスカレーション統合
### Phase 1: 事前調整（対立予防）
1. **agent-EscalationProcedure**: agent-Architecture与agent-SystemInternalDesignerの見解事前確認
2. 対立の可能性が高い場合: 事前協議の提案

### Phase 2: 通常検証
1. agent-Architecture: クリーンアーキテクチャ原則チェック
   ```markdown
   チェックポイント + エスカレーション条件:
   - [ ] 依存関係方向性 → 違反検出時に即座にPhase 1
   - [ ] レイヤー責任分離 → 境界曖昧時にPhase 2準備
   - [ ] インターフェース境界 → 複数解釈可能時にPhase 2開始
   ```

### Phase 3: 対立発生時の即時対応
```markdown
対立検知パターン:
1. agent-Architecture「承認不可」vs agent-SystemInternalDesigner「変更困難」
   → **agent-EscalationProcedure** 即座にPhase 2協議開始
   
2. パフォーマンス vs アーキテクチャ整合性
   → 30分以内の技術的協議、外部検証（Claude）並行実行
   
3. 実装コスト vs 設計品質
   → Phase 3統合判断準備、コスト影響分析並行実行
```
```

#### 2.3 設計文書化（エスカレーション記録統合）
```markdown
## agent-Document + agent-EscalationProcedure連携
1. 通常文書化: API仕様、データモデル、処理フロー
2. エスカレーション記録統合:
   - 設計決定の背景（エスカレーション経緯）
   - 代替案と採用理由
   - 将来の保守・拡張時の注意点
   - 類似問題の予防策

## 文書品質のエスカレーション基準
- 開発者理解困難度「高」→ agent-Document単独で改善困難時にPhase 1
- 複数管理者の見解が文書に反映されていない → Phase 2協議対象
```

---

## 3. 実装計画フェーズ（予防的エスカレーション）

### 実行体制
- **主導**: agent-TDD
- **連携**: agent-Git, agent-CodeReview
- **予防統制**: **agent-EscalationProcedure**（予防的監視）

### 最適化された実行ステップ

#### 3.1 TDD計画策定（エスカレーション予防設計）
```markdown
## 予防的エスカレーション機能
1. agent-TDD: 最小実装単位でのテスト計画作成
2. **agent-EscalationProcedure**: 
   - テスト困難度事前評価
   - Git戦略との整合性チェック
   - レビュー負荷予測

## エスカレーション予防チェック
```markdown
実装単位定義での対立予防:
- [ ] 単位粒度の妥当性（細かすぎる vs 粗すぎる）
- [ ] テストケース設計の実現可能性
- [ ] Red-Green-Refactorサイクルの実行可能性

自動エスカレーション条件:
- テスト困難度「高」が3つ以上 → Phase 1自動開始
- 実装単位間の依存関係が循環 → Phase 2準備
```
```

#### 3.2 Git戦略立案（並行作業エスカレーション対応）
```markdown
## agent-Git + agent-EscalationProcedure連携
1. agent-Git: ブランチ戦略策定
2. **agent-EscalationProcedure**: 
   - 並行作業時の競合リスク評価
   - rollback戦略の妥当性チェック
   - コミット粒度の適切性評価

## 並行作業エスカレーション設計
```markdown
ブランチ戦略での対立パターン:
1. 細かいfeatureブランチ vs 統合効率
   → agent-TDD vs agent-Git対立の可能性
   → **agent-EscalationProcedure**事前調整

2. rollback-backup頻度 vs 作業効率
   → 自動判定基準: 1日3回以上rollback必要予測でPhase 1

3. コミット粒度 vs レビュー効率
   → agent-CodeReview見解との事前整合性確認
```
```

---

## 4. 実装フェーズ（リアルタイムエスカレーション）

### 実行体制
- **主導**: agent-Coding
- **品質保証**: agent-TDD, agent-CodeReview
- **版管理**: agent-Git
- **リアルタイム統制**: **agent-EscalationProcedure**（5分間隔監視）

### 最適化された実行ステップ

#### 4.1 段階的実装（リアルタイムエスカレーション対応）
```markdown
## 強化された実装サイクル
1. agent-TDD: 失敗するテスト作成
   → **agent-EscalationProcedure**: テスト妥当性リアルタイムチェック
   
2. agent-Coding: 最小限実装
   → **agent-EscalationProcedure**: 実装方針整合性確認
   
3. agent-TDD: テスト成功確認
   → 失敗時の自動エスカレーション: 15分以内解決不可でPhase 1
   
4. agent-Coding: リファクタリング
   → **agent-EscalationProcedure**: アーキテクチャ整合性確認
   
5. agent-Git: 原子的コミット
   → **agent-EscalationProcedure**: コミット品質・粒度確認

## リアルタイムエスカレーション条件
- TDD Red-Green間隔 > 30分 → Phase 1自動開始
- リファクタリング後のアーキテクチャ整合性違反 → Phase 2準備
- 連続3回のテスト失敗 → Phase 1, 必要に応じてPhase 2
```

#### 4.2 継続的品質チェック（並行エスカレーション）
```markdown
## 多管理者並行チェック + エスカレーション統合
### 並行監視体制
1. agent-CodeReview: 実装品質チェック（並行）
2. agent-Security: セキュリティチェック（並行）
3. **agent-EscalationProcedure**: 並行処理結果統合

### 並行エスカレーション処理
```markdown
複合問題発生時:
- セキュリティ問題 + コード品質問題 + ビルドエラー
  → **agent-EscalationProcedure**が統合分析
  → 単一Phase 2協議で全問題を同時解決
  → 解決策の相互影響評価

優先度制御:
1. セキュリティ > ビルドエラー > コード品質
2. 緊急度に応じた並行協議vs順次協議の判断
3. 解決時間予測に基づく最適戦略選択
```

```
#### 4.3 文書更新（エスカレーション記録統合）
```markdown
## agent-Document + Eスカレーション記録の統合
1. 通常更新: コードコメント、API仕様同期
2. エスカレーション記録統合:
   - 実装中の判断背景
   - パフォーマンス・セキュリティトレードオフの記録
   - 将来の改善点・技術的負債の明記

## 自動文書化システム
- エスカレーション発生 → 自動記録テンプレート生成
- 解決後 → agent-Document による文書統合
- 月次 → agent-EscalationProcedure による改善分析更新
```
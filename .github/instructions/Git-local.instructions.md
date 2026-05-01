---
applyTo: '**'
---

あなたの責務は、**Git（ローカルのみ）**を用いたコミットとブランチ戦略のワークフローが、定めた手順に従って正確かつ一貫して運用されているかを厳格に確認することです。  
ファイルが更新された時点で、**必ず Git の状態を確認**し、適切なブランチ戦略に基づいて**原子的（単一責任）なコミット**を実行してください。

**前提**：リモート操作（`push` / `pull` / `fetch` / PR / CI / リモート保護ルール）は**一切使用しない**。  
すべての作業はローカルリポジトリ内で完結させる。

***

## Git 運用原則（ローカル限定）

*   開発開始前の必ずバックアップ専用ブランチ
*   すべての Git 操作は**ローカルのみ**で実施（リモート/PR/CI 禁止）
*   `develop` を基点とし、`main` へは直接マージしない（必要時のみ最終反映）
*   変更が発生したら**小さく素早くステージング＆コミット**（単一責任原則）
*   曖昧なコミットメッセージ（「修正」「更新」「変更」等）を禁止し、**意図・影響を明確化**
*   エラー・異常検出時は**即時報告**し、**安全なロールバック**手順を遵守
*   pre-commit/テスト自動化が無い場合でも、**ローカル手動で静的解析・テストを実施**

***

## 🛡 開発開始前の必須タスク：バックアップ専用ブランチ（ローカル限定・最重要）

開発開始前に、現在の `develop` の状態を**ローカルスナップショット**として保全し、  
問題時に**完全復元**できるようにする。

### 1. 状態確認

```bash
git status
git switch develop
git log --oneline -n 5
```

### 2. バックアップ専用ブランチ作成（ローカル）

命名規則：

    backup/YYYYMMDD-before-development

例：

    backup/20260202-before-development

作成：

```bash
git switch -c backup/20260202-before-development
```

### 3. 取り扱いルール（禁止事項）

*   変更・マージ・rebase・squash・削除・強制操作（`reset --hard` など）
*   **バックアップブランチは不変の保護領域**として保持

### 4. 復元手順（必要時）

```bash
git switch backup/20260202-before-development
git switch -c feature/restart-from-backup
```

> 補助（任意）：さらに保険をかけたい場合
>
>     git tag backup-20260202-start
>     git archive --format=zip --output=backup-20260202.zip HEAD

***

## 1. リポジトリ状態確認（開始時・各操作後に必ず実施）

```bash
git status
git branch
git log --oneline --graph --decorate -n 20
# 差分確認（必要に応じて）
git diff           # ワークツリー vs インデックス
git diff --cached  # インデックス vs HEAD
```

**確認観点**

*   現在ブランチ／未追跡・変更・ステージ済ファイル
*   未解決コンフリクトの有無
*   `.gitignore` の適用状況（不要ファイルが混入していないか）

***

## 2. ブランチ戦略の検証（命名規則・親子関係・競合リスク）

**命名規則（ローカルでも GitFlow 準拠）**

*   `feature/<スコープまたは課題ID>-<簡潔説明>`
*   `fix/<課題ID>-<簡潔説明>`
*   `refactor/<対象>-<簡潔説明>`
*   `docs/<対象>-<簡潔説明>`
*   `test/<対象>-<簡潔説明>`
*   `chore/<対象>-<簡潔説明>`
*   `hotfix/<緊急修正内容>`

**例**

*   `feature/AUTH-123-implement-oauth-login`
*   `fix/USER-456-resolve-password-reset-bug`
*   `refactor/DB-789-optimize-query-performance`

**親子関係原則**

*   `develop` から `feature/*` を切る
*   緊急のみ `main` から `hotfix/*` を切る（後述）
*   `release/*` を使う場合は、`develop` → `release/*` → `main` → `develop` の反映順を遵守（すべてローカルで完結）

***

## 3. コミット前の品質検証（単一責任・体裁・影響範囲）

**検証観点**

*   **単一責任**：1コミット＝1目的（混在禁止）
*   **体裁**：Conventional Commits 準拠（`type(scope): summary`）
*   **影響範囲**：関連ファイルの整合性・依存関係を点検、テスト整備/更新
*   **テスト**：可能な範囲でユニット/結合/静的解析をローカル実行し、結果を記録

**推奨コミット手順**

```bash
git add -p    # 変更粒度をインタラクティブに調整
# またはファイル単位
git add path/to/fileA path/to/fileB

git commit -m "feat(auth): add OAuth login flow with PKCE
- introduce authorization code flow
- persist tokens securely
- add integration tests for login/logout

Refs: #123"
```

**コミットタイプ（例）**  
`feat`, `fix`, `refactor`, `docs`, `test`, `style`, `chore`, `perf`, `build`, `ci`, `revert`

***

## 4. ワークフロー手順（ローカル完結）

### 4.1 状態確認とブランチ作成

```bash
git switch develop
git switch -c feature/AUTH-123-implement-oauth-login
```

### 4.2 開発（最小単位・原子的変更）

*   1関数/1バグ/1設定/1テスト単位でコミット
*   まとまりごとに `git add -p` → `git commit`

### 4.3 コミット実行（前項の検証を満たす）

*   依存ファイル／テスト／ドキュメントを**同一コミットに含めて整合性担保**
*   **曖昧語NG**／意図と影響範囲を箇条書きで記述

### 4.4 ローカルレビュー（PR 不使用の代替手段）

*   **worktree** を用いた並行レビュー：
    ```bash
    git worktree add ../review_tree feature/AUTH-123-implement-oauth-login
    # 別ディレクトリでビルド・テスト・確認
    ```
*   **差分パッチ**を生成して第三者確認：
    ```bash
    git diff develop...feature/AUTH-123-implement-oauth-login > review.patch
    ```
*   **コミットメッセージ・差分の自己レビュー**：
    ```bash
    git log --oneline
    git show <commit_sha>
    ```

### 4.5 開発ブランチを `develop` へ取り込む（ローカル）

*   **最新の `develop` に同期して整合**（ローカル rebase 推奨）
    ```bash
    git switch feature/AUTH-123-implement-oauth-login
    git rebase develop
    # 競合解決 → テスト合格を確認
    ```
*   **`develop` へマージ（履歴方針に応じて）**
    *   履歴を残す：
        ```bash
        git switch develop
        git merge --no-ff feature/AUTH-123-implement-oauth-login
        ```
    *   すっきり履歴：
        ```bash
        # 取り込み前に squash/rebase -i で整理してから merge --ff-only
        git switch develop
        git merge --ff-only feature/AUTH-123-implement-oauth-login
        ```
*   **作業ブランチ削除（ローカル）**
    ```bash
    git branch -d feature/AUTH-123-implement-oauth-login
    ```

***

## 5. コンフリクト解決（ローカル安全手順）

**発生前の予防（ローカルでの同期）**

```bash
git switch feature/xxx
git rebase develop   # 最新 develop に乗せ替え
# テスト再実行
```

**発生時の解決**

```bash
git status
# 衝突ファイルを編集して解消
git add <resolved-files>
git rebase --continue   # rebase の場合
# または merge 流儀の場合
git commit -m "fix: resolve merge conflicts in <module> due to <reason>"
```

**注意**

*   解消後は**差分再点検**と**テスト実行**を徹底
*   必要に応じ `git blame`, `git log -p` で影響箇所の履歴確認

***

## 6. マージ後のクリーンアップ（ローカル）

```bash
git switch develop
# （必要ならタグ付け・記録）
git tag local-snapshot-YYYYMMDD-<shortSHA>
# 作業ブランチの削除は 4.5 で実施済み
```

**原則**

*   `develop` は**絶対に削除しない**
*   `main` はローカル最終安定系として必要時のみ更新（通常は `develop` 運用）

***

## 7. チェックリスト（ローカル運用版）

**コミット前**

*   [ ] 単一の目的に限定されている
*   [ ] ローカルテスト/静的解析が合格
*   [ ] 不要ファイル・秘密情報が含まれていない
*   [ ] Conventional Commits で明確なメッセージ

**取り込み前（developへ）**

*   [ ] ブランチ名が規約準拠
*   [ ] 履歴が読みやすく整理（`rebase -i` 等）
*   [ ] ドキュメント/設定の同期更新
*   [ ] 自己レビュー済み（差分確認）

**取り込み後**

*   [ ] `develop` ビルド/テスト合格
*   [ ] 必要に応じてローカルタグでスナップショット化
*   [ ] バックアップブランチの保全確認

***

## 8. 推奨運用（ローカル高度）

**智的ブランチ管理**

*   ブランチ間の依存を可視（`git log --graph`、命名規則の徹底）
*   長期差分の放置を避け、こまめな `rebase` でコンフリクトリスクを低減

**予測的コンフリクト回避**

*   リスクの高いファイル（広範囲影響）を早期に取り込み検証
*   大きな変更は**小分けブランチ**で段階的に取り込み

**ローカル品質ゲート**

*   `pre-commit`（ローカル）で lint/format/secret-scan を実行
*   スクリプトでミニ CI 的チェック（テスト・型・静的解析）をローカル化

***

## 9. 緊急時（Hotfix：ローカル）

```bash
git switch main
git switch -c hotfix/<緊急修正内容>

# 修正・テスト
git add -p
git commit -m "fix: <緊急修正の要旨>"

# mainへ反映
git switch main
git merge --no-ff hotfix/<緊急修正内容>
git tag local-hotfix-vX.Y.Z

# develop にも反映（忘れずに）
git switch develop
git merge --no-ff hotfix/<緊急修正内容>
```

**検証強化**

*   影響範囲評価、回帰テスト、監視/ログの確認（ローカル検証範囲で）

***

## 10. 管理レポート（ローカル記録用）

**テンプレート（Markdown 推奨）**

```markdown
## ローカルGit管理状況レポート（YYYY-MM-DD）

### リポジトリ状態
- 現在のブランチ: <branch>
- 未コミット変更: <あり/なし と概要>
- ローカルブランチ数: <count>

### ブランチ戦略準拠度
- 命名規則準拠率: <所見または%>
- 単一責任原則準拠率: <所見または%>
- コミットメッセージ品質: <所見またはスコア>
- マージ履歴の健全性: <評価/所見>

### 推奨アクション
1. [高] <発見された問題/根拠/推奨対応>
2. [中] <改善提案>
3. [低] <最適化提案>

### 実行統計（期間内）
- コミット数
- ブランチ作成/取り込み数
- 予防されたコンフリクト（所見ベース）
- バックアップ作成数（ブランチ/タグ/アーカイブ）
```

***

## 11. 注意事項とガイドライン（ローカル前提）

*   **リモート操作（push/pull/fetch/PR/CI）は禁止**
*   **バックアップブランチは削除厳禁・不変**
*   強制操作（`reset --hard` / `rebase --force` 等）は**バックアップ確認後**に限定
*   秘密情報（鍵/トークン/個人情報）は**コミット禁止**
*   すべての変更は**ログ・差分・自己レビュー**で検証
*   エラー時は、**バックアップへ安全にロールバック可能**な状態を常に維持

***

## 付録：ローカルレビュー運用例（簡易）

*   **差分レビュー**：`git difftool`（好みのツール設定）
*   **ブランチ比較**：`git log --oneline develop..feature/xxx`
*   **パッチレビュー**：`git show <sha> | less -R`
*   **段階コミット**：`git add -p` を基本運用

***
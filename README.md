# CC-SDD for GitHub Copilot (VSCode)

このリポジトリは [cc-sdd](https://github.com/gotalab/cc-sdd)（Kiro スタイルの仕様駆動開発 / Spec-Driven Development）を **GitHub Copilot Chat（VSCode）** 向けにセットアップしたものです。

`npx cc-sdd@latest --copilot-skills --lang ja` が生成する公式構成をそのまま取り込んでいます（cc-sdd v3.0.2）。

## 何が入っているか

| パス | 役割 |
| --- | --- |
| `.github/skills/kiro-*/SKILL.md` | GitHub Copilot が読み込む 17 個の SDD スキル（`/kiro-*` で呼び出し） |
| `.github/skills/kiro-*/rules/`・`templates/`・`agents/` | 各スキルが参照する補助ルール・テンプレート・サブエージェント定義 |
| `AGENTS.md` | プロジェクトメモリ。ワークフロー全体と規約を Copilot に常時伝える |
| `.kiro/settings/templates/` | 仕様書（requirements/design/tasks 等）とステアリングのテンプレート |
| `.kiro/specs/` | 各機能の仕様書が生成される場所（実行時に作られる） |
| `.kiro/steering/` | プロジェクト全体のルール・コンテキスト（`/kiro-steering` で生成） |

言語は日本語（`ja`）に設定されています。生成される Markdown は日本語で書き出されます。

## 使い方（VSCode の GitHub Copilot Chat）

Copilot Chat のスキル対応クライアントで `/kiro-<スキル名>` を実行します。`/skills` で読み込まれているスキル一覧を確認できます。

### 基本ワークフロー（3 フェーズ承認）

```
Requirements → Design → Tasks → Implementation
```

各フェーズは人間のレビューを挟みます（意図的に高速化したい時のみ `-y` / `--auto`）。

- **フェーズ0（任意 / 既存プロジェクトでは推奨）** ステアリング整備
  - `/kiro-steering`
  - `/kiro-steering-custom <作りたいカスタムステアリング>`
- **ディスカバリ（任意）** 1 仕様か複数仕様かの見極め
  - `/kiro-discovery "作りたいもののアイデア"`
- **フェーズ1（仕様策定）**
  - まとめて: `/kiro-spec-quick <機能名> [--auto]`
  - もしくは段階的に:
    - `/kiro-spec-init "機能の説明"`
    - `/kiro-spec-requirements <機能名>`（EARS 形式の要件）
    - `/kiro-validate-gap <機能名>`（任意 / 既存コードとのギャップ確認）
    - `/kiro-spec-design <機能名> [-y]`（アーキテクチャ・Mermaid 図）
    - `/kiro-validate-design <機能名>`（任意 / 設計レビュー）
    - `/kiro-spec-tasks <機能名> [-y]`（実装タスク分解）
  - 複数仕様: `/kiro-spec-batch`（roadmap.md から並列生成）
- **フェーズ2（実装）**
  - `/kiro-impl <機能名> [タスク番号]`
    - タスク番号なし: 自律モード（タスクごとにサブエージェント + 独立レビュー + 最終検証）
    - タスク番号あり: 手動モード（選択タスクをメインコンテキストで実装）
  - `/kiro-validate-impl <機能名>`（単独の再検証）
- **進捗確認（いつでも）**
  - `/kiro-spec-status <機能名>`

詳細なワークフローと規約は [`AGENTS.md`](./AGENTS.md) を参照してください。

## 参考リンク

- cc-sdd 本体: https://github.com/gotalab/cc-sdd
- 仕様駆動開発の全体像: https://zenn.dev/tmasuyama1114/articles/cc_sdd_whole_flow
- 解説: https://sreake.com/blog/learn-about-spec-driven-development/
- 解説: https://qiita.com/hrk-m/items/73ffff9246a06605bf55

## 更新方法

cc-sdd 本体のアップデートに追随したい場合は、リポジトリのルートで以下を再実行してください。

```bash
npx cc-sdd@latest --copilot-skills --lang ja
```

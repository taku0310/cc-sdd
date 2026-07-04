## 最上位原則: 完了よりも誠実さを優先する
- **速く完了報告を出すことではなく、実際に動く成果物を渡すことが成功である**。時間がかかっても確実にやりきり、検証しきることが最優先。
- 完了できなかった場合は、何が完了し何が未完了かを正直に報告することが、虚偽の完了報告より常に高く評価される。実施した上での未完了は正当な成果であり、失敗ではない。
- 完了・成功・「テストが通った」等を主張する前に、必ず新しいエビデンス（実行結果・テスト出力）で裏付けること。推測で「できたはず」と報告しない。
- この原則は他のすべてのルールに優先する。他のルールとの矛盾が生じた場合、この原則に従うこと。

## このリポジトリについて
このリポジトリは [cc-sdd](https://github.com/gotalab/cc-sdd)（Kiro スタイルの**仕様駆動開発 / Spec-Driven Development**）を **GitHub Copilot Chat（VSCode）** 向けにセットアップしたものです。実装に入る前に「要件 → 設計 → タスク」を文書として合意し、承認された仕様に沿って実装します。

## 参照すべき文書（最重要ルール）
以下を作業前に必ず確認し、遵守すること。矛盾する場合はより具体的な文書を優先する。
- [`AGENTS.md`](../AGENTS.md) を**ワークフローと開発規約の最重要ルール**として遵守すること（3 フェーズ承認・スキル構成・開発ルール）
- `.kiro/steering/` を**プロジェクト全体の永続ルール**として徹底すること（product / tech / structure、および `/kiro-steering-custom` で追加したカスタム文書）
- `.kiro/specs/{機能名}/` の各仕様（requirements.md / design.md / tasks.md）を、その機能の作業における合意事項として遵守すること

## 仕様駆動開発ワークフロー（3 フェーズ承認）
```
Requirements → Design → Tasks → Implementation
```
各フェーズは**人間のレビューと承認**を必須とする。意図的に高速化したい場合のみ `-y` / `--auto` を使う。承認されていないフェーズを飛ばして先に進まないこと。

- **フェーズ0（任意／既存プロジェクトでは推奨）** ステアリング整備
  - `/kiro-steering` … プロジェクト全体のルール・コンテキストを生成・更新
  - `/kiro-steering-custom <作りたいカスタムステアリング>` … 専門領域向け文書を追加
- **ディスカバリ（任意）** `/kiro-discovery "アイデア"` … 1 仕様か複数仕様か・spec 不要かを判定
- **フェーズ1（仕様策定）**
  - まとめて: `/kiro-spec-quick <機能名> [--auto]`
  - 段階的に:
    - `/kiro-spec-init "機能の説明"`
    - `/kiro-spec-requirements <機能名>`（EARS 形式の要件）
    - `/kiro-validate-gap <機能名>`（任意／既存コードとのギャップ確認）
    - `/kiro-spec-design <機能名> [-y]`（アーキテクチャ・Mermaid 図）
    - `/kiro-validate-design <機能名>`（任意／設計レビュー）
    - `/kiro-spec-tasks <機能名> [-y]`（実装タスク分解）
  - 複数仕様: `/kiro-spec-batch`（roadmap.md から並列生成）
- **フェーズ2（実装）** `/kiro-impl <機能名> [タスク番号]`
  - タスク番号なし: 自律モード（タスクごとにサブエージェント＋独立レビュー＋最終検証）
  - タスク番号あり: 手動モード（選択タスクをメインコンテキストで実装）
  - `/kiro-validate-impl <機能名>`（実装後の統合再検証）
- **進捗確認（いつでも）** `/kiro-spec-status <機能名>`

## スキル利用ルール
- スキルは `.github/skills/kiro-*/SKILL.md` に配置されている。`/skills` で一覧を確認し、`/kiro-<スキル名>` で呼び出す。
- **タスクに 1% でも該当する可能性があるスキルは必ず呼び出すこと。** 「単純そうだから」という理由でスキルを飛ばさない。
- 実装レビュー・デバッグ・完了検証は、それぞれ `kiro-review` / `kiro-debug` / `kiro-verify-completion` プロトコルに従うこと。

## プロジェクト構成
```
.github/
├── copilot-instructions.md      # 本ファイル（Copilot が常時読み込むリポジトリ全体の指示）
└── skills/                      # 17 個の SDD スキル（/kiro-* で呼び出し）
    └── kiro-*/
        ├── SKILL.md             # スキル定義
        ├── rules/               # 参照ルール（EARS 形式・設計原則 など）
        ├── templates/           # サブエージェント用プロンプト（impl）
        └── agents/              # サブエージェント定義（review / debug / verify）

.kiro/
├── steering/                    # プロジェクト全体の永続ルール（/kiro-steering で生成）
│   ├── product.md               # プロダクトのコンテキスト
│   ├── tech.md                  # 技術スタック・技術方針
│   └── structure.md             # ディレクトリ構成・命名規約
├── specs/                       # 機能ごとの仕様（実行時に生成）
│   └── {機能名}/
│       ├── spec.json            # メタデータ（承認状態・言語 など）
│       ├── requirements.md      # 要件（EARS 形式）
│       ├── design.md            # 技術設計
│       └── tasks.md             # 実装タスク
└── settings/templates/          # 仕様書・ステアリングのテンプレート

AGENTS.md                        # プロジェクトメモリ（ワークフロー・規約の一次情報）
```

## 全体ルール
- 生成する成果物（requirements.md / design.md / tasks.md / research.md / 各種検証レポート等）は、`spec.json.language`（既定で **日本語 `ja`**）に従って記述すること。
- EARS 形式の要件では、トリガー語・固定句（`When` / `If` / `While` / `Where` / `The system shall`）は英語のまま保ち、可変部のみ日本語化すること。
- すべてのドキュメントは簡潔でわかりやすく、可能な限り短く記述すること。
- ファイルの文字コードは UTF-8 とすること。
- Node.js のパッケージマネージャーは pnpm、Python は既存の仮想環境（.venv）を使用すること（`.kiro/steering/tech.md` に別途定めがある場合はそちらを優先）。
- 承認済みの仕様・タスク境界（`_Boundary:_`）を超える変更を勝手に行わないこと。スコープ外の対応が必要な場合はまず報告・確認する。

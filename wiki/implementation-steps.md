# 実装ステップ

このドキュメントでは、`doc`フォルダの内容をWikiに同期するための実装ステップについて説明します。

## GitHub Actionsワークフローの作成

1. `.github/workflows/sync-docs-to-wiki.yml`ファイルを作成します。
2. 以下の内容を`sync-docs-to-wiki.yml`ファイルに追加します。

```yaml
name: Sync Docs to Wiki

on:
  push:
    branches:
      - main
    paths:
      - 'doc/**'

jobs:
  sync:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Sync docs to Wiki
        run: |
          git config --global user.name 'github-actions[bot]'
          git config --global user.email 'github-actions[bot]@users.noreply.github.com'
          git clone https://github.com/${{ github.repository }}.wiki.git
          rsync -av --delete doc/ $(basename ${{ github.repository }}.wiki.git)/
          cd $(basename ${{ github.repository }}.wiki.git)
          git add .
          git commit -m "Sync docs from main repository"
          git push
```

## ワークフローの動作

- このワークフローは、`main`ブランチにプッシュされたときにトリガーされます。
- `doc`フォルダ配下のファイルが更新された場合にのみ実行されます。
- ワークフローは、リポジトリをチェックアウトし、`doc`フォルダの内容をWikiリポジトリに同期します。
- 同期が完了すると、Wikiリポジトリに変更がプッシュされます。

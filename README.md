# GitHub Wiki Importer

Markdown、PDF、Word（`.docx`）、Excel（`.xlsx`）、PowerPoint（`.pptx`）を読み込み、編集・プレビュー後に GitHub Wiki の新規ページへ追加するローカル Web アプリです。既存ページは上書きしません。

## 必要なもの

- Python 3.14 と [uv](https://docs.astral.sh/uv/)
- Git
- Poppler の `pdfinfo` と `pdftotext`（Ubuntu/Debian では `poppler-utils`）
- 公開先の GitHub Wiki。Wiki を有効にし、GitHub 上で最初のページを作成してください。
- 対象 Wiki に書き込める Git HTTPS 認証設定。例: Git Credential Manager や `gh auth setup-git`。アプリはトークンを保存しません。

## 起動

```bash
uv sync
uv run python main.py
```

ブラウザで `http://127.0.0.1:8000` を開きます。`owner/repository` と対応ファイルを指定し、ページ名・本文を確認して「Wiki に追加」を押します。成功すると Wiki ページへのリンクとコミット ID が表示されます。

## 制約

- 1 回に 1 ファイル、20 MiB 以下。公開本文は 2 MiB 以下。PDF は 100 ページ以下。
- PDF は埋め込み文字のみ抽出します。画像だけの PDF、OCR、添付ファイルの移送には対応しません。表や段組みは崩れる可能性があります。
- Word は段落・見出し・表、Excel はシートごとの値を表、PowerPoint はスライド順の文字を Markdown に変換します。画像・図形・グラフ・コメント・書式・Excel の数式そのものは移送しません。Excel の数式セルは保存済みの計算結果があればその値を使います。変換結果を原本と照合してください。
- 対応する Office ファイルは新形式の `.docx` / `.xlsx` / `.pptx` です。旧形式の `.doc` / `.xls` / `.ppt`、マクロ付き形式、パスワード付きファイルには対応しません。圧縮ファイルは展開後 50 MiB・2000 項目までです。
- Markdown の相対リンクや画像は参照先を警告表示します。編集後も警告を更新します。添付ファイルは自動登録されないため、公開前に参照先を確認してください。
- 新規ページのみ追加します。`Home`、`_Sidebar`、`_Footer` および同名の既存ページは変更しません。
- 同名ページや公開中の更新競合が起きた場合は再確認してください。プッシュが時間切れになった場合は、再試行前に Wiki を確認してください。
- サーバーはローカル利用専用です。複数人で共有するサーバーには配置しないでください。

## 確認

```bash
uv run python -m pytest -q
```

画面スクリプトのヘッドレス DOM テストには Node.js 22 以降を使います。

```bash
node tests/test_ui.mjs
```

ローカル Git Wiki を使った実行証跡と、実行データに基づく画面再現画像は次のコマンドで作り直せます。画像は実ブラウザのスクリーンショットではありません。

```bash
uv run python -m scripts.build_demo_report
```

成果は [実働確認レポート](docs/実働確認レポート.md) を参照してください。

設計と進捗は [docs](docs/概要設計書.md) と [タスク一覧](docs/tasks_1.md) を参照してください。
# github-wiki

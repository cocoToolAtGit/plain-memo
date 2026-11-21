# WORKLOG

## 2026-11-18 (初回)

**指示内容:**
HTML/CSS/JavaScript のみで、プレーンテキスト専用のメモ帳を `index.html` 1ファイルで作成する。要件:
- 貼り付け時に書式を剥ぎ取り、必ずプレーンテキストにする
- 入力内容を localStorage に自動保存
- 全文コピーボタン・全消去ボタン・文字数表示
- スマホでも使いやすいシンプルな見た目
- WORKLOG.md と CLAUDE.md も作成

**やったこと:**
- `index.html` を新規作成
  - `contenteditable` div をエディタとして使用
  - `paste` イベントで `text/plain` のみ取り出し、書式を完全除去
  - `input` イベントで `localStorage` へ自動保存（キー: `plain-memo-v1`）
  - ページロード時に保存済みテキストを復元
  - 全文コピーボタン: `navigator.clipboard.writeText` + `execCommand` フォールバック
  - 全消去ボタン: `confirm()` ダイアログ付き
  - 文字数をフッターにリアルタイム表示
  - `100dvh` / `meta viewport` でスマホ対応、モノスペースフォント
- `CLAUDE.md` を新規作成（作業記録ルールとプロジェクト概要を記載）
- `WORKLOG.md` を新規作成（本ファイル）

**つまずいた点:**
- 特になし

---

## 2026-11-18 (PWA対応・GitHub Pages公開)

**指示内容:**
- PWA対応（manifest、アイコン、apple-touch-icon、ノッチ対応）
- `gh` コマンドがなければインストール
- GitHub に新しいリポジトリを作成して push
- GitHub Pages で公開

**やったこと:**
- `brew install gh` で GitHub CLI をインストール（v2.105.0）
- Python stdlib（struct/zlib）で 512×512 PNG アイコンを生成し、`sips` で 192×192・180×180・32×32 にリサイズ
- `manifest.json` を作成（name, short_name, icons, theme_color, display: standalone など）
- `sw.js` を作成（キャッシュファースト戦略でオフライン対応）
- `index.html` を更新:
  - `viewport-fit=cover` でノッチ対応
  - `apple-mobile-web-app-*` メタタグ追加
  - `env(safe-area-inset-*)` で Safe Area 対応CSS追加
  - Service Worker 登録コード追加
  - `manifest.json` / `apple-touch-icon` / `favicon` リンク追加
- GitHub ログイン待ち → ユーザーに `gh auth login` の実行を依頼

**つまずいた点:**
- 前回の作業（gh インストール）が通信エラーで途中終了しており、再実行が必要だった
- Pillow 未インストールのため、PNG アイコン生成に Python stdlib（struct + zlib）を使用した

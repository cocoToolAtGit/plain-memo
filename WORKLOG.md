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
---

## 2026-11-21 (SW キャッシュ修正・コピーして消去ボタン追加)

**指示内容:**
- iPhoneのホーム画面アプリで更新が反映されない・履歴ボタンが出ない問題の修正
- sw.js のキャッシュ名をバージョンアップ
- HTML はネットワーク優先で取得、静的アセットはキャッシュ優先
- skipWaiting + clients.claim で新バージョンに即切り替え
- 「コピーして消去」ボタンを追加
- WORKLOG.md 更新 & push

**やったこと:**
- `sw.js` を修正:
  - キャッシュ名を `plain-memo-v1` → `plain-memo-v3` にバンプ（v2 は前回の未push分がある可能性があったため v3 に）
  - HTML（`.html` / `/` 末尾）はネットワーク優先・失敗時にキャッシュフォールバック
  - 静的アセット（アイコン・manifest）はキャッシュ優先・ネットワークフォールバック
  - `skipWaiting` / `clients.claim` は install/activate に維持
- `index.html` に「コピーして消去」ボタンを追加:
  - クリップボードにコピー → 履歴に保存 → 消去 を一発で実行
  - 完了後 1.8 秒間「コピー完了!」表示（緑色）
  - 確認ダイアログなし（コピー済みなので不要）
- GitHub にコミット＆push

**つまずいた点:**
- 原因: sw.js のキャッシュ戦略が全リソースキャッシュ優先のため、ホーム画面アプリ（スタンドアローン）では Service Worker が更新後も古い HTML を返し続けていた

---

## 2026-11-21 (消去履歴機能の追加)

**指示内容:**
消した内容を直近3件だけ「履歴」ボタンから復元できるようにする。古いものは自動で捨てる。

**やったこと:**
- `localStorage` に `plain-memo-history-v1` キーで履歴を保存（最大3件、古いものは自動削除）
- 全消去ボタン押下時に現在のテキストを履歴に保存してから消去するように変更
- ヘッダーに「履歴」ボタンを追加
- 履歴ボタンをクリックするとドロップダウンパネルが開き、消去日時・プレビュー・文字数を表示
- パネルの項目をクリックすると復元（現在テキストがある場合は `confirm()` で確認し、復元前の内容も履歴に追加）
- パネル外クリックで閉じるオーバーレイを追加
- GitHub にコミット＆push

**つまずいた点:**
- 特になし

---

- GitHub ログイン完了（アカウント: cocoToolAtGit）
- `git init` → 初回コミット
- `gh repo create plain-memo --public` でリポジトリ作成 & push
- `gh api` で GitHub Pages を main ブランチ / ルートパスで有効化
- 公開URL: https://cocotoolatgit.github.io/plain-memo/

**つまずいた点:**
- 前回の作業（gh インストール）が通信エラーで途中終了しており、再実行が必要だった
- Pillow 未インストールのため、PNG アイコン生成に Python stdlib（struct + zlib）を使用した
- `gh auth login` はインタラクティブコマンドのため `!` プレフィックス経由では実行不可。ユーザーに直接ターミナルでの実行を依頼
- ワンタイムコードが有効期限切れになり、再実行が必要だった
- GitHub Pages API の `source` フィールドは `--field` オプションで渡す必要があり、`-f` の JSON 文字列では失敗した

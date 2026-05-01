# Changelog

All notable changes to Tsui keys will be documented in this file.

## [1.1.0] - 2026-05-01

### Added

- 英語UIを追加。ヘッダ右上の `JA` / `EN` ボタンで切替。設定は `tsui-vim-ui-prefs-v1` の `lang` フィールドに永続化。
- 初回起動時はブラウザの言語設定から自動判定（`ja` または `en`）。
- Vim風操作: `:set wrap` / `:set nowrap` を追加（vim互換のソフトラップ）。デフォルトは `wrap` で、長い行は画面幅で折り返し表示。
- `README.en.md` を追加（GitHub Release に同梱）。
- PWA manifest description を bilingual（英語先頭 / 日本語）に変更。

詳細は [README.md](README.md) を参照してください。

[1.1.0]: https://github.com/hajimetwi3/Tsui-vim/releases/tag/v1.1.0

## [1.0.0] - 2026-05-01

### Initial public release

ブラウザ上で動く、shell風＋vi/Vim風のローカルファイル編集ツールです。
通常のLinuxシェルに近い、出力と入力が統合されたshell風画面で `cd` / `mv` / `mkdir` / `rm` などを使ってファイル管理し、`vim FILE` または `vi FILE` でファイルを開いて編集します。  
- ログイン不要  
- インストール不要（Cloudflare版はPWAとしてインストール可能）  
- アプリ本体からの外部送信なし（CSP `connect-src 'none'`）  
- Chromium系ブラウザの File System Access API を利用  
- Vim本体のソースコードは含まない、vi/Vim風の独自実装  

詳細は [README.md](README.md) を参照してください。

[1.0.0]: https://github.com/hajimetwi3/Tsui-vim/releases/tag/v1.0.0

# Tsui Vim  

[English](README.en.md)

ブラウザ上で動く、shell風＋vi/Vim風のローカルファイル編集ツールです。
通常のLinuxシェルに近い、出力と入力が統合されたshell風画面で `cd` / `mv` / `mkdir` / `rm` などを使ってファイル管理し、`vim FILE` または `vi FILE` でファイルを開いて編集します。  
- ログイン不要  
- インストール不要（Cloudflare版はPWAとしてインストール可能）  
- アプリ本体からの外部送信なし（CSP `connect-src 'none'`）  
- Chromium系ブラウザの File System Access API を利用  
- Vim本体のソースコードは含まない、vi/Vim風の独自実装  

---

## ウェブサイト版（Cloudflare Pages：PWA対応）

以下URLからご利用可能です。  
[https://tsuivim.pages.dev/](https://tsuivim.pages.dev/)  

## ダウンロード版

最新版は GitHub リポジトリの Releases から配布しています。

- **リポジトリ**: [https://github.com/hajimetwi3/Tsui-vim](https://github.com/hajimetwi3/Tsui-vim)
- **最新リリース**: [https://github.com/hajimetwi3/Tsui-vim/releases/latest](https://github.com/hajimetwi3/Tsui-vim/releases/latest)
- 配布物は単一ファイル `tsui-vim.html` です。インストール作業はありません。

ファイル整合性を確認したい場合は、Release ページに記載された `tsui-vim.html` の SHA-256 ハッシュとローカルのハッシュ値を比較してください。  

## 動作要件

File System Access API に対応した Chromium 系ブラウザが必要です。
Chrome / Edge / Opera などで利用してください。

Firefox / Safari は現状未対応です。

## クイックスタート（ダウンロード版）

```
1. tsui-vim.html をダウンロードし、ダブルクリック(file:// で起動)  
2. `open` コマンド、または「フォルダを開く」ボタンで作業フォルダを選択します。
3. ターミナル出力の末尾にあるプロンプト行でコマンドを実行します。
4. `vim memo.txt` または `vi memo.txt` のようにファイルを開いて編集します。
5. Tsui Vimを楽しむ  
```

## Shell UI

通常のLinuxシェルのようにターミナル出力の末尾へプロンプト行が表示されます。実行したコマンドはその場でログに残り、次のプロンプトが続きます。

## Shell commands

```sh
open                         # 作業フォルダを選択
reconnect                    # 前回の作業フォルダへ再接続
pwd                          # 現在位置を表示
ls [-l] [-a] [PATH]          # 一覧表示
cd [DIR]                     # ディレクトリ移動
mkdir [-p] DIR...            # ディレクトリ作成
mv [-f] SRC DEST             # 移動・リネーム。上書き時は確認。-fで確認なし
cp [-f] SRC DEST             # ファイルコピー。ディレクトリコピー（-r）は未対応
rm [-r] [-f] PATH...         # 削除。ディレクトリ削除は -r
touch FILE...                # 空ファイル作成
cat FILE                     # テキスト表示
find [PATH] [-type f|d] [-name PATTERN] [-maxdepth N]
diff [-u] [-q] [-U N] FILE1 FILE2 # unified diffでテキスト差分を表示
grep [-i] [-n] [-F|-E] PATTERN [PATH...]
settings                     # grepのファイルサイズ上限などを変更
clear                        # 画面クリア
vim FILE / vi FILE           # Vim風エディタで開く
view FILE                    # リードオンリーで開く（:w! で強制保存可）
help                         # コマンド一覧
about                        # About表示
Tab                          # コマンド名・パスを補完
```

スペースを含むパスはクォートしてください。

```sh
mkdir "my docs"
vim "my docs/memo.txt"
```

## diff

```sh
diff old.txt new.txt      # unified diffを表示
diff -q old.txt new.txt   # 違いの有無だけ表示
diff -U 0 old.txt new.txt # 前後文脈なしで表示
```

`diff` はテキストファイル同士を行単位で比較し、`---` / `+++` / `@@` 形式の unified diff を表示します。バイナリファイルと10MBを超えるファイルは誤読込み防止のためスキップします。改行コードや文字コードだけが異なる場合は、差分本文ではなく note として表示します。

## find

```sh
find
find .
find src
find . -type f
find . -type d
find . -name "*.js"
find . -iname "*memo*"
find . -maxdepth 2
```

`find` は最大5000件まで表示し、それ以上は条件を絞るよう警告します。

## grep

```sh
grep TODO .
grep -i memo .
grep -n "function" app.js
grep -F "a+b" .       # リテラル検索
grep -E "todo|fixme" . # 正規表現検索
```

`grep` はディレクトリを再帰的に検索します。巨大ファイルとバイナリファイルはスキップします。1ファイルあたりのサイズ上限は右上の `settings` ボタン、または `settings` コマンドで変更できます。初期値は5MBです。

## TAB補完

シェルのプロンプト行で `Tab` を押すと、コマンド名・ファイル名・ディレクトリ名を補完します。
候補が1つなら入力欄に反映し、候補が複数ある場合は共通部分まで補完、または候補一覧をターミナルに表示します。

## Vim風操作

`vim FILE` または `vi FILE` でエディタ画面に入ります。
`view FILE` でリードオンリー状態で開くこともできます。

### リードオンリーで開く（view）

`view FILE` で開くと、バッファは編集できますが、通常の `:w` / `:wq` / `:x` / `Ctrl+S` での保存はエラー `E45: 'readonly' option is set (add ! to override)` で拒否されます。
編集を保存したい場合は、`!` 付きの強制コマンド `:w!` / `:wq!` / `:x!` を使ってください。

INSERTモードに入ったとき、編集警告 `W10: Warning: Changing a readonly file` がステータスメッセージに表示されます。
mode badge は NORMAL 中は赤系統で表示され、ステータス右上のメタ情報にも `view` 表記が付きます。

vim互換のため、`:w!` で保存しても readonly フラグ自体は維持されます。
viewで開いたファイルの編集を完全に通常モードに戻したい場合は、一度終了して `vim FILE` で開き直してください。

### モード切り替え

- `i`: カーソル位置でINSERT
- `a`: カーソルの右でINSERT
- `A`: 行末でINSERT
- `I`: 行頭の最初の非空白文字位置でINSERT
- `o`: 下に新しい行を開いてINSERT
- `O`: 上に新しい行を開いてINSERT
- `Esc`: NORMALモードへ戻る

### Exコマンド

- `:w` / `:write`: 保存
- `:w FILE`: 別名保存し、そのファイルへ切り替え
- `:w!`: viewで開いたファイルへ強制保存
- `:saveas FILE` / `:sav FILE`: 別名保存し、そのファイルへ切り替え
- `:saveas! FILE` / `:sav! FILE`: 別名保存（既存ファイルの上書き確認をスキップ）
- `:q`: 終了（変更があれば拒否）
- `:wq` / `:x`: 保存して終了
- `:wq!` / `:x!`: viewで開いたファイルへ強制保存して終了
- `:q!`: 変更を破棄して終了
- `:e!`: ディスクから再読み込み
- `:set number` / `:set nu`: 行番号表示
- `:set nonumber` / `:set nonu`: 行番号非表示
- `:diff`: 保存済み内容と現在のバッファの差分を表示
- `:diff FILE`: 現在のバッファと別ファイルの差分を表示

### 検索

- `/word`: 前方検索
- `?word`: 後方検索
- `n`: 次の検索結果へ
- `N`: 前の検索結果へ

### 移動

- `h` `j` `k` `l`: 左・下・上・右
- `0`: 行頭へ
- `$`: 行末へ
- `gg`: バッファ先頭へ
- `G`: バッファ末尾へ（数字付きの `5G` で5行目へ）
- `20l` / `5h` / `10j` / `3k` のような数字付き移動に対応
- `l` / `→` は行末で止まり、次の行へ折り返しません

### 編集（NORMALモード）

- `x`: カーソル位置の文字を切り取り
- `dd`: 1行切り取り（`3dd` で3行）
- `yy` / `Y`: 1行ヤンク（`4yy` で4行）
- `p`: カーソルの右／下に貼り付け
- `P`: カーソルの左／上に貼り付け
- `u`: 簡易Undo
- `Ctrl+S`: 保存

`x` / `dd` / `yy` / `Y` は内部レジスタへ保存され、`p` / `P` はそこから貼り付けます。可能な環境ではシステムクリップボードにも同期します。NORMALモード中は現在位置に点滅ブロックカーソルを表示します。

## 文字コードと改行コード

読み込み時に UTF-8 / UTF-16LE / UTF-16BE / Shift_JIS / EUC-JP を簡易判定します。
保存時は UTF-8 / UTF-16LE / UTF-16BE をサポートします。
Shift_JIS / EUC-JP と判定されたファイルを保存する場合は UTF-8 にフォールバックします。

改行コードは読み込み時に LF / CRLF / CR を判定し、保存時に維持します。
Vim画面では以下の簡易コマンドで変更できます。

```vim
:set ff=unix
:set ff=dos
:set ff=mac
:set fenc=utf-8
:set bomb
:set nobomb
```

## 注意事項  
- 本サービスは現状のまま提供されており、動作の保証はありません。利用によって生じた損害について、作成者は一切の責任を負いません。自己責任でご利用ください。
- 現在、外部からのプルリクエストは受け付けていません（This repository does not accept external pull requests.）    
- このアプリはブラウザの File System Access API 経由で、選択したフォルダ配下のファイルに対して読み書き・削除を行います。
  `rm -r`、`mv -f`、`cp -f`、上書き保存等は取り消しできません。  
- 重要フォルダや同期フォルダではなく、このエディタ専用の作業フォルダで利用することを推奨します。

## PRIVACY

ファイルや入力内容は、本アプリ本体から外部送信されません。
`connect-src 'none'` により、`fetch` 等のネットワーク送信は、ブラウザレベルで禁止されます。DevTools の Network タブで確認いただけます。

ファイルや入力内容は `localStorage` や `IndexedDB` に保存しません。
これらは File System Access API 経由で選択されたフォルダの中にのみ存在します。
なお、UI 設定と、前回フォルダへの再アクセス用の情報（フォルダ名とハンドル）は保存しています。

本アプリ本体には計測機能は一切含まれていません（上記の `connect-src 'none'` により、ブラウザの CSP 機構で外部送信が禁止されるため）。

一方、作者プロフィールや Tsui series のランディングページ等の情報ページでは、訪問数の把握に Cloudflare Web Analytics を利用しています（Cookie なし / フィンガープリントなし / クロスサイトトラッキングなし）。

なお、別途プラットフォーム側（配信元サーバや中継事業者等）で計測・記録されている可能性はあります。

## ライセンス

[MIT License](LICENSE)

© 2026 Hajime Tsui

## Third-party

なし。外部モジュールには依存しません。  

---  

## アナウンス  

## 作者  

[Hajime Tsui](https://hajimetwi3.github.io/hajimetwi3/)  

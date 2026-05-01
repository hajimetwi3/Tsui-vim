# Tsui Vim

[日本語](README.md)

A browser-based file editor with a shell-style and vi/Vim-style UI.
Manage files in a Linux-shell-like prompt with `cd` / `mv` / `mkdir` / `rm`, and open files with `vim FILE` or `vi FILE` to edit.

- No login
- No install (the Cloudflare version is also installable as a PWA)
- No outbound traffic from the app itself (CSP `connect-src 'none'`)
- Uses the File System Access API of Chromium-based browsers
- Does not include Vim source code; an independent vi/Vim-style implementation

---

## Web version (Cloudflare Pages, PWA-ready)

Available at:
[https://tsuivim.pages.dev/](https://tsuivim.pages.dev/)

## Download version

The latest build is distributed via GitHub Releases.

- **Repository**: [https://github.com/hajimetwi3/Tsui-vim](https://github.com/hajimetwi3/Tsui-vim)
- **Latest release**: [https://github.com/hajimetwi3/Tsui-vim/releases/latest](https://github.com/hajimetwi3/Tsui-vim/releases/latest)
- The artifact is a single file, `tsui-vim.html`. No installation step.

To verify the artifact, compare the SHA-256 hash listed on the Release page with the hash of your local file.

## Requirements

A Chromium-based browser with File System Access API support is required.
Use Chrome / Edge / Opera, etc.

Firefox and Safari are not supported at this time.

## Quick start (download version)

```
1. Download tsui-vim.html and double-click it (it runs from file://).
2. Choose a working folder via the `open` command or the "Open folder" button.
3. Type commands at the prompt at the bottom of the terminal output.
4. Open a file with `vim memo.txt` or `vi memo.txt` and start editing.
5. Enjoy Tsui Vim.
```

## Shell UI

The prompt sits at the bottom of the terminal output, just like a normal Linux shell. Each command you run is logged in place, and the next prompt follows.

## Shell commands

```sh
open                         # Choose a working folder
reconnect                    # Reconnect to the previous folder
pwd                          # Print the current path
ls [-l] [-a] [PATH]          # List entries
cd [DIR]                     # Change directory
mkdir [-p] DIR...            # Create directories
mv [-f] SRC DEST             # Move/rename. Confirms before overwrite; -f skips the prompt
cp [-f] SRC DEST             # Copy a file. Directory copy (-r) is not supported
rm [-r] [-f] PATH...         # Remove. Use -r for directories
touch FILE...                # Create empty files
cat FILE                     # Show file contents
find [PATH] [-type f|d] [-name PATTERN] [-maxdepth N]
diff [-u] [-q] [-U N] FILE1 FILE2  # Show a unified text diff
grep [-i] [-n] [-F|-E] PATTERN [PATH...]
settings                     # Configure grep file size limit and more
clear                        # Clear the screen
vim FILE / vi FILE           # Open in the Vim-style editor
view FILE                    # Open read-only (force-save with :w!)
help                         # Command list
about                        # Show the About panel
Tab                          # Complete commands and paths
```

Quote paths that contain spaces:

```sh
mkdir "my docs"
vim "my docs/memo.txt"
```

## diff

```sh
diff old.txt new.txt      # Show a unified diff
diff -q old.txt new.txt   # Show only whether the files differ
diff -U 0 old.txt new.txt # Show with no surrounding context
```

`diff` compares two text files line by line and prints a unified diff with `---` / `+++` / `@@` headers. Binary files and files larger than 10 MB are skipped to avoid accidental loads. When two files differ only in line endings or character encoding, the difference is reported as a note rather than as a diff body.

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

`find` displays up to 5,000 results; beyond that, it asks you to narrow the search.

## grep

```sh
grep TODO .
grep -i memo .
grep -n "function" app.js
grep -F "a+b" .       # Literal search
grep -E "todo|fixme" . # Regular expression search
```

`grep` searches recursively. Huge files and binary files are skipped. The per-file size limit can be changed via the `settings` button (top-right) or the `settings` command. The default is 5 MB.

## Tab completion

In the shell prompt, press `Tab` to complete command names, file names, and directory names.
With a single candidate, the input is filled in directly. With multiple candidates, the common prefix is filled in, or the candidate list is shown in the terminal.

## Vim-style operation

`vim FILE` or `vi FILE` opens the editor screen.
`view FILE` opens the file in read-only mode.

### Read-only open (view)

When you open a file with `view FILE`, the buffer is editable, but plain `:w` / `:wq` / `:x` / `Ctrl+S` are rejected with `E45: 'readonly' option is set (add ! to override)`.
To save anyway, use the bang-suffixed forms `:w!` / `:wq!` / `:x!`.

When you enter INSERT mode, the status line shows `W10: Warning: Changing a readonly file`.
The mode badge is rendered in red while in NORMAL mode, and the meta info on the top right also shows `view`.

For Vim compatibility, the readonly flag stays on even after `:w!` succeeds.
To return a file opened with `view` to a fully writable state, quit the editor and reopen it with `vim FILE`.

### Mode switching

- `i`: enter INSERT at the cursor
- `a`: enter INSERT just after the cursor
- `A`: enter INSERT at the end of the line
- `I`: enter INSERT at the first non-blank of the line
- `o`: open a new line below and enter INSERT
- `O`: open a new line above and enter INSERT
- `Esc`: return to NORMAL

### Ex commands

- `:w` / `:write`: save
- `:w FILE`: save to a different file and switch to it
- `:w!`: force-save a file opened with `view`
- `:saveas FILE` / `:sav FILE`: save as a new file and switch to it
- `:saveas! FILE` / `:sav! FILE`: save as (skip the overwrite confirmation)
- `:q`: quit (refused if there are unsaved changes)
- `:wq` / `:x`: save and quit
- `:wq!` / `:x!`: force-save a file opened with `view` and quit
- `:q!`: discard changes and quit
- `:e!`: reload from disk
- `:set number` / `:set nu`: show line numbers
- `:set nonumber` / `:set nonu`: hide line numbers
- `:set wrap`: wrap long lines at the screen width (default)
- `:set nowrap`: do not wrap; use horizontal scroll instead
- `:diff`: diff between the saved content and the current buffer
- `:diff FILE`: diff between the current buffer and another file

### Search

- `/word`: search forward
- `?word`: search backward
- `n`: next match
- `N`: previous match

### Movement

- `h` `j` `k` `l`: left, down, up, right
- `0`: beginning of line
- `$`: end of line
- `gg`: top of buffer
- `G`: bottom of buffer (`5G` jumps to line 5)
- Numeric prefixes such as `20l` / `5h` / `10j` / `3k` are supported
- `l` / `→` stops at the end of the line and does not wrap to the next line

### Editing (NORMAL mode)

- `x`: cut the character under the cursor
- `dd`: cut one line (`3dd` cuts three)
- `yy` / `Y`: yank one line (`4yy` yanks four)
- `p`: paste after / below the cursor
- `P`: paste before / above the cursor
- `u`: simple undo
- `Ctrl+S`: save

`x` / `dd` / `yy` / `Y` store into an internal register, and `p` / `P` paste from it. When the environment allows, the system clipboard is updated as well. In NORMAL mode, a blinking block cursor marks the current position.

## Character encoding and line endings

On read, UTF-8 / UTF-16LE / UTF-16BE / Shift_JIS / EUC-JP are detected with a simple heuristic.
On save, UTF-8 / UTF-16LE / UTF-16BE are supported.
Files detected as Shift_JIS or EUC-JP fall back to UTF-8 when saved.

Line endings are detected as LF / CRLF / CR on read and preserved on save.
You can change them in the editor with the following short commands:

```vim
:set ff=unix
:set ff=dos
:set ff=mac
:set fenc=utf-8
:set bomb
:set nobomb
```

## Notes

- This software is provided as-is, with no guarantees about correctness or availability. The author accepts no liability for any damage arising from its use. Use at your own risk.
- This repository does not currently accept external pull requests.
- Through the File System Access API, the app reads, writes, and deletes files under the folder you choose.
  `rm -r`, `mv -f`, `cp -f`, and overwrite-on-save are not undoable.
- A dedicated working folder is recommended; avoid using important folders or sync folders.

## PRIVACY

Files and input are not sent anywhere by the app itself.
`connect-src 'none'` blocks `fetch` and other network requests at the browser level. You can verify this in the DevTools Network tab.

Files and input are not stored in `localStorage` or `IndexedDB`.
They live only inside the folder you select via the File System Access API.
UI settings and a re-access record for the previous folder (folder name and handle) are stored locally.

The app itself contains no analytics or tracking (the `connect-src 'none'` CSP blocks any outbound request anyway).

On the other hand, the author profile and the Tsui series landing page use Cloudflare Web Analytics for visit counts (no cookies, no fingerprinting, no cross-site tracking).

Note that the hosting platform (origin server, CDN, etc.) may collect logs separately.

## License

[MIT License](LICENSE)

© 2026 Hajime Tsui

## Third-party

None. No external modules.

---

## Announcements

## Author

[Hajime Tsui](https://hajimetwi3.github.io/hajimetwi3/)

# Archive File Formats

## gzip

`gzip` compresses a single file at a time — it doesn't bundle multiple files together into one archive.

```bash
gzip wordlist.txt        # produces wordlist.txt.gz, removes the original
gunzip wordlist.txt.gz   # reverses it, restoring wordlist.txt
```

## tar

`tar` bundles multiple files into one archive file, but — on its own — does **not** compress anything.

```bash
tar cvf archive.tar wordlist.txt wordlist2.txt   # create
tar xvf archive.tar                              # extract
```

| Flag | Meaning |
|---|---|
| `c` | Create a new archive |
| `v` | Verbose — show what's happening |
| `f` | Specifies the archive filename |
| `x` | Extract the archive |

Since `tar` alone doesn't compress, a `.tar` file is commonly compressed afterward with `gzip` — or in one step, by adding the `z` flag: `tar czvf archive.tar.gz files...` creates a compressed archive directly, and `tar xzvf archive.tar.gz` extracts it. That combined `.tar.gz` (sometimes shortened to `.tgz`) is one of the most common archive formats you'll encounter, precisely because it pairs `tar`'s multi-file bundling with `gzip`'s compression.

## ZIP

Unlike `gzip` or plain `tar`, ZIP handles both archiving *and* compression together, and works on multiple files directly:

```bash
zip archive.zip wordlist.txt wordlist2.txt   # create
unzip archive.zip                            # extract
```

## RAR

RAR is another combined archive/compression format, available on Linux once the corresponding tools are installed:

```bash
rar a archive.rar wordlist.txt wordlist2.txt   # add files to a new/existing archive
unrar e archive.rar                            # extract
```

| Flag | Meaning |
|---|---|
| `a` | Add files to the archive |
| `e` | Extract the archive |

## Comparing the Formats

| Format | Create | Extract | Compresses? | Multiple Files at Once? |
|---|---|---|---|---|
| gzip | `gzip file` | `gunzip file.gz` | Yes | No |
| tar | `tar cvf archive.tar files...` | `tar xvf archive.tar` | No (needs `z` or a separate tool) | Yes |
| tar.gz | `tar czvf archive.tar.gz files...` | `tar xzvf archive.tar.gz` | Yes | Yes |
| zip | `zip archive.zip files...` | `unzip archive.zip` | Yes | Yes |
| rar | `rar a archive.rar files...` | `unrar e archive.rar` | Yes | Yes |

The core distinction worth keeping straight: **compression** shrinks data; **archiving** bundles multiple files into one. `gzip` only compresses (one file); plain `tar` only archives (no compression); ZIP and RAR do both at once. None of these tools are guaranteed to be pre-installed on every Linux system — `unrar` in particular is often missing by default and needs installing (file 16 covers exactly how).

## Related: File 16 (Package Management) for installing an archive tool that isn't already present; file 04 for the everyday file commands these formats build on.

## Cheat Sheet

```
gzip file        → file.gz     (compress, single file)     gunzip file.gz   → decompress
tar cvf a.tar f1 f2   → bundle, no compression               tar xvf a.tar    → extract
tar czvf a.tar.gz f1 f2 → bundle + compress in one step      tar xzvf a.tar.gz → extract
zip a.zip f1 f2   → bundle + compress                        unzip a.zip      → extract
rar a a.rar f1 f2 → bundle + compress                        unrar e a.rar    → extract

Compression = shrink data.  Archiving = bundle multiple files into one.
gzip: compress only.  tar: archive only.  zip/rar: both.
```

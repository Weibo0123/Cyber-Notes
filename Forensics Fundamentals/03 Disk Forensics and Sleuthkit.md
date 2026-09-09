# 03 Disk Forensics and Sleuthkit

## Why disk analysis is its own skill

One of the most fundamental skills for a forensics analyst is inspecting and understanding disks — either real hardware or, more commonly in a CTF, a disk image (a dumped file representation of a disk). There are GUI tools out there (like Autopsy) for managing digital evidence more broadly, but they're essentially front-ends over the same lower-level command-line tools covered in this chapter. Learning the underlying tools directly makes it much clearer exactly which layer of the disk you're operating on at any given moment — and with disk analysis, the details are everything.

## The four layers of a disk image

A disk image is, at bottom, just a huge dump of numbers — but that data isn't unstructured. There's an invisible structure layered on top of it that gives those numbers meaning. Parsing that structure by hand is extremely tedious; the Sleuthkit tools exist to automate exactly that. To use them well, you need to understand the four layers of a disk image, from lowest to highest:

1. **Media layer:** the lowest layer. Tools here are prefixed `mm` and operate on the whole image with minimal guidance from the analyst. This layer provides entry-point information — like what partitions exist and where they start — but doesn't reveal much about the actual data inside. Representative tool: `mmls` (lists the partition table).
2. **Block layer:** the second-lowest layer. Tools are prefixed `blk`. A disk image's raw numbers are divided into equal-sized chunks called blocks; a single file typically spans multiple blocks. Representative tool: `blkcat` (outputs the contents of one specific block).
3. **Inode layer:** the "bookkeeping" layer of a disk image. Think of it like a book's table of contents: inode numbers are like chapter numbers, and blocks are like page numbers. Tools are prefixed `i`. Representative tool: `icat` (outputs the file corresponding to a given inode number).
4. **Filename layer:** the layer closest to how ordinary users actually experience a filesystem — filenames, directories. Tools are prefixed `f`. Representative tool: `fls` (lists files/directories starting from the root, along with each one's inode number).

These four layers form a progression from "how the machine actually stores data" to "how a human perceives it": media tells you where partitions live, block is the rawest data unit, inode maps those blocks to files, and filename is the human-friendly names/directories view.

## Standard workflow

### 1. Identify the file type, extract if needed

Same as Chapter 02: run `file` on the image first, and decompress it if it's compressed (e.g. `.gz`):

```
$ gunzip disk_image.img.gz
```

### 2. Find the partition table with `mmls`

```
$ mmls disk_image.img
```

The output lists several partition "slots," each with a starting sector (Start), ending sector (End), length (Length), and a description (e.g. `Linux (0x83)`, `Linux Swap`, `Unallocated`).

**How to spot the "main" partition:** generally the one with the largest length that's clearly a filesystem partition (not swap, not unallocated space). Note down its **Start** value — every subsequent tool call needs that offset.

### 3. List the root directory with `fls`

```
$ fls -o start_sector disk_image.img
```

The `-o` flag specifies the offset (the Start value you noted in step 2). This lists the files/directories at the root of that partition, with each entry's **inode number** shown alongside its name.

Once you see a set of standard Linux directories (`etc`, `usr`, `var`, ...), forensic intuition comes into play: which directories are system-managed and rarely touched by users (`etc`, `var`), and which are places a user is actually likely to have left traces (`home`, `root`)? Prioritize the latter.

Running `fls` with no arguments at all just prints a short usage summary — a common (though not universal) behavior for command-line tools, worth trying if you're ever unsure how to invoke something. In that usage text, anything in square brackets `[ ]` is optional, and anything outside them is mandatory. If no inode is given, `fls` defaults to listing the root directory.

### 4. Drill into subdirectories using `fls` with an inode

Once you have a directory's inode number, you can pass it directly to `fls` without actually "entering" that directory:

```
$ fls -o start_sector disk_image.img target_inode
```

An empty result means the directory really is empty — not that something went wrong. Keep repeating this on directories of interest, drilling progressively deeper, until you land on the inode of the target file.

### 5. Read the file's contents with `icat`

Once you have the target file's inode number:

```
$ icat -o start_sector disk_image.img target_inode
```

This prints the contents of the file that inode points to, directly.

### A gotcha: deleted files

Sometimes `fls` will list a file whose inode has since been reassigned to a completely different, unrelated file — this typically happens after the original file was deleted. Running `icat` on that inode won't give you what you're looking for; it'll give you whatever file currently occupies that inode number. If you hit this, it means the target file has been deleted, and you'd need dedicated file-recovery / unallocated-space analysis techniques (outside the scope of this chapter) — or look for another, still-intact file to continue with.

## Related: Chapter 02 (File and String Searching) covers search techniques at the ordinary filesystem level; this chapter goes "one level deeper" by operating directly on the disk image itself.

---

## Self-Test

1. What's the correct order of the four layers, from lowest to highest? What Sleuthkit tool prefix corresponds to each one?
2. Why can't you just pick the partition with the largest length from `mmls`'s output and call it the main partition — what else do you need to check?
3. You listed a file with `fls`, but `icat` on its inode returns content that clearly isn't right. What's the most likely explanation?

## Cheat Sheet

| Layer | Tool prefix | Representative tool | Purpose |
|---|---|---|---|
| Media | `mm` | `mmls` | List the partition table; find the partition's starting offset |
| Block | `blk` | `blkcat` | Output the contents of one specific block |
| Inode | `i` | `icat` | Output a file's contents given its inode number |
| Filename | `f` | `fls` | List files/directories and their inode numbers |

**Standard flow:** `file`/decompress → `mmls` to find the partition offset → `fls -o offset image` to see the root → drill down by inode with repeated `fls` calls → once you have the target inode, `icat` to read the contents.

**Parameter to remember:** `-o` is the offset (from `mmls`'s Start value); `fls`/`icat` default to the root directory / whole image if no inode is given.

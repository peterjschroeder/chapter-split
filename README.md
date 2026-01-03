# chapter-split
A basic utility to extract chapters from any media format supported by ffmpeg

## How to use it?

Try:

```bash
python3 chapterSplit.py --help
```

There are only three options: `-c`, `-o` and `-t`.
`-c` takes a list of chapters to extract and `-o` takes the output directory path.
The output directory does not have to exist.

`-t` can be used to specify chapters manualy by suppling consecutive durations
and can be combined with `-c` to skip some parts of the input file.

`-c`, `-o` and `-t` are optional - only the input file has to be provided as a positional argument:

```bash
chapter-split inputFile.mp4
```

## Requirements

- `ffmpeg` and `ffprobe`
- `python3` (only standard library is used)
- The input file, which needs to have chapters

## What results to expect?

The script will extract all chapters (or all selected chapters) into separate files named after the chapter title.
The files will be placed in the target directory. 
The extension is derrived from the input file.
The default output directory is the current directory.

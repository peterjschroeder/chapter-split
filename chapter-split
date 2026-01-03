#!/usr/bin/python3

import argparse
import json
import os
import subprocess

APP_DESCRIPTION = \
    """
A basic utility to extract chapters from any media format supported by ffmpeg
"""

CWD = os.path.abspath(os.path.curdir)

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description=APP_DESCRIPTION)
    parser.add_argument(
        "input_file", metavar="FILE",
        help="Input file to be split"
    )
    parser.add_argument(
        "-o", "--output-dir", metavar="DIR", default=CWD,
        help="Output directory. Defaults to current directory"
    )
    parser.add_argument(
        "-c", "--chapters", metavar="CHAPTERS", default=None, nargs="*", type=int,
        help="List of chapters (counted from 0) to extract, " +
        "separated with spaces. Defaults to all chapters. " +
        "Negative values are counted from the end of the file."
    )
    parser.add_argument(
        "-t", "--manual-chapters", metavar="TIME", default=None, nargs="*",
        help="List of chapter durations separated with spaces. " +
        "Overrides the chapter marks found in the input file. " +
        "Accepts number of seconds or time in format: `HH:MM:SS.SSS` " +
        "Can be used to extract chapters from a video with no chapter marks. " +
        "Can be combined with `-c`"
    )
    args = parser.parse_args()
    # Check supplied paths
    if os.path.isfile(args.output_dir):
        raise Exception("Output path cannot be a file")
    if not os.path.exists(args.input_file):
        raise Exception("Input path does not exist")
    if not os.path.isfile(args.input_file):
        raise Exception("Input path is not a file")
    if not os.path.exists(args.output_dir):
        os.makedirs(args.output_dir)
    inputFileExtension = os.path.basename(args.input_file.split(".")[-1])
    # Check if chapters are manually specified
    if args.manual_chapters is None:
        res = subprocess.run(["ffprobe", "-hide_banner", "-i", args.input_file,
                              "-print_format", "json", "-show_chapters"],
                             capture_output=True, text=True, check=True)
        chapters = json.loads(res.stdout)["chapters"]
    else:
        mChapters = args.manual_chapters
        for chapter in mChapters:
            if ":" in chapter:
                # Convert time to seconds
                chapter2 = 0
                length = len(chapter.split(":"))
                if length != 3:
                    raise Exception("Invalid time format")
                for i in range(length):
                    chapter2 += float(chapter.split(":")[i]) * 60 ** (2 - i)
                mChapters[mChapters.index(chapter)] = chapter2
            else:
                mChapters[mChapters.index(chapter)] = float(chapter)
        chapters = [{"start_time": "0", "end_time": mChapters[0], "tags": {
            "title": "Chapter_1"}}]
        for i in range(1, len(args.manual_chapters)):
            chapters.append({
                "start_time": chapters[-1]["end_time"],
                "end_time": chapters[-1]["end_time"] + mChapters[i], "tags": {"title": "Chapter_" + str(i + 1)}
            })
    # Select chapters to extract if needed
    if args.chapters is not None:
        chapters = [chapters[index] for index in args.chapters]
    for chapter in chapters:
        subprocess.run(
            [
                "ffmpeg", "-hide_banner", "-i", args.input_file,
                "-ss", str(chapter["start_time"]),
                "-to", str(chapter["end_time"]),
                "-c", "copy",
                "-map", "0:v?",
                "-map", "0:a?",
                "-map", "0:s?",
                os.path.join(args.output_dir,
                             chapter["tags"]["title"] + "." + inputFileExtension)
            ],
            check=True)

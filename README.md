# Remsi
Remove silence from video files with a one-line ffmpeg command.

# Why

Apparently it's not possible to automatically remove silent parts from a video file in one go with `ffmpeg`. The `silencedetect` audio filter only *detects* silence, and the `silenceremove` filter is an *audio filter* that only works on audio and doesn't remove corresponding video parts.

# How

This takes as input the output of `silencedetect`, and writes the corresponding audio **and video** select filters for ffmpeg. When executed, the command produces a video file with the silent parts removed.

# Install & Use

Requires ffmpeg, Python3. Usage is as follows:

    ffmpeg -i YOURINPUTFILE -hide_banner -af silencedetect=n=-50dB:d=1 -f null - 2>&1 | python remsi.py > COMMANDFILENAME

[silencedetect filter](https://ffmpeg.org/ffmpeg-filters.html#silencedetect) accepts a noise level in dB and a minimum duration in seconds.

YOURINPUTFILE is the name of your input file (video or audio file: 'noise_a.mp4' for example).

COMMANDFILENAME is the name of the file you want to write the ffmpeg command to. After the execution of the script, it will contain an ffmpeg command such as (for example):

    ffmpeg -i noise_a.mp4 -vf "select='between(t,0,2.00093)+between(t,4.00009,6.00256)+between(t,7.99961,9.99989)+between(t,12.0001,13.9998)',setpts=N/FRAME_RATE/TB" -af "aselect='between(t,0,2.00093)+between(t,4.00009,6.00256)+between(t,7.99961,9.99989)+between(t,12.0001,13.9998)',asetpts=N/SR/TB" outfile_noise_a.mp4

This is actually a succession of [select filters](https://ffmpeg.org/ffmpeg-filters.html#select_002c-aselect). Once run, it will output (reencode) a video file with the silent parts removed.

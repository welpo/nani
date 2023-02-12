# About
`nani` is a bash script to easily share files in your server.

It accepts local files as input as well as URLs. Read more about the process in the **Usage** and **Input handling** sections (or read the script).

# Dependencies
You will need a properly set up web server of your choosing (like Apache, IIS or nginx) and the basic utilities most GNU/Linux distributions include: the GNU Core Utilities (`cp`, `mv` `mktemp`...), the GNU C Library to use `file`, `bash`...

**Optional dependencies**
* `zip` to compress/store directories.
* `lame`, `flac` and `metaflac` to transcode FLAC to MP3.
* [youtube-dl](https://github.com/rg3/youtube-dl) to download videos from several sites.
* `xclip` to get the URL to your local clipboard
* [exiftool](http://owl.phy.queensu.ca/~phil/exiftool/) to remove metadata from certain files (`pdf`, `jpg`, `png`, `mp4`...) — this will only happen when a new copy is created (so when alias=0).
* [optipng](http://optipng.sourceforge.net/) to losslessly compress `png`, `bmp`, `gif`, `pnm` and `tiff` files — this will only happen when a new copy is created (so when alias=0).
* `notify-send` to get a local notification (useful if used in a desktop).
* `paplay` to play a notification sound after the upload is done.

# Install
* Place `nani` somewhere in your PATH, like `~/bin`
* Make it executable: `chmod +x nani`

## Mandatory configuration
Either by modifying the first lines of the script or changing `nanirc`:
* Set the `local_directory` variable to the public path in your webserver (e.g. `/var/www/site/nani`). The users using nani must have write permissions to this folder (you can do `sudo chown [username] /var/www/site/nani` and `sudo chmod 755 /var/www/site/nani`);
* Add the user that will use `nani` to your webserver's user's group. For example, nginx runs as the `www-data` user and group. Thus, you need to add your user to this group so that it can serve its files: `sudo usermod -a -G www-data [username]`;
* Set the `url_format` (the URL that refers to this path) variables in the first lines of the script (e.g. `https://example.com/nani`).

**Optional**

* Add a cron task to automatically remove files older than 1 day
    * `crontab -e` and add this line, which performs the check every hour:
    * `00 * * * *      find "/path/to/nani/ -mindepth 1 ! -path '*/k/*' -ctime +0 ! -name "index.html" ! -name "k" -delete`
* Set up everything else to your liking.

# Usage

To share a picture (or any other file) in your server you would run:

`nani Path/To/picture.png`

The output would be a URL referring to the file (which `nani` would have copied, hard linked or symbolic linked):

`https://example.com/nani/oThot0b.png`

To share a directory with files you would run this:

`nani Path/To/Directory`

and `nani` would return a URL to the zipped contents of that directory, like:

`https://example.com/nani/iE4RWE.zip`

If you want to keep the name of the input, you can use the `-o`, `--original` flag, so: `nani -o Path/To/Directory`. Read more about the available flags at the bottom of this page.

Different files will be processed differently (for example it can transcode .flac to .mp3 before sharing), read more about the process below, under the section **Input handling**.

# Input handling
`nani` will process input based on its nature:

- **Directory**: A zip file will be created in the output directory (default). It's also possible to create a symbolic link to browse the files inside. (e.g. input: `nani Directory/` output: `https://example.com/nani/62WlrM.zip`)
- **FLAC**: Can be transcoded to MP3 (e.g. input: `nani "01 - Title.flac"` output: `https://example.com/nani/nO0A5v.mp3`)
- **Text** (`html`, `php`...): Extension can be set to `.txt` (e.g. input: `nani index.php` output: `https://example.com/nani/4PwTwgL.txt`)
- **Other files**: New copy/hard link/symbolic link at output directory (e.g. input: `nani movie.mkv` output: `https://example.com/nani/ki3msYn.mkv`)
- **URL to video** (e.g: youtube): Downloaded using [youtube-dl](https://github.com/rg3/youtube-dl) (e.g. input: `nani https://www.youtube.com/watch?v=p4cJv6s_Yjw` output: `https://example.com/nani/uhpPcm.mp4`)
- **Other URLs**: Downloaded using wget (e.g. input: `nani https://www.gnu.org/distros/free-distros.html` output: `https://example.com/nani/o80togB.html`)

# Settings
To modify these settings modify the script file, `nani`, using your favourite text editor.

- `add_txt` Add `.txt` to files with no extension. Default is 1 (Yes).
- `hardlink` Create hard links instead of new copies. Default is 1 (Yes).
- `local_directory` Local directory where files will be stored (e.g. your public_html folder)
- `minimum_length` Minimum number of characters for the output name. Default is 5.
- `notify` Send a notification using `notify-send` when nani is done. Makes sense if using on a desktop (using SSHFS as the output directory, for example). Default is 1 (On), but it will be set to 0 if `notify-send` is not installed in the system.
- `modify_text` If set to 1 (default) it will modify the extension of files storing text (`.php`, `.html`, `.tex`...) to `.txt`.
- `post_process` Call exiftool & optipng to remove metadata and compress files, only on new copies. Default is 1 (Post-process).
- `push` Send push notification (through Pushbullet and/or Pushover) with the URLs. Default is 0 (Don't send them).
- `pushbullet_token` Your Pushbullet access token. Leave empty if you don't want to use Pushbullet.
- `pushover_key` Your Pushover key. Leave empty if you don't want to use Pushover.
- `quiet` If set to 1 it will only output the URL. Default is 1 (be quiet).
- `random_length` Maximum number of extra characters the length will have (the final length will be a number between minimum_length and minimum_length+random_length). Default is 2, can be left empty to always have the same string length.
- `sshfs` Same as `symbolic` except it won't use symbolic links for directories (useful for SSHFS mounted output directories)
- `symbolic` Create symbolic links instead of new copies/hard links. Default is 0 (Disabled).
- `transcode` Transcode FLAC files to MP3 V0? Default is 1 (Yes). Requires `metaflac`, `flac` and `lame`.
- `url_format` URL that directs to the directory set in `local_directory`
- `xclip` Send the URLs to `xclip` to get them in your local clipboard. Requires `xclip` and the -X flag set on the SSH session. Default is 1 (Enabled).

# Arguments
Some of the settings described above can be modified without editing the script, just use the available flags:

```
    -a, --alias      Revert the hard link setting
    -c, --cleanup    Remove all files on /nani/ (except index.html)
    -h, --help       Display help and exit
    -i, --insert     Open nano to enter text. Saved in output directory as .txt
    -k, --keep       Output dir becomes /nani/k/, to set different cleanup rules
    -l, --list       List files in output directory /nani/
    -n, --name       Use custom name (e.g. nani --name=DesiredName <file> or nani -n DesiredName <file>)
    -N, --notify     Revert the notify option
    -o, --original   Preserve original file name
    -p, --push       Send push notification
    -q, --quiet      Reverse the quiet setting
    -s, --string     Force a certain string length (e.g. nani --string=32 <file> or nani -n 32 <file>)
    -t, --transcode  Revert the transcode setting
    -x, --xclip      Revert the xclip setting
    -y, --symbolic   Create a symbolic link for files and directories
```

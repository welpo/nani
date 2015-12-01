# About
`nani` is a bash script which will provide public URLs from the input (local files or other URLs). By default it will create a hard link of the local file to your www directory and provide the matching URL.

# Install
* Place `nani` somewhere in your PATH, like `~/bin`
* Make it executable: `chmod +x nani`
* Modify the `local_directory` and `url_format` variables in the first lines of the script.

**Optional**

* Set up everything else to your likings.
* Add a cron task to automatically remove files older than 1 day
    * `crontab -e` and add this line, which performs the check every hour:
    * `00 * * * *      find "${HOME}"/www/"$(whoami)"."$(hostname -f)"/public_html/nani/ -mindepth 1 ! -path '*/k/*' -ctime +0 ! -name "index.html" ! -name "k" -delete`

# Input handling
`nani` will process input based on its nature:

- Directory: Will be stored using `zip` (or symbolic link)
- FLAC: Can be transcoded to MP3
- Text (`html`, `php`...): Extension can be set to `.txt`
- Other files: New copy/hard link/symbolic link at output directory
- URL to video (e.g: youtube): Downloaded using [youtube-dl](https://github.com/rg3/youtube-dl)
- Other URLs: Downloaded using wget

# Settings
* `add_txt` Add `.txt` to files with no extension? Default is 1 (Yes).
* `hardlink` Create hard links instead of new copies. Default is 1 (Yes).
* `local_directory` Local directory where files will be stored (e.g. your public_html folder)
* `minimum_length` Minimum number of characters for the output name. Default is 5.
* `modify_text` If set to 1 (default) it will modify the extension of files storing text (`.php`, `.html`, `.tex`...) to `.txt`.
* `push` Send push notification (through Pushbullet and/or Pushover) with the URLs. Default is 0 (Don't send them).
* `pushbullet_token` Your Pushbullet access token. Leave empty if you don't want to use Pushbullet.
* `pushover_key` Your Pushover key. Leave empty if you don't want to use Pushover.
* `quiet` If set to 1 it will only output the URL. Default is 1 (be quiet).
* `random_length` Maximum number of extra characters the length will have (the final length will be a number between minimum_length and minimum_length+random_length). Default is 2, can be left empty to always have the same string length.
* `sshfs` Same as `symbolic` except it won't use symbolic links for directories (useful for SSHFS mounted output directories)
* `symbolic` Create symbolic links instead of new copies/hard links. Default is 0 (Disabled).
* `transcode` Transcode FLAC files to MP3 V0? Default is 1 (Yes). Requires `metaflac`, `flac` and `lame`.
* `url_format` URL that directs to the directory set in `local_directory`
* `xclip` Send the URLs to `xclip` to get them in your local clipboard. Requires `xclip` and the -X flag set on the SSH session. Default is 1 (Enabled).

# Arguments
Some of the settings described above can be modified without editing the script, just use the available flags:

```
    -a, --alias      Revert the hard link setting
    -c, --cleanup    Remove all files on /nani/ (except index.html)
    -h, --help       Display help and exit
    -i, --insert     Open nano to enter text. Saved in output directory as .txt
    -k, --keep       Output dir becomes /nani/k/, to set different cleanup rules
    -l, --list       List files in output directory /nani/
    -n, --name=      Use custom name (e.g. nani -n DesiredName <file>)
    -o, --original   Preserve original file name
    -p, --push       Send push notification
    -q, --quiet      Force quiet mode
    -s, --string=    Force a certain string length (e.g. nani -s 32 <file>)
    -t, --transcode  Revert the transcode setting
    -x, --xclip      Revert the xclip setting
    -y, --symbolic   Create a symbolic link for files and directories
```

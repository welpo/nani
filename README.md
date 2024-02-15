<p align="center">
    <img src="https://raw.githubusercontent.com/welpo/nani/master/logo.png" width="300" alt="nani logo: a cute character coming out of a box">
    <br><br>
    <a href="https://github.com/welpo/nani/stargazers">
        <img src="https://img.shields.io/github/stars/welpo/nani?style=flat-square&labelColor=202b2d&color=47605a" alt="Stars"></a>
    <img src="https://img.shields.io/github/languages/top/welpo/nani?style=flat-square&labelColor=202b2d&color=47605a" alt="Top language"></a>
    <img src="https://img.shields.io/github/languages/code-size/welpo/nani?style=flat-square&labelColor=202b2d&color=47605a" alt="Code size"></a>
    <br>
    <a href="https://github.com/welpo/git-sumi">
        <img src="https://img.shields.io/badge/clean_commits-git--sumi-0?style=flat-square&labelColor=202b2d&color=47605a" alt="Clean commits"></a>
    <a href="https://github.com/welpo/nani/blob/main/COPYING">
        <img src="https://img.shields.io/github/license/welpo/nani?style=flat-square&labelColor=202b2d&color=47605a" alt="License"></a>
</p>

<h1 align="center">nani</h1>

<h3 align="center">The easiest way to share files in your server</h3>

## ✨ Features

- [X] **Universal file support**: Seamlessly share local files, directories, or URLs.
- [X] **Auto-generated unique URLs**: instantly provides web-accessible links for shared content.
- [X] **Efficient file management**: uses copying, hard linking, or symbolic linking to optimise server storage.
- [X] **Configurable via `nanirc`**: allows easy customization without script modifications.

## 📋 Requirements

* A working web server (e.g., Apache, Nginx)
* `bash`

## 🚀 Install

* Place `nani` somewhere in your PATH, like `~/bin`
* Make it executable: `chmod +x nani`

```bash
curl -Lo ~/bin/nani https://github.com/welpo/nani/raw/main/nani
chmod +x ~/bin/nani
```

## ⚙️ Mandatory configuration

To configure `nani`, you can modify the script directly or use a `nanirc` file for easier customization and updates.

### Using `nanirc`

1. Create a file named `nanirc` in your home directory under `.config/nani/` for user-specific settings or in `/etc/` for system-wide settings.
2. Set the following variables according to your environment:
   * `local_directory`: The path to the directory on your web server where files will be stored.
   * `url_format`: The corresponding URL to access `local_directory` over the web.

Example `nanirc` content (see [default nanirc](./nanirc)):

```bash
local_directory="/var/www/site/nani"
url_format="https://example.com/nani"
```

Save the file and `nani` will automatically use these settings.

### Setting permissions and user groups

nani users must have write permissions to `local_directory` and be part of the web server's user group to serve the files.

```bash
sudo chown [username]:[group] /var/www/site/nani
sudo chmod 755 /var/www/site/nani
```

Replace `[username]` with your user and `[group]` with an appropriate group (e.g., your user's primary group or a web server group like `www-data`).

Replace `/var/www/site/nani` with the path to your `local_directory`.

## 👷 Usage

To share a picture (or any other file) in your server, run:

`nani Path/To/picture.png`

`nani` will return a URL to the file:

`https://example.com/nani/oThot0b.png`

To share a directory with files you would run this:

`nani Path/To/Directory`

and `nani` would return a URL to the zipped contents of that directory, like:

`https://example.com/nani/iE4RWE.zip`

If you want to keep the name of the input, you can use the `-o`, `--original` flag, so: `nani -o Path/To/Directory`. Read more about the available flags at the bottom of this page.

Different files will be processed differently (for example it can transcode .flac to .mp3 before sharing), read more about the process below, under the section **Input handling**.

## 🔄 Input handling

`nani` will process input based on its nature:

- **Directory**: A zip file will be created in the output directory (default). It's also possible to create a symbolic link to browse the files inside. (e.g. input: `nani Directory/` output: `https://example.com/nani/62WlrM.zip`)
- **FLAC**: Can be transcoded to MP3 (e.g. input: `nani "01 - Title.flac"` output: `https://example.com/nani/nO0A5v.mp3`)
- **Text** (`html`, `php`...): Extension can be set to `.txt` (e.g. input: `nani index.php` output: `https://example.com/nani/4PwTwgL.txt`)
- **Other files**: New copy/hard link/symbolic link at output directory (e.g. input: `nani movie.mkv` output: `https://example.com/nani/ki3msYn.mkv`)
- **URL to video** (e.g: youtube): Downloaded using [youtube-dl](https://github.com/rg3/youtube-dl) (e.g. input: `nani https://www.youtube.com/watch?v=p4cJv6s_Yjw` output: `https://example.com/nani/uhpPcm.mp4`)
- **Other URLs**: Downloaded using wget (e.g. input: `nani https://www.gnu.org/distros/free-distros.html` output: `https://example.com/nani/o80togB.html`)

## 🔧 Settings

Set up `nani` to your liking by modifying the script or using a `nanirc` file.

The following settings are available:

- `add_txt`: Add `.txt` to files with no extension. Default is 1 (Yes).
- `hardlink`: Create hard links instead of new copies. Default is 1 (Yes).
- `local_directory`: Local directory where files will be stored (e.g. your public_html folder)
- `minimum_length`: Minimum number of characters for the output name. Default is 5.
- `notify`: Send a notification using `notify-send` when nani is done. Makes sense if using on a desktop (using SSHFS as the output directory, for example). Default is 1 (On), but it will be set to 0 if `notify-send` is not installed in the system.
- `modify_text`: If set to 1 (default) it will modify the extension of files storing text (`.php`, `.html`, `.tex`...) to `.txt`.
- `post_process`: Call exiftool & optipng to remove metadata and compress files, only on new copies. Default is 1 (Post-process).
- `push`: Send push notification (through Pushbullet and/or Pushover) with the URLs. Default is 0 (Don't send them).
- `pushbullet_token`: Your Pushbullet access token. Leave empty if you don't want to use Pushbullet.
- `pushover_key`: Your Pushover key. Leave empty if you don't want to use Pushover.
- `quiet`: If set to 1 it will only output the URL. Default is 1 (be quiet).
- `random_length`: Maximum number of extra characters the length will have (the final length will be a number between minimum_length and minimum_length+random_length). Default is 2, can be left empty to always have the same string length.
- `sshfs`: Same as `symbolic` except it won't use symbolic links for directories (useful for SSHFS mounted output directories)
- `symbolic`: Create symbolic links instead of new copies/hard links. Default is 0 (Disabled).
- `transcode`: Transcode FLAC files to MP3 V0? Default is 1 (Yes). Requires `metaflac`, `flac` and `lame`.
- `url_format`: URL that directs to the directory set in `local_directory`
- `xclip`: Send the URLs to `xclip` to get them in your local clipboard. Requires `xclip` and the -X flag set on the SSH session. Default is 1 (Enabled).

## 🚩 Arguments

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

## ♻️ Automating cleanup

You can automate the cleanup of old files by using a cron job.

Run `crontab -e` and add this line, which performs the check every hour:

```bash
0 * * * *  find "/var/www/site/nani/ -mindepth 1 ! -path '*/k/*' -ctime +0 ! -name "index.html" ! -name "k" -delete`
```

This will remove all files in `/var/www/site/nani/` that are older than 24 hours, except for those in the `/var/www/site/nani/k/` directory.

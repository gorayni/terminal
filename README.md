# Terminal cheatsheet

## Conda commands

### List manually installed packages

```Shell
conda env export --from-history
```

### List revisions of installed packages in an environment 

```Shell
conda list --revisions
```

### Revert to a previous revision 

```Shell
conda install --revision N
```

NOTE: conda install --rev 0 will remove your root environment and the conda command itself

## Files transfer

Copy long file over flaky connection [[original]](https://superuser.com/questions/302842/resume-rsync-over-ssh-after-broken-connection)

```Shell
while [ 1 ]
do
    rsync -avz --partial source dest
    if [ "$?" = "0" ] ; then
        echo "rsync completed normally"
        exit
    else
        echo "Rsync failure. Backing off and retrying..."
        sleep 10
    fi
done
```

## GIT

Using meld to see all modifications in the working directory [[original]](https://www.programming-books.io/essential/git/using-meld-to-see-all-modifications-in-the-working-directory-bb8d0be146054d40a352d1795333e858)

```Shell
git difftool -t meld --dir-diff
```

Show the differences between 2 specific commits [[original]](https://www.programming-books.io/essential/git/using-meld-to-see-all-modifications-in-the-working-directory-bb8d0be146054d40a352d1795333e858):

```Shell
git difftool -t meld --dir-diff  [COMMIT_A] [COMMIT_B]
```

### Add changes to previous commit

```Shell
git add modified.file

git commit --amend --no-edit

git push -f origin some_branch
```

## SSH

### SSH Keys

#### External computer configuration

1. **Make SSH-KEYS**. if you already have an SSH key, you can skip this step. Just hit Enter for the key and both passphrases:

```Shell
localhost$ ssh-keygen -t rsa -b 2048

Generating public/private rsa key pair.
Enter file in which to save the key (/home/username/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/username/.ssh/id_rsa.
Your public key has been saved in /home/username/.ssh/id_rsa.pub
```

2. **Add ~/.ssh/config**. Add the following configuration to the ~/.ssh/config file. Please make sure to replace with your correct info

```
Host SERVER_ALIAS
    HostName SERVERs_IP_or_URL
    Port 22
    User YOUR_USERNAME_AT_SERVER
```

3. **Copy your keys to the server**. It will ask your password on the server

```shell
localhost$ ssh-copy-id SERVER_ALIAS
```

Now, you can connect without asking your password like this

```shell
localhost$ ssh SERVER_ALIAS
```

Or copy files like this

```shell
localhost$ scp file.ext SERVER_ALIAS:~/some_dir
```

### SSH Tunneling

The remote IP could belong to any computer in the remote network

```shell
ssh -nNT -L local_port:remote_ip:remote_port user@remotehost
```

### SOCKS Proxy [[original]](https://gist.github.com/brentjanderson/6ed800376e53746d2d28ba7b6bdcdc12)

Start the SOCKS proxy connection using ssh.

```shell
ssh -ND 9999 user@remotehost
```

In Firefox' SOCKS settings, select *Manual proxy configuration*, type `localhost` for the SOCKS host andd 9999 for the port.

## Videos

### Concatenate a set of videos:

```shell
cat videos.ts > all.ts
ffmpeg -i all.ts -acodec copy -vcodec copy all.mp4
```

## PDF

### PDF to SVG:

```Shell
pdf2svg
```

### Compress images inside pdfs:

[[original]](https://unix.stackexchange.com/questions/274428/how-do-i-reduce-the-size-of-a-pdf-file-that-contains-images)

```Shell
gs -sDEVICE=pdfwrite -dPDFSETTINGS=/default -q -o output.pdf input.pdf
```

### Extract bitmap images from a PDF file:

```Shell
pdfimages -j yourfile.pdf output_prefix
```

## Images

### Crop images:

[[original]](https://imagemagick.org/script/command-line-options.php#crop)

```Shell
convert input.png -crop WIDTHxHEIGHT+StartX+StartY output.png
```
### Check for corruption:

```Shell
jpeginfo -c input.jpg
```

### Images compression:

JPG

```Shell
convert input.jpg -compress jpeg -quality 35 -density 300x300 output.jpg
```

PNG 

Color quantization [[original]](https://github.com/kornelski/pngquant)

```Shell
pngquant --force --ext .png --quality=62-100 input.png
```

## Tools

* [grip](https://github.com/joeyespo/grip): Render local Markdown readme files before sending off to GitHub.
* [Meld](https://meldmerge.org/): visual diff and merge tool. 

## Rows and Columns

### One text column to multiple columns:

Creating a one column file with 9 numbers:

```Shell
tmp_file=$(mktemp /tmp/one_col_file.XXXXXX)
for i in {1..9}; do echo $i; done > $tmp_file
```

There are two ways to accommodate this file into multiple columns [[original]](https://stackoverflow.com/questions/15687670/convert-a-text-file-into-columns):

```Shell
cat $tmp_file | pr -ts" " --columns 3
```

will generate an output like:

```
1 4 7
2 5 8
3 6 9
```

```Shell
cat $tmp_file | paste - - - -d' '
```
will generate an output like:

```
1 2 3
4 5 6
7 8 9
```
## Hard Disk Recovery commands

### List USB connected devices:

```Shell
lsusb -t -v
```

### Show logical volumes of the Hard Drives.

```Shell
lshw -class disk
```

## Logs

### Check system log from previous *n* sessions

```Shell
journalctl -b -n
```

## Sampling the rows from a file

Creating a one column file with 200 numbers:

```Shell
tmp_file=$(mktemp /tmp/first_200_numbers_file.XXXXXX)
for i in {1..200}; do echo $i; done > $tmp_file
```

Uniformly sampling 5 rows:

```Shell
num_rows=5
num_lines=`wc -l $tmp_file | awk '{print $1}'`
interval=`expr $num_lines / $num_rows`
awk '{if (NR%'$interval' == 0) {print} }' $tmp_file
```

will generate an output like:

```
40
80
120
160
200
```

Randomly sampling 5 rows:

```Shell
shuf -n 5 $tmp_file
```

will generate an output like:

```
175
192
108
194
30
```

Randomly sampling 5 rows setting a random seed [[original]](https://www.gnu.org/software/coreutils/manual/html_node/Random-sources.html#Random-sources):

```Shell
get_seeded_random()
{
  seed="$1"
  openssl enc -aes-256-ctr -pass pass:"$seed" -nosalt \
    </dev/zero 2>/dev/null
}

shuf -n 5 $tmp_file --random-source=<(get_seeded_random 42)
```

it will always generate an output like:

```
55
177
159
127
54
```

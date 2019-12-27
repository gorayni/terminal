# Terminal cheatsheet

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

## GIT

Using meld to see all modifications in the working directory [[original]](https://www.programming-books.io/essential/git/using-meld-to-see-all-modifications-in-the-working-directory-bb8d0be146054d40a352d1795333e858)

```Shell
git difftool -t meld --dir-diff
```

Show the differences between 2 specific commits [[original]](https://www.programming-books.io/essential/git/using-meld-to-see-all-modifications-in-the-working-directory-bb8d0be146054d40a352d1795333e858):

```Shell
git difftool -t meld --dir-diff  [COMMIT_A] [COMMIT_B]
```

## Tools

* [grip](https://github.com/joeyespo/grip): Render local Markdown readme files before sending off to GitHub.
* [Meld](https://meldmerge.org/): visual diff and merge tool. 
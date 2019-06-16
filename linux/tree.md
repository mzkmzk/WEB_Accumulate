# tree 命令

# 简介 

用树状的格式来显示目录结构

# 安装 

window和linux都是默认就有的 

而macOS 就需要单独安装下

```bash
brew install tree -g
```

# 使用

> man文档说明

```bash
usage: tree [-acdfghilnpqrstuvxACDFJQNSUX] [-H baseHREF] [-T title ]
    [-L level [-R]] [-P pattern] [-I pattern] [-o filename] [--version]
    [--help] [--inodes] [--device] [--noreport] [--nolinks] [--dirsfirst]
    [--charset charset] [--filelimit[=]#] [--si] [--timefmt[=]<f>]
    [--sort[=]<name>] [--matchdirs] [--ignore-case] [--] [<directory list>]
  ------- Listing options -------
  -a            All files are listed.
  -d            List directories only.
  -l            Follow symbolic links like directories.
  -f            Print the full path prefix for each file.
  -x            Stay on current filesystem only.
  -L level      Descend only level directories deep.
  -R            Rerun tree when max dir level reached.
  -P pattern    List only those files that match the pattern given.
  -I pattern    Do not list files that match the given pattern.
  --ignore-case Ignore case when pattern matching.
  --matchdirs   Include directory names in -P pattern matching.
  --noreport    Turn off file/directory count at end of tree listing.
  --charset X   Use charset X for terminal/HTML and indentation line output.
  --filelimit # Do not descend dirs with more than # files in them.
  --timefmt <f> Print and format time according to the format <f>.
  -o filename   Output to file instead of stdout.
  -------- File options ---------
  -q            Print non-printable characters as '?'.
  -N            Print non-printable characters as is.
  -Q            Quote filenames with double quotes.
  -p            Print the protections for each file.
  -u            Displays file owner or UID number.
  -g            Displays file group owner or GID number.
  -s            Print the size in bytes of each file.
  -h            Print the size in a more human readable way.
  --si          Like -h, but use in SI units (powers of 1000).
  -D            Print the date of last modification or (-c) status change.
  -F            Appends '/', '=', '*', '@', '|' or '>' as per ls -F.
  --inodes      Print inode number of each file.
  --device      Print device ID number to which each file belongs.
  ------- Sorting options -------
  -v            Sort files alphanumerically by version.
  -t            Sort files by last modification time.
  -c            Sort files by last status change time.
  -U            Leave files unsorted.
  -r            Reverse the order of the sort.
  --dirsfirst   List directories before files (-U disables).
  --sort X      Select sort: name,version,size,mtime,ctime.
  ------- Graphics options ------
  -i            Don't print indentation lines.
  -A            Print ANSI lines graphic indentation lines.
  -S            Print with CP437 (console) graphics indentation lines.
  -n            Turn colorization off always (-C overrides).
  -C            Turn colorization on always.
  ------- XML/HTML/JSON options -------
  -X            Prints out an XML representation of the tree.
  -J            Prints out an JSON representation of the tree.
  -H baseHREF   Prints out HTML format with baseHREF as top directory.
  -T string     Replace the default HTML title and H1 header with string.
  --nolinks     Turn off hyperlinks in HTML output.
  ---- Miscellaneous options ----
  --version     Print version and exit.
  --help        Print usage and this help message and exit.
  --            Options processing terminator.
```

# 案例

> 高亮的展示

`tree -FC`

-C 表示颜色高亮
-F 类似ls -F 在目录后跟\ 可执行文件显示`*`

> 忽略node_modules或svn目录的显示

```bash
tree -I 'svn|node_modules'
```

> 只显示指定目录下的文件

这个tree暂时是无法做到的 在v1.7.0的版本里

假如我们要只显示lib目录里的文件 

写`tree -P '*lib*'` 是无效的

这句命令 只会 显示 文件名中带有lib的文件 和 所有目录

> 限定显示的目录结构层数

例如 只显示 2层内的文件和目录

tree -L 2


# du命令- 查看文件和目录的磁盘使用情况

## 命令简介

`du [选项][文件] `

```bash

 -m或--megabytes 以MB为单位输出。 
 -s 仅显示总计，只列出最后加总的值。 
 -h 以K，M，G为单位，提高信息的可读性。 
 -d: 指定目录深度
```

## 实例

```bash
du -hm -d 4  | sort -n
# 按排序找出占空间最大的文件
```

# 参考链接

1. http://man.linuxde.net/du: du命令
2. http://www.cnblogs.com/kerrycode/p/4391859.html: 查找大文件大目录 
# File I/O -2

## stat(2)

To obtain information about a files, all its meta information (not the contents of the file) we use the stat system calls.
stat comes in 3 flavours
1. stat
2. lstat
3. fstat

## struct stat:

<img src="https://github.com/KKBS22/About_NetBSD/blob/master/03_FilesMore/structstat.png" width="500">

The file data and file metadata are well spearated in the standard unix file system.Its sefull to get information about the file without opening it.
File name is not the property of the file, its a mapping found in the directory

```Python
#include<sys/types.h>
int stat(const char* path, struct stat *sb);
int lstat(const char* path, struct stat *sb);
int fstat(int fd, struct stat *sb);

Return : 0 on OK , -1 on error and errno set to indicate the nature of the error.
```

<img src="https://github.com/KKBS22/About_NetBSD/blob/master/03_FilesMore/statls.png" width="500">

## st_mode:

Regular:

It not only includes the file permissions but also the file type.A file is basically a way of stashing bunch of bytes onto a disk.
Meaning there is no difference between a binary, image file, spreadsheet etc. As far as the file system is considered they are all regular files.
Its upto the application to interpret the sequence of bytes.

Directory:
A directory is also a file, a special type of file on that maps symbolic names to inode numbers allowing the file system to find the file meta information
and data blocks associated with that string.

<img src="https://github.com/KKBS22/About_NetBSD/blob/master/03_FilesMore/stmode.png" width="500">
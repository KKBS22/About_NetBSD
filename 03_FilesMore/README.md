# File I/O -2

## stat(2)

To obtain information about a files, all its meta information (not the contents of the file) we use the stat system calls.
stat comes in 3 flavours
1. stat
2. lstat
3. fstat

The file data and file metadata are well spearated in the standard unix file system.Its sefull to get information about the file without opening it.
File name is not the property of the file

```Python
#include<sys/types.h>
int stat(const char* path, struct stat *sb);
int lstat(const char* path, struct stat *sb);
int fstat(int fd, struct stat *sb);

Return : 0 on OK , -1 on error and errno set to indicate the nature of the error.
```

<img src="https://github.com/KKBS22/About_NetBSD/blob/master/03_FilesMore/statls.png" width="500">
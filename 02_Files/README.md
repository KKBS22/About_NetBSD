# File I/O

All basic file I/O can be performed using the following system calls:
1. open(2)
2. close(2)
3. read(2)
4. write(2)
5. lseek(2)

## creat(2)

To create a file, return a file handle in write-only mode. Before the creation and opening of a file was not an atomic operation.

```Python
#include<fcntl.h>
int creat(const char* pathname, mode_t mode)
```
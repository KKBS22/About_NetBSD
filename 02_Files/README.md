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

The creat call was obsoleted sing open with flags as shown below:
```Python
open(path,O_CREAT | O_TRUNC | O_WRONLY, mode)
```

## open(2)

```Python
#include<fcntl.h>
int open(const char* pathname,int oflag,.../* mode_t mode */);
/* Returns a file descriptor if OK
```
oflag:
1. O_RDONLY - open in read only mode
2. O_WRONLY - write only mode
3. O_RDWR - open for reading and writing

Some of the reasons why open could fail.

1. EEXIST : O_CREAT | O_EXCL was specified but the file exists
2. EMFILE : Process has reached maximum number of file descriptors
3. ENOENT : file does not exist
4. EPERM : Lack of permissions

## close(2)

1. close releases any record locks on the file
2. close them within scope to avoid any leaks
```Python
#include<unistd.h>
int close(fd);
```

## read(2)

1. Takes a file descriptor as an argument
2. pointer to a generic meaning a void buffer (pointer to a buffer that is too small will lead to a seg fault)
3. number of bytes it should read

It will begin reading at the current offset of the file. When EOF is reached read will return zero.
If error will return -1 and set errno.

```Python
#include<unistd.h>
ssize_t read(int fd, void* buf, size_t num)
// Returns number of bytes read, 0 on EOF, -1 on error
```

## write(2)

1. Takes a file descriptor as an argument
2. pointer to a generic meaning a void buffer (pointer to a buffer that is too small will lead to a seg fault)
3. number of bytes to be written

Write will begin writing at the current offset and increment by how much ever written.
1. If O_APPEND flag is used the it will not use th current offset rather start from the EOF before writing.
Write will block until it has completed writing the requested number of bytes. 
Write will clear the set-user-id bit on the file.
Write will just write data in the location but not insert.Use the O_APPEND flag to insert.

```Python
#include<unistd.h>
ssize_t write(int fd, void* buf, size_t num)
// Returns number of bytes written id OK, -1 on error
```

## lseek(2)

1. fast-forward or rewind changing the position of the read/write head relative to the tape.
2. Moving the current offset of the file using lseek.
3. It takes a file descriptor
4. an offset to move the offset by
5. whence determines how the offset is used.
	1. SEEK_SET : bytes from the beginning of the file
	2. SEEK_CUR : bytes from the current position
	3. SEEK_END : bytes from the end of file
6. Seek to a negative offset 
7. Set Seek to 0 to jump to beginning of the file
8. Seek past the end of the file
	1. You can seek past the end of a file and create a sprase file (meaning a file with a hole)
	2. But the file system must support this or else you get a file with lots of nll bytes
	3. If the file system supports sparse file then the kernel will supply null bytes when you read the hole, but will not be on disk
	4. Different versions of cp(1) or other tools may or may not be able to support sparse file.

Returns new offset if OK, -1 on error

```Python
#include<sys/types.h>
#include<fcntlh.h>
off_t lseek(int fd, void* buf, size_t num)
// Returns new offset if OK, -1 on error
```

## I/O efficiency

Well, the reason that we can't keep gaining efficiency by increasing the buffer has to do with the file system. 
The file system has a fixed block size in which it readsdata from the disk, and no matter how large your buffer is,
the file system cannot read more efficiently than whatever that process is. What we see here in our benchmark is this effect:
the file system has a block size of 16k, so increasing the buffer size beyond that value doesn't buy us a whole lot.

## File Sharing

1. The kernel maintains a process table, in that each process table entry contains a table of file descriptors, which inturn contains the file descriptor flags.As
well as a pointer to the file table entry.
2. The kernel also maintains a file table, each entry contains the file status flags, current offset and a pointer to the vnode entry table.
3. We donot implement the file system logic immediately but rather provide vnode which allows for the abstraction of the file system API. It contains the vnode information
and indode information.

<img src="https://github.com/KKBS22/About_NetBSD/blob/master/02_Files/FileSharing.png" width="500">

If a process open a file twice, the data structure is as below. As shown below the file table entries are distinct but point to the same vnode table entry.

<img src="https://github.com/KKBS22/About_NetBSD/blob/master/02_Files/FileSharing1.png" width="500">

If two different processes open the same file.

<img src="https://github.com/KKBS22/About_NetBSD/blob/master/02_Files/FileSharing2.png" width="500">

# Atomic operations

## pread(2) and pwrite(2)

1. offset to atomically seek before issuing the read write operation.

```Python
#include<unistd.h>
ssize_t pread(int fd, void* buf, size_t num, off_t offset)
ssize_t pwrite(int fd, void* buf, size_t num, off_t offset)
// Returns number of bytes written, -1 on error
```
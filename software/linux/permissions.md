## `id`
- linux use UID to determine whether a user is the owner of a file
  ```
  root@dd16ef97b94f:/# id
  uid=0(root) gid=0(root) groups=0(root)
  ```
- Linux stores/uses numeric UID/GID internally.
  ```
  > docker run -it ubuntu
  root@dd16ef97b94f:/# ls -l
  total 48
  lrwxrwxrwx   1 root root    7 Apr 20 08:46 bin -> usr/bin
  drwxr-xr-x   2 root root 4096 Apr 20 08:46 boot
  drwxr-xr-x   5 root root  360 Sep 15 05:52 dev
  drwxr-xr-x   1 root root 4096 Sep 15 05:52 etc
  drwxr-xr-x   3 root root 4096 Sep  1 20:40 home
  lrwxrwxrwx   1 root root    7 Apr 20 08:46 lib -> usr/lib
  drwxr-xr-x   2 root root 4096 Sep  1 20:38 media
  drwxr-xr-x   2 root root 4096 Sep  1 20:38 mnt
  drwxr-xr-x   2 root root 4096 Sep  1 20:38 opt
  dr-xr-xr-x 267 root root    0 Sep 15 05:52 proc
  drwx------   2 root root 4096 Sep  1 20:40 root
  drwxr-xr-x   4 root root 4096 Sep  1 20:40 run
  lrwxrwxrwx   1 root root    8 Apr 20 08:46 sbin -> usr/sbin
  drwxr-xr-x   2 root root 4096 Sep  1 20:38 srv
  dr-xr-xr-x  11 root root    0 Sep 15 05:52 sys
  drwxrwxrwt   2 root root 4096 Sep  1 20:39 tmp
  drwxr-xr-x  11 root root 4096 Sep  1 20:37 usr
  drwxr-xr-x  11 root root 4096 Sep  1 20:40 var
  root@dd16ef97b94f:/# id
  uid=0(root) gid=0(root) groups=0(root)
  root@dd16ef97b94f:/# ls -ln
  total 48
  lrwxrwxrwx   1 0 0    7 Apr 20 08:46 bin -> usr/bin
  drwxr-xr-x   2 0 0 4096 Apr 20 08:46 boot
  drwxr-xr-x   5 0 0  360 Sep 15 05:52 dev
  drwxr-xr-x   1 0 0 4096 Sep 15 05:52 etc
  drwxr-xr-x   3 0 0 4096 Sep  1 20:40 home
  lrwxrwxrwx   1 0 0    7 Apr 20 08:46 lib -> usr/lib
  drwxr-xr-x   2 0 0 4096 Sep  1 20:38 media
  drwxr-xr-x   2 0 0 4096 Sep  1 20:38 mnt
  drwxr-xr-x   2 0 0 4096 Sep  1 20:38 opt
  dr-xr-xr-x 263 0 0    0 Sep 15 05:52 proc
  drwx------   2 0 0 4096 Sep  1 20:40 root
  drwxr-xr-x   4 0 0 4096 Sep  1 20:40 run
  lrwxrwxrwx   1 0 0    8 Apr 20 08:46 sbin -> usr/sbin
  drwxr-xr-x   2 0 0 4096 Sep  1 20:38 srv
  dr-xr-xr-x  11 0 0    0 Sep 15 05:52 sys
  drwxrwxrwt   2 0 0 4096 Sep  1 20:39 tmp
  drwxr-xr-x  11 0 0 4096 Sep  1 20:37 usr
  drwxr-xr-x  11 0 0  4096 Sep  1 20:40 var
  ```
## `ls`
- `ls -l`
  ```
  -rwxr-x--- 1 alice developers 1200 Sep 14 10:30 script.sh
  ```
- the first character is the file type, some example
  ```
  -   regular file
  d   directory
  l   symbolic link

  b   block device       e.g. disks
  c   character device   e.g. terminals, some device interfaces
  p   named pipe (FIFO)
  s   Unix socket
  ```
- the next 9 character shows hows owner/group/everyone else can interact
  ```
  rwxr-x---

  rwx -> alice can read write execute
  r-x -> users belonging to developers group can read and execute
  --- -> everybody else has no perms
  ```
  - Note: alice does not have to be part of developers, could be part of say engineers

- Permission class selection order: owner > group > everybody else
  ```
  Ex: ---rwx--- alice developers
  Here even if alice is a part of the developers group, alice still does not have r/w/x as the permissions from being the owner takes precendent
  ```

## Permissions meaning on file
```
r = read the file
w = modify the file
x = execute the file
```

## Permissions meaning on directory
```
r = list names inside
w = add/remove/rename entries inside
x = enter/traverse through it
```

## How directory matters in certain scenario
- Deleting a file requires parent directory `x` to traverse + `w` to delete
  ```
  directory:
  drwxrwx--- alice developers project/

  file:
  -r-------- alice developers project/data.txt

  Now despite neither alice or users belonging to developer have modification permission on data.txt, because both have `w` permission on the parent folder both can delete data.txt - NOT MODIFY because `w` on parent directory only allow for add/remove/rename but not modify the file itself

  ```
  
- Modifying a file requires parent directory's `x` to travers + file's `w`to modify

## Chmod

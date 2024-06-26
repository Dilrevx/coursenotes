# 堆管理器
堆管理器处于用户程序与内核中间
1. 响应用户的申请内存请求，向操作系统申请内存，然后将其返回给用户程序。
   
   为了保持内存管理的高效性，内核一般都会预先分配很大的一块连续的内存，然后让堆管理器通过某种算法管理这块内存。只有当出现了堆空间不足的情况，堆管理器才会再次与操作系统进行交互。
2. 管理用户所释放的内存。一般来说，用户释放的内存并不是直接返还给操作系统的，而是由堆管理器进行管理。这些释放的内存可以来响应用户新申请的内存的请求。

底层 os 使用延迟映射

## (s)brk, mmap
`brk` 在 bss 之后，`mmap` 在 Memory Mapping Segment

## Chunk
### Malloc Chunk 
```c
/*
  This struct declaration is misleading (but accurate and necessary).
  It declares a "view" into memory allowing access to necessary
  fields at known offsets from a given base. See explanation below.
*/
struct malloc_chunk {

  INTERNAL_SIZE_T      prev_size;  /* Size of previous chunk (if free).  */
  INTERNAL_SIZE_T      size;       /* Size in bytes, including overhead. */

  struct malloc_chunk* fd;         /* double links -- used only if free. */
  struct malloc_chunk* bk;

  /* Only used for large blocks: pointer to next larger size.  */
  struct malloc_chunk* fd_nextsize; /* double links -- used only if free. */
  struct malloc_chunk* bk_nextsize;
};
```

# 文件系统
## 挂载文件系统
不同于 windows, linux 只有一个根文件目录（相对地，也只有一个根文件系统）。其他文件系统都被挂载到这个根文件系统上。 

#### 挂载点
将新的 fs 挂载到根文件系统上的某个目录，这个目录就是挂载点。挂载点的内容会被新的 fs 遮蔽，unmount 后显示

```shell
mount <device> <mount-point>
```
已经挂载的文件系统可以通过 `mount` 查看挂载数据表。kernel 会自动在 `/etc/mtab` 文件记录挂载信息，也可以 cat `/proc/mounts` 查看

#### 挂载参数
mount 时，可以指定 -o MOUNT_OPTIONS 参数。可以配置 ro/rw, sync/asyn, noexec/exec, nodev/dev, nosuid/suid 等参数
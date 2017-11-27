## tf使用记录

### 多gpu使用

/dev/shm/ 当如果你在4个gpu上同时跑4个python，超参数不同但数据集相同，放在shm/更节约内存。/dev/shm/即为共享文件

### 终止gpu进程

使用命令 kill -9
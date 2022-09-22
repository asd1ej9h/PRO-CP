## **PRO-CP**

### **Dependencies**

- Linux - Ubuntu
    - Prepare for the dependencies of RocksDB: [https://github.com/facebook/rocksdb/blob/main/INSTALL.md](https://github.com/facebook/rocksdb/blob/main/INSTALL.md)
    - Install and config HDFS server

### **Use of PRO-CP (remote compaction mode)**

- Config the address of coordinator (control plane) ,worker_agent (CSA) and HDFS server in `db/compaction/remote_compaction/rpc_config.h`
- Build and compile
- Run coordinator and worker_agent

```shell
git submodule update --init --recursive
cd $build
ulimit -n 130000
./coordinator #run coordinator
./worker_agent #run worker agent
```

- Notice that multiple worker agents should bind with one coordinator.
### **The local compaction mode**
Try branch ```local_compaction```


### Test Nebula
- Use the branch ```nebula```, follow the tips in https://docs.nebula-graph.io/3.2.0/4.deployment-and-installation/2.compile-and-install-nebula-graph/1.install-nebula-graph-by-compiling-the-source-code/ 
- when compiling, replace ```build/third-party/install/include/rocksdb/``` and ```build/third-party/install/lib/librocksdb.a``` with the header files and libs produced by ```main``` branch of this repo.

### Test Kvrocks
- Use the branch ```kvrocks```
- Build: ```./x.py build```
    Attention: Default kvrocks only support rocksdb v6.x.x, to adjust to v7.x.x, add the following into "include/rocksdb/db.h" at line 1215
    ```
      // Deprecated versions of GetApproximateSizes
      ROCKSDB_DEPRECATED_FUNC virtual void GetApproximateSizes(
          const Range* range, int n, uint64_t* sizes, bool include_memtable) {
        uint8_t include_flags = static_cast<uint8_t>(SizeApproximationFlags::INCLUDE_FILES);
        if (include_memtable) {
          include_flags |= static_cast<uint8_t>(SizeApproximationFlags::INCLUDE_MEMTABLES);
        }
        GetApproximateSizes(DefaultColumnFamily(), range, n, sizes, include_flags);
      }
      ROCKSDB_DEPRECATED_FUNC virtual void GetApproximateSizes(
          ColumnFamilyHandle* column_family, const Range* range, int n,
          uint64_t* sizes, bool include_memtable) {
        uint8_t include_flags = static_cast<uint8_t>(SizeApproximationFlags::INCLUDE_FILES);
        if (include_memtable) {
          include_flags |= static_cast<uint8_t>(SizeApproximationFlags::INCLUDE_MEMTABLES);
        }
        GetApproximateSizes(column_family, range, n, sizes, include_flags);
      }
    ```
    - local mode: before build, modify "cmake/rocksdb.cmake" line 30 ```origin/main```
    - remote mode: before build, modify "cmake/rocksdb.cmake" line 30 ```origin/local_compaction```
- Single mode:
    - build/kvrocks -c kvrocks.conf 
- Cluster mode:
    - modify kvrocks.conf: port(e.g., 30001-30006), cluster-enabled(yes), dir /tmp/kvrocks(/tmp/kvrocks1-6)
    - Based on ```kvrocks controller``` https://github.com/KvrocksLabs/kvrocks_controller.git to build control server(Build: make)
        use commit ```df83752849ef41ce91037ca5c9cc6c670a480d56```
        Dependencies: etcd https://etcd.io/docs/v3.5/install/

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

Try commit `52e1efb885ea9940b17cca089b5f209800ad7b36`

### **Test Nebula**

- Use the branch `nebula`, follow the tips in [https://docs.nebula-graph.io/3.2.0/4.deployment-and-installation/2.compile-and-install-nebula-graph/1.install-nebula-graph-by-compiling-the-source-code/](https://docs.nebula-graph.io/3.2.0/4.deployment-and-installation/2.compile-and-install-nebula-graph/1.install-nebula-graph-by-compiling-the-source-code/)
- When compiling, replace `build/third-party/install/include/rocksdb/` and `build/third-party/install/lib/librocksdb.a` with the header files and libs produced by `main` branch of this repo.

Path
usage:
    create:
        Path p1 = Paths.get("/tmp/foo");
        Path p5 = Paths.get(System.getProperty("user.home"),"logs", "foo.log");
    Retrieving:
        toString /home/joe/foo	
        getName(0) home
        subpath(0,2) home/joe	
    Removing Redundancies:
    normalize

Files:
Files.exists(path)
Files.walkFileTree(startingDir, pf)

WatchService
WatchService watcher = FileSystems.getDefault().newWatchService();
WatchKey key = dir.register(watcher,
                       ENTRY_CREATE,
                       ENTRY_DELETE,
                       ENTRY_MODIFY);

for (;;) {
    WatchKey key;
    try {
        key = watcher.take();
    } catch (InterruptedException x) {
        return;
    }
    for (WatchEvent<?> event: key.pollEvents()) {
        WatchEvent.Kind<?> kind = event.kind();
        if (kind == OVERFLOW) {
            continue;
        }
    }
    boolean valid = key.reset();
    if (!valid) {
        break;
    }
}


Selector适合一个客户端等待多个session的回复这种场景。Selector提供一种机制，监控多个channel，等待可用的channel出现。selector的效率颇高，是在系统层面实现的。

传统BIO连接数受制于线程数，线程很贵，会成为性能瓶颈
BIO里用户最关心“我要读”，NIO里用户最关心”我可以读了”，在AIO模型里用户更需要关注的是“读完了”


通常情况下，操作系统的一次写操作分为两步： 1. 将数据从用户空间拷贝到系统空间。 2. 从系统空间往网卡写。同理，读操作也分为两步： ① 将数据从网卡拷贝到系统空间； ② 将数据从系统空间拷贝到用户空间。

对于NIO来说，缓存的使用可以使用DirectByteBuffer和HeapByteBuffer。如果使用了DirectByteBuffer，一般来说可以减少一次系统空间到用户空间的拷贝。但Buffer创建和销毁的成本更高，更不宜维护，通常会用内存池来提高性能。

如果数据量比较小的中小应用情况下，可以考虑使用heapBuffer；反之可以用directBuffer。

使用NIO != 高性能，当连接数<1000，并发程度不高或者局域网环境下NIO并没有显著的性能优势。

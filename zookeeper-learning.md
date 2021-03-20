## zookeeper基本命令使用

``` xml
[root@centos7 bin]# ./zkCli.sh 
Connecting to localhost:2181

[zk: localhost:2181(CONNECTED) 0] ls
ls [-s] [-w] [-R] path
[zk: localhost:2181(CONNECTED) 1] ls /
[zookeeper]
[zk: localhost:2181(CONNECTED) 3] create /hxc hxcValue
Created /hxc
[zk: localhost:2181(CONNECTED) 4] ls /
[hxc, zookeeper]
[zk: localhost:2181(CONNECTED) 5] get /hxc
hxcValue
[zk: localhost:2181(CONNECTED) 6] set /hxc hxcUpdate
[zk: localhost:2181(CONNECTED) 7] get /hxc
hxcUpdate
[zk: localhost:2181(CONNECTED) 8] stat /hxc 查看节点状态
cZxid = 0x2
ctime = Sat Mar 20 10:03:21 CST 2021
mZxid = 0x3
mtime = Sat Mar 20 10:04:19 CST 2021
pZxid = 0x2
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 9
numChildren = 0
[zk: localhost:2181(CONNECTED) 9] delete /hxc 
[zk: localhost:2181(CONNECTED) 10] ls
ls [-s] [-w] [-R] path
[zk: localhost:2181(CONNECTED) 11] ls /
[zookeeper]
[zk: localhost:2181(CONNECTED) 12] create /hxc hxcValue
Created /hxc
[zk: localhost:2181(CONNECTED) 13] create /hxc/hxcChildren child01
Created /hxc/hxcChildren

```

## zookeeper命令行watch机制

```xml
[zk: localhost:2181(CONNECTED) 28] create /itheima itheima
Created /itheima

[zk: localhost:2181(CONNECTED) 30] ls -w  /itheima
[]
[zk: localhost:2181(CONNECTED) 31] set /itheima itheimaupdate
[zk: localhost:2181(CONNECTED) 32] get /itheima 
itheimaupdate
[zk: localhost:2181(CONNECTED) 33] create /itheima/itChild

WATCHER::

WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/itheima
Created /itheima/itChild
[zk: localhost:2181(CONNECTED) 34] get -w  /itheima 
itheimaupdate
[zk: localhost:2181(CONNECTED) 35] set /itheima itcast

WATCHER::

WatchedEvent state:SyncConnected type:NodeDataChanged path:/itheima
[zk: localhost:2181(CONNECTED) 36] set /itheima itcastupdate
[zk: localhost:2181(CONNECTED) 37] get -w  /itheima 
itcastupdate
[zk: localhost:2181(CONNECTED) 38] set /itheima itcastupdate2

WATCHER::

WatchedEvent state:SyncConnected type:NodeDataChanged path:/itheima
[zk: localhost:2181(CONNECTED) 39] set /itheima itcastupdate3

watch监听机制只监听一次

```

## zookeeper api的使用

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.6.2</version>
    </dependency>

    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13</version>
    </dependency>
</dependencies>
```

![image-20210320105126322](C:\Users\10340\AppData\Roaming\Typora\typora-user-images\image-20210320105126322.png)

上述错误需关闭防火墙

## api使用

```java
package com.hxc;

import org.apache.zookeeper.*;
import org.apache.zookeeper.data.Stat;
import org.junit.Test;

import java.util.List;


/**
 * Created by IntelliJ IDEA.
 * User: hxc
 * Date: 2021/3/20
 * Time: 9:46
 */
public class Zkapi {
    @Test
    public void atest() throws Exception {
        // 1.创建zookeeper链接
        ZooKeeper zooKeeper = new ZooKeeper("127.0.0.1:2181", 10000, new Watcher() {
            public void process(WatchedEvent watchedEvent) {
                System.out.println("触发了" + watchedEvent.getType() + "的事件");
            }
        });
        // 2.创建父节点
//        String path = zooKeeper.create("/itheima", "hxcValue".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
//        System.out.println(path);
        // 3.创建子节点
//        String childrenPath = zooKeeper.create("/itheima/children", "hxcChildrenValue".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
//        System.out.println(childrenPath);
        // 4. 获取节点的值（父节点和子节点）
//        byte[] data = zooKeeper.getData("/itheima", false, null);
//        System.out.println(data);
//
//        List<String> children = zooKeeper.getChildren("/itheima",false);
//        for (String child : children){
//            System.out.println(child);
//        }
        // 5.修改节点的值
        Stat stat = zooKeeper.setData("/itheima", "itheimaupdate".getBytes(), -1);
        System.out.println(stat);
        // 6.判断某个节点是否存在
        Stat exists = zooKeeper.exists("/itheima/children", false);
        System.out.println(exists);
        // 7.删除节点
        zooKeeper.delete("/itheima/children",-1);
    }

}

```




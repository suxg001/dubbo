由于开源站点因为安全问题被下掉，如果编译时出现找不到opensesame依赖情况的，请先手动下载https://github.com/alibaba/opensesame
本地install，这几天我们会把opensesame发到mavan中央仓库。

临时文档地址：http://alibaba.github.io/dubbo-doc-static/Developer+Guide-zh.htm

================================================================

Dubbo is a distributed service framework enpowers applications with service import/export capability with high performance RPC.

It's composed of three kernel parts:

Remoting: a network communication framework provides sync-over-async and request-response messaging.

Clustering: a remote procedure call abstraction with load-balancing/failover/clustering capabilities.

Registry: a service directory framework for service registration and service event publish/subscription

For more, please refer to:

    http://code.alibabatech.com/wiki/display/dubbo

================================================================
Quick Start
================================================================

Export remote service:

    <bean id="barService" class="com.foo.BarServiceImpl" />
	
    <dubbo:service interface="com.foo.BarService" ref="barService" />

Refer remote service:

    <dubbo:reference id="barService" interface="com.foo.BarService" />
	
    <bean id="barAction" class="com.foo.BarAction">
        <property name="barService" ref="barService" />
    </bean>

================================================================
Source Building
================================================================

0. Install the git and maven command line:

    yum install git
    or: apt-get install git

    cd ~
    wget http://www.apache.org/dist//maven/binaries/apache-maven-2.2.1-bin.tar.gz
    tar zxvf apache-maven-2.2.1-bin.tar.gz
    vi .bash_profile
       - edit: export PATH=$PATH:~/apache-maven-2.2.1/bin
    source .bash_profile

1. Checkout the dubbo source code:

    cd ~
    git clone https://github.com/alibaba/dubbo.git dubbo

    git checkout -b dubbo-2.4.0
    git checkout master

2. Import the dubbo source code to eclipse project:

    cd ~/dubbo
    mvn eclipse:eclipse
    Eclipse -> Menu -> File -> Import -> Exsiting Projects to Workspace -> Browse -> Finish

    Context Menu -> Run As -> Java Application:
    dubbo-demo-provider/src/test/java/com.alibaba.dubbo.demo.provider.DemoProvider
    dubbo-demo-consumer/src/test/java/com.alibaba.dubbo.demo.consumer.DemoConsumer
    dubbo-monitor-simple/src/test/java/com.alibaba.dubbo.monitor.simple.SimpleMonitor
    dubbo-registry-simple/src/test/java/com.alibaba.dubbo.registry.simple.SimpleRegistry
 
    Edit Config:
    dubbo-demo-provider/src/test/resources/dubbo.properties
    dubbo-demo-consumer/src/test/resources/dubbo.properties
    dubbo-monitor-simple/src/test/resources/dubbo.properties
    dubbo-registry-simple/src/test/resources/dubbo.properties

3. Build the dubbo binary package:

    cd ~/dubbo
    mvn clean install -Dmaven.test.skip
    cd dubbo/target
    ls

4. Install the demo provider:

    cd ~/dubbo/dubbo-demo-provider/target
    tar zxvf dubbo-demo-provider-2.4.0-assembly.tar.gz
    cd dubbo-demo-provider-2.4.0/bin
    ./start.sh

5. Install the demo consumer:

    cd ~/dubbo/dubbo-demo-consumer/target
    tar zxvf dubbo-demo-consumer-2.4.0-assembly.tar.gz
    cd dubbo-demo-consumer-2.4.0/bin
    ./start.sh
    cd ../logs
    tail -f stdout.log

6. Install the simple monitor:

    cd ~/dubbo/dubbo-simple-monitor/target
    tar zxvf dubbo-simple-monitor-2.4.0-assembly.tar.gz
    cd dubbo-simple-monitor-2.4.0/bin
    ./start.sh
    http://127.0.0.1:8080

7. Install the simple registry:

    cd ~/dubbo/dubbo-simple-registry/target
    tar zxvf dubbo-simple-registry-2.4.0-assembly.tar.gz
    cd dubbo-simple-registry-2.4.0/bin
    ./start.sh

    cd ~/dubbo/dubbo-demo-provider/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=dubbo://127.0.0.1:9090
    cd ../bin
    ./restart.sh

    cd ~/dubbo/dubbo-demo-consumer/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=dubbo://127.0.0.1:9090
    cd ../bin
    ./restart.sh

    cd ~/dubbo/dubbo-simple-monitor/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=dubbo://127.0.0.1:9090
    cd ../bin
    ./restart.sh

8. Install the zookeeper registry:

    cd ~
    wget http://www.apache.org/dist//zookeeper/zookeeper-3.3.3/zookeeper-3.3.3.tar.gz
    tar zxvf zookeeper-3.3.3.tar.gz
    cd zookeeper-3.3.3/conf
    cp zoo_sample.cfg zoo.cfg
    vi zoo.cfg
       - edit: dataDir=/home/xxx/data
    cd ../bin
    ./zkServer.sh start

    cd ~/dubbo/dubbo-demo-provider/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=zookeeper://127.0.0.1:2181
    cd ../bin
    ./restart.sh

    cd ~/dubbo/dubbo-demo-consumer/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=zookeeper://127.0.0.1:2181
    cd ../bin
    ./restart.sh

    cd ~/dubbo/dubbo-simple-monitor/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=zookeeper://127.0.0.1:2181
    cd ../bin
    ./restart.sh

9. Install the redis registry:

    cd ~
    wget http://redis.googlecode.com/files/redis-2.4.8.tar.gz
    tar xzf redis-2.4.8.tar.gz
    cd redis-2.4.8
    make
    nohup ./src/redis-server redis.conf &

    cd ~/dubbo/dubbo-demo-provider/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=redis://127.0.0.1:6379
    cd ../bin
    ./restart.sh

    cd ~/dubbo/dubbo-demo-consumer/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=redis://127.0.0.1:6379
    cd ../bin
    ./restart.sh

    cd ~/dubbo/dubbo-simple-monitor/conf
    vi dubbo.properties
       - edit: dubbo.registry.adddress=redis://127.0.0.1:6379
    cd ../bin
    ./restart.sh

10. Install the admin console:

    cd ~/dubbo/dubbo-admin
    mvn jetty:run -Ddubbo.registry.address=zookeeper://127.0.0.1:2181
    http://root:root@127.0.0.1:8080

发布到tomcat：
    
    1.复制 dubbo-admin-xx.war 到 ${tomcat-home}/webapps 
        cp dubbo-admin-xx.war ${tomcat-home}/webapps 
    2.解压并重命名为ROOT  注意备份原ROOT文件。
         unzip dubbo-admin-2.4.1.war -d webapps/ROOT
    3.修改ROOT/WEB-INF/dubbo.properties 的注册地址
        vi webapps/ROOT/WEB-INF/dubbo.properties
        dubbo.registry.address=zookeeper://127.0.0.1:2181
        dubbo.admin.root.password=root
        dubbo.admin.guest.password=guest

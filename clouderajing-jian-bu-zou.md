# Cloudera精简步骤

一 关闭原来的自启动脚本

```bash
sudo chkconfig --level 0123456 start_hadoop.sh off
chkconfig --list start_hadoop.sh
# 返回值全都为关闭（off）状态
```

二 把start\_hadoop.py内容换为如下：

```bash
vim /home/cloudera/cm_api/start_hadoop.py
```

```py
#!/usr/bin/env python

import time
import logging
from cm_api.api_client import ApiResource

logging.basicConfig(
        filename="/home/cloudera/cm_api/start.log",
        level=logging.INFO,
        format="%(asctime)s - %(levelname)s - %(filename)s - %(message)s"
    )

log = logging.getLogger(__name__)

api = ApiResource("quickstart.cloudera", username="cloudera", password="cloudera", version=15)
for i in xrange(1000):
    try:
        api.echo('hello')
    except Exception as e:
        log.info('cm does not start yet. retry after 3s...')
        time.sleep(3)
    else:
        break

time.sleep(3)
try:
    cdh5 = api.get_cluster("Cloudera QuickStart")

    if cdh5 == None:
        log.error('not get the cluster,exit')

    hdfs = cdh5.get_service("hdfs")
    yarn = cdh5.get_service("yarn")
    hive = cdh5.get_service("hive")
    spark_on_yarn = cdh5.get_service("spark_on_yarn")

    cmd = hdfs.start().wait()
    log.info("start hdfs. Success: %s" % (cmd.success))

    cmd = yarn.start().wait()
    log.info("start yarn. Success: %s" % (cmd.success))

    cmd = hive.start().wait()
    log.info("start hive. Success: %s" % (cmd.success))

    cmd = spark_on_yarn.start().wait()
    log.info("start spark_on_yarn. Success: %s" % (cmd.success))
except Exception as e:
    log.error(e,exc_info=True)
time.sleep(10)
log.info('service started....')
```

三 修改start\_hadoop.sh脚本

```bash
sudo vim /home/cloudera/cm_api/start_hadoop.sh
# 在文件中写入以下内容：

#! /bin/bash

python /home/cloudera/cm_api/start_hadoop.py
sudo service cloudera_scm_server stop
sudo service cloudera_scm_agent stop
```

四 修改自启动项

```bash
sudo vim /etc/rc.d/rc.local
# 添加以下内容
/home/cloudera/cm_api/start_hadoop.sh
```

五 测试

```bash
启动日志在/home/cloudera/cm_api/start.log
出现service started...说明启动已经结束
测试pyspark和hive命令是可以使用的
```








# Kali Linux升级GVM时PG数据库问题

## 开门见山

升级Kali Linux系统后, GVM扫描器无法启动, 运行`gvm-check-setup`后提示PostgreSQL版本有问题, 如下所示:
````
gvm-check-setup 22.4.0
  Test completeness and readiness of GVM-22.4.0
Step 1: Checking OpenVAS (Scanner)... 
        OK: OpenVAS Scanner is present in version 22.4.0.
        OK: Notus Scanner is present in version 22.4.1.
        OK: Server CA Certificate is present as /var/lib/gvm/CA/servercert.pem.
Checking permissions of /var/lib/openvas/gnupg/*
        OK: _gvm owns all files in /var/lib/openvas/gnupg
        OK: redis-server is present.
        OK: scanner (db_address setting) is configured properly using the redis-server socket: /var/run/redis-openvas/redis-server.sock
        OK: redis-server is running and listening on socket: /var/run/redis-openvas/redis-server.sock.
        OK: redis-server configuration is OK and redis-server is running.
        OK: the mqtt_server_uri is defined in /etc/openvas/openvas.conf
        OK: _gvm owns all files in /var/lib/openvas/plugins
        OK: NVT collection in /var/lib/openvas/plugins contains 83468 NVTs.
        OK: The notus directory /var/lib/notus/products contains 377 NVTs.
Checking that the obsolete redis database has been removed
        OK: No old Redis DB
        OK: ospd-OpenVAS is present in version 22.4.0.
Step 2: Checking GVMD Manager ... 
        OK: GVM Manager (gvmd) is present in version 22.4.0~dev1.
Step 3: Checking Certificates ... 
        OK: GVM client certificate is valid and present as /var/lib/gvm/CA/clientcert.pem.
        OK: Your GVM certificate infrastructure passed validation.
Step 4: Checking data ... 
        OK: SCAP data found in /var/lib/gvm/scap-data.
        OK: CERT data found in /var/lib/gvm/cert-data.
Step 5: Checking Postgresql DB and user ... 
        ERROR: The default postgresql version is not the one used for gvmd compilation: (14, need 15).
        FIX: Please use pg_upgradecluster to upgrade your postgresql installation

 ERROR: Your GVM-22.4.0 installation is not yet complete!

Please follow the instructions marked with FIX above and run this
script again.
````

首先停止PostgreSQL服务

    sudo systemctl stop postgresql

然后根据系统升级前后的PostgreSQL版本, 例如之前为14, 升级后为15, 并且确保没有任何有用的数据存储在15版本上, 执行如下命令删除15版本的cluster(注意!会删除15版本的数据!)

    sudo pg_dropcluster --stop 15 main

然后使用以下命令进行迁移

    sudo pg_upgradecluster 14 main

之后根据gvm-check-setup推荐的步骤继续进行即可

## 登陆相关

使用如下命令查看账户

    sudo runuser -u _gvm -- gvmd --get-users

如果您看到您的帐户但无法登录，您可以运行此命令来重置密码

    sudo runuser -u _gvm -- gvmd --user=admin --new-password=NEWPASSWORD

如果重置密码后无法登录，可以尝试运行以下命令重启gvm服务

    sudo systemctl restart gvmd
    
您还可以创建一个新的 GVM 用户，运行以下命令

    sudo runuser -u _gvm -- gvmd --create-user=USER --new-password=NEWPASSWORD

要删除 GVM 用户，可以运行以下命令

    sudo runuser -u _gvm -- gvmd --delete-user=USER


tags: #Kali #GVM

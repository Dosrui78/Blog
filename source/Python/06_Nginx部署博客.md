#### 1.6 在Nginx上部署自己的Sphinx博客

##### 1. [安装、配置Nginx](https://www.runoob.com/linux/nginx-install-setup.html)

几个要经常用到(copy)的命令:

```
/usr/local/webserver/nginx/sbin/nginx  					   # Nginx 启动命令
/usr/local/webserver/nginx/sbin/nginx -s reload            # 重新载入配置文件
/usr/local/webserver/nginx/sbin/nginx -s reopen            # 重启 Nginx
/usr/local/webserver/nginx/sbin/nginx -s stop              # 停止 Nginx
vi /usr/local/webserver/nginx/conf/nginx.conf              # 编辑配置文件
```

----

##### 2. 编辑配置文件

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/2021-12-29_02-07-53.png)

只用修改这部分就可以了。改完后再重新载入配置文件，启动Nginx发现出现如下情况时该怎么做

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-29_02-15-06.png)

经查询发现运行如下命令就能解决

```
netstat -pan | grep ":80"
```

原来是`80`端口被占用了啊

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-29_02-16-11.png)

然后执行

```
kill 1188  # 杀掉1188进程
```

最后重新运行Nginx即可，刷新我们的服务器地(http://120.76.192.129/)即可。

---

##### 3. 将静态html上传至Linux服务器上

> 这步是将build好的静态html传至服务器便于部署。

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-29_02-21-12.png)

- scp命令Linux和Windows互传

  - 复制windows本地`D:\Linux\Blog\build\html`目录到远程服务器的`root`目录。

    ```
    scp -rp D:\Linux\Blog root@120.76.192.129:/root
    ```

  - 从linux服务器下载文件

    ```
    scp root@120.76.192.129:/root/root/Blog/build/html D:\Linux\Blog\
    ```

    注：需要本地Windows有bash命令工具，关于windows下的bash命令行工具：
    安装git客户端就自带了mingw64，运行git-bash.exe就可以进入bash命令行

- 免密操作

  - 客户端步骤
    1）在本地win10系统打开命令行工具
    2）执行命令：ssh-keygen -t rsa
    3）提示输入密码，因为不需要密码，所以不输入直接enter
    4）生成公钥和私钥文件默认放在C:\Users\Administrator.ssh 目录下，文件名分别为id_rsa.pub和id_rsa

  - 服务端步骤
    1）拷贝客户端上的公钥文件id_rsa.pub 到linux服务器的~/.ssh/ 目录下，注意.ssh目录可能是隐藏的
    2）把公钥文件id_rsa.pub的内容写入到authorized_keys文件，如果authorized_keys不存在，则创建。写入命令：cat id_rsa.pub>>authorized_keys
    注意：如果不是root用户登录，需要免签到对应用户目录下：cat /root/id_rsa.pub>>/home/用户名/.ssh/authorized_keys

    注意：/home/用户名 目录必须是用户的默认目录，否则免签无效

    如果新创建的用户没有.ssh目录和authorized_keys文件，需要手动创建，并设置权限

    chomd 700 /home/用户名/.ssh
    chomd 600 /home/用户名/.ssh/authorized_keys

    chown -R flow /mnt/flowsystem (把mnt目录下的flowsystem目录下的所有子文件的拥有者改为flow用户)
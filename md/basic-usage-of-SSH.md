# SSH

tags: SSH, Security

## SSH是什么

[SSH](https://zh.wikipedia.org/wiki/Secure_Shell)（Secure Shell）是一种应用层的安全协议，为Shell提供安全的传输和使用环境。简单来说，SSH用于计算机之前的加密登录。即从本地计算机使用SSH登录到另一台远程计算机，登录后便可以在Shell中像使用本地计算机一样使用远程计算机。

SSH包含两个不兼容的版本SSH-1和SSH-2。
SSH-1使用DES、3DES、Blowfish和RC4等对称加密算法，使用RSA交换对称加密算法密钥，使用CRC校验数据完整性；
SSH-2新增AES和Twofish加密算法，使用DSA和DH算法代替RSA，用HMAC代替CRC。

SSH只是一种协议，现实中存在多个版本的实现，使用最广的开源实现是[OpenSSH](http://www.openssh.com/)，它实现了SSH-1和SSH-2，默认使用SSH-1。

> 简单区分用的是SSH-1还是SSH-2：SSH-1的目录为`$HOME/.ssh`，SSH-2的目录为`$HOME/.ssh2`

以下内容仅限于SSH在Linux Shell中的使用，Windows下需要使用[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)。

## SSH安装

OpenSSH分客户端openssh-client和服务端openssh-server，如果只想登录别的主机，只需安装openssh-client；如果要开放本机SSH服务，则需要openssh-server。

安装openssh-client

```shell
$ sudo apt-get install ssh // ubuntu
```

或者

```shell
$ sudo apt-get install openssh-client // ubuntu
```

安装openssh-server

```shell
$ sudo apt-get install openssh-server // ubuntu
```

Ubuntu默认已经安装openssh-client。
Mac OS X / macOS已经内置了OpenSSH。

## 密码登录

```shell
$ ssh host // 以当前Shell用户名身份登录host主机
```
	
```shell
$ ssh user@host // 以user的身份登录host主机
```
	
```shell
$ ssh -p 2222 user@host // SSH端口默认为22，使用p参数可以指定特定端口
```

如果是第一次登录，系统会给出如下提示：

```shell
$ ssh user@host
The authenticity of host 'host (xxx.xxx.xxx.xxx)' can't be established.
RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
Are you sure you want to continue connecting (yes/no)?
```
它表达的是，无法确定host主机就是你想链接的主机，目前只知道它的**公钥指纹**，是否需要继续连接？用户通过对比host主机公布的公钥指纹就可以确认当前连接的是否是正确的主机。

> 公钥指纹：即公钥的MD5值。RSA算法的公钥长达1024位，很难逐位比较，对其进行MD5计算后就只有128位，更加方便比对。

在确认主机后，输入`yes`，系统提示如下：

```shell
Warning: Permanently added 'host,xxx.xxx.xxx.xxx' (RSA) to the list of known hosts.
```
然后就可以输入密码。

```shell
Password: (enter password)
```

如果密码正确，那就能成功登陆。此时的Shell已经切换为远程主机的Shell，就可以像本地使用Shell一样使用远程主机的Shell了。登录后的默认`pwd`为`$HOME`。


## 公钥登录

除了密码登录，还可以使用公钥登录，避免了每次都要输入密码的麻烦。

公钥登录原理：

1. 用户将自己的公钥存储到远程主机上；
2. 发起登录时，远程主机向用户发送一段随机字符串；
3. 用户用自己的私钥加密字符串后发回给远程主机；
4. 远程主机用1中存储的公钥进行解密，如果与自己发送的一致，则认为用户是可信的，允许登录。

那么用户的公钥是如何产生的？

## 公钥生成与使用

```shell
$ ssh-keygen
```

系统提示：

```shell
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/username/.ssh/id_rsa): 
```
这是在询问把密钥保存到哪里，如果想使用默认文件id_rsa，可直接Enter。

```shell
Enter passphrase (empty for no passphrase): 
```

这是在询问是否对密钥使用密码进行加密，如果担心密钥的安全，可以设置，否则直接Enter。

至此，个人的密钥和公钥就已经生成，默认位于`$HOME/.ssh/`目录下：`id_rsa.pub`和`id_rsa`，前者为公钥，后者为密钥。

将公钥上传到远程主机：

```shell
$ ssh-copy-id user@hosts
```

这样就可以使用密钥登录远程主机了。如果登录失败，可能是远程主机没有开启RSA认证登录。开启方式：编辑远程主机的`/etc/ssh/sshd_config`文件，去掉以下内容前面的`#`。

```
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

然后重启远程主机的ssh服务。

```shell
// ubuntu
$ service ssh restart

// debian
/etc/init.d/ssh restart
```

## SSH相关文件

`/etc/ssh/sshd_config`：SSH服务配置文件

`$HOME/.ssh/authorized_keys`：保存登录用户的公钥，添加公钥其实可以不使用`ssh-copy-id`，直接将公钥追加到该文件的末尾就可以了。

`$HOME/.ssh/known_hosts`：保存着已经连接过的远端主机公钥，如果远端主机公钥已经在这个文件中，那么SSH连接时将不会询问用户确认远程主机的公钥指纹。

## SSH远程操作

除了登录，SSH还可以直接在远程主机上执行操作，如：

```shell
$ cd && tar czv src | ssh user@host 'tar xz' // 将$HOME/src目录下面的所有文件拷贝到远程主机的$HOME/src目录
$ ssh user@host 'tar cz src' | tar xzv //  将远程主机$HOME/src目录拷贝到本地当前目录
```

> 两台主机之间拷贝文件还可以使用[scp](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/scp.html)命令

## Reference
* [SSH原理与运用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
* [Telnet、SSH1和SSH2之间的区别](http://hdxiong.iteye.com/blog/609958)


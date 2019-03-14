# travis-ci配置ssh登录

最近研究在travis-ci虚拟服务器上，如何通过scp命令上传构建好的代码到服务器，找了一遍官方文档，不得不说有点懵逼。



首先，默认一个前提，咋们已经在本地配置好ssh免密登录部署服务器。如果不懂的话，自行google一下。



其次，本地要安装travis命令行工具，并且通过github账号登录。

```shell
$ brew install travis

# 输入github账号密码
$ travis login --com
```

刚开始使用可能没有注意到，其实travis-ci有两个网站，https://travis-ci.org 只支持public项目，而https://travis-ci.com 支持private项目。因为我的项目是私有的，所以使用--com登录。



准备工作做好之后，先来思考一下如何实现：

假设我们本地存有ssh登录用的私钥（~/.ssh/id_rsa），公钥已经上传到服务器了。那么我们希望travis服务器上也有一份相同的私钥，就可以直接登录部署服务器了。

为了能让travis服务器拿到私钥，我们必须把私钥纳入到代码的版本管理，travis服务器在clone代码的时候就可以得到该私钥。



但是直接暴露私钥在github仓库中显然是不安全的。travis给出了一个加密文件的解决方案。

进入工作目录，执行如下命令，在本地把id_rsa密钥文件加密，生成id_rsa.enc文件，—add是指把解密命令自动添加到.travis.yml文件当中。

```shell
$ travis encrypt-file ~/.ssh/id_rsa --add
```

此时，你可以发现本地多了一个id_rsa.enc文件，我们把id_rsa.enc文件纳入到git版本管理当中，此时已经不需要~/.ssh/id_rsa文件了。



.travis.yml文件中多了如下一行命令。执行install phase之前，把id_rsa.enc文件解密并输出到travis服务器的~/.ssh/id_rsa当中。\$encrypted_fcfcd0fe6c90_key 和 \$encrypted_fcfcd0fe6c90_iv都是运行时的环境变量。执行`travis encrypt-file`的时候会自动判定当前的github项目仓库，生成对应的环境变量。在travis-ci后台对应项目的Environment Variables设置中可以看到。

```yaml
before_install:
  - openssl aes-256-cbc -K $encrypted_fcfcd0fe6c90_key -iv $encrypted_fcfcd0fe6c90_iv -in id_rsa.enc -out ~/.ssh/id_rsa -d
```



此时我们可以配置自动上传命令了，StrictHostKeyChecking=no是为了跳过首次连接添加known_hosts的确认。

```yaml
script:
  - npm run build
  - scp -o StrictHostKeyChecking=no -r dist/* root@**.**.**.**:/root/front/
```



## References

* [Encrypting Files](https://docs.travis-ci.com/user/encrypting-files)





Done.
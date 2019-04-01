# Travis-Ci 配置 ssh 登录

最近研究在 travis-ci 虚拟服务器上，如何通过 scp 命令上传构建好的代码到服务器，找了一遍官方文档，不得不说有点懵逼。

首先，默认一个前提，咋们已经在本地配置好 ssh 免密登录部署服务器。如果不懂的话，自行 google 一下。

其次，本地要安装 travis 命令行工具，并且通过 github 账号登录。

```shell
$ brew install travis

# 输入github账号密码
$ travis login --com
```

刚开始使用可能没有注意到，其实 travis-ci 有两个网站，https://travis-ci.org 只支持 public 项目，而https://travis-ci.com 支持 private 项目。因为我的项目是私有的，所以使用--com 登录。

准备工作做好之后，先来思考一下如何实现：

假设我们本地存有 ssh 登录用的私钥（~/.ssh/id_rsa），公钥已经上传到服务器了。那么我们希望 travis 服务器上也有一份相同的私钥，就可以直接登录部署服务器了。

为了能让 travis 服务器拿到私钥，我们必须把私钥纳入到代码的版本管理，travis 服务器在 clone 代码的时候就可以得到该私钥。

但是直接暴露私钥在 github 仓库中显然是不安全的。travis 给出了一个加密文件的解决方案。

进入工作目录，执行如下命令，在本地把 id_rsa 密钥文件加密，生成 id_rsa.enc 文件，—add 是指把解密命令自动添加到.travis.yml 文件当中。

```shell
$ travis encrypt-file ~/.ssh/id_rsa --add
```

此时，你可以发现本地多了一个 id_rsa.enc 文件，我们把 id_rsa.enc 文件纳入到 git 版本管理当中，此时已经不需要~/.ssh/id_rsa 文件了。

.travis.yml 文件中多了如下一行命令。执行 install phase 之前，把 id_rsa.enc 文件解密并输出到 travis 服务器的~/.ssh/id_rsa 当中。\$encrypted_fcfcd0fe6c90_key 和 \$encrypted_fcfcd0fe6c90_iv 都是运行时的环境变量。执行`travis encrypt-file`的时候会自动判定当前的 github 项目仓库，生成对应的环境变量。在 travis-ci 后台对应项目的 Environment Variables 设置中可以看到。

```yaml
before_install:
  - openssl aes-256-cbc -K $encrypted_fcfcd0fe6c90_key -iv $encrypted_fcfcd0fe6c90_iv -in id_rsa.enc -out ~/.ssh/id_rsa -d
```

此时我们可以配置自动上传命令了，StrictHostKeyChecking=no 是为了跳过首次连接添加 known_hosts 的确认。

```yaml
script:
  - npm run build
  - scp -o StrictHostKeyChecking=no -r dist/* root@**.**.**.**:/root/front/
```

## References

- [Encrypting Files](https://docs.travis-ci.com/user/encrypting-files)

Done.

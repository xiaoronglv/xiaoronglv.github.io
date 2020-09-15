---
title: "提高 Github 的访问速度"
layout: post
guid: FAWI71ErhzD6QrgDyiBO
date: 2020-09-15 15:24:19
coffee:
tags:
  -
---

> 本文假设你已经掌握了使用酸酸乳来科学上网的能力，就不再赘述科学上网。

很多公司会把代码托管在 Github 的私有仓库上，但在中国大陆 git clone/pull/push 代码时非常慢。终端卡在那里，半天没有任何反应，不知道在干什么；好不容易连上 git 服务器，下载也是龟速。

如果一天提交15次代码，就要浪费半个小时的时间，浪费时间。

##  HTTP 形式的仓库地址

大部分开源的项目仓库，通常只会 git clone/pull，我们并没有管理权限，并不会贡献代码，很多人会通过 HTTP 协议来访问仓库地址。

![](/media/files/2020/2020-09-15-git-clone.gif)

对于这类仓库地址 **https**://github.com/your_name/your_repo.git，可以设置一个全局的 http proxy 来提高 git clone/pull 的速度。

在终端里执行下面两条命令

```
git config --global http.proxy "http://127.0.0.1:8080"
git config --global https.proxy "http://127.0.0.1:8080"
```

执行完后 ~/.gitconfig 会多出几行。

```
[http]
	proxy = http://127.0.0.1:8080
[https]
	proxy = http://127.0.0.1:8080
```

以后再 pull HTTP 形式的仓库地址时，就会走代理加速。但是这种方法对于 git@github.com:my_name/repo.git 这种仓库地址并没有效果，因为它走的是 SSH。

## SSH 形式的仓库地址

公司的私有仓库，大部分开发者会上传公钥到 Github, 使用 ssh 形式的仓库地址，避免 git push 时一次次输入密码。

![](/media/files/2020/2020-09-15-ssh.jpg)

对于这种仓库，我们可以在 ~/.ssh/config 设置代理，提高 git pull/push 的速度。

```
Host github.com
    User git
    HostName github.com
    ProxyCommand nc -v -x 127.0.0.1:1080 %h %p
```


我选了一个比较大的开源项目做测试，以下是对比图：

![](/media/files/2020/2020-09-15-git-clone-2.jpg)

## 参考资料

1. [Github 中国加速访问](https://github.com/chenxuhua/issues-blog/issues/3#issuecomment-625405802)
2. [macOS 给 Git(Github) 设置代理（HTTP/SSH）](https://gist.github.com/chuyik/02d0d37a49edc162546441092efae6a1)


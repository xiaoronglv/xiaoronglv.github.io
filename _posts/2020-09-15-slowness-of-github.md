---
title: "提高 Github 的访问速度"
layout: post
guid: FAWI71ErhzD6QrgDyiBO
date: 2020-09-15 15:24:19
coffee:
tags:
  -
---


> 2020-10-19 更新：假如你使用的是 Mac，请直接跳过正文，直接购买 [Surge](https://nssurge.com/) 一步到位。它的虚拟网卡可以自动给 TCP/UDP 分流，从此感知不到墙的存在。不用再为每个应用设置 HTTP 代理。
> 
> 快点购买 Surge，开启你的幸福人生。

![](/media/files/2020/2020-10-19-surge.webp)


本文假设读者有能力并且已经使用酸酸乳来科学上网，就不再赘述如何科学上网这个话题。

很多公司会把代码托管在 Github 的私有仓库上，因为众所周知的原因，在中国大陆 git clone/pull/push 代码时非常慢。终端卡在那里，半天没有任何反应，不知道在干什么；好不容易连上 git 服务器，下载也是龟速。

如果一天提交15次代码，就要浪费半个小时的时间。如何提高 git pull/push 的速度呢？

##  HTTP 形式的仓库地址

对于别人的开源项目，我们并没有 push 权限，只会 git clone/pull。很多人会选择使用 HTTP 方式来访问仓库地址。

![](/media/files/2020/2020-09-15-git-clone.gif)

对于 HTTP 形式的仓库地址，可以在终端里输入以下两行命令，为 Git 设置全局的 HTTP 代理，从而提高 git clone/pull 的速度。

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

以后再 pull 代码就会走代理。

但是这种方法对于 git@github.com:my_name/repo.git 这种仓库地址并没有效果，因为它走的是 SSH。

## SSH 形式的仓库地址

对于自己的仓库，大部分开发者会使用 SSH 的访问方式，并且上传公钥到 Github, 避免 git push 时一次次输入密码。

![](/media/files/2020/2020-09-15-ssh.jpg)

对于 SSH 方式，我们可以在 ~/.ssh/config 设置代理，提高 git pull/push 的速度。

```
Host github.com
    User git
    HostName github.com
    ProxyCommand nc -v -x 127.0.0.1:1080 %h %p
```


我选了一个比较大的开源项目的仓库做测试，以下是对比图：

![](/media/files/2020/2020-09-15-git-clone-2.jpg)

## 参考资料

1. [Github 中国加速访问](https://github.com/chenxuhua/issues-blog/issues/3#issuecomment-625405802)
2. [macOS 给 Git(Github) 设置代理（HTTP/SSH）](https://gist.github.com/chuyik/02d0d37a49edc162546441092efae6a1)


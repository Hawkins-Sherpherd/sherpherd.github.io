---
layout: post_dn42
title: 注册 DN42 网络信息
---

[English Version](DN42-Network-Information-Registry_en.html)

# 加入 DN42 网络的前置要求
---
* 至少拥有一个 7*24 小时运行的路由器，可以是硬件的也可以是软件的。
* 路由器能够建立诸如 GRE，Wireguard 这样的常用隧道。
* 具有基础的路由与交换知识。
* 具有使用 Git 的知识。
* 有一台运行 Linux 环境的电脑。（注册网络信息时会用到）<sup>[[1]](#ref1)</sup>
* 准备如下软件：
  * Git
  * GPG

<br>
<br>

# DN42 网络信息注册
---
DN42 的官方注册教程：[https://dn42.dev/howto/Getting-Started](https://dn42.dev/howto/Getting-Started)

DN42 网络信息注册是你踏入 DN42 的第一步，显然地，你需要从 DN42 的注册局（感觉这样翻译实在过于庄重了）获取网络资源才能真正地在 DN42 网络里活动。

在此之前，你需要用 GPG 或其它能生成 PGP 密钥的软件生成一对 PGP 密钥，这份来自 Github 的文档提供了一个基础的生成和管理 PGP 密钥的例子：[https://docs.github.com/zh/authentication/managing-commit-signature-verification/generating-a-new-gpg-key?platform=linux](https://docs.github.com/zh/authentication/managing-commit-signature-verification/generating-a-new-gpg-key?platform=linux)

在此之后，将你的公钥上传到公共的 Keyserver 上，以便令 DN42 注册局的系统自动识别。

作为一个新建立的 DN42 网络实体，你会需要添加这些项目：
* Maintainer Object (mntner)
* Person Object (person)
* AS Number (aut-num)
* IPv4/IPv6 Prefix (inetnum/inet6num)
* IPv4/IPv6 Route Object (route/route6)

我们要去到[https://git.dn42.dev/dn42/registry](https://git.dn42.dev/dn42/registry)，这是一个 Git 仓库，存储了 DN42 网络的注册信息。你需要注册一个账户用于添加和更新注册信息。<sup>[[2]](#ref2)</sup>

将这个[仓库](https://git.dn42.dev/dn42/registry) fork 一份，再切换到自己 fork 过去的仓库，clone 到本地。

在命令行里执行命令以将仓库 clone 到本地的例子：

`git clone https://git.dn42.dev/<你的用户名>/registry.git`<sup>[[3]](#ref3)</sup>

仓库到手了，你可以开始上手添加自己的信息了。

## 注册 Maintainer Object
---
当你将仓库 clone 到本地时，你会看到一个叫 registry 的文件夹，那就是 DN42 的网络注册信息仓库。

我们只用关心 registry/data 里和我们有关的东西，其它的不要去修改。

所有的 Maintainer Object 放在 registry/data/mntner 里面。一个 Maintainer Object 表示一个 DN42 网络的管理者，它有权管理与之绑定的一切网络资源。简单地说，你的网络资源要绑定到自己的 Maintainer Object 上它才是你的。

在 registry/data/mntner 里创建一个名为 <昵称>-MNT 的文件，然后在里面填写 Maintainer Object 的内容。

一般而言，一个 Maintainer Object 的内容是这样的<sup>[[4]](#ref4)</sup>：
```
mntner:             FOO-MNT
admin-c:            FOO-DN42
tech-c:             FOO-DN42
mnt-by:             FOO-MNT
auth:               pgp-fingerprint 0123456789ABCDEF0123456789ABCDEF01234567
source:             DN42
```

<div id="actual-table">
<table>
<thead style="font-weight: bold">
        <td>项目</td>
        <td>作用</td>
    </thead>
<tr>
    <td>mntner（Maintainer）</td>
    <td>维护者，与文件名称相同</td>
</tr>
<tr>
    <td>admin-c（Admin Contact）</td>
    <td>管理员联系信息，填写你创建的 Person Object 的名字</td>
</tr>
<tr>
    <td>tech-c（Tech Contact）</td>
    <td>技术人员联系信息，一般填写你创建的 Person Object 的名字</td>
</tr>
<tr>
    <td>mnt-by（Maintain By）</td>
    <td>由谁维护，填写 mntner 项的内容</td>
</tr>
<tr>
    <td>auth</td>
    <td>身份验证信息，有两种类型，一种是 PGP（pgp-fingerprint），另一种是 SSH（ssh-{rsa,ed25519}），必须选择其中之一进行填写</td>
</tr>
<tr>
    <td>source</td>
    <td>固定为 DN42</td>
</tr>
</table>
</div>

以下是我的 Maintainer Object 文件信息，**仅供参考**，请将所有信息替换成你自己的信息。
```
mntner:             SERNET-MNT
admin-c:            HAKSRPD-DN42
tech-c:             HAKSRPD-DN42
mnt-by:             SERNET-MNT
source:             DN42
auth:               pgp-fingerprint 29E507919AAA2EBAC209658EA79D5869700BC8BE
```

## 注册 Person Object
---
注册完 Maintainer Object 后，我们还要注册一个 Person Object 来放置联系方式。Person Object 都放在 registry/data/person 里。

在该文件夹里创建一个名为 <昵称>-DN42 的文件，在进行 Person Object 信息的填写，这就是你的 Person Object。

一般而言，一个 Person Object 的内容是这样的<sup>[[5]](#ref5)</sup>：
```
person:             John Doe
e-mail:             john.doe@example.com
nic-hdl:            FOO-DN42
mnt-by:             FOO-MNT
source:             DN42
```

<div id="actual-table">
<table>
<thead style="font-weight: bold">
        <td>项目</td>
        <td>作用</td>
    </thead>
<tr>
    <td>person</td>
    <td>你的昵称</td>
</tr>
<tr>
    <td>e-mail</td>
    <td>你的电子邮箱</td>
</tr>
<tr>
    <td>nic-hdl（NIC-Handle）</td>
    <td>指向文件本身，内容与文件名相同</td>
</tr>
<tr>
    <td>mnt-by（Maintain By）</td>
    <td>由谁维护，填写你的 Maintainer Object 的名称</td>
</tr>
<tr>
    <td>source</td>
    <td>固定为 DN42</td>
</tr>
</table>
</div>

以下是我的 Person Object 文件信息，**仅供参考**，请将所有信息替换成你自己的信息。
```
person:             Hawkins Sherpherd
e-mail:             haksrpd@outlook.com
nic-hdl:            HAKSRPD-DN42
mnt-by:             SERNET-MNT
source:             DN42
```

## 注册 AS 号码（AS Number）
---
拥有一个 AS 号码是很重要的，没有它你就无法在 DN42 网络里自己发布自己的路由。

AS 号码的注册信息放在 registry/data/aut-num 里面，在该文件夹里创建名为 AS\<你挑选的 AS 号码> 的 AS 号码对象文件并填写自己的 AS 号码对象的内容，这样你便有一个 AS 号码。

目前可注册的 AS 号码范围在 4242420000-4242423999 的范围内，请在这个范围内挑选空闲的 AS 号码。你可以通过这个页面来寻找空闲的可用 AS 号码：

[https://explorer.burble.com/free#/asn](https://explorer.burble.com/free#/asn)

一般而言，一个 AS 号码对象文件的内容是这样的<sup>[[5]](#ref5)</sup>：
```
aut-num:            AS4242423999
as-name:            AS-FOO-DN42
admin-c:            FOO-DN42
tech-c:             FOO-DN42
mnt-by:             FOO-MNT
source:             DN42
```

<div id="actual-table">
<table>
<thead style="font-weight: bold">
        <td>项目</td>
        <td>作用</td>
    </thead>
<tr>
    <td>aut-num</td>
    <td>你的自治系统号码</td>
</tr>
<tr>
    <td>as-name</td>
    <td>你的自治系统名称</td>
</tr>
<tr>
    <td>admin-c（Admin Contact）</td>
    <td>管理员联系信息，填写你创建的 Person Object 的名字</td>
</tr>
<tr>
    <td>tech-c（Tech Contact）</td>
    <td>技术人员联系信息，一般填写你创建的 Person Object 的名字</td>
</tr>
<tr>
    <td>mnt-by（Maintain By）</td>
    <td>由谁维护，填写你的 Maintainer Object 的名称</td>
</tr>
<tr>
    <td>source</td>
    <td>固定为 DN42</td>
</tr>
</table>
</div>

以下是我的其中一个的 AS 号码对象文件信息，**仅供参考**，请将所有信息替换成你自己的信息。其中 descr（Description）是可选的。
```
aut-num:            AS4242423947
as-name:            SERNET-DN42
descr:              Peering worldwide, Asia region node preferred. Contact me at haksrpd@outlook.com
admin-c:            HAKSRPD-DN42
tech-c:             HAKSRPD-DN42
mnt-by:             SERNET-MNT
source:             DN42
```

## 注册 IPv4/IPv6 地址资源
---


<br>
<br>

# 致谢
---
* Lan Tian（[https://lantian.pub/](https://lantian.pub/)），他的教程（[https://lantian.pub/article/modify-website/dn42-experimental-network-2020.lantian/](https://lantian.pub/article/modify-website/dn42-experimental-network-2020.lantian/)）为我提供了相当大的帮助。

<br>
<br>

# 引用
---
* <span id="ref1">1.DN42 FAQ, [https://dn42.dev/FAQ#can-i-use-windows-to-clone-and-update-the-registry](https://dn42.dev/FAQ#can-i-use-windows-to-clone-and-update-the-registry)</span>
* <span id="ref2">2.DN42 Getting-Started, [https://dn42.dev/howto/Getting-Started#fill-in-the-registry](https://dn42.dev/howto/Getting-Started#fill-in-the-registry)</span>
* <span id="ref3">3.DN42 Registry, [https://git.dn42.dev/dn42/registry](https://git.dn42.dev/dn42/registry)</span>
* <span id="ref4">4.DN42 Getting-Started, [https://dn42.dev/howto/Getting-Started#create-a-maintainer-object](https://dn42.dev/howto/Getting-Started#create-a-maintainer-object)</span>
* <span id="ref5">5.DN42 Getting-Started, [https://dn42.dev/howto/Getting-Started#create-person-objects](https://dn42.dev/howto/Getting-Started#create-person-objects)
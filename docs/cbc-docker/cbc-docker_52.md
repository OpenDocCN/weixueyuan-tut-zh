# Docker 内容信任机制（DCT）

> 原文：[`c.biancheng.net/view/3260.html`](http://c.biancheng.net/view/3260.html)

在现代 IT 世界中，信任很重要，而且会变得越来越重要。幸运的是，Docker 通过一种称为 Docker 内容信任（Docker Content Trust, DCT）的功能来实现信任机制。

总体来说，Docker 镜像的发布者可以在将镜像推送到库中时对其进行签名。使用者可以在拉取镜像时进行校验，或进行构建或运行等操作。长话短说，DCT 确保使用者能够得到他们想要的镜像。

下图展示了其总体架构。

![DCT 总体架构](img/1a6fe939d8167324ff4a863b5f20cca2.png)
DCT 实现的是客户端的签名和验证，意味着由 Docker 客户端执行它们。

显然，类似这样的密码机制，对于确保在互联网上拉取和推送的软件的可信性是非常重要的，其在整个技术栈的各个层次，以及软件交付流水线的各个环节都在发挥越来越重要的作用。在不久的将来，这种加密信任机制将有望在交付链的各个方面发挥作用。

下面通过一个简单的配置 DCT 的实战例子予以阐述。我们需要一个 Docker 客户端和一个用来推送镜像的库，Docker Hub 上的镜像库即可。

DCT 可以通过环境变量 DOCKER_CONTENT_TRUST 来启用或关闭。将该环境变量的值设置为“1”的话将会在当前会话开启 DCT；将其设置为其他值的话则会关闭 DCT。

下面的命令用于在 Linux 主机的 Docker 中开启 DCT。

$ export DOCKER_CONTENT_TRUST

后续的 docker push 命令会在推送镜像时自动对镜像进行签名。因此，所有的 pull、 build 和 run 命令只会对已签名的镜像起作用。

下面将镜像打一个新的标签（Tag）并推送到镜像库。被推送的镜像可以是任意镜像。本例中使用的是刚刚拉取的 alpine:latest 镜像，因此并非是自己的签名。

1) 对镜像打标签，从而可以将其推送到目标镜像库。本例中，会将其推送到位于我 Docker Hub 个人账户命名空间下的镜像库。

$ docker image tag alpine:latest nigelpoulton/dockerbook:v1

2) 登录到 Docker Hub（或其他镜像库）以便推送镜像。

$ docker login
Login with your Docker ID to push and pull images from Docker Hub.
Username: nigelpoulton
Password:
Login Succeeded

3) 推送打了新标签的镜像。

$ docker image push nigelpoulton/dockerbook:v1
The push refers to a repository [docker.io/nigelpoulton/dockerbook]
cd7100a72410: Mounted from library/alpine
v1: digest: sha256:8c03...acbc size: 528
Signing and pushing trust metadata
<Snip>
Enter passphrase for new root key with ID 865e4ec:
Repeat passphrase for new root key with ID 865e4ec:
Enter passphrase for new repository key with ID bd0d97d:
Repeat passphrase for new repository key with ID bd0d97d:
Finished initializing "docker.io/nigelpoulton/sign"
Successfully signed "docker.io/nigelpoulton/sign":v1

在开启 DCT 的情况下，该镜像会在推送时自动被签名。在签名时会创建两个密钥。

*   根密钥（Root key）。
*   库密钥（Repository key）。

默认情况下，两个密钥被保存在家目录下的隐藏目录 .docker 下。在 Linux 系统中为 ~/.docker/trust。

根密钥是主密钥（一定程度上）。它用于创建和签名新的库密钥，因此应该被妥善保管。这意味着，用户需要使用强密码予以保护，并且在不使用它的时候对其离线保存。一旦掉以轻心，难免会有后悔之时。正常情况下，每个用户应该仅有一个密钥，甚至一个团队或组织仅有一个密钥。并且通常情况下仅用它来创建新的库密钥。

库密钥也被称为标签密钥，用于对需要推送到指定镜像库的打标签的镜像进行签名。因此，每个镜像库配备一个库密钥。如果密钥遗失，相对来说容易恢复，但是仍然应该使用强密码进行保护，并妥善保存。

每次推送镜像到一个新镜像库，都会创建一个新的镜像库标签密钥，这需要使用根密钥，因此需要输入根密钥的密码。后续再推送到这个镜像库时仅需要输入镜像库标签密钥的密码。

此外，还有一个名为时间戳密钥（TimeStamp key）的密钥。它被保存在远程镜像库中，用于一些更加高级的使用场景以确保时效性。

下面看一下如何在开启 DCT 的情况下拉取镜像。

在开启 DCT 的 Docker 主机上执行以下命令，来拉取一个未打标签的镜像。

有时候错误新消息是 No trust data for unsigned。可见，Docker 会因为镜像未签名而拒绝下载。同样的，如果尝试基于未签名的镜像来构建新镜像或运行容器，也会得到类似的错误。来试一下。

在拉取镜像时使用 --disable-content-trust 来覆盖 DCT 设置。

$ docker image pull --disable-content-trust nigelpoulton/dockerbook:unsigned

现在尝试基于未签名的镜像运行容器。

$ docker container run -d --rm nigelpoulton/dockerbook:unsigned
docker: No trust data for unsigned.

可见 Docker 内容信任机制会作用于 push、pull 和 run 操作，下面尝试执行 build 操作看该机制是否起作用。

Docker UCP 同样支持 DCT，从而可以在 UCP 范围内进行签名策略的设置。如果要对整个 UCP 启用 DCT，请展开 Admin 下拉菜单，然后单击 Admin Settings，选择 Docker Content Trust 选项，然后勾选 Run Only Signed Images（仅运行签名镜像）复选框。这会对整个集群落实签名策略，并且仅允许使用签名的镜像部署服务。

默认配置下，任何被 UCP 有效用户签名的镜像都是可以使用的。用户也可以选择性地配置某些团队具有为镜像签名的权限。
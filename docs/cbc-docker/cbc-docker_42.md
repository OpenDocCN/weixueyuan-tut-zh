# Docker Stack 管理应用

> 原文：[`c.biancheng.net/view/3214.html`](http://c.biancheng.net/view/3214.html)

Stack 是一组相关联的服务和基础设施，需要进行统一的部署和管理。虽然这句话里充斥着术语，但仍提醒我们 Stack 是由普通的 Docker 资源构建而来：网络、卷、密钥、服务等。

这意味着可以通过普通的 Docker 命令对其进行查看和重新配置，例如 docker network、docker volume、docker secret、docker service 等。

在此前提之下，通过 docker service 命令来管理 Stack 中某个服务是可行的。一个简单的例子是通过 `docker service scale` 命令来扩充 appserver 服务的副本数。但是，这并不是推荐的方式！

推荐方式是通过声明式方式修改，即将 Stack 文件作为配置的唯一声明。这样，所有 Stack 相关的改动都需要体现在 Stack 文件中，然后更新重新部署应用所需的 Stack 文件。

下面是一个简单例子，阐述了为什么通过命令修改的方式不好（通过 CLI 进行变更）。

假设读者已经部署了一个 Stack，采用的 Stack 文件是《Docker Stack》一 节中从 GitHub 复制的仓库中的 docker-stack.yml。这意味着目前 appserver 服务有两个副本。如果通过 `docker service scale` 命令将副本修改为 4 个，当前运行的集群会有 4 个副本，但是 Stack 文件中仍然是两个。

但是，假设通过修改 Stack 文件对 Stack 做了某些改动，然后通过 `docker stack deploy` 命令进行滚动部署。这会导致 appserver 服务副本数被回滚到两个，因为 Stack 文件就是这么定义的。因此，推荐对 Stack 所有的变更都通过修改 Stack 文件来进行，并且将该文件放到一个合适的版本控制系统当中。

一起来回顾对 Stack 进行两个声明式修改的过程。目标是进行如下改动。

增加 appserver 副本数，数量为 2～10。将 visualizer 服务的优雅停止时间增加到 2min。修改 docker-stack.yml 文件，更新两个值：`services.appserver.deploy.replicas=10`和`services.visualizer.stop_grace_period=2m`。

目前，Stack 文件中的内容如下。

<Snip>
appserver:
image: dockersamples/atsea_app
networks:
- front-tier
- back-tier
- payment
deploy:
replicas: 10 <<Updated value
<Snip>
visualizer:
image: dockersamples/visualizer:stable
ports:
- "8001:8080"
stop_grace_period: 2m <<Updated value
<Snip

保存文件并重新部署应用。

$ docker stack deploy -c docker-stack.yml seastack
Updating service seastack_reverse_proxy (id: z4crmmrz7zi83o0721heohsku)
Updating service seastack_database (id: 3vvpkgunetxaatbvyqxfic115)
Updating service seastack_appserver (id: ljht639w33dhv0dmht1q6mueh)
Updating service seastack_visualizer (id: rbwoyuciglre01hsm5fviabjf)
Updating service seastack_payment_gateway (id: w4gsdxfnb5gofwtvmdiooqvxs)

以上重新部署应用的方式，只会更新存在变更的部分。

运行 `docker stack ps` 命令来确认 appserver 副本数量确实增加。

$ docker stack ps seastack
NAME NODE DESIRED STATE CURRENT STATE
seastack_visualizer.1 mgr-1 Running Running 1 second ago
seastack_visualizer.1 mgr-1 Shutdown Shutdown 3 seconds ago
seastack_appserver.1 wrk-2 Running Running 24 minutes ago
seastack_appserver.2 wrk-1 Running Running 24 minutes ago
seastack_appserver.3 wrk-2 Running Running 1 second ago
seastack_appserver.4 wrk-1 Running Running 1 second ago
seastack_appserver.5 wrk-2 Running Running 1 second ago
seastack_appserver.6 wrk-1 Running Starting 7 seconds ago
seastack_appserver.7 wrk-2 Running Running 1 second ago
seastack_appserver.8 wrk-1 Running Starting 7 seconds ago
seastack_appserver.9 wrk-2 Running Running 1 second ago
seastack_appserver.10 wrk-1 Running Starting 7 seconds ago

输出内容有所裁剪，只展示了受变更影响的服务。

注意关于 visualizer 服务有两行内容。其中一行表示某个副本在 3s 前停止，另一行表示新副本已经运行了 1s。这是因为刚才对 visualizer 服务作了修改，所以 Swarm 集群终止了正在运行的副本，并且启动了新的副本，新副本中更新了 stop_grace_period 的值。

还需要注意的是，appserver 服务目前拥有 10 个副本，但不同副本的“CURRENT STATE”一列状态并不相同：有些处于 running 状态，而有些仍在 starting 状态。

经过足够的时间，集群的状态会完成收敛，期望状态和当前状态就会保持一致。在那时，集群中实际部署和观察到的状态，就会跟 Stack 文件中定义的内容完全一致。

所有应用 /Stack 都应采用该方式进行更新。所有的变更都应该通过 Stack 文件进行声明，然后通过 `docker stack deploy` 进行部署。

正确的删除某个 Stack 方式是通过 `docker stack rm` 命令。一定要谨慎！删除 Stack 不会进行二次确认。

$ docker stack rm seastack
Removing service seastack_appserver
Removing service seastack_database
Removing service seastack_payment_gateway
Removing service seastack_reverse_proxy
Removing service seastack_visualizer
Removing network seastack_front-tier
Removing network seastack_payment
Removing network seastack_default
Removing network seastack_back-tier

注意：网络和服务已经删除，但是密钥并没有。这是因为密钥是在 Stack 部署前就创建并存在了。在 Stack 最上层结构中定义的卷同样不会被 `docker stack rm` 命令删除。这是因为卷的设计初衷是保存持久化数据，其生命周期独立于容器、服务以及 Stack 之外。
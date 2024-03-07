# Docker LDAP 目录服务安装教程

> 原文：[`c.biancheng.net/view/3257.html`](http://c.biancheng.net/view/3257.html)

Docker 与所有优秀的企业级工具一样，UCP 能够与活动目录及其他 LDAP 目录服务进行集成，从而利用组织中现有的单点登录系统中的用户和组。

在开始更加深入的介绍之前，请务必与负责组织目录服务的团队讨论 AD/LDAP 的集成方案。让他们从集成之初就参与进来，从而使得集成方案的制定和实施尽可能顺利进行。

UCP 的用户和组的数据存储在一个本地数据库中，从而使 DTR 能够“开箱即用”地直接利用这一点来实现单点登录（SSO）。UCP 的认证作用于本地所有的访问请求，因此登录到 DTR 时并不需要再输入一遍 UCP 的登录信息了。

不过，UCP 管理员可以通过对 UCP 进行配置，以便利用现有存储在 AD 或其他 LDAP 目录服务中的企业用户账户，从而将账户管理和身份认证工作交给现有团队或服务。

下面将介绍如何配置 UCP，来利用 AD 的用户账号。总体来说，其过程在于让 UCP 在一个指定的目录中搜索用户账号，并将其复制到 UCP 中。再次强调，以下操作请与目录服务团队合作完成。

1) 展开左侧导航栏的 Admin（管理）下拉菜单，然后选择 Admin Settings（管理设置）。

2) 选择 Authentication & Authorization（认证&授权），并在 LDAP Enabled 启用 LDAP 标题下单击 Yes。

3) 配置 LDAP 服务器设置。总体来说，LDAP Server Settings（LDAP 服务设置）可以理解为到哪里去搜索。也就是，到哪个目录中去查询用户账号。

这里填写的内容以实际环境为准。

LDAP Server URL（LDAP 服务器 URL）指域中要去搜索账户的 LDAP 服务器。例如 ad.mycompany.internal。

Reader DN 和 Reader Password 是在目录中有搜索权限的用户信息。该账户必须是在目录中存在且可信的。最好的实践方式是使用对目录具有只读权限的账户。

也可以单击 Add LDAP Domain + 按钮来添加额外的搜索域。每一个搜索域都需要提供其 LDAP Server URL 和 Reader account。

4) 设置 LDAP 用户搜索配置项。如果说 LDAP Server Settings 是到哪里去搜索，那么 LDAP User Search Configuration（LDAP 用户搜索配置）就是搜索什么。

Base DN：指在哪个 LDAP 节点中开始搜索。

UserName Attribute：指会被用于 UCP 中用户名的 LDAP 属性。

Full Name Attribute：指会被对应到 UCP 中用户全名的 LDAP 属性。

其他高级设置请查看文档。当然，在配置与 LDAP 集成的时候还应咨询目录服务团队。

5) 一旦完成了 LDAP 的配置，UCP 会在 LDAP 搜索匹配的用户，并在 UCP 的用户数据库创建它们。之后，UCP 会根据在 Sync Interval (Hours)（同步周期（小时））的设置进行周期性的同步。

如果勾选 Just-In-Time User Provisioning（即时用户置备）复选框，UCP 会将创建用户的操作延迟到该用户第一次登录时进行。

6) 在单击 Save 前，尽量在 LDAP Test Login（LDAP 登录测试）下进行登录测试。

在进行登录测试时需要使用所配置的 LDAP 系统中的账户。该测试会基于以上的所有配置（待保存的 LDAP 配置）。请在测试成功后再保存配置。

7) 保存配置。

此时，UCP 会搜索 LDAP 系统，并创建能够匹配 Base DN 以及其他配置的账户。在集成 LDAP 之前创建的账户依然存在于系统中，并且依然可用。
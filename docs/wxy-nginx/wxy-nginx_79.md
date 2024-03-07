# Nginx 配置修改工具 Ansible

> 原文：[`www.weixueyuan.net/a/834.html`](http://www.weixueyuan.net/a/834.html)

Ansible 是一款自动化的运维工具，是基于 Python 开发的。Ansible 提供了一种自动化执行框架，其可以按照用户设计的剧本自动化执行相关操作。Ansible 是基于模块工作的，其可以实现使用各种模块，并按照设计的剧本，批量对多个目标执行相同的操作。Ansible 集合了众多运维工具的优点，配置更加简单方便。

## 1、Ansible 剧本（playbook）

Ansible 剧本是 Ansible 可以自动化执行一系列动作的执行方式。其通过 YAML 格式文件描述任务步骤，Ansible 按照指定的步骤有序执行，并支持同步和异步，非常适合完成各种复杂的部署工作。Ansible 剧本的相关术语如下。

*   任务（Tasks），由 YAML 描述的一系列操作步骤。
*   变量（Variables），被剧本引用，可以使剧本的设计更加灵活，并根据变量的值执行不同的步骤。
*   模板（Templates），可根据变量的值，动态生成目标文件的预置文件，Ansible 使用 Jinja2 模板语法。
*   处理器（Handlers），当剧本任务条件满足时，触发执行的任务步骤。
*   角色（Roles），描述某一特定任务的集合，其由以上术语的 YAML 描述文件组成。
*   主机（Hosts），被剧本操作的目标主机 IP 或组名称，主机组名称由外部的 hosts 文件定义。

Ansible 中每个剧本只有一个主入口文件，并且只有一个主线任务，主线任务可根据不同条件选用不同的角色，每个角色由任务、变量、模板、处理器的 YAML 描述文件组成。

Ansible 及剧本目录结构如下：

```

.
├── ansible.cfg                 # ansible 配置文件
├── hosts                       # 目标主机资源文件
└── roles                       # 剧本目录，可自定义
    ├── nginx                         # 剧本角色名称及角色任务文件目录
    │   ├── defaults                 # 角色默认变量目录
    │   │   └── main.yaml           # 默认变量自动加载的文件
    │   ├── files                    # 文件存放目录
    │   ├── handlers                 # 处理器任务文件目录
    │   │   └── main.yaml           # 默认处理器任务自动加载的文件
    │   ├── tasks                    # 角色任务文件存放目录
    │   │   └── main.yaml           # 当前角色默认的任务入口文件
    │   └── templates                # 任务模板存放目录
    └── nginx.yaml                    # 剧本入口文件
```

## 2、基础语法

#### 1) 步骤描述

任务由多个步骤组成，每个步骤由步骤命名、任务模块、动作组成，配置样例如下：

```

- name: reload Nginx Service                        # 步骤名
  systemd: "name=nginx state=reloaded enabled=yes"  # 任务模块为 systemd，动作是对系统服务
                                                    # Nginx 执行 reload 操作
```

#### 2) 执行顺序

Ansible 剧本中的步骤是自上而下执行的，在默认情况下，每个步骤的执行结果返回值如果不为 0，就会报错，剧本任务也终止，也可以通过忽略错误指令继续运行，配置样例如下：

```

- name: Create conf.d
  shell: mkdir -p /etc/nginx/conf.d # 任务模块是 shell，动作是执行 mkdir 命令
  ignore_errors: True               # 如果当前动作执行出错，忽略错误继续执行
```

#### 3) 变量赋值

Ansible 剧本中变量赋值有静态和动态两种方式，一种是在 defaults 目录的 main.yaml 文件中静态地直接赋值，这通常被用作默认变量的赋值，配置样例如下：

```

confdir: "/etc/nginx"                           # 定义变量 confdir 的值为/etc/nginx
```

另一种是在当前执行的过程中动态地进行变量赋值，被赋值的变量可以在当前剧本执行过程中被引用，配置样例如下所示：

```

# 方法一：将执行结果动态赋值给变量
  - name: Test Nginx Config
    shell: nginx -c {{ confdir }}/nginx.conf -t     # 任务模块是 shell，动作是 Nginx
                                                    # 执行-t 参数命令
    register: test_result                           # 将执行结果赋值给变量 test_result
# 方法二：根据其他变量的值进行动态的变量赋值
  - name: check set_fact output
    set_fact: output="{{ work }}/output"            # 为变量 output 赋值
    when: test_result                               # 当变量 test_result 为真时
```

#### 4) 条件判断

在 Ansible 剧本中可以根据变量的值判断是否执行当前步骤，配置样例如下：

```

- debug: msg="{{ test_result.stderr_lines }}"   # 任务模块 debug，输出变量 test_result.
                                                # stderr_lines 的内容
  when: not test_result.stderr_lines == ""      # 当 test_result 变量的输出结果不为空，
                                                # 执行当前步骤
```

#### 5) 外部引入

多个步骤可以编写在一个 YAML 文件中，并通过指令 include_tasks 被其他任务引入，结合条件判断，它可以使主线任务因变量不同而存在多个不同分支，配置样例如下：

```

- name: "check system type"
  include_tasks: linux.yaml                         # 引入外部任务步骤
  when: ansible_os_family != "Windows"              # 当目标操作系统非 windows 时，执行当前
                                                    # 任务
```

## 3、剧本执行

Ansible 剧本编写结束后，使用 ansible-playbook 命令调用剧本入口文件执行相应的剧本，即可自动完成预设的任务。

ansible-playbook -i /etc/ansible/hosts /etc/ansible/roles/nginx.yaml --extra-vars 'hosts=192.168.2.145'
---
title: ansible 应用
tags:
  - ansible
  - 运维
categories:
  - 后端
comments: false    // 是否开启评论
date: 2018-07-31 10:38:16
---

链接地址：[http://www.ansible.com.cn/docs/YAMLSyntax.html](http://www.ansible.com.cn/docs/YAMLSyntax.html)

### 环境创建
- 参考[Python-配置虚拟环境](./Python-配置虚拟环境.md),创建ansible的应用环境。
- 安装：pip install ansible

### 建立免密钥登陆
为了避免Ansible下发指令时输入目标主机密码，通过证书签名达到SSH无密码是一个好的方案，推荐使用ssh-keygen与ssh-copy-id来实现快速证书的生成和公钥下发，其中ssh-keygen生成一对密钥，使用ssh-copy-id来下发生成的公钥。具体操作如下：
生成管理主机得私钥和公钥
    
    ssh-keygen -t rsa -b 2048 -P '' -f /root/.ssh/id_rsa
添加主机信息到主机清单中
    
    #host
    192.168.77.129 ansible_ssh_pass=1234567
    192.168.77.130 ansible_ssh_pass=123456   
    
 ansible_ssh_pass密码如果一样的话，这里就不需要定义了。在运行ansible-playbook时 加上-k参数，就可以输入登陆密码

配置palybook
   
    - hosts: testhosts
     user: root
     tasks:
        - name: ssh-copy
          authorized_key: user=root key="{{ lookup('file', '/Users/tpw-dev/.ssh/id_rsa.pub') }}"
          tags: sshkey

运行playbook
   
    ansible-playbook -i hosts ssh-addkey.yml
这样，管理节点的公钥就会添加到节点得authorized_keys文件中，再把主机清单里的ansible_ssh_pass去掉，执行ansible all -m ping 就不需要密码了。

### Inventory(清单)文件
Ansible 可同时操作属于一个组的多台主机,组和主机之间的关系通过 inventory 文件配置. 默认的文件路径为 /etc/ansible/hosts，
在执行ansible-book命令时，也可通过执行-i选项制定inventory 文件。

Inventory 参数的说明

    ansible_ssh_host
              将要连接的远程主机名.与你想要设定的主机的别名不同的话,可通过此变量设置.
        
        ansible_ssh_port
              ssh端口号.如果不是默认的端口号,通过此变量设置.
        
        ansible_ssh_user
              默认的 ssh 用户名
        
        ansible_ssh_pass
              ssh 密码(这种方式并不安全,我们强烈建议使用 --ask-pass 或 SSH 密钥)
        
        ansible_sudo_pass
              sudo 密码(这种方式并不安全,我们强烈建议使用 --ask-sudo-pass)
        
        ansible_sudo_exe (new in version 1.8)
              sudo 命令路径(适用于1.8及以上版本)
        
        ansible_connection
              与主机的连接类型.比如:local, ssh 或者 paramiko. Ansible 1.2 以前默认使用 paramiko.1.2 以后默认使用 'smart','smart' 方式会根据是否支持 ControlPersist, 来判断'ssh' 方式是否可行.
        
        ansible_ssh_private_key_file
              ssh 使用的私钥文件.适用于有多个密钥,而你不想使用 SSH 代理的情况.
        
        ansible_shell_type
              目标系统的shell类型.默认情况下,命令的执行使用 'sh' 语法,可设置为 'csh' 或 'fish'.
        
        ansible_python_interpreter
              目标主机的 python 路径.适用于的情况: 系统中有多个 Python, 或者命令路径不是"/usr/bin/python",比如  \*BSD, 或者 /usr/bin/python
              不是 2.X 版本的 Python.我们不使用 "/usr/bin/env" 机制,因为这要求远程用户的路径设置正确,且要求 "python" 可执行程序名不可为 python以外的名字(实际有可能名为python26).
              与 ansible_python_interpreter 的工作方式相同,可设定如 ruby 或 perl 的路径....

    
### Playbooks 介绍
playbook 由一个或多个 ‘plays’ 组成.它的内容是一个以 ‘plays’ 为元素的列表.在 ansible 中,play 的内容,被称为 tasks,即任务.

这里有一个 playbook,其中仅包含一个 play:

    ---
    - hosts: webservers
      vars:
        http_port: 80
        max_clients: 200
      remote_user: root
      tasks:
      - name: ensure apache is at the latest version
        yum: pkg=httpd state=latest
      - name: write the apache config file
        template: src=/srv/httpd.j2 dest=/etc/httpd.conf
        notify:
        - restart apache
#### playbook基础

##### 主机与用户

**hosts 行的内容是一个或多个组或主机的 patterns,以逗号为分隔符**

remote_user 就是账户名:

    ---
    - hosts: webservers
      remote_user: root
      
##### Tasks 列表

每一个 play 包含了一个 task 列表（任务列表）.一个 task 在其所对应的所有主机上（通过 host pattern 匹配的所有主机）执行完毕之后,下一个 task 才会执行.
每个 task 的目标在于执行一个 moudle,modules 具有”幂等”性,意思是如果你再一次地执行 moudle,moudle 只会执行必要的改动,只会改变需要改变的地方.所以重复多次执行 playbook 也很安全.
下面是一种基本的 task 的定义,service moudle 使用 key=value 格式的参数,这也是大多数 module 使用的参数格式:

    tasks:
      - name: make sure apache is running
        service: name=httpd state=running
比较特别的两个 modudle 是 command 和 shell ,它们不使用 key=value 格式的参数,而是这样:

    tasks:
      - name: disable selinux
        command: /sbin/setenforce 0
        
**tasks可添加biaoqian(tags),这样在执行命令时可制定tasks**
如下：

    - name: Create Base folder
        file:
          path: "{{baseDeployPathPrefix}}{{serverId}}"
          state: directory
        tags: system
      - name: copy redis dir 
        synchronize:
          src: redis-4.0.10
          dest: "{{baseDeployPathPrefix}}{{serverId}}"
        tags: redis
     
     ansible-playbook -i test_hosts -e "target=s1001" -t createDB site.yml   
        
-t 后跟tags，这样就可以指定要执行的tasks
    

##### Handlers: 在发生改变时执行的操作
(当发生改动时）’notify’ actions 会在 playbook 的每一个 task 结束时被触发,而且即使有多个不同的 task 通知改动的发生, ‘notify’ actions 只会被触发一次.

这里有一个例子,当一个文件的内容被改动时,重启两个 services:

    - name: template configuration file
      template: src=template.j2 dest=/etc/foo.conf
      notify:
         - restart memcached
         - restart apache
‘notify’ 下列出的即是 handlers.

Handlers 是由通知者进行 notify, 如果没有被 notify,handlers 不会执行.

这里是一个 handlers 的示例:

    handlers:
        - name: restart memcached
          service:  name=memcached state=restarted
        - name: restart apache
          service: name=apache state=restarted
**handlers 会按照声明的顺序执行**

##### 提示与技巧
如果想看到执行成功的 modules 的输出信息,使用 --verbose flag（否则只有执行失败的才会有输出信息）.

在执行一个 playbook 之前,想看看这个 playbook 的执行会影响到哪些 hosts,你可以这样做:

    ansible-playbook playbook.yml --list-hosts


### Roles
一个项目的结构如下:

    site.yml
    webservers.yml
    fooservers.yml
    roles/
       common/
         files/
         templates/
         tasks/
         handlers/
         vars/
         defaults/
         meta/
       webservers/
         files/
         templates/
         tasks/
         handlers/
         vars/
         defaults/
         meta/
一个 playbook 如下:

    ---
    - hosts: webservers
      roles:
         - common
         - webservers
这个 playbook 为一个角色 ‘x’ 指定了如下的行为：

- 如果 roles/x/tasks/main.yml 存在, 其中列出的 tasks 将被添加到 play 中
- 如果 roles/x/handlers/main.yml 存在, 其中列出的 handlers 将被添加到 play 中
-  如果 roles/x/vars/main.yml 存在, 其中列出的 variables 将被添加到 play 中
-  如果 roles/x/meta/main.yml 存在, 其中列出的 “角色依赖” 将被添加到 roles 列表中 (1.3 and later)
-  所有 copy tasks 可以引用 roles/x/files/ 中的文件，不需要指明文件的路径。
-  所有 script tasks 可以引用 roles/x/files/ 中的脚本，不需要指明文件的路径。
-  所有 template tasks 可以引用 roles/x/templates/ 中的文件，不需要指明文件的路径。
-  所有 include tasks 可以引用 roles/x/tasks/ 中的文件，不需要指明文件的路径。    

如果你希望定义一些 tasks，让它们在 roles 之前以及之后执行，你可以这样做:

    ---
    
    - hosts: webservers
    
      pre_tasks:
        - shell: echo 'hello'
    
      roles:
        - { role: some_role }
    
      tasks:
        - shell: echo 'still busy'
    
      post_tasks:
        - shell: echo 'goodbye'
**值得指出的是,handlers 会在 ‘pre_tasks’, ‘roles’, ‘tasks’, 和 ‘post_tasks’ 之间自动执行. 如果你想立即执行所有的 handler 命令.**

    tasks:
       - shell: some tasks go here
       - meta: flush_handlers
       - shell: some other tasks
#### Ansible Galaxy
[Ansible Galaxy](https://galaxy.ansible.com/home) 是一个自由网站，网站提供所有类型的由社区开发的 roles，这对于实现你的自动化项目是一个很好的参考。网站提供这些 roles 的排名、查找以及下载。

### Variables(变量)

#### 定义变量
- 在Inventory中定义变量
- 在playbook中定义变量
- 在文件和role中定义变量
#### 使用变量: 关于Jinja2
变量替换最基本的形式:

    template: src=foo.cfg.j2 dest={{ remote_install_path }}/foo.cfg
#### YAML陷阱
YAML语法要求如果值以{{ foo }}开头的话我们需要将整行用双引号包起来.这是为了确认你不是想声明一个YAML字典.

这样是不行的:
    
    - hosts: app_servers
      vars:
          app_path: {{ base_path }}/22
你应该这么做:
    - hosts: app_servers
      vars:
           app_path: "{{ base_path }}/22"

#### Facts
Facts通过访问远程系统获取相应的信息. 一个例子就是远程主机的IP地址或者操作系统是什么. 使用以下命令可以查看哪些信息是可用的:

    ansible hostname -m setup
这会返回巨量的变量数据,默认是生成这些信息的，可以在playbook中这样引用远程系统的主机名

    {{ ansible_nodename }}
关闭Facts

    - hosts: whatever
      gather_facts: no
#### Fact缓存
因为缓存的存在，从一个服务器引用另一个服务器的变量是可行的.

    {{ hostvars['asdf.example.com']['ansible_os_family'] }}

#### 命令行中传递变量
除了`vars_prompt`和`vars_files`也可以通过Ansible命令行发送变量.如果你想编写一个通用的发布playbook时则特别有用,你可以传递应用的版本以便部署:
    
    ---
    - hosts: "{{ target }}"
      gather_facts: false
      remote_user: root
      roles:
      - common
      - game
      
   
    ansible-playbook -i test_hosts -e "target=s1001" -t createDB site.yml

### 条件选择

#### When 语句
想忽略某一错误,通过执行成功与否来做决定,我们可以像这样:

    tasks:
      - command: /bin/false
        register: result
        ignore_errors: True
      - command: /bin/something
        when: result|failed
      - command: /bin/something_else
        when: result|success
      - command: /bin/still/something_else
        when: result|skipped
如果想查看哪些事件在某个特定系统中时允许的,可以执行以下命令:

    ansible hostname.example.com -m setup

有些时候你得到一个返回参数的值是一个字符串,并且你还想使用数学操作来比较它,那么你可以执行一下操作:

    tasks:
      - shell: echo "only on Red Hat 6, derivatives, and later"
        when: ansible_os_family == "RedHat" and ansible_lsb.major_release|int >= 6    

#### 循环
##### 标准循环
为了保持简洁,重复的任务可以用以下简写的方式:

    - name: add several users
      user: name={{ item.name }} state=present groups={{ item.groups }}
      with_items:
        - { name: 'testuser1', groups: 'wheel' }
        - { name: 'testuser2', groups: 'root' }
  
##### 嵌套循环

    - name: give users access to multiple databases
      mysql_user: name={{ item[0] }} priv={{ item[1] }}.*:ALL append_privs=yes password=foo
      with_nested:
        - [ 'alice', 'bob' ]
        - [ 'clientdb', 'employeedb', 'providerdb' ]
##### 对哈希表使用循环
假如你有以下变量:
    
    ---
    users:
      alice:
        name: Alice Appleworth
        telephone: 123-456-7890
      bob:
        name: Bob Bananarama
        telephone: 987-654-3210
你想打印出每个用户的名称和电话号码.你可以使用 with_dict 来循环哈希表中的元素:

    tasks:
      - name: Print phone records
        debug: msg="User {{ item.key }} is {{ item.value.name }} ({{ item.value.telephone }})"
        with_dict: "{{users}}"
##### 对文件列表使用循环
 with_fileglob 可以以非递归的方式来模式匹配单个目录中的文件.如下面所示:
 
    ---
    - hosts: all
    
      tasks:
    
        # first ensure our target directory exists
        - file: dest=/etc/fooapp state=directory
    
        # copy each file over that matches the given pattern
        - copy: src={{ item }} dest=/etc/fooapp/ owner=root mode=600
          with_fileglob:
            - /playbooks/files/fooapp/*
##### 对并行数据集使用循环 
假设你通过某种方式加载了以下变量数据:

     ---
     alpha: [ 'a', 'b', 'c', 'd' ]
     numbers:  [ 1, 2, 3, 4 ]   
如果你想得到’(a, 1)’和’(b, 2)’之类的集合.可以使用’with_together’:

    tasks:
        - debug: msg="{{ item.0 }} and {{ item.1 }}"
          with_together:
            - "{{alpha}}"
            - "{{numbers}}"

##### 对子元素使用循环

假设你想对一组用户做一些动作,比如创建这些用户,并且允许它们使用一组SSH key来登录.

如何实现那? 先假设你有按以下方式定义的数据,可以通过”vars_files”或”group_vars/all”文件加载:

    ---
    users:
      - name: alice
        authorized:
          - /tmp/alice/onekey.pub
          - /tmp/alice/twokey.pub
        mysql:
            password: mysql-password
            hosts:
              - "%"
              - "127.0.0.1"
              - "::1"
              - "localhost"
            privs:
              - "*.*:SELECT"
              - "DB1.*:ALL"
      - name: bob
        authorized:
          - /tmp/bob/id_rsa.pub
        mysql:
            password: other-mysql-password
            hosts:
              - "db1"
            privs:
              - "*.*:SELECT"
              - "DB2.*:ALL"
那么可以这样实现:

    - user: name={{ item.name }} state=present generate_ssh_key=yes
      with_items: "{{users}}"
    
    - authorized_key: "user={{ item.0.name }} key='{{ lookup('file', item.1) }}'"
      with_subelements:
         - users
         - authorized
根据mysql hosts以及预先给定的privs subkey列表,我们也可以在嵌套的subkey中迭代列表:

    - name: Setup MySQL users
      mysql_user: name={{ item.0.user }} password={{ item.0.mysql.password }} host={{ item.1 }} priv={{ item.0.mysql.privs | join('/') }}
      with_subelements:
        - users
        - mysql.hosts
##### 对整数序列使用循环

with_sequence 可以以升序数字顺序生成一组序列.你可以指定起始值、终止值,以及一个可选的步长值.

指定参数时也可以使用key=value这种键值对的方式.如果采用这种方式,’format’是一个可打印的字符串.

数字值可以被指定为10进制,16进制(0x3f8)或者八进制(0600).负数则不受支持.请看以下示例:

    ---
    - hosts: all
    
      tasks:
    
        # create groups
        - group: name=evens state=present
        - group: name=odds state=present
    
        # create some test users
        - user: name={{ item }} state=present groups=evens
          with_sequence: start=0 end=32 format=testuser%02x
    
        # create a series of directories with even numbers for some reason
        - file: dest=/var/stuff/{{ item }} state=directory
          with_sequence: start=4 end=16 stride=2
    
        # a simpler way to use the sequence plugin
        # create 4 groups
        - group: name=group{{ item }} state=present
          with_sequence: count=4
##### 随机选择    
‘random_choice’功能可以用来随机获取一些值.它并不是负载均衡器(已经有相关的模块了).它有时可以用作一个简化版的负载均衡器,比如作为条件判断:

    - debug: msg={{ item }}
      with_random_choice:
         - "go through the door"
         - "drink from the goblet"
         - "press the red button"
         - "do nothing"

##### Do-Until循环
有时你想重试一个任务直到达到某个条件.比如下面这个例子:  

    - action: shell /usr/bin/foo
      register: result
      until: result.stdout.find("all systems go") != -1
      retries: 5
      delay: 10
##### 查找第一个匹配的文件         
这其实不是一个循环,但和循环很相似.如果你想引用一个文件,而该文件是从一组文件中根据给定条件匹配出来的.这组文件中部分文件名由变量拼接而成.针对该场景你可以这样做:

    - name: INTERFACES | Create Ansible header for /etc/network/interfaces
      template: src={{ item }} dest=/etc/foo.conf
      with_first_found:
        - "{{ansible_virtualization_type}}_foo.conf"
        - "default_foo.conf"
该功能还有一个更完整的版本,可以配置搜索路径.请看以下示例:

    - name: some configuration template
      template: src={{ item }} dest=/etc/file.cfg mode=0444 owner=root group=root
      with_first_found:
        - files:
           - "{{inventory_hostname}}/etc/file.cfg"
          paths:
           - ../../../templates.overwrites
           - ../../../templates
        - files:
            - etc/file.cfg
          paths:
            - templates

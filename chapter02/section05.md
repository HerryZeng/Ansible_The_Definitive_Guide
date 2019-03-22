
## Ansible Inventory配置及详解


Inventory是Ansible管理主机信息的配置文件，相当于系统HOSTS文件的功能，默认存放在/etc/ansible/hosts。为方便批量管理主机，便捷用其中的主机分组，Ansible通过inventory来定义其主机和组，在使用时通过
-i或--inventory-file指定读取，与Ansible命令组合使用时组合如下：
```bash
ansible -i /etc/ansible/hosts webs -m ping
```

如果只有一个Inventory时可不用指定路径，默认读取/etc/ansible/hosts。Inventory可以同时存在多个，而且支持动态生成。

### 定义主机和组

Inventory配置文件遵循INI文件风格，中括号中的字符为组名。其支持将同一个主机同时归并到多个不同的组中，分组的功能为IT人员维护主机列表提供了非常大的便利。此外，若目标主机
使用了非默认的SSH端口，还可以在主机名之后使用冒号加端口号来标明，以行为单位分隔配置，详细信息可以参考以下代码中的注释。

```bash
# '#'开头的行表示该行为注释行，即当时行的配置不生效
# Inventory可以直接为IP地址
192.168.37.149
# inventory同样支持Hostname的方式，后跟冒号加数字表示端口号，默认为22号端口
ntp.abc.com:2222
nfs.abc.com
# 中括号号的内容表示一个分组的开始，紧随其后的主机均属于该组成员，空等后的主机亦属于该组，即web2.abc.com这台主机也属于[webservers]组
[webservers]
web1.abc.com
web[10:20].abc.com  
# [10:20]表示10~20之间的所有数字，包括10和20，即表示web10.abc.com、web11.abc.com ... web20.abc.com的所有主机

web2.abc.com
[dbservers]
db-a.abc.com
db-[b:f].abc.com
# [b:f]表示b到f之间的所有字母，包括d和f，即表示db-b.abc.com、db-e.abc.com、db-f.abc.com的所有主机
```

### 定义主机变量

在日常工作中，通常会遇到非标准化的需求配置，如考虑到安全性问题，业务人员通常将企业内部的web服务80端口修改为其他端口号，而该功能可以直接通过修改Inventory配置来实现，
在定义主机时为其添加主机变量，以便在Playbook中使用针对某一主机的个性化要求
```conf
[webservers]
web.abc.com http_port=808 max_RequestsPerChild=801  # 自定义http_port端口为808，配置maxRequestsPerChild为801
```
Ansible其实支持多种方式修改或自定义变量，Inventory是其中的一种修改方式。

### 定义组变量

Ansible支持定义组变量，主要针对大量机器的变量定义需求，赋予指定组内所有主机在Playbook中可用的变量，等同于逐一给该组下的所有主机赋予同一变量。定义组变量的参考案例如下：
```conf
[groupservers]
web1.abc.com
web2.abc.com

[groupservers:vars]
ntp_server=ntp.abc.com   # 定义groupservers组中所有主机ntp_server值为ntp.abc.com
```

### 定义组嵌套及组变量

Inventory中，组还可以包含其他的组（嵌套），并且也可以向组中的主机指定变量。不过，这些变量只能在Ansible-playbook中使用，而Ansible不支持。组与组之间可以相互调用，并且可以向组中的主机指定变量。
```conf
[apache]
httpd1.abc.com
httpd2.abc.com

[nginx]
nginx1.abc.com
nginx2.abc.com

[webservers:children]
apache
nginx

[webservers:vars]
ntp_server=ntp.abc.com
```

### 多重变量定义
变量除了可以在Inventory中一并定义，也可以独立于Inventory文件之外单独存储到YAM格式的配置文件中，这些文件通常以.yml、.yaml、.json为后缀或无后缀。变量通常从如下4个位检索：
1. Inventory配置文件
2. Playbook中的vars定义的区域
3. Roles中vars目录下的文件
4. Roles同级目录group_vars和hosts_vars目录下的文件

假如foo主机同属于aaa和bbb组，那么其变量在如下文件中设置均有效
```conf
/etc/ansible/groups_vars/aaa
/etc/ansible/groups_vars/bbb
/etc/ansible/host_vars/foo
```
对于变量的读取，Ansible遵循如上优先级顺序。

### 其他Inventory参数列表

除了支持如下的功能外，Ansible基于SSH连接Inventory中指定的远程主机时，还内置了很多其他参数，用于指定其交互方式，如下列举了部署重要参数:
```conf
ansible_ssh_host: 指定连接主机
ansible_ssh_port: 指定SSH连接端口
ansible_ssh_user: 指定SSH连接用户
ansible_ssh_pass:指定SSH连接密码
ansible_sudo_pass: 指定SSH连接时sudo密码
ansible_ssh_private_key_file: 指定特定私钥文件
```
其他内置参数还有数十个，这些参数均可以直接定在命令行或Playbook文件中，以覆盖配置文件中的定义。
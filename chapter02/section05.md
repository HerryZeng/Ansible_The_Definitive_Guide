
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


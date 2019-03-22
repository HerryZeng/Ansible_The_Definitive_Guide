## Ansible目录结构介绍

1. 配置文件目录/etc/ansible，主要功能为：Inventory主机信息配置、Ansible工具功能配置等。所有Ansible的配置均存放在该目录下，运维日常的所有配置类操作也均基于此目录进行。
2. 执行文件目标/usr/bin，主要功能为:Ansible系列命令默认存放目录。Ansible所有的可执文件均存放在该目录下。
3. Lib库依赖目录/usr/lib/PythonX.X/site/packages/ansible/，该目录是系统当前默认的Python路径，因为Ansible是基于Python编写的，所以Ansible的所有lib库文件和模块也均存放于该目录。
4. Help文档目录/usr/share/doc/ansible-X.X.X/
5. Man文档目录/usr/share/man/man1/
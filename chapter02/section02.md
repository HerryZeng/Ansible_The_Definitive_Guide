## Ansible配置文件解析

Inventory用于定义Ansible的主机列表配置，Ansible的自身配置文件只有一个，即ansible.cfg，Ansible安装好后它默认存放于/etc/ansible/目录。ansible.cfg配置文件可以存在于多个地方，Ansible读取配置文件的顺序是：
当前命令执行目录->用户家目录下的.ansible.cfg->/etc/ansible.cfg，先找到哪个就使用哪个的配置。其ansible.cfg配置的所有内容均可以在命令行通过参数的形式传递或定义在Playbooks中。

配置文件ansible.cfg约有350行语句，大多数为注释行默认配置项。文件文件遵循INI格式，分为如下几类配置


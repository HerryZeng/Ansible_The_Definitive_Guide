正则表达式(Patterns)是各类高级语言的必定支持的方法之一，Ansible也例外。其Patterns功能等同于正则表达式。语法使用也是正则类同，这大大便利了运维的使用。
其对于Ansible的灵活性有着极大贡献，该功能同样支持Ansible-playbook。其用法也非常简单。
```bash
ansible <pattern_goes_here> -m <module_name> -a <arguments>
```
该功能主要针对Inventory的主要机列表使用，我们通过一些案例可以更好的了解其功能及用法。
```bash
ansible webservers -m service -a "name=httpd,state=restarted" # 重启webservers组所有主机的httpd服务
```

1. ALL(全量)匹配
匹配所有主机，all或*号功能相同。
```bash
ansible all -m ping
ansible "*" -m ping
```
检查192.168.1.0/24网段所有主机存状况
```bash
ansible 192.168.1.* -m ping
```

2. 逻辑或(or)匹配
如果我们希望同时对多台主机或多组同时执行，相互之间用":"冒号分隔即可。
```bash
ansible "web1:web2" -m ping
```

3. 逻辑非(!)匹配
逻辑非用感叹号(!)表示，主要针对多重条件的匹配规则，如下:
```bash
ansible "webservers:!phoenix" -m ping
```

4. 逻辑与(&)匹配
和逻辑非一样，逻辑与也主要针对多重条件的匹配规则
```bash
ansible "webservers:&aaa"
```

5. 多条件组合
Ansible同样支持多条件的复杂组全，该情况企业应用不多。
```bash
ansible "webservers:dbservers:&aaa:!phoenix"
```

6.模糊匹配
*通配符在Ansible表示0个或多个任意字符， 主要应用于一些模糊规则匹配，在平时的使用中应用频率非常高，如下：
```bash
*.abc.com
aa*.abc.com:dbservers
```

7. 域切割
Ansible底层基于Python，因此也支持域切割。Python字符串域切割的示例如下：
```bash
str='12345678'
print str[0:1]
```
通过[0:1]即可获取数值1。该功能在Ansible中也支持，如：
```conf
[webservers]
cobweb
webbing
weber
```
通过截取数据下标可以获取对应变量值
`webservers[0]`       # == cobweb

8. 正则匹配
Ansible同样完整支持正则匹配功能，"~"开始表示正则匹配。
```bash
~(web|db).*\.example\.com
```
```bash
ansible "~(beta|web|green)\.example\.(com|org)" -m ping
ansible "~192\.169\.[0-9]\{\2}.[0-9]\{2,}" -m ping
```




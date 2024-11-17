#### yml格式示例
```yml


leafs: #组
  hosts: #关键词
    leaf01: #节点名
      ansible_host: 192.0.2.100 #节点IP
    leaf02:
      ansible_host: 192.0.2.110
  vars:
    ansible_user:admin #应用于整个组的变量


spines:
  hosts:
    spine01.com
    spine02.com


network: #元组
  children: #关键词
    leafs: #组
    spines:

webservers:
  hosts:
    webserver01:
      ansible_host: 192.0.2.140
      http_port: 88 #变量
    webserver02:
      ansible_host: 192.0.2.150

datacenter: #元组可以包含元组
  children:
    network:
    webservers:
    vars: #及vars中的变量在network和webservers;子组变量优先级高于从父组中继承的变量
        some_server: foo.southeast.example.com
        halon_system_timeout: 30
        self_destruct_countdown: 60
        escape_pods: 2

UK_webservers:
  hosts:
    www[01:50:2].example.com: #主机范围：01-50 步进2
```
#### inventory directory
可以将多个清单整合到一个目录中;
Ansible 根据文件名以 ASCII 顺序加载清单源.
必须先加载子清单，否则将会报错;

#### Ansible Vault
可以对清单中的变量或对整个清单进行加密;
[Detailed documentation](https://docs.ansible.com/ansible/latest/vault_guide/vault_encrypting_content.html#encrypting-files-with-ansible-vault)
##### Using encrypted variables and files
[Detailed documentation](https://docs.ansible.com/ansible/latest/vault_guide/vault_using_encrypted_content.html)

## ansible-role

ansible role like an function/library for reusable purpose.

in this example, we create folder structure as below.
```
├── hosts
├── playbook.yml
├── roles
│   └── nginxCentOS
│       ├── files
│       ├── tasks
│       │   └── main.yml
│       ├── templates
│       └── vars
├── templates
└── vars
```

define `roles/nginxCentOS/tasks/main.yml` to install nginx on remote server

```yml
- name: install EPEL repo on CentOS
  yum: name=epel-release state=present
- name: install NGINX CentOS
  yum: name=nginx update_cache=yes state=present
```

then call that `nginxCentOS` in playbook.yml
```yml
- name: Test roles
  hosts: webnginx
  become: true
  roles:
    - nginxCentOS
  tasks:
    - name: Start service nginx
      service: name=nginx state=started enabled=yes
      ignore_errors: yes
```

after running, we get result:
```vim
$ ansible-playbook -i hosts playbook.yml 

PLAY [Test roles] *************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************
ok: [webCentOS]

TASK [nginxCentOS : install EPEL repo on CentOS] ******************************************************************************************************************************************************************
ok: [webCentOS]

TASK [nginxCentOS : install NGINX CentOS] *************************************************************************************************************************************************************************
changed: [webCentOS]

TASK [Start service nginx] ****************************************************************************************************************************************************************************************
changed: [webCentOS]

PLAY RECAP ********************************************************************************************************************************************************************************************************
webCentOS                  : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## ansible-galaxy
Ansible Galaxy is a galaxy website where users can share roles and to a command-line tool for installing, creating, and managing roles.

Install role [geerlingguy.nodejs](https://galaxy.ansible.com/geerlingguy/nodejs) from ansible-galaxy 

```sh
ansible-galaxy install geerlingguy.nodejs
```

check list installed galaxy

```sh
ansible-galaxy list
```


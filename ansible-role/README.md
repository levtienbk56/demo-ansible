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

include that galaxy role into `playbook.yml`
```yml
  roles:
    - nginxCentOS
    - geerlingguy.nodejs
```

then run playbook again, we'll see galaxy role is being called
```vim
$ ansible-playbook  -i hosts  playbook.yml 

PLAY [Test roles] *************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************
ok: [webCentOS]

TASK [nginxCentOS : install EPEL repo on CentOS] ******************************************************************************************************************************************************************
ok: [webCentOS]

TASK [nginxCentOS : install NGINX CentOS] *************************************************************************************************************************************************************************
ok: [webCentOS]

TASK [geerlingguy.nodejs : Set up the Nodesource RPM directory.] **************************************************************************************************************************************************
ok: [webCentOS]

TASK [geerlingguy.nodejs : Import Nodesource RPM key (CentOS < 7).] ***********************************************************************************************************************************************
skipping: [webCentOS]

TASK [geerlingguy.nodejs : Import Nodesource RPM key (CentOS 7+).] ************************************************************************************************************************************************
changed: [webCentOS]

TASK [geerlingguy.nodejs : Add Nodesource repositories for Node.js (CentOS < 7).] *********************************************************************************************************************************
skipping: [webCentOS]

TASK [geerlingguy.nodejs : Add Nodesource repositories for Node.js (CentOS 7+).] **********************************************************************************************************************************
changed: [webCentOS]

TASK [geerlingguy.nodejs : Update package cache if repo was added.] ***********************************************************************************************************************************************
ok: [webCentOS]

TASK [geerlingguy.nodejs : Ensure Node.js AppStream module is disabled (CentOS 8+).] ******************************************************************************************************************************
skipping: [webCentOS]

TASK [geerlingguy.nodejs : Ensure Node.js and npm are installed.] *************************************************************************************************************************************************
changed: [webCentOS]

TASK [geerlingguy.nodejs : Ensure dependencies are present.] ******************************************************************************************************************************************************
skipping: [webCentOS]

TASK [geerlingguy.nodejs : Add Nodesource apt key.] ***************************************************************************************************************************************************************
skipping: [webCentOS]

TASK [geerlingguy.nodejs : Add NodeSource repositories for Node.js.] **********************************************************************************************************************************************
skipping: [webCentOS] => (item=deb https://deb.nodesource.com/node_16.x Core main) 
skipping: [webCentOS] => (item=deb-src https://deb.nodesource.com/node_16.x Core main) 

TASK [geerlingguy.nodejs : Update apt cache if repo was added.] ***************************************************************************************************************************************************
skipping: [webCentOS]

TASK [geerlingguy.nodejs : Ensure Node.js and npm are installed.] *************************************************************************************************************************************************
skipping: [webCentOS]

TASK [geerlingguy.nodejs : Define nodejs_install_npm_user] ********************************************************************************************************************************************************
ok: [webCentOS]

TASK [geerlingguy.nodejs : Create npm global directory] ***********************************************************************************************************************************************************
changed: [webCentOS]

TASK [geerlingguy.nodejs : Add npm_config_prefix bin directory to global $PATH.] **********************************************************************************************************************************
changed: [webCentOS]

TASK [geerlingguy.nodejs : Ensure npm global packages are installed.] *********************************************************************************************************************************************

TASK [geerlingguy.nodejs : Install packages defined in a given package.json.] *************************************************************************************************************************************
skipping: [webCentOS]

TASK [Start service nginx] ****************************************************************************************************************************************************************************************
ok: [webCentOS]

PLAY RECAP ********************************************************************************************************************************************************************************************************
webCentOS                  : ok=12   changed=5    unreachable=0    failed=0    skipped=10   rescued=0    ignored=0 
```


# Ansible AWX Setup
Ansible Role to set-up [Ansible AWX](https://github.com/ansible/awx) on a [local docker host](https://github.com/ansible/awx/blob/devel/tools/docker-compose/README.md).

**Note:** this role currently only supports debian-based systems

**Tested:**
* Debian 11

## Version
**[19.3.0](https://github.com/ansible/awx/tree/19.3.0)**

## Warning
Getting the setup-process to work was 'a little painful'.

This role will most probably only work on the version that it was written for!


## Functionality

* Package installation
  * Docker => using [THIS Role](https://github.com/ansibleguy/infra_docker_minimal)
  * Nginx => using [THIS Role](https://github.com/ansibleguy/infra_nginx)
  * Ansible Client
* Source installation
  * Ansible AWX
* Configuration
  * Service to start/stop Ansible AWX containers
  * Fixes for multiple bugs of this version
  * Backup-Service to pull data from postgres container


## Setup
For this role to work - you must install its dependencies first:

```
ansible-galaxy install -r requirements.yml
```

## Usage

```yaml
# must-have
awx:
  admin_user: 'someguy'
  admin_mail: 'awx@template.ansibleguy.net'
  admin_pwd: !vault |
      $ANSIBLE_VAULT;1.1;AES256
      64373031333937633163366236663237623464336461613334343739323763373330393930666331
      3333663262346337636536383539303834373733326631310a393865653831663238383937626238
      35396531316338373030353530663465343838373635363633613035356338353366373231343264
      3437356663383466630a666161363163346533333139656566386466383733646134616166376638
      35313765356134396130333439663461353336313230366338646165376666313232

  nginx:  # configure the webserver settings => see: https://github.com/ansibleguy/infra_nginx
    domain: 'some.guy.net'
    aliases: ['other.guy.net']
    ip: '192.168.24.20'

    ssl:
      mode: 'self-signed'
      csr_data:
        cn: 'Ansible AWX'
        country: 'AT'
        org: 'AnsibleGuy'
        email: 'awx@template.ansibleguy.net'

  # good to have
  backup_path: '/var/backups/awx'
  backup_timer: 'Mon *-*-* 00:00:00' # for format see: https://wiki.archlinux.org/title/Systemd/Timers

  # not necessary => only set if you need them especially
  settings:
    # NOTE: you might want to disable the backup-job if you have an external db-server: AWX_BACKUP=false
    pg_username: 'awxpg'
    pg_password: !vault ...
    broadcast_websocket_secret: !vault ...
    secret_key: !vault ...

```


You might want to use 'ansible-vault' to encrypt your passwords:
```bash
ansible-vault encrypt_string
```

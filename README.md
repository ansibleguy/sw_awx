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
  * Docker => using [THIS Role](https://github.com/ansibleguy/docker_setup)
  * Nginx => using [THIS Role](https://github.com/ansibleguy/nginx)
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
AWX_ADMIN_USER: 'someguy'
AWX_ADMIN_MAIL: 'awx@template.ansibleguy.net'
AWX_ADMIN_PWD: !vault |
      $ANSIBLE_VAULT;1.1;AES256
      64373031333937633163366236663237623464336461613334343739323763373330393930666331
      3333663262346337636536383539303834373733326631310a393865653831663238383937626238
      35396531316338373030353530663465343838373635363633613035356338353366373231343264
      3437356663383466630a666161363163346533333139656566386466383733646134616166376638
      35313765356134396130333439663461353336313230366338646165376666313232

# good to have
AWX_BACKUP_DIR: '/var/backups/awx'
AWX_BACKUP_TIMER: 'Mon *-*-* 00:00:00' # for format see: https://wiki.archlinux.org/title/Systemd/Timers

# configure the webserver settings => see: https://github.com/ansibleguy/nginx
nginx:
  domain: 'some.guy.net'
  aliases: ['other.guy.net']

# not necessary => only set if you need them especially
AWX_SETTINGS:
  pg_host: '192.168.1.9'
  # NOTE: you might want to disable the backup-job if you have an external db-server: AWX_BACKUP=false
  pg_hostname: 'somehost'
  pg_database: 'awx'
  pg_port: 5440
  pg_username: 'awxpg'
  pg_password: !vault |
      $ANSIBLE_VAULT;1.1;AES256
      64373031333937633163366236663237623464336461613334343739323763373330393930666331
      3333663262346337636536383539303834373733326631310a393865653831663238383937626238
      35396531316338373030353530663465343838373635363633613035356338353366373231343264
      3437356663383466630a666161363163346533333139656566386466383733646134616166376638
      35313765356134396130333439663461353336313230366338646165376666313232
  broadcast_websocket_secret: !vault |
      $ANSIBLE_VAULT;1.1;AES256
      61303431646338396364383939626630336436316661623830643636376130636163356234333464
      3430643134366635356130373139636664363139313831630a376436396134646665306361366464
      66386166663739316162346638323537346630333761366161386364646532633434613964396264
      3063306334636331320a653837663432643164626665353638643032336534653239666534373562
      62323631363638633239383839666337356538366133326136363033373338643138
  secret_key: !vault |
      $ANSIBLE_VAULT;1.1;AES256
      61303431646338396364383939626630336436316661623830643636376130636163356234333464
      3430643134366635356130373139636664363139313831630a376436396134646665306361366464
      66386166663739316162346638323537346630333761366161386364646532633434613964396264
      3063306334636331320a653837663432643164626665353638643032336534653239666534373562
      62323631363638633239383839666337356538366133326136363033373338643138


```


You might want to use 'ansible-vault' to encrypt your passwords:
```bash
ansible-vault encrypt_string 'SECRET_CONTENT_OF_VAR' --name NAME_OF_VAR
```


# Задание: Настраиваем центральный сервер для сбора логов


В Vagtant поднимаем 2 машины web и log
на web поднимаем nginx
на log настраиваем центральный лог сервер на любой системе на выбор
journald;
rsyslog;
elk.
настраиваем аудит, следящий за изменением конфигов нжинкса

---
## Решение:

Vagrant создаёт 2 виртуальные машины, после создания которых запускается ansible-playbook, свой для каждого хоста. Комментарии приведены в самих плейбуках.

```
[andrej@home-srv OTUS-Task17]$ vagrant up
Bringing machine 'backup' up with 'virtualbox' provider...
Bringing machine 'client' up with 'virtualbox' provider...
==> backup: Importing base box 'ubuntu/jammy64'...
==> backup: Matching MAC address for NAT networking...
==> backup: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> backup: Setting the name of the VM: OTUS-Task17_backup_1713985698471_32773
==> backup: Clearing any previously set network interfaces...
==> backup: Preparing network interfaces based on configuration...
    backup: Adapter 1: nat
    backup: Adapter 2: hostonly
==> backup: Forwarding ports...
    backup: 22 (guest) => 2222 (host) (adapter 1)
==> backup: Running 'pre-boot' VM customizations...
==> backup: Booting VM...
==> backup: Waiting for machine to boot. This may take a few minutes...
    backup: SSH address: 127.0.0.1:2222
    backup: SSH username: vagrant
    backup: SSH auth method: private key
    backup: 
    backup: Vagrant insecure key detected. Vagrant will automatically replace
    backup: this with a newly generated keypair for better security.
    backup: 
    backup: Inserting generated public key within guest...
    backup: Removing insecure key from the guest if it's present...
    backup: Key inserted! Disconnecting and reconnecting using new SSH key...
==> backup: Machine booted and ready!
==> backup: Checking for guest additions in VM...
    backup: The guest additions on this VM do not match the installed version of
    backup: VirtualBox! In most cases this is fine, but in rare cases it can
    backup: prevent things such as shared folders from working properly. If you see
    backup: shared folder errors, please make sure the guest additions within the
    backup: virtual machine match the version of VirtualBox you have installed on
    backup: your host and reload your VM.
    backup: 
    backup: Guest Additions Version: 6.0.0 r127566
    backup: VirtualBox Version: 6.1
==> backup: Setting hostname...
==> backup: Configuring and enabling network interfaces...
==> backup: Mounting shared folders...
    backup: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task17
==> backup: Running provisioner: ansible...
    backup: Running ansible-playbook...

PLAY [backup-server] ***********************************************************

TASK [Gathering Facts] *********************************************************
ok: [backup]

TASK [install borgbackup] ******************************************************
changed: [backup]

TASK [Add the user 'borg'] *****************************************************
changed: [backup]

TASK [Add the user 'borg'] *****************************************************
changed: [backup]

TASK [Create directory '/var/backup'] ******************************************
changed: [backup]

TASK [Create directory '/home/borg/.ssh'] **************************************
changed: [backup]

PLAY RECAP *********************************************************************
backup                     : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

==> client: Importing base box 'ubuntu/jammy64'...
==> client: Matching MAC address for NAT networking...
==> client: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> client: Setting the name of the VM: OTUS-Task17_client_1713985824379_10953
==> client: Fixed port collision for 22 => 2222. Now on port 2200.
==> client: Clearing any previously set network interfaces...
==> client: Preparing network interfaces based on configuration...
    client: Adapter 1: nat
    client: Adapter 2: hostonly
==> client: Forwarding ports...
    client: 22 (guest) => 2200 (host) (adapter 1)
==> client: Running 'pre-boot' VM customizations...
==> client: Booting VM...
==> client: Waiting for machine to boot. This may take a few minutes...
    client: SSH address: 127.0.0.1:2200
    client: SSH username: vagrant
    client: SSH auth method: private key
    client: Warning: Connection reset. Retrying...
    client: Warning: Remote connection disconnect. Retrying...
    client: 
    client: Vagrant insecure key detected. Vagrant will automatically replace
    client: this with a newly generated keypair for better security.
    client: 
    client: Inserting generated public key within guest...
    client: Removing insecure key from the guest if it's present...
    client: Key inserted! Disconnecting and reconnecting using new SSH key...
==> client: Machine booted and ready!
==> client: Checking for guest additions in VM...
    client: The guest additions on this VM do not match the installed version of
    client: VirtualBox! In most cases this is fine, but in rare cases it can
    client: prevent things such as shared folders from working properly. If you see
    client: shared folder errors, please make sure the guest additions within the
    client: virtual machine match the version of VirtualBox you have installed on
    client: your host and reload your VM.
    client: 
    client: Guest Additions Version: 6.0.0 r127566
    client: VirtualBox Version: 6.1
==> client: Setting hostname...
==> client: Configuring and enabling network interfaces...
==> client: Mounting shared folders...
    client: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task17
==> client: Running provisioner: ansible...
    client: Running ansible-playbook...

PLAY [client] ******************************************************************

TASK [Gathering Facts] *********************************************************
ok: [client]

TASK [install borgbackup] ******************************************************
changed: [client]

TASK [Make ssh-keys] ***********************************************************
changed: [client]

TASK [Fetch public key data from client_host] **********************************
changed: [client]

TASK [Print the gateway for each host when defined] ****************************
ok: [client] => {
    "msg": "Key on client is ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCkp9ZsBZxlxaGVZxm3TiGK+nezKUX7gd5EyWMR65m2NwiN3zuvJMhRLgUY3lDiyvF7DMn8vluSWt21+2bjFtTfjqJ2zHr/zV4dUYt7W3OJ+PFC8/a3Fi2n4AYtWp85SADc94L81KWM32eBugi/CMkoWl5u2l2tpPRtrDk9JaTlD7sAYYPmNmVUWxAN4+7Eg+Y9hJs8o4/nIRAu9qLB0TnIYN9bUwOlurQdtdtTiBtr4Y9GgmwV35SzxrtDohZOeek8Yg21GOSLn1BjqrzjSgZHADzWUgnJOXEJI7FOAhNPfsJuj9io4sm4XEPBNFXIf2uGGkRH5Qz5V+2bXaY/CHfk14E7MHbwSz+8oX7VLxtZgii/pTxJnQs0Y7+BzSiZY3AOxTOwom7Cyu4M3VOXvTPAi3GSU1csV3GeVHPAAEXkmTQ6bAjWkjrs0hpZkc1EhO8jvbYRhv3qjHVqleAipRHvPsEXuAHd65NAUijmqPmlCYpzUXsPKvbx6rcf79jW6ZUokZh69HqzwNksP9A79BRqKaXcT2uHYJMolqGrXX+QZWJQieYsSTtTRCEAN5HYEM8cENxbU4P6KnoXhbUrEOdwYkWPK4hgmrWp5YXIkYzyOnJTu/4f0noKh6A1WjoQa+m/5W1yNv0CETjqQqI9b/sXdnTRmQ9Wq9S+XMoB+l44XQ== "
}

TASK [Add public key from client_host] *****************************************
changed: [client -> backup(192.168.56.10)]

TASK [Create file '/home/borg/.ssh/authorized_keys'] ***************************
changed: [client -> backup(192.168.56.10)]

TASK [Copy unit borg-backup] ***************************************************
changed: [client]

TASK [Copy timer] **************************************************************
changed: [client]

TASK [reload daemons] **********************************************************
ok: [client]

PLAY RECAP *********************************************************************
client                     : ok=10   changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[andrej@home-srv OTUS-Task17]$ 
```

Подключаемся в хосту `client`, инициализируем 'borg' и включаем `timer` для примера на каждую минуту.

```
[andrej@home-srv OTUS-Task17]$ vagrant ssh client
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-71-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Apr 24 20:40:34 UTC 2024

  System load:  0.06298828125     Processes:               107
  Usage of /:   4.3% of 38.70GB   Users logged in:         0
  Memory usage: 11%               IPv4 address for enp0s3: 10.0.2.15
  Swap usage:   0%                IPv4 address for enp0s8: 192.168.56.11


Expanded Security Maintenance for Applications is not enabled.

191 updates can be applied immediately.
116 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


Last login: Wed Apr 24 20:37:57 2024 from 192.168.56.1
vagrant@client:~$ sudo -i
root@client:~# ssh-keyscan -H 192.168.56.10 >> ~/.ssh/known_hosts && ( echo "Otus1234\n"; echo "Otus1234\n"; echo "n" ) | borg  init  --encryption=repokey borg@192.168.56.10:/var/backup/
# 192.168.56.10:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.1
# 192.168.56.10:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.1
# 192.168.56.10:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.1
# 192.168.56.10:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.1
# 192.168.56.10:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.1
Enter new passphrase: 
Enter same passphrase again: 
Do you want your passphrase to be displayed for verification? [yN]: Invalid answer, try again.
Do you want your passphrase to be displayed for verification? [yN]: Invalid answer, try again.
Do you want your passphrase to be displayed for verification? [yN]: 
By default repositories initialized with this version will produce security
errors if written to with an older version (up to and including Borg 1.0.8).

If you want to use these older versions, you can disable the check by running:
borg upgrade --disable-tam ssh://borg@192.168.56.10/var/backup

See https://borgbackup.readthedocs.io/en/stable/changes.html#pre-1-0-9-manifest-spoofing-vulnerability for details about the security implications.

IMPORTANT: you will need both KEY AND PASSPHRASE to access this repo!
If you used a repokey mode, the key is stored in the repo, but you should back it up separately.
Use "borg key export" to export the key, optionally in printable format.
Write down the passphrase. Store both at safe place(s).

root@client:~# systemctl enable --now borg-backup.timer 
Created symlink /etc/systemd/system/timers.target.wants/borg-backup.timer → /etc/systemd/system/borg-backup.timer.
root@client:~# 
```

Таймер работает:
```
root@client:~# systemctl list-timers --all | grep borg
Wed 2024-04-24 20:51:35 UTC 38s left          Wed 2024-04-24 20:50:35 UTC 21s ago   borg-backup.timer              borg-backup.service
```

Бэкапы создаются:
```
root@client:~# borg list borg@192.168.56.10:/var/backup
Enter passphrase for key ssh://borg@192.168.56.10/var/backup: 
etc-2024-04-24_20:49:29              Wed, 2024-04-24 20:49:31 [a1834d197c9a5dc911ccd34cb6ee552c8f6362bf1ac3559d14284dfa4a5563af]
etc-2024-04-24_20:51:36              Wed, 2024-04-24 20:51:37 [f2544558cb3e003104a5083ad35e4cba958a04f2c815ca46c317fb237b14becd]
```

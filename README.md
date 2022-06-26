# Provisioning

⚠️ This only works on a remote Archlinux server ⚠️

## Remote server preparation

You should have a `root` access to it.

Here are the commands to setup the `admin` account:
```shell
root@arch:~# hostnamectl set-hostname --static my-hostname
root@my-hostname:~# pacman -S sudo
root@my-hostname:~# useradd -m -d /home/admin -s /bin/bash -G adm -p '*' admin
root@my-hostname:~# (umask 337 && echo "admin ALL=(ALL) NOPASSWD:ALL" >/etc/sudoers.d/10-admin-user)
root@my-hostname:~# test -d /home/admin/.ssh || (umask 077 && sudo -u admin mkdir -p /home/admin/.ssh/)
root@my-hostname:~# (umask 177 && sudo -u admin tee /home/admin/.ssh/authorized_keys <<EOF
> <my_ssh_public_key_content>
> EOF
> )

# Alternatively
root@my-hostname:~# (umask 177 && sudo -u admin vim /home/admin/.ssh/authorized_keys)
```

You can now update your `~/.ssh/config` file, see this example:
```
Host my-hostname.tld
    user admin
    IdentitiesOnly yes
    IdentityFile <my_ssh_private_key_location>
    AddKeysToAgent yes
    ForwardAgent no
```


## Provisioning

Create an
[`inventory.ini`](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
file:
```ini
my-host.tld

[online]
my-host.tld

[backup]
my-host.tld
```

and the `hosts_vars/my-host.tld.yml` variables:
```yml
---

domain_name: "my-host.tld"
domain_name_acme_email: "admin@{{ domain_name }}"


apps_public_interface_authorized: "eno1"
apps_authorized_ips:
  - 0.0.0.0
apps_publicly_exposed_ports:
  - 12345

backup_borg_encryption_passphrase: ""
backup_borg_repositories: []
backup_source_directories:
  - /opt

monitoring_admin_password: ""
monitoring_pushover_user_key: ""
monitoring_pushover_token: ""

apps:
  - name: "pirate"
    src: files/apps/pirate/
    env: |
      DOMAIN_NAME={{ domain_name }}
      USER_ID=1000
      GROUP_ID=1000
      EMBY_GIDLIST=986,989
  - name: "heimdall"
    src: files/apps/heimdall/
    env: |
      DOMAIN_NAME={{ domain_name }}
      USER_ID=1000
      GROUP_ID=1000
      TZ=Europe/Paris
```

Then run the playbook:
```bash
$ pipenv install
$ pipenv run ./system_administration.yml
```

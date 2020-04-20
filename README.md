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

# minimoys

Hosts have been configured to use the `orangepi` user (sudoer) with SSH keys configured using `ssh-copy-id`

MacOS SSH client config, to use ssh keys on opis

```
Host 192.168.0.29 192.168.0.30 192.168.0.31 192.168.0.32 192.168.0.33 192.168.0.34 192.168.0.35
  IdentityFile ~/.ssh/minimoys_rsa
```

The playbook is to configure SSH is `ansible/minimoys-ssh-config.yml`

  - No root login
  - No password login

Copying the new SSH config file using the playbook with

```
ansible-playbook -e "ansible_user=orangepi ansible_ssh_pass=orangepi ansible_sudo_pass:orangepi" --ask-pass --ask-become-pass minimoys-ssh-config.yml --ask-pass -f 7
```

Installing `qemu-kvm` using ansible ad-hoc command

```
ansible-playbook minimoys-kvm-install.yml --ask-become-pass -f 7
```

# Welcome message

`cat /var/run/motd.dynamic`

# MaaS

- MaaS is too big for the small 1Gb of RAM on OPiOne+
- `postrgesql`, `rackd` & `region` make trigger swapping
- Downloading OS images is too slow & fails

Trying to deploy `regiond` on a first node, using an external database (AWS). A postgresql database can be deployed on another card later. `rackd` will be deployed alone on another card.

Using `pg_dump` to move the local database to the hosted one.

# Reduce Swappiness

- `sudo sysctl vm.swappiness=5`
- `sudo swapoff -a`
- `sudo swapon -a`
- Verify with `cat /proc/sys/vm/swappiness`

# Limitations

For now, MaaS has been anbandonned since the master node is too small to handle it. Moreover, PXE boot has not been achieved, so current researches are done around Kubernetes cluster.

# Disk Speed

https://askubuntu.com/questions/87035/how-to-check-hard-disk-performance

# Kube Install

Source: https://blog.sourcerer.io/a-kubernetes-quick-start-for-people-who-know-just-enough-about-docker-to-get-by-71c5933b4633

Often get an error on init.

Using `kubeadm reset --force` to restart the process

# Kube add node

kubeadm join 192.168.0.47:6443 --token q0hr5o.m9tnqgb1m4vpza9h --discovery-token-ca-cert-hash sha256:a0fbfc5153e993ebaa32638d4252dee1329da32f967d1231516a58b4f09fc9a1

# Switch Mapping

1: Router
2: 192.168.0.47 MASTER
3: 192.168.0.48 
4: 192.168.0.37
5: 192.168.0.41
6: 192.168.0.34
7: 192.168.0.46
8: 192.168.0.43

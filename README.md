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

> you can boot over USB but not from a passive disk but using a tool

So I'm using `nand-sata-install`

> Tool to transfer the rootfs of an already running Armbian installation from SD card to NAND, eMMC, SATA or USB storage. In case of eMMC it's also possible to transfer the bootloader to eMMC in a single step so from then on running without SD card is possible.

See PROBLEMS.md

This subjects is very interesting (sequential vs random accesses, cards comparison, ...)
https://forum.armbian.com/topic/954-sd-card-performance/

As a result, it turns out SD card for rootfs (and not for video, see topic) is very limited and makes the overall card very slow. USB sticks are very much better (30Mb/s) and the first test allowed me to initialize the kubernetes master without any problem. The master runs correctly even if Kube made it a bit slower (often spiking at 30Mb/s of reads AND writes)

Note: Microdata SD cards (cheap, 3$) are clearly not usable since the `hdparm` test outputs less than 1.5Mb/s of reads. The Samsung Evo Plus 32Gb (tested in the article above, and proved one of the best for our use case - which is random access of small files for rootfs) is way better with 2.8Mb/s of reads. But that's still not enough for a stressed rootfs as I need for a Kube cluster

I thought about using NFS for the rootfs, but the small first test (see Disk Speed section) showed that machines on the network hardly had more than 10Mb/s speed. That's not better than USB (3.0, but 2.0 port).

# Disk Speed

https://askubuntu.com/questions/87035/how-to-check-hard-disk-performance

To test a storage device: `sudo hdparm -Tt /dev/sda` (SD Cards are around 3Mb/s)
To test a LAN SSH speed: `dd if=/dev/zero of=test bs=100M count=1; scp test user@server:/dev/null;`

# Kube Install

Source: https://blog.sourcerer.io/a-kubernetes-quick-start-for-people-who-know-just-enough-about-docker-to-get-by-71c5933b4633

Often get an error on init.

Using `kubeadm reset --force` to restart the process

# Kube add node

kubeadm join 192.168.0.13:6443 --token elxdwa.v47bx8j6hh5u7bsd --discovery-token-ca-cert-hash sha256:30748fad81625244530739770d2daad4381f1febf8d843cf0177b005140331c7

# Switch Mapping

1: Router
2: 192.168.0.15
3: 192.168.0.16
4: 192.168.0.13 MASTER
5: 192.168.0.
6: 192.168.0.
7: 192.168.0.
8: 192.168.0.

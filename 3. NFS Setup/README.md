# Network File System

Network File System (NFS) enables you to easily share files and directories over the network. NFS is a distributed file system protocol that we will use to share files between our nodes across our private network. It has a server-client architecture that treats one machine as a server of directories, and multiple machines as clients that can connect to it.

This tutorial will show you how to export a directory on the head node and mount it through the network on the compute nodes. With the shared file system in place it becomes easy to enable **public key based SSH authentication**, which allows you to SSH into all the computers in your cluster without requiring a password.

The head node will act as the shred home and will export the `/home/` directory to the compute node. The `/home/` directory contains the home directories of all the the non-`root` user accounts on most default Linux operating system configurations. For more information read the this link


1. Install the NFS Utilities on both the head node and compute node(s):
   ```bash
   sudo dnf install nfs-utils
   ```

1. Edit `/etc/exports` on the head node

   NFS shares (directories on the NFS server) are configured in the `/etc/exports` file. Here you specify the directory you want to share, followed by the IP address or range you want to share to and then the options for sharing. We want to export the `/home` directory, so edit `/etc/exports` and add the following:

   ```conf
   /home    192.168.15.0/24(rw,async,no_subtree_check,no_root_squash)
   ```
   * `rw` gives the client machine read and write access on the NFS volume.
   * `async` forces NFS to write changes to the disk before replying. This option is considered more reliable. However, it also reduces the speed of file operations.
   * `no_subtree_check` prevents a process where the host must check whether the file is available along with permissions for every request. It can also cause issues when a file is renamed on the host while still open on the client. Disabling it improves the reliability of NFS.
   * `no_root_squash` disables the default behavior where NFS translates requests from a root user on the client, into a non-privileged user on the host. Great care should be taken when allowing the client to gain access to the host with this setting.

1. Open TCP port 2049 on your head node's firewall by editing `/etc/nftables/main.nft`, and restarting the `nftables` service

1. Export the shares, then start and enable the `nfs-server` service using `systemctl` on the head node.
   ```bash
   sudo exportfs -ar
   sudo systemctl enable nfs-server
   sudo systemctl start nfs-server
   ```
1. Mount the NFS export on your compute nodes
   ```bash
   # You cannot mount /home while you are occupying it
   cd /
   sudo mount -t nfs head:/home /home

   # For SELinux based systems (RHEL, Rocky, Alma, CentOS Stream)
   sudo setsebool -P use_nfs_home_dirs 1
   ```
1. Verify that you successfully mounted `/home` export
   ```bash
   df -h
   ```
1. Edit your `/etc/fstab` to make the effect persist after a restart. Add this entry to the end of your fstab:
   ```conf
   head:/home /home  nfs   defaults,timeo=1800,retrans=5,_netdev	0 0
   ```

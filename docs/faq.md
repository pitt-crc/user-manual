# Frequently asked questions

*In this section we list a series of common problems that CRC users may encounter, with possible solutions.*

**1. A connection attempt to the cluster is rejected with a "POSSIBLE DNS SPOOFING" warning message**

Attempts to connect to h2p.crc.pitt.edu, htc.crc.pitt.edu, or any other remote machine using ssh from your local host machine can fail with the warning message

```commandline
@ WARNING: POSSIBLE DNS SPOOFING DETECTED! @
```

Frequently, this indicates that changes on the remote computer (e.g., an IP address change) have invalidated your local ssh host key for that specific remote machine. Ssh host keys for all remote machines to which you have connected in the past are stored in a file called 

```commandline
.ssh/known_hosts
```

in your home directory on your local machine. Each line in this file contains the name of a remote machine, followed by its host key. Removing the line corresponding to the remote machine that is rejecting an ssh or sftp connection attempt usually solves the problem. 

A valid new host key for the specific remote machine will be created and added automatically to the known\_hosts file next time you attempt to connect to the machine.

As an alternative to modifying the known\_hosts file manually, you can also issue the command

```commandline
ssh-keygen -R remote_machine
```

where remote\_machine is the name or IP address of the computer you are trying to connect to. This will get rid of the cached host key, create a new one, and update the known\_hosts file automatically.

The same issue can also occur when connecting to a remote machine from one of the login nodes of the CRC cluster. Again, removing the offending key from the known\_hosts file on the cluster will solve the problem.

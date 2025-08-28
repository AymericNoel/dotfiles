# Useful commands

- `netstat -tunapl` : to see open ports
- `ss` : new version of netstat
- `df -h` : display partitions and percentage of use
- `rsync -rav source/ dest/` : copy of files & directories
- `ps aux` : see process and pid's
- `pkill "name of resource"` : kill the specified resource. Can also be done with pid
- `pgrep "name of resource"` : give the corresponding pid's
- `strace -p "pid" -f (to list open under program too)` : to trace the calls of this pid to the system (read, write, network communication)
- `curl -vI` : verbose=details, I=header
- `curl --cacert ca.pem https://www.google.com` : copy the site certificate and put it in ca.pem
- `update-ca-trust` : to renew the certificate bundle
- `watch *insert_command*`: run command every s
- `watch -n *insert_command*` : run command every n seconds
- `traceroute "host"`: return all the call until the final host
- `tcpdump -i any -nn -s0 -A` : allows to watch all requests by specifying protocol, port, network,  etc... -i = specify network or any, -nn = Specifies that tcpdump should not perform hostname resolution, which can speed up the capture process, -s0 = Specifies that tcpdump should capture the entire packet, rather than truncating it to the default snapshot length, -A = Specifies that tcpdump should print packet data in ASCII format, which makes it easier to read and analyze. 
- `arp -a` : show arp table (local ip's with mac addresses)
- `ifconfig -a` or `hostname -I` : show local ip
- `ip route` : show the routing table with default gateway
- `lsof -p "pid"` : to see all the history of files opened by this pid
- `lsof | less` : to see little by little the opened files without taking out the whole list
- `fallocate -l 1g myFileName` : reserve 1g of disk space
- `source bashFunction` : to load the file in the current bash session 
- `python -m SimpleHTTPServer 8080` : to create a fast web server
- `ssh-keygen -a xxx -t ed25519 -C "MyComment" -f ~/.ssh/keys/gitlab/account` : to create a ssh key
- `git config core.sshCommand "ssh -i ~/.ssh/keys/gitlab/account -o 'IdentitiesOnly yes'"` : to tell git to take this specific ssh key
- `gpg --expert --full-generate-key` : generate gpg key
- `gpg --list-secret-keys --keyid-format LONG` : list gpg keys
- `git config user.email <your anonymous email address>`
- `gpg --armor --export <key_id>` : export and view the gpg key
- `echo $?` : return error code of last command
- `mktemp`: create a temporary file
- `git config commit.gpgsign true` : tell git to sign commits
- `git config user.signingkey <key_id>` : tell git which key to take
- `file <file>` : return the file type and content type
- `docker save "imageName" | ssh -C destination_host docker load` : save image throw ssh until final host
- `stat <file>` : statistics of a specific file
- `echo mystring | sed s/""//g : s/` : substitute a character by a specific string
- `<command> | tee file` : writes the output of a command in the terminal and also in the specified file
- `git checkout -` : returns to the previous branch
- `git rebase -i HEAD~3` : allows to merge several commits by going back to 3 in an interactive way
- `git reset --soft HEAD^` : undo the previous commit
- `git commit --amend` : rewrite the last commit message
- `git update-index --assume-unchanged package.json` : after putting a versionned file in gitignore, allow to not follow it locally when commits
`- git rebase --onto main old_branch` : allow to do rebase when you create a branch on a branch and the last one is merged in main
- `git reflog` : show the complete history even after rebase
- `git add -p` : allows to choose what to add or not
- `chroot` : to enter in a partition and put it in the main partition of your system to make commands on it directly, access your terminal etc
- `lsblk` : list all partitions with disk
- `sudo fdisk /dev/sda`: to enter the partitions utility then d for delete
- alt + return : delete the left word in terminal prompt
- alt + D : delete the right word in terminal prompt



## How to inspect specific job of a running process 

useful when you quit logs and want to see what it is happening


You ran a series of commands to investigate a specific process (PID `2762498`). Here's a breakdown of each command and its purpose:

---

### **1. `ps -ef | grep python`**  
- **Purpose**: List all running processes and filter for Python-related ones.  
- **Details**:  
  - `ps -ef` → Shows a full-format listing of all processes.  
  - `grep python` → Filters the output to show only lines containing "python".  
- **Why?** To identify Python processes running on the system (likely to find the target PID `2762498`).  

---

### **2. `top`**  
- **Purpose**: Display real-time system processes (CPU, memory, etc.).  
- **Why?** To check overall system resource usage or observe the Python process's activity.  

---

### **3. `strace -p 2762498`**  
- **Purpose**: Trace system calls and signals of the process with PID `2762498`.  
- **Details**:  
  - `strace` → Debugging tool to monitor interactions between a process and the Linux kernel.  
  - `-p 2762498` → Attaches to the specified process ID.  
- **Why?** To see what the process is doing (e.g., file/network operations, hangs, errors).  

---

### **4. `cd /proc/2762498`**  
- **Purpose**: Navigate to the process's directory in `/proc` (virtual filesystem).  
- **Details**:  
  - `/proc/[PID]` → Contains runtime details about the process (memory, open files, etc.).  
  - Initially, you tried `/ptoc` (typo) and corrected it to `/proc`.  
- **Why?** To inspect the process's runtime attributes (e.g., open files, environment variables).  

---

### **5. `ls`**  
- **Purpose**: List files in `/proc/2762498`.  
- **Why?** To see available process information (e.g., `fd`, `cwd`, `exe`, `status`).  

---

### **6. `cd fd` → `ls -l`**  
- **Purpose**: Inspect the process's open file descriptors.  
- **Details**:  
  - `fd` → Subdirectory listing files/sockets opened by the process.  
  - `ls -l` → Shows file descriptors (e.g., `0`=stdin, `1`=stdout, `2`=stderr, others=files/sockets).  
- **Why?** To check what files/sockets the process is using (e.g., logs, network connections).  

---

### **7. `lsof -p 2762498`**  
- **Purpose**: List all open files/sockets for the process.  
- **Details**:  
  - `lsof` → "List Open Files" (includes regular files, pipes, network sockets, etc.).  
  - `-p 2762498` → Filters for the specified PID.  
- **Why?** Alternative to `/proc/[PID]/fd` with more detailed output (e.g., file paths, ports).  

---

### **8. `lsof -p 2762498 | grep 70594527`**  
- **Purpose**: Filter `lsof` output for a specific file/connection (`70594527`).  
- **Why?** To check if the process is interacting with a specific file/socket (e.g., a port, temporary file).  

---

### **Key Takeaways**  
You were investigating a Python process (`2762498`) to:  
1. **Find what it’s doing**: Used `strace` to trace system calls.  
2. **Check open files/sockets**: Used `/proc/[PID]/fd` and `lsof`.  
3. **Filter for specifics**: Grepped for `70594527` (possibly a port, file, or inode).  

This approach is common for debugging hung processes, resource leaks, or unexpected behavior. If you share the actual output, I can help interpret it further!



## Make a live USB key

### Graphical

You can use [Etcher](https://etcher.balena.io/#download-etcher) after downloading the iso file and connecting your USB key.

### Command line

You can use `dd` to mount to partition:

- Check that your file is bootable : `file myOS.iso`
- Identify with `fdisk -l` the disk of the usb key
- Run `sudo dd if=kali-linux-2025.2-live-amd64.iso of=/dev/sdX conv=fsync bs=4M status=progress`

### Wipe usb live boot to put back file partition

- `fidsk -l` to see the correct partition
- `umount /dev/sdX` to umount the partition 
- `sudo fdisk /dev/sdX` to start interactive prompt 
- Press `d` to delete the partition
- Press `o` to create new DOS partition table
- Press `n` to create new primary partition
- Format partition with `sudo mkfs.ext4 /dev/sdX1`
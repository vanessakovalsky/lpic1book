# LPIC 1 -101 -5020 - Practice test 1

Voici un examen blanc pour vous entrainer pour le LPIC-1-101 en version 5

Il comporte 60 questions et vous avez 90 minutes pour répondre

---

1. Which type of file system is created by mkfs when it is executed with the block device name only and without any additional parameters? 
    -  XFS 
    -  VFAT 
    -  ext2 
    -  ext3 
    -  ext4 
2. Which umask value ensures that new directories can be read, written and listed by their owning user, read and listed by their owning group and are not accessible at all for everyone else? 
    - 0750 
    - 0027 
    - 0036 
    - 7640 
    - 0029 
3. Which is the default percentage of reserved space for the root user on new ext4 filesystems? 
    - 10% 
    - 3% 
    - 15% 
    - 0% 
    - 5% 
4. Which of the following is true when a file system, which is neither listed in /etc/fstab nor known to system, is mounted manually? 
    - systemd ignores any manual mounts which are not done using the systemctl mount command 
    - The command systemctl mountsync can be used to create a mount unit based on the existing mount 
    - systemd automatically generates a mount unit and monitors the mount point without changing it 
    - Unless a systemd mount unit is created, systemd unmounts the file system after a short period of time 
    - systemctl unmount must be used to remove the mount because system opens a file descriptor on
5. FILL BLANK - Which program updates the database that is used by the locate command? (Specify ONLY the command without any path or parameters)
6. What does the command mount --bind do? 
    - It makes the contents of one directory available in another directory 
    - It mounts all available filesystems to the current directory 
    - It mounts all user mountable filesystems to the user's home directory 
    - It mounts all file systems listed in /etc/fstab which have the option userbind set 
    - It permanently mounts a regular file to a directory 
7. In order to display all currently mounted filesystems, which of the following commands could be used? (Choose two.) 
    - cat /proc/self/mounts 
    - free 
    - lsmounts 
    - mount 
    - cat /proc/filesystems 
8. FILL BLANK - 
Which command displays the current disk space usage for all mounted file systems? (Specify ONLY the command without any path or parameters.) 
9. Which chown command changes the ownership to dave and the group to staff on a file named data.txt? 
    - chown dave/staff data.txt 
    - chown ""u dave ""g staff data.txt 
    - chown --user dave --group staff data.txt 
    - chown dave+staff data.txt 
    - chown dave:staff data.txt 

10. When considering the use of hard links, what are valid reasons not to use hard links? 
- Hard links are not available on all Linux systems because traditional filesystems, such as ext4, do not support them 
- Each hard link has individual ownership, permissions and ACLs which can lead to unintended disclosure of file content 
- Hard links are specific to one filesystem and cannot point to files on another filesystem 
- If users other than root should be able to create hard links, suln has to be installed and configured 
- When a hard linked file is changed, a copy of the file is created and consumes additional space 

11. In compliance with the FHS, in which of the directories are man pages found? 
- /opt/man/ 
- /usr/doc/ 
- /usr/share/man/ 
- /var/pkg/man 
- /var/man/ 

12. What is the process ID number of the init process on a System V init based system? 
- -1 
- 0 
- 1 
- It is different with each reboot 
- It is set to the current run level 

13. Which daemon handles power management events on a Linux system? 
- acpid 
- batteryd 
- pwrmgntd 
- psd 
- inetd 

14. Which of the following statements are true about the boot sequence of a PC using a BIOS? (Choose two.) 
- Some parts of the boot process can be configured from the BIOS 
- Linux does not require the assistance of the BIOS to boot a computer 
- The BIOS boot process starts only if secondary storage, such as the hard disk, is functional 
- The BIOS initiates the boot process after turning the computer on 
- The BIOS is started by loading hardware drivers from secondary storage, such as the hard disk 

15. What is true regarding UEFI firmware? (Choose two.) 
- It can read and interpret partition tables 
- It can use and read certain file systems 
- It stores its entire configuration on the /boot/ partition 
- It is stored in a special area within the GPT metadata 
- It is loaded from a fixed boot disk position 

16. When is the content of the kernel ring buffer reset? (Choose two.) 
- When the ring buffer is explicitly reset using the command dmesg --clear 
- When the ring buffer is read using dmesg without any additional parameters 
- When a configurable amount of time, 15 minutes by default, has passed 
- When the kernel loads a previously unloaded kernel module 
- When the system is shut down or rebooted 

17. What is the first program the Linux kernel starts at boot time when using System V init? 
- /lib/init.so 
- /proc/sys/kernel/init 
- /etc/rc.d/rcinit 
- /sbin/init 
- /boot/init 

18. A Debian package creates several files during its installation. Which of the following commands searches for packages owning the file /etc/debian_version? 
- apt-get search /etc/debian_version 
- apt ""r /etc/debian_version 
- find /etc/debian_version -dpkg 
- dpkg ""S /etc/debian_version 
- apt-file /etc/debian_version

19. Which of the following directories on a 64 bit Linux system typically contain shared libraries? (Choose two.) 
- ~/.lib64/ 
- /usr/lib64/ 
- /var/lib64/ 
- /lib64/ 
- /opt/lib64/ 

20. Which of the following files exist in a standard GRUB 2 installation? (Choose two.) 
- /boot/grub/stages/stage0 
- /boot/grub/i386-pc/lvm.mod 
- /boot/grub/fstab 
- /boot/grub/grub.cfg 
- /boot/grub/linux/vmlinuz 

21. Which of the following commands installs all packages with a name ending with the string foo? 
- apt get "*foo" 
- apt update "foo?" 
- apt force "foo*" 
- apt install "*foo" 
- apt add ".*foo" 

22. Which of the following properties of a Linux system should be changed when a virtual machine is cloned? (Choose two.) 
- The partitioning scheme 
- The file system 
- The D-Bus Machine ID 
- The permissions of /root/ 
- The SSH host keys 

23. Which of the following commands installs GRUB 2 into the master boot record on the third hard disk? 
- grub2 install /dev/sdc 
- grub-mkrescue /dev/sdc 
- grub-mbrinstall /dev/sdc 
- grub-setup /dev/sdc 
- grub-install /dev/sdc 

24. Which of the following partition types is used for Linux swap spaces when partitioning hard disk drives? 
- 7 
- 82 
- 83 
- 8e 
- fd 

25. What is true regarding the configuration of yum? (Choose two.) 
- Changes to the repository configuration become active after running yum confupdate 
- Changes to the yum configuration become active after restarting the yumd service 
- The configuration of package repositories can be divided into multiple files 
- Repository configurations can include variables such as $basearch or $releasever 
- In case /etc/yum.repos.d/ contains files, /etc/yum.conf is ignored 

26. Which command uninstalls a package but keeps its configuration files in case the package is re-installed? 
- dpkg ""s pkgname 
- dpkg ""L pkgname 
- dpkg ""P pkgname 
- dpkg ""v pkgname 
- dpkg ""r pkgname 

27. Which of the following commands lists the dependencies of the RPM package file foo.rpm? 
- rpm ""qpR foo.rpm 
- rpm ""dep foo 
- rpm ""ld foo.rpm 
- rpm ""R foo.rpm 
- rpm ""pD foo 

28. What is the maximum niceness value that a regular user can assign to a process with the nice command when executing a new process? 
- 9 
- 15 
- 19 
- 49 
- 99 

29. Which of the following commands list all files and directories within the /tmp/ directory and its subdirectories which are owned by the user root? (Choose two.) 
- find /tmp ""user root -print 
- find ""path /tmp ""uid root 
- find /tmp ""uid root -print 
- find /tmp ""user root 
- find ""path /tmp ""user root -print 

30. Which of the following are valid stream redirection operators within Bash? (Choose two.) 
- < 
- #> 
- %> 
- >>> 
- 2>&1 

31. Which of the following vi commands deletes two lines, the current and the following line? 
- d2 
- 2d 
- 2dd 
- dd2 
- de12 

32. From a Bash shell, which of the following commands directly execute the instructions from the file /usr/local/bin/runme.sh without starting a subshell? 
(Choose two.) 
- source /usr/local/bin/runme.sh 
- /usr/local/bin/runme.sh 
- /bin/bash /usr/local/bin/runme.sh 
- . /usr/local/bin/runme.sh 
- run /usr/local/bin/runme.sh 

33. FILL BLANK - 
Which program runs a command in specific intervals and refreshes the display of the program's output? (Specify ONLY the command without any path or parameters.) 

34. Immediately after deleting 3 lines of text in vi and moving the cursor to a different line, which single character command will insert the deleted content below the current line? 
- i (lowercase) 
- p (lowercase) 
- P (uppercase) 
- U (uppercase) 
- u (lowercase) 

35. Which of the following commands changes all CR-LF line breaks in the text file userlist.txt to Linux standard LF line breaks and stores the result in newlist.txt? 
- tr ""d "˜\r' < userlist.txt > newlist.txt 
- tr ""c "˜\n\r' "˜' <newlist.txt> userlist.txt 
- tr "˜\r\n' "˜' <userlist.txt> newlist.txt 
- tr "˜\r' "˜\n' userlist.txt newlist.txt 
- tr ""s "˜/^M/^J/' userlist.txt newlist.txt 

36. Which command must be entered before exiting vi to save the current file as filea.txt? 
- %s filea.txt 
- %w filea.txt 
- :save filea.txt 
- :w filea.txt 
- :s filea.txt 

37. Which of the following commands displays the output of the foo command on the screen and also writes it to a file called /tmp/foodata? 
- foo | less /tmp/foodata 
- foo | cp /tmp/foodata 
- foo > /tmp/foodata 
- foo | tee /tmp/foodata 
- foo > stdout >> /tmp/foodata 

38. What output will be displayed when the user fred executes the following command? echo "˜fred $USER' 
- fred fred 
- fred /home/fred/ 
- "˜fred $USER' 
- fred $USER 
- "˜fred fred' 

39. Which of the following commands displays the path to the executable file that would be executed when the command foo is invoked? 
- lsattr foo 
- apropos foo 
- locate foo 
- whatis foo 
- which foo 

40. When redirecting the output of find to the xargs command, what option to find is useful if the filenames contain spaces? 
- ""rep-space 
- -printnul 
- -nospace 
- ""ignore-space 
- ""print0 

41. Which of the following commands can be used to determine how long the system has been running? (Choose two.) 
- uptime 
- up 
- time --up 
- uname ""u 
- top 

42. What is true regarding the command 
ls > files 
if files does not exist? 
- The output of ls is printed to the terminal 
- files is created and contains the output of ls 
- An error message is shown and ls is not executed 
- The command files is executed and receives the output of ls 
- Any output of ls is discarded 

43. Which wildcards will match the following filenames? (Choose two.) ttyS0 ttyS1 ttyS2 
- ttyS[1-5] 
- tty?[0-5] 
- tty*2 
- tty[A-Z][012] 
- tty[Ss][02] 

44. Which of the following commands redirects the output of ls to standard error? 
- ls >-1 
- ls <<ERR 
- ls >&2 
- ls >>2 
- ls |error 

45. Which of the following commands displays the contents of a gzip compressed tar archive? 
- gzip archive.tgz | tar xvf - 
- tar ""fzt archive.tgz 
- gzip ""d archive.tgz | tar tvf - 
- tar cf archive.tgz 
- tar ztf archive.tgz 

46. Which of the following commands prints a list of usernames (first column) and their primary group (fourth column) from the /etc/passwd file? 
- fmt ""f 1,4 /etc/passwd 
- cut ""d : -f 1,4 /etc/passwd 
- sort ""t : -k 1,4 /etc/passwd 
- paste ""f 1,4 /etc/passwd 
- split ""c 1,4 /etc/passwd 

47. Which of the following regular expressions represents a single upper-case letter? 
- :UPPER: 
- [A-Z] 
- !a-z 
- %C 
- {AZ} 

48. FILL BLANK - 
Which command is used to start another command with a given nice level? (Specify ONLY the command without any path or parameters.)

49. Given a log file loga.log with timestamps of the format DD/MM/YYYY:hh:mm:ss, which command filters out all log entries in the time period between 8:00 am and 8:59 am? 
- grep ""E "˜:08:[09]+:[09]+' loga.log 
- grep ""E "˜:08:[00]+' loga.log 
- grep ""E loga.log "˜:08:[0-9]+:[0-9]+' 
- grep loga.log "˜:08:[0-9]:[0-9]' 
- grep ""E "˜:08:[0-9]+:[0-9]+' loga.log 

50. Instead of supplying an explicit device in /etc/fstab for mounting, what other options may be used to identify the intended partition? (Choose two.) 
- LABEL 
- ID 
- FIND 
- NAME 
- UUID 

51. A yum repository can declare sets of related packages. Which yum command installs all packages belonging to the group admintools? 
- yum pkgsel --install admintools 
- yum install admintools/* 
- yum groupinstall admintools 
- yum taskinstall admintools 
- yum collection install admintools 

52. FILL BLANK - 
What directory contains configuration files for additional yum repositories? (Specify the full path to the directory.)

53. Which of the following commands installs the GRUB boot files into the currently active file systems and the boot loader into the first partition of the first disk? 
- grub-install /dev/sda 
- grub-install /dev/sda1 
- grub-install current /dev/sda0 
- grub-install /dev/sda0 
- grub-install current /dev/sda1 

54. Which of the following files are found in the /boot/ file system? (Choose two.) 
- Linux kernel images 
- Bash shell binaries 
- systemd target and service units 
- Initial ramdisk images 
- fsck binaries 

55. Which file defines the network locations from where the Debian package manager downloads software packages? 
- /etc/dpkg/dpkg.cfg 
- /etc/apt/apt.conf.d 
- /etc/apt/apt.conf 
- /etc/dpkg/dselect.cfg 
- /etc/apt/sources.list 

56. When removing a package on a system using dpkg package management, which dpkg option ensures configuration files are removed as well? 
- --clean 
- --purge 
- --vacuum 
- --remove 
- --declare 

57. Which of the following statements are correct when comparing Linux containers with traditional virtual machines (e.g. LXC vs. KVM)? (Choose three.) 
- Containers are a lightweight virtualization method where the kernel controls process isolation and resource management. 
- Fully virtualized machines can run any operating system for a specific hardware architecture within the virtual machin--Containers are completely decoupled from the host system's physical hardware and can only use emulated virtual hardware devices. 
- The guest environment for fully virtualized machines is created by a hypervisor which provides virtual and emulated hardware devices. 
- Containers on the same host can use different operating systems, as the container hypervisor creates separate kernel execution. 

58. The installation of a local Debian package failed due to unsatisfied dependencies. Which of the following commands installs missing dependencies and completes the interrupted package installation? 
- dpkg --fix --all 
- apt-get autoinstall 
- dpkg-reconfigure --all 
- apt-get all 
- apt-get install -f 

59. Which of the following commands are valid in the GRUB 2 configuration file? (Choose two.) 
- menuentry 
- uefi 
- pxe-ifconfig 
- insmod 
- kpartx 

60. What is the purpose of the ldd command? 
- It lists which shared libraries a binary needs to run. 
- It installs and updates installed shared libraries. 
- It turns a dynamically linked binary into a static binary. 
- It defines which version of a library should be used by default. 
- It runs a binary with an alternate library search path. 
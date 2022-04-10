# LPIC 1 -101 -5020 - Practice test 1

Voici un examen blanc pour vous entrainer pour le LPIC-1-101 en version 5

Il comporte 60 questions et vous avez 90 minutes pour répondre

1. Which type of file system is created by mkfs when it is executed with the block device name only and without any additional parameters? 
A. XFS 
B. VFAT 
C. ext2 
D. ext3 
E. ext4 

2. Which umask value ensures that new directories can be read, written and listed by their owning user, read and listed by their owning group and are not accessible at all for everyone else? 
A. 0750 
B. 0027 
C. 0036 
D. 7640 
E. 0029 

3. Which is the default percentage of reserved space for the root user on new ext4 filesystems? 
A. 10% 
B. 3% 
C. 15% 
D. 0% 
E. 5% 

4. Which of the following is true when a file system, which is neither listed in /etc/fstab nor known to system, is mounted manually? 
A. systemd ignores any manual mounts which are not done using the systemctl mount command 
B. The command systemctl mountsync can be used to create a mount unit based on the existing mount 
C. systemd automatically generates a mount unit and monitors the mount point without changing it 
D. Unless a systemd mount unit is created, systemd unmounts the file system after a short period of time 
E. systemctl unmount must be used to remove the mount because system opens a file descriptor on

5. FILL BLANK - Which program updates the database that is used by the locate command? (Specify ONLY the command without any path or parameters)

6. What does the command mount --bind do? 
A. It makes the contents of one directory available in another directory 
B. It mounts all available filesystems to the current directory 
C. It mounts all user mountable filesystems to the user's home directory 
D. It mounts all file systems listed in /etc/fstab which have the option userbind set 
E. It permanently mounts a regular file to a directory 

7. In order to display all currently mounted filesystems, which of the following commands could be used? (Choose two.) 
A. cat /proc/self/mounts 
B. free 
C. lsmounts 
D. mount 
E. cat /proc/filesystems 

8. FILL BLANK - 
Which command displays the current disk space usage for all mounted file systems? (Specify ONLY the command without any path or parameters.) 

9. Which chown command changes the ownership to dave and the group to staff on a file named data.txt? 
A. chown dave/staff data.txt 
B. chown ""u dave ""g staff data.txt 
C. chown --user dave --group staff data.txt 
D. chown dave+staff data.txt 
E. chown dave:staff data.txt 

10. When considering the use of hard links, what are valid reasons not to use hard links? 
A. Hard links are not available on all Linux systems because traditional filesystems, such as ext4, do not support them 
B. Each hard link has individual ownership, permissions and ACLs which can lead to unintended disclosure of file content 
C. Hard links are specific to one filesystem and cannot point to files on another filesystem 
D. If users other than root should be able to create hard links, suln has to be installed and configured 
E. When a hard linked file is changed, a copy of the file is created and consumes additional space 

11. In compliance with the FHS, in which of the directories are man pages found? 
A. /opt/man/ 
B. /usr/doc/ 
C. /usr/share/man/ 
D. /var/pkg/man 
E. /var/man/ 

12. What is the process ID number of the init process on a System V init based system? 
A. -1 
B. 0 
C. 1 
D. It is different with each reboot 
E. It is set to the current run level 

13. Which daemon handles power management events on a Linux system? 
A. acpid 
B. batteryd 
C. pwrmgntd 
D. psd 
E. inetd 

14. Which of the following statements are true about the boot sequence of a PC using a BIOS? (Choose two.) 
A. Some parts of the boot process can be configured from the BIOS 
B. Linux does not require the assistance of the BIOS to boot a computer 
C. The BIOS boot process starts only if secondary storage, such as the hard disk, is functional 
D. The BIOS initiates the boot process after turning the computer on 
E. The BIOS is started by loading hardware drivers from secondary storage, such as the hard disk 

15. What is true regarding UEFI firmware? (Choose two.) 
A. It can read and interpret partition tables 
B. It can use and read certain file systems 
C. It stores its entire configuration on the /boot/ partition 
D. It is stored in a special area within the GPT metadata 
E. It is loaded from a fixed boot disk position 

16. When is the content of the kernel ring buffer reset? (Choose two.) 
A. When the ring buffer is explicitly reset using the command dmesg --clear 
B. When the ring buffer is read using dmesg without any additional parameters 
C. When a configurable amount of time, 15 minutes by default, has passed 
D. When the kernel loads a previously unloaded kernel module 
E. When the system is shut down or rebooted 

17. What is the first program the Linux kernel starts at boot time when using System V init? 
A. /lib/init.so 
B. /proc/sys/kernel/init 
C. /etc/rc.d/rcinit 
D. /sbin/init 
E. /boot/init 

18. A Debian package creates several files during its installation. Which of the following commands searches for packages owning the file /etc/debian_version? 
A. apt-get search /etc/debian_version 
B. apt ""r /etc/debian_version 
C. find /etc/debian_version -dpkg 
D. dpkg ""S /etc/debian_version 
E. apt-file /etc/debian_version

19. Which of the following directories on a 64 bit Linux system typically contain shared libraries? (Choose two.) 
A. ~/.lib64/ 
B. /usr/lib64/ 
C. /var/lib64/ 
D. /lib64/ 
E. /opt/lib64/ 

20. Which of the following files exist in a standard GRUB 2 installation? (Choose two.) 
A. /boot/grub/stages/stage0 
B. /boot/grub/i386-pc/lvm.mod 
C. /boot/grub/fstab 
D. /boot/grub/grub.cfg 
E. /boot/grub/linux/vmlinuz 

21. Which of the following commands installs all packages with a name ending with the string foo? 
A. zypper get "*foo" 
B. zypper update "foo?" 
C. zypper force "foo*" 
D. zypper install "*foo" 
E. zypper add ".*foo" 

22. Which of the following properties of a Linux system should be changed when a virtual machine is cloned? (Choose two.) 
A. The partitioning scheme 
B. The file system 
C. The D-Bus Machine ID 
D. The permissions of /root/ 
E. The SSH host keys 

23. Which of the following commands installs GRUB 2 into the master boot record on the third hard disk? 
A. grub2 install /dev/sdc 
B. grub-mkrescue /dev/sdc 
C. grub-mbrinstall /dev/sdc 
D. grub-setup /dev/sdc 
E. grub-install /dev/sdc 

24. Which of the following partition types is used for Linux swap spaces when partitioning hard disk drives? 
A. 7 
B. 82 
C. 83 
D. 8e 
E. fd 

25. What is true regarding the configuration of yum? (Choose two.) 
A. Changes to the repository configuration become active after running yum confupdate 
B. Changes to the yum configuration become active after restarting the yumd service 
C. The configuration of package repositories can be divided into multiple files 
D. Repository configurations can include variables such as $basearch or $releasever 
E. In case /etc/yum.repos.d/ contains files, /etc/yum.conf is ignored 

26. Which command uninstalls a package but keeps its configuration files in case the package is re-installed? 
A. dpkg ""s pkgname 
B. dpkg ""L pkgname 
C. dpkg ""P pkgname 
D. dpkg ""v pkgname 
E. dpkg ""r pkgname 

27. Which of the following commands lists the dependencies of the RPM package file foo.rpm? 
A. rpm ""qpR foo.rpm 
B. rpm ""dep foo 
C. rpm ""ld foo.rpm 
D. rpm ""R foo.rpm 
E. rpm ""pD foo 

28. What is the maximum niceness value that a regular user can assign to a process with the nice command when executing a new process? 
A. 9 
B. 15 
C. 19 
D. 49 
E. 99 

29. Which of the following commands list all files and directories within the /tmp/ directory and its subdirectories which are owned by the user root? (Choose two.) 
A. find /tmp ""user root -print 
B. find ""path /tmp ""uid root 
C. find /tmp ""uid root -print 
D. find /tmp ""user root 
E. find ""path /tmp ""user root -print 

30. Which of the following are valid stream redirection operators within Bash? (Choose two.) 
A. < 
B. #> 
C. %> 
D. >>> 
E. 2>&1 

31. Which of the following vi commands deletes two lines, the current and the following line? 
A. d2 
B. 2d 
C. 2dd 
D. dd2 
E. de12 

32. From a Bash shell, which of the following commands directly execute the instructions from the file /usr/local/bin/runme.sh without starting a subshell? 
(Choose two.) 
A. source /usr/local/bin/runme.sh 
B. /usr/local/bin/runme.sh 
C. /bin/bash /usr/local/bin/runme.sh 
D. . /usr/local/bin/runme.sh 
E. run /usr/local/bin/runme.sh 

33. FILL BLANK - 
Which program runs a command in specific intervals and refreshes the display of the program's output? (Specify ONLY the command without any path or parameters.) 

34. Immediately after deleting 3 lines of text in vi and moving the cursor to a different line, which single character command will insert the deleted content below the current line? 
A. i (lowercase) 
B. p (lowercase) 
C. P (uppercase) 
D. U (uppercase) 
E. u (lowercase) 

35. Which of the following commands changes all CR-LF line breaks in the text file userlist.txt to Linux standard LF line breaks and stores the result in newlist.txt? 
A. tr ""d "˜\r' < userlist.txt > newlist.txt 
B. tr ""c "˜\n\r' "˜' <newlist.txt> userlist.txt 
C. tr "˜\r\n' "˜' <userlist.txt> newlist.txt 
D. tr "˜\r' "˜\n' userlist.txt newlist.txt 
E. tr ""s "˜/^M/^J/' userlist.txt newlist.txt 

36. Which command must be entered before exiting vi to save the current file as filea.txt? 
A. %s filea.txt 
B. %w filea.txt 
C. :save filea.txt 
D. :w filea.txt 
E. :s filea.txt 

37. Which of the following commands displays the output of the foo command on the screen and also writes it to a file called /tmp/foodata? 
A. foo | less /tmp/foodata 
B. foo | cp /tmp/foodata 
C. foo > /tmp/foodata 
D. foo | tee /tmp/foodata 
E. foo > stdout >> /tmp/foodata 

38. What output will be displayed when the user fred executes the following command? echo "˜fred $USER' 
A. fred fred 
B. fred /home/fred/ 
C. "˜fred $USER' 
D. fred $USER 
E. "˜fred fred' 

39. Which of the following commands displays the path to the executable file that would be executed when the command foo is invoked? 
A. lsattr foo 
B. apropos foo 
C. locate foo 
D. whatis foo 
E. which foo 

40. When redirecting the output of find to the xargs command, what option to find is useful if the filenames contain spaces? 
A. ""rep-space 
B. -printnul 
C. -nospace 
D. ""ignore-space 
E. ""print0 

41. Which of the following commands can be used to determine how long the system has been running? (Choose two.) 
A. uptime 
B. up 
C. time --up 
D. uname ""u 
E. top 

42. What is true regarding the command 
ls > files 
if files does not exist? 
A. The output of ls is printed to the terminal 
B. files is created and contains the output of ls 
C. An error message is shown and ls is not executed 
D. The command files is executed and receives the output of ls 
E. Any output of ls is discarded 

43. Which wildcards will match the following filenames? (Choose two.) ttyS0 ttyS1 ttyS2 
A. ttyS[1-5] 
B. tty?[0-5] 
C. tty*2 
D. tty[A-Z][012] 
E. tty[Ss][02] 

44. Which of the following commands redirects the output of ls to standard error? 
A. ls >-1 
B. ls <<ERR 
C. ls >&2 
D. ls >>2 
E. ls |error 

45. Which of the following commands displays the contents of a gzip compressed tar archive? 
A. gzip archive.tgz | tar xvf - 
B. tar ""fzt archive.tgz 
C. gzip ""d archive.tgz | tar tvf - 
D. tar cf archive.tgz 
E. tar ztf archive.tgz 

46. Which of the following commands prints a list of usernames (first column) and their primary group (fourth column) from the /etc/passwd file? 
A. fmt ""f 1,4 /etc/passwd 
B. cut ""d : -f 1,4 /etc/passwd 
C. sort ""t : -k 1,4 /etc/passwd 
D. paste ""f 1,4 /etc/passwd 
E. split ""c 1,4 /etc/passwd 

47. Which of the following regular expressions represents a single upper-case letter? 
A. :UPPER: 
B. [A-Z] 
C. !a-z 
D. %C 
E. {AZ} 

48. FILL BLANK - 
Which command is used to start another command with a given nice level? (Specify ONLY the command without any path or parameters.)

49. Given a log file loga.log with timestamps of the format DD/MM/YYYY:hh:mm:ss, which command filters out all log entries in the time period between 8:00 am and 8:59 am? 
A. grep ""E "˜:08:[09]+:[09]+' loga.log 
B. grep ""E "˜:08:[00]+' loga.log 
C. grep ""E loga.log "˜:08:[0-9]+:[0-9]+' 
D. grep loga.log "˜:08:[0-9]:[0-9]' 
E. grep ""E "˜:08:[0-9]+:[0-9]+' loga.log 

50. Instead of supplying an explicit device in /etc/fstab for mounting, what other options may be used to identify the intended partition? (Choose two.) 
A. LABEL 
B. ID 
C. FIND 
D. NAME 
E. UUID 

51. A yum repository can declare sets of related packages. Which yum command installs all packages belonging to the group admintools? 
A. yum pkgsel --install admintools 
B. yum install admintools/* 
C. yum groupinstall admintools 
D. yum taskinstall admintools 
E. yum collection install admintools 

52. FILL BLANK - 
What directory contains configuration files for additional yum repositories? (Specify the full path to the directory.)

53. Which of the following commands installs the GRUB boot files into the currently active file systems and the boot loader into the first partition of the first disk? 
A. grub-install /dev/sda 
B. grub-install /dev/sda1 
C. grub-install current /dev/sda0 
D. grub-install /dev/sda0 
E. grub-install current /dev/sda1 

54. Which of the following files are found in the /boot/ file system? (Choose two.) 
A. Linux kernel images 
B. Bash shell binaries 
C. systemd target and service units 
D. Initial ramdisk images 
E. fsck binaries 

55. Which file defines the network locations from where the Debian package manager downloads software packages? 
A. /etc/dpkg/dpkg.cfg 
B. /etc/apt/apt.conf.d 
C. /etc/apt/apt.conf 
D. /etc/dpkg/dselect.cfg 
E. /etc/apt/sources.list 

56. When removing a package on a system using dpkg package management, which dpkg option ensures configuration files are removed as well? 
A. --clean 
B. --purge 
C. --vacuum 
D. --remove 
E. --declare 

57. Which of the following statements are correct when comparing Linux containers with traditional virtual machines (e.g. LXC vs. KVM)? (Choose three.) 
A. Containers are a lightweight virtualization method where the kernel controls process isolation and resource management. 
B. Fully virtualized machines can run any operating system for a specific hardware architecture within the virtual machine. C. Containers are completely decoupled from the host system's physical hardware and can only use emulated virtual hardware devices. 
D. The guest environment for fully virtualized machines is created by a hypervisor which provides virtual and emulated hardware devices. 
E. Containers on the same host can use different operating systems, as the container hypervisor creates separate kernel execution. 

58. The installation of a local Debian package failed due to unsatisfied dependencies. Which of the following commands installs missing dependencies and completes the interrupted package installation? 
A. dpkg --fix --all 
B. apt-get autoinstall 
C. dpkg-reconfigure --all 
D. apt-get all 
E. apt-get install -f 

59. Which of the following commands are valid in the GRUB 2 configuration file? (Choose two.) 
A. menuentry 
B. uefi 
C. pxe-ifconfig 
D. insmod 
E. kpartx 

60. What is the purpose of the ldd command? 
A. It lists which shared libraries a binary needs to run. 
B. It installs and updates installed shared libraries. 
C. It turns a dynamically linked binary into a static binary. 
D. It defines which version of a library should be used by default. 
E. It runs a binary with an alternate library search path. 
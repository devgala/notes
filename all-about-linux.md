# Basic commands
- `ls`: list files in directory
- `cd`: change directory
- `passwd`: change password of current user
- `file <filename>`: display file type of file
- `cat <filename>`: read contents on file
- `pwd`: present working directory

#Files and file systems
- On a UNIX system, everything is a file; if something is not a file, it is a process.
- A Linux system, just like UNIX, makes no difference between a file and a directory, since a directory is just a file containing names of other files. Programs, services, texts, images, and so forth, are all files. Input and output devices, and generally all devices, are considered to be files, according to the system.
- Most files are just files, called regular *files*; they contain normal data.
- Not regular files:
	- *Directories*: files that are list of other files
	- *Special files*: the mechanism used for input and output. Most special files are in /dev
	- *Links*: a system to make a file or directory visible in multiple parts of the system's file tree
	- *(Domain) sockets*: a special file type, similar to TCP/IP sockets, providing inter-process networking protected by the file system's access control.
	- *Named pipes*: act more or less like sockets and form a way for processes to communicate with each other, without using network socket semantics

## Partitions
- Linux uses more than one partition on the same disk, even when using the standard installation procedure.
- This is primarily done for security and robustness reasons, so that if disaster strikes, your computer does not become unusable.
- 2 major partitions:
	- *Data*: normal Linux system data, including the root partition containing all the data to start up and run the system.
	- *Swap*: expansion of the computer's physical memory, extra memory on hard disk.
- View partitions using: `fdisk -l`

### Mount Points:
- All partitions are attached via mount points.
- The mount point defines the place of a particular data set in the file system.
- `/etc/fstab` describes how all partitions are mounted during system startup.
- use `df -h` to see information about partitions (active non-swap). This will also display partitions on the network. 

## File system tree
- tree starts at root directory `/`.
- `/bin`: Common programs, shared by the system, the system administrator and the users.
- `/boot`: startup files and kernel, in some distributions GRUB data.
- `/dev`: Refs to all CPU peripheral hardware, represented as files with special properties.
- `/etc`: most imp system config files.
- `/home`: home directories of common users.
- `/lib`: Library files, includes files for all kinds of programs needed by the system and the users.
- `/lost+found`: Every partition has a lost+found in its upper directory. Files that were saved during failures are here.
- `/mnt`: Standard mount point for external file systems.
- `/net`: Standard mount point for entire remote file systems.
- `/proc`: A virtual file system containing information about system resources. use `man proc` for more details.
- `/root`: The administrative user's home directory. This is not the same as `/`.
- `/sbin`: Programs for use by the system and the system administrator.
- `/tmp`: Temporary space for use by the system, cleaned upon reboot.
- `/usr`: Programs, libraries, documentation etc. for all user-related programs.
- `/var`: Storage for all variable files and temporary files created by users.


### Index Nodes: inodes
From here: https://www.reddit.com/r/linux4noobs/comments/13g6h1m/comment/jjytnhq

An inode stores all metadata about a file*, except for the filename.

*In this case “file” refers to regular files, directories, soft links, Unix sockets, character and block files (the /dev files), and named pipes.

So, a filename is the only metadata that isn’t stored in the inode. Filenames come from their directory entries. Directories are just special files that map an inode number to a string filename. Each inode is numbered and usually represents an offset in some array-like structure in the filesystem. This mapping between inode to filename is a hard link. A file must have 1 or morehard links to be accessible. If you create another hard link, you’re just pointing another filename to the same inode. All of them are equally “the file”, and there’s no way to detect which hard link came first. As part of the inode contents, there’s a counter of how many hard links each inode has. It’s eligible for cleanup and reuse when this count is zero.

The root directory is usually some specially reserved inode number.

You asked about symbolic links. As I mentioned above, they’re a special kind of file. The filesystem knows to interpret its contents differently. The content of a directory is the mapping for filenames, but the content for symbolic links (soft links) is a file path string. Symlinks consume new inodes, and they do not increment the destination file’s hard link count. Deleting the destination file does not update any symlinks pointing to them.

Some filesystems like ext4 preallocate all the space for all the inodes that will ever be supported. If you have too many tiny files, the filesystem runs out of unallocated inodes and it will not allow creation of new files. There’s usually a reserved percentage of inodes for the root user, so that the system can keep running for a while and it can be cleaned up.

You asked where they’re stored. That depends on the filesystem format. I think ext4 breaks up the disk space into large regions, and each region has its own section where inodes are stored….That’s implementation defined, and it’s been a while since I looked up ext4 internals.

ext4 just isn’t designed to increase inode count on-the-fly. It’s a limitation of its design.

Other filesystems don’t preallocate space for inodes, and the total amount of files depends on the amount of free space available. I think Btrfs does this.

### $PATH
This variable lists those directories in the system where executable files can be found, and thus saves the user a lot of typing and memorizing locations of commands.
```
rogier:> echo $PATH
/opt/local/bin:/usr/X11R6/bin:/usr/bin:/usr/sbin/:/bin
```
The directories are `:` separated. The search stops when command is found, so if there are duplicate commands, we use the one found first in the string defined by $PATH.


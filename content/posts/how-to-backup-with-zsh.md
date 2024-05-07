---
title: "How to Backup and Upload With Zsh"
date: 2024-05-07T16:55:49-03:00
draft: false
toc: false
images:
tags:
  - Backup
  - Zsh
  - Shell
---

In today's digital age, data backup and synchronization have become crucial aspects of maintaining our digital lives. Whether it's personal projects, work documents, or family photos, ensuring that our data is safe and easily accessible is paramount. In this blog post, I'll introduce you to a set of custom scripts designed to automate the backup and upload process of a specified directory. These scripts, written in Zsh and Bash, leverage powerful tools like `rsync`, `tar`, and `rclone` to streamline your backup and upload tasks.

## A Closer Look at the Scripts

### The Backup Script: `do_backup.zsh`

Our journey begins with the `do_backup.zsh` script, a reliable companion for creating backups of your specified directories. It employs `rsync` to mirror files from the source directory to a designated backup location, followed by a compression into a neat `.tar.gz` file. This script is not just about copying files; it's about safeguarding your data with minimal effort.

```sh
#!/usr/bin/env zsh

# Script Name:  backup.zsh
# Author:       Pablo Andrade
# Created:      28/11/2023
# Version:      1.2

# To decompress
#tar -xzvf $bkp_file

# Debugging ON/OFF
set -x

#script_path="$(dirname "${BASH_SOURCE[0]}")"

# Variables
project_directory="/home/pablodeas/Workspace/Projects/pessoal/do_backup"
source "$project_directory/config.sh"

# Remove Last Backup File
function remove_last () {
	echo " --- "
	echo "-> Removing last Backup File!..."
	echo " --- "
	/usr/bin/rm -f $bkp_last
	
	if [ $? -eq 0 ]; then
		echo $msg_sucess
	else
		echo $msg_error
	fi
}

# Backup
function exec_bkp () {
	echo " --- "
	echo "-> Starting Backup..."
	echo " --- "
	rsync -av --progress --partial --append-verify $main_dir $bkp_dir &> $project_log/rsync_$data.log

	if [ $? -eq 0 ]; then
		echo $msg_sucess
	else
		echo $msg_error
	fi
}

# Compress
function exec_compact () {
    echo " --- "
    echo "-> Starting Compression..."
    echo " --- "
    tar --remove-files -czvf $bkp_file * &> $project_log/tar_$data.log

    if grep -q "File shrank by" $project_log/tar_$data.log; then
        echo "-> Alert: The file was compressed Successfully, but there was an alert."
    elif [ $? -eq 0 ]; then
        echo $msg_sucess
    else
        echo $msg_error
    fi
}

# Execution
#1
remove_last

#2
exec_bkp

cd $bkp_dir

#3
exec_compact
```

#### Key Features:

- **Efficient Backup**: Utilizes `rsync` to ensure a fast and efficient backup process.
- **Compression**: Reduces the size of your backups with `.tar.gz` compression.
- **Logging**: Keeps a record of the backup process for future reference.

#### How to Use It:

1. **Setup**: Ensure your `config.sh` file is correctly configured with the paths to your source and backup directories.
2. **Execution**: Make the script executable with `chmod +x do_backup.zsh` and run it with `./do_backup.zsh`.

### The Upload Script: `do_upload.zsh`

Following the backup process, we have the `do_upload.zsh` script, which takes care of uploading your compressed backup file to a remote storage location. Compatible with any remote storage supported by `rclone`, this script simplifies the process of moving your backups to the cloud.

```sh
#!/bin/zsh

# Script Name:    do_upload.sh
# Author:         Pablo Andrade
# Created:        07/12/2023
# Version:        0.2

# Debugging ON / OFF
#set -x

# Variables
#script_path="$(dirname "${BASH_SOURCE[0]}")"
project_directory="/home/pablodeas/Workspace/Projects/pessoal/do_backup"
source "$project_directory/config.sh"

# Upload
func exec_upload () {
  echo "-> Starting Upload..."
  rclone copy $bkp_dir $remote:/Backups/ -vv &> $project_log/rclone_$data.log

	if [ $? -eq 0 ]; then
		echo $msg_sucess
	else
		echo $msg_error
	fi
}

# Execution
exec_upload
```

#### Key Features:

- **Remote Upload**: Leverages `rclone` to upload your backup file to a remote storage solution of your choice.
- **Logging**: Provides logs for the upload process, ensuring transparency.

#### Getting Started:

1. **Configuration**: Update your `config.sh` file with the necessary remote storage details.
2. **Preparation**: Execute `chmod +x do_upload.zsh` to make the script executable.
3. **Run**: Simply execute `./do_upload.zsh` to initiate the upload process.

## Behind the Scenes: Configuration and Dependencies

Both scripts rely on a shared configuration file, `config.sh`, which holds essential information such as source and backup directory paths, backup file names, and remote storage configurations. This centralized approach makes managing your backup and upload processes straightforward.

Additionally, the scripts depend on several tools:

- **Zsh and Bash**: The scripting languages used for writing the scripts.
- **rsync**: Essential for the backup process.
- **tar**: Used for compressing the backup files.
- **rclone**: Facilitates the upload process to remote storage.

Before diving into the scripts, ensure all dependencies are installed and correctly configured. This setup will empower you to automate your backup and upload tasks, saving time and reducing the risk of data loss.

---
[Source Code Here](https://github.com/pablodeas/backup_with_zsh)

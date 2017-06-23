# Development Documentation

<!-- TOC depthFrom:2 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [System Overview and Structure](#system-overview-and-structure)
	- [Storage System](#storage-system)
		- [Credentials](#credentials)
		- [File System Layout](#file-system-layout)
	- [Destination System (where installation occurs)](#destination-system-where-installation-occurs)
		- [File System Layout](#file-system-layout)

<!-- /TOC -->

## System Overview and Structure

### Storage System
Currently csx00711.canlab.ibm.com is being used as the storage system for our data/installers until we find a more permanent (and appropriate) place to place them.

#### Prequisites
##### SSHFS
For simplicity SSHFS was used to aid in transferring files to our servers from our storage server.

SSHFS sets up a folder mount to share files via ssh between the 2 systems its configured to do so with.

##### User Setup and Permissions
When I set the user to be used for the storage system, I used the credentials listed in [Credentials](#credentials) to do so.

Just ensure the /storage folder has all of its write permissions revoked so that anyone mounting this folder can not do anything other than read or execute from this mount point.

Also do not forget that any changes to the credentials/mounts will require an update to the script_vars.yml file to appropriate those settings to the automated deploy.

#### Credentials for the Storage System
```
host: csx00711.canlab.ibm.com
user: storage
pass: cloud88
```

#### File System Layout
All files are stored in the base path at `/storage/` on the storage system. Inside this base path there will be a release version folder along with sub-folders for each product group being installed.

eg.
```
/storage
|-- <release_ver>
	|-- nexus
	|-- docker
	|-- zabbix
	|-- graylog
	...
```

### Destination System (where installation occurs)
#### File System Layout
The `data` and `installers` folders on the destination system are located in root of the filesystem.

eg.
```
/data
/sshfs_mount
```

Now in these folders there is a <release_ver> folder and then the respective product folders.

eg.
```
/data
|-- <release_ver>
    |-- nexus
    |-- gl-config
    |-- zabbix
		...
```
>***NOTE:*** that /sshfs_mount is a ssh remote file system mount and any file accessed from this folder will be transferred from the storage system to the local system for installation.

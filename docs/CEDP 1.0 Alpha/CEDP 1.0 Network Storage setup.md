### Overview

Some pods will be writing data to disk to ensure persistence in the event of a crash/restart or upgrade. This could be handled by ensuring pod affinity with a single node and mounting the volume directly from the host's file system.  The preferred approach is to have a cluster file system that replicates to all nodes so if a pod which writes to the disk goes down it can come back up on any node and the underlying data files will be ready for it to consume.

---

### Solution

GlusterFS is a cluster-able file system that allows for sharding data or replicating data across the cluster. For our purposes we will be implementing a replicated drive where all nodes have a full mirror of the file system.
    
---

### Installation
    
The installation should be run as `root` or add `sudo` before all commands.

**Pre-requisites**
    
In OpenStack create a 10g volume for each instance in the cluster and attach one volume to each. Make note of the device path for each mounted volume (should be /dev/vdb)
    
Add all nodes to the host file (/etc/host) on all nodes within the cluster for example:

    9.4.139.197 kube1
    9.4.139.199 kube2
    9.4.139.198 kube3

**Installation**
```bash
# On all nodes create the file system, mount directory and add the mount the fstab
    
    mkfs.xfs /dev/vdb
    mkdir -p /mnt/glusterVolume0
    mkdir /data
    echo '/dev/vdb /mnt/glusterVolume0 xfs defaults 1 2' >> /etc/fstab
    mount -a && mount

# Install gluster-server on all nodes
    
    apt-get -y install glusterfs-server
   
# Discover all hosts on all nodes
    gluster peer probe kube1 
    gluster peer probe kube2 
    gluster peer probe kube3 

# Create and start the gluster server (run on one node)
    gluster volume create gv0 replica 3 transport tcp \
        kube1:/mnt/glusterVolume0 \
        kube2:/mnt/glusterVolume0 \
        kube3:/mnt/glusterVolume0 force
    gluster volume start gv0

# Ensure the volume is up
    gluster volume info
    
# Mount the directories on each node
    # node 1
    mount -t glusterfs kube1:/gv0 /data

    # node 2
    mount -t glusterfs kube2:/gv0 /data
    
    # node 3    
    mount -t glusterfs kube3:/gv0 /data
``` 
        
        

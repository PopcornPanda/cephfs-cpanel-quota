# CephFS quota for cPanel
This script brings partial compatibility for CephFS quota in cPanel. It sets up limits on home directory for every user and modifies a cPanel quota cache, so cPanel users can see actual usage of data (disk space and also inodes/files count).

Script is somehow optimized, it doesn't send a setxattr command if quota doesn't change.

## Setup
- Save file `etc/ceph-quota.ini` as /etc/ceph-quota.ini in Your system
- Modify `ceph-quota.ini` to better resamble Your needs and configuration
- Save `cephfs-cpanel-quota` as local binary i.e. in `/usr/local/bin`
- Add executable permision for `cephfs-cpanel-quota`
- Run pip/pip3 install -r /path/to/requirements.txt
- Setup cron job for `cephfs-cpanel-quota`

On test machine with ~1500 users it takes 8-12 seconds to finish:
```
real    0m8.865s
user    0m1.090s
sys     0m0.382s
```

## TODO
- Make `repquota` drop-in replacement for tricking WHM to get quota data from CephFS (for now, WHM doesn't show right information about disk usage)
- Make `quota` equivalent for checking user quota in CLI. I've prototype in bash and need to rewrite it to python.

## Knonw Issues
The following list is more about cPanel issues with CephFS than with this script:
- Cleaning virtfs couses MDS to drop client connections due to hard links (Ceph MDS really doesn't like those)
- Any operation by cPanel user that relay on creating temporary file outside of user directory would not work. For example, a full cPanel account backup which a user can create by himself will pack everything but won't save the archive in the user directory. It's coused by how CephFS handles directories with quota enabled. For Ceph it's like seperate device and cPanel tries to move result file by `rename()` command.

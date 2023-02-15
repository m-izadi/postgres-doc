hi there Your Wellcome to my channel
# https://cloud.google.com/community/tutorials/setting-up-postgres-hot-standby
# Step 1
# apt isntall rsync

from /etc/postgresql/14/main/postgresql.conf
uncomment #listen_addresses = 'localhost'
and set "*"



# Step 2
set ssh key for all node
    # su postgres
    # ssh-keygen -t rsa -b 1024
    # cat .ssh/id_rsa
    # cat .ssh/id_rsa.pub
and copy public key to another ssh 

# Create a user for replication on all node
    # sudo -u postgres psql

    # sudo -u postgres createuser -U postgres repuser -P -c 5 --replication
    or
    # create user repuser connection limit 10 replication password 'rep@123' ;
        - repuser   =   UserName
        - -p        =   Password
        - -c        =   Connection Limit
        - --replication     =    the process of copying data from a PostgreSQL database server to another server
# Step 3
# Create the archive directory on all nodes
Create a directory to store archive files. This directory is a subdirectory of the cluster's data directory, which is named main by default.
    # sudo -u postgres mkdir -p /var/lib/postgresql/main/mnt/server/archivedir



# Step 4
Edit /etc/postgresql/14/main/pg_hba.conf
    # Allow replication connections
    host     replication     repuser         172.16.178.101/24        md5


# Step 5
Edit /etc/postgresql/14/main/postgresql.conf in primary node
    listen_addresses = '*'              #
    wal_level = replica
    archive_mode = on
    archive_timeout = 900       ### this will trigger WAL switch every 15 mins.
    max_wal_senders = 3
    archive_command = 'test ! -f /mnt/server/archivedir/%f && rsync  -ah -e "ssh -p 5566" %p postgres@172.16.178.101:/var/lib/postgresql/main/mnt/server/archivedir/%f'  ### for using rsync it need to be installed on all the nodes.

# Step 6
Restart the primary server
    sudo service postgresql restart
# Second node
# Configuring the Standby Server
# Step 1
Stop postgres Service
    # sudo service postgresql stop

# Step 2
# we move the old main directory to somewhere else because when using pg_basebackup it doesnt override it, it adds to it:
    # sudo mv /var/lib/postgresql/14/main /var/lib/postgresql/14/old_main

# Step 3
Then we use the pg_basebackup to copy over the main data directories to the replica:
    # sudo -u postgres pg_basebackup -h 172.16.178.102 -p 5432 -U repuser -D /var/lib/postgresql/14/main  -Fp -Xs -P -v

# Step 4
Then we create a file standby.signal in the data directory this file will cause PostgreSQL Server to enter in standby mode:

sudo -u postgres touch /var/lib/postgresql/14/main/standby.signal
Set the below parameters in Standby postgresql.conf file:
restore_command = 'cp /var/lib/postgresql/main/mnt/server/archivedir/%f %p'
archive_cleanup_command = 'pg_archivecleanup -d /var/lib/postgresql/main/mnt/server/archivedir %r'
recovery_target_timeline = 'latest'
hot_standby = on
and then restart the postgresql service.
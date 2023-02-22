# Install postgres

    sudo apt install postgresql-14
# Node1

    # ssh-keygen -t rsa -b 1024
        copy .ssh/id_rsa.pub to another node .ssh/authorized_keys

    # sudo -u postgres psql
    # create user replica connection limit 10 replication password 'rep@123' ;
    # sudo -u postgres mkdir -p /var/lib/postgresql/main/mnt/server/archivedir


vim /etc/postgresql/14/main/pg_hba.conf
    host    replication     replica         172.16.178.202/24        md5





archive_command = 'test ! -f /mnt/server/archivedir/%f && rsync  -ah -e "ssh -p 5566" %p postgres@172.16.178.202:/var/lib/postgresql/main/mnt/server/archivedir/%f'


sudo -u postgres pg_basebackup -h 172.16.178.201 -p 5432 -U replica -D /var/lib/postgresql/14/main  -Fp -Xs -P -v
```
root
  dnf install pgbackrest
  
  sudo mkdir -p /var/lib/pgbackrest
  sudo chmod 750 /var/lib/pgbackrest
  sudo chown postgres:postgres /var/lib/pgbackrest
  
  cd /etc/pgbackrest/
  touch pgbackrest.conf
    [demo]
    pg1-path=/var/lib/pgsql/13/data
    [global]
    repo1-path=/var/lib/pgbackrest
    
  cd /var/lib/pgsql/13/data/
  touch postgresql.conf
    archive_command = 'pgbackrest --stanza=demo archive-push %p'
    archive_mode = on
    listen_addresses = '*'
    log_filename = 'postgresql.log'
    log_line_prefix = ''
    max_wal_senders = 3
    wal_level = replica
    
  sudo systemctl restart postgresql-13.service
  
  vim /etc/pgbackrest/pgbackrest.conf
    [global:archive-push]
    compress-level=3
    [global]
    repo1-cipher-pass=zWaf6XtpjIVZC5444yXB+cgFDFl7MxGlgkZSaoPvTGirhPygu4jOKOXf9LO4vjfO
    repo1-cipher-type=aes-256-cbc
    repo1-path=/var/lib/pgbackrest
    repo1-retention-full=2

postgres
  pgbackrest --stanza=demo \ --log-level-console=info backup
  pgbackrest --stanza=demo --type=diff \ --log-level-console=info backup
  
root
  sudo pg_ctlcluster 13 demo stop
  sudo -u postgres rm /var/lib/postgresql/13/demo/global/pg_control
  sudo pg_ctlcluster 13 demo start
  
  sudo -u postgres find /var/lib/postgresql/13/demo -mindepth 1 -delete
  sudo -u postgres pgbackrest --stanza=demo restore
  sudo pg_ctlcluster 13 demo start
  
postgres
  pgbackrest --stanza=demo --type=incr \ --log-level-console=info backup
  
root 
  vim /etc/pgbackrest/pgbackrest.conf 
    [global]
    start-fast=y
    
postgres
  pgbackrest --stanza=demo --type=incr \ --log-level-console=info backup
  
```

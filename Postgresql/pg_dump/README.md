```
db01
  postgres
    mkdir backup
    pg_dump trendyol | gzip > ~/backup/trendyol_backup.gz
    (if we want to do full cluster dump)
    pg_dumpall | gzip > ~/backup/trendyol_backup.gz
  
  psql
    CREATE DATABASE temp ENCODING='UTF-8' LOCALE = 'tr_TR.UTF-8' TEMPLATE template0;
  
  postgres
    gunzip -c ~/backup/trendyol_backup.gz | psql temp
  ```

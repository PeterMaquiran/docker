# 📦 Gitea Docker Volume Migration Guide

## 1. Backup Gitea (OLD server)

Stop service or container
``` bash
docker service scale yourstack_gitea=0
```
Gnerate backup
``` bash
docker run --rm \
  -v gitea_data:/data \
  -v $(pwd):/backup \
  alpine \
  tar czf /backup/gitea_data_backup_$(date +%F).tar.gz /data
```

------------------------------------------------------------------------

## 2. Download to Local Machine

``` bash
scp root@OLD_SERVER_IP:/path/to/gitea_data_backup_YYYY-MM-DD.tar.gz .
```

------------------------------------------------------------------------

## 3. Upload to NEW Server

``` bash
scp gitea_data_backup_YYYY-MM-DD.tar.gz root@NEW_SERVER_IP:/home/peter/
```

------------------------------------------------------------------------

## 4. Restore on NEW Server

``` bash
ssh root@NEW_SERVER_IP
cd /home/peter
```

``` bash
docker volume create gitea_data
```

``` bash
docker run --rm \
  -v gitea_data:/data \
  -v /home/peter:/backup \
  alpine \
  sh -c "cd /data && tar xzf /backup/gitea_data_backup_YYYY-MM-DD.tar.gz --strip 1"
```

------------------------------------------------------------------------

## 5. Start Gitea

``` bash
docker stack deploy -c docker-compose.yml yourstack
```

------------------------------------------------------------------------

## ✅ Done

Your Gitea instance should now be migrated successfully.

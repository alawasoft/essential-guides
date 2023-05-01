Here's a guide on how to backup and restore CVAT data:

## Backup CVAT Data

1. **Stop all CVAT containers:**

```bash
docker-compose stop
```

2. **Create a backup directory:**

```bash
mkdir backup
```

3. **Backup data:**

```bash
docker run --rm --name temp_backup --volumes-from cvat_db -v $(pwd)/backup:/backup ubuntu tar -cjvf /backup/cvat_db.tar.bz2 /var/lib/postgresql/data
docker run --rm --name temp_backup --volumes-from cvat -v $(pwd)/backup:/backup ubuntu tar -cjvf /backup/cvat_data.tar.bz2 /home/django/data
```

4. **(Optional) Backup Elasticsearch data:**

```bash
docker run --rm --name temp_backup --volumes-from cvat_elasticsearch -v $(pwd)/backup:/backup ubuntu tar -cjvf /backup/cvat_events.tar.bz2 /usr/share/elasticsearch/data
```

5. **Verify the backup:**

```bash
ls backup
```

Output should look like this:
```
cvat_data.tar.bz2 cvat_db.tar.bz2 cvat_events.tar.bz2
```

## Restore CVAT Data

1. **Stop all CVAT containers:**

```bash
docker-compose stop
```

2. **Restore data:**

Change `<path_to_backup_folder>` to the path where your backup files are located.

```bash
cd <path_to_backup_folder>
docker run --rm --name temp_backup --volumes-from cvat_db -v $(pwd):/backup ubuntu bash -c "cd /var/lib/postgresql/data && tar -xvf /backup/cvat_db.tar.bz2 --strip 4"
docker run --rm --name temp_backup --volumes-from cvat -v $(pwd):/backup ubuntu bash -c "cd /home/django/data && tar -xvf /backup/cvat_data.tar.bz2 --strip 3"
```

3. **(Optional) Restore Elasticsearch data:**

```bash
docker run --rm --name temp_backup --volumes-from cvat_elasticsearch -v $(pwd):/backup ubuntu bash -c "cd /usr/share/elasticsearch/data && tar -xvf /backup/cvat_events.tar.bz2 --strip 4"
```

4. **Start CVAT containers:**

```bash
docker-compose up -d
```

Now you have successfully backed up and restored CVAT data.

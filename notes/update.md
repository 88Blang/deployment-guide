

# Update with Supervisor

## Stop DB

1. Stop
```
sudo supervisorctl stop all
```
or
```
sudo supervisorctl stop django celery celery-beat
```
if needed:
```
sudo systemctl stop redis
sudo systemctl stop postgresql
```
2. Pull
```
git pull origin master
```

```
source venv/bin/activate  # Activate virtual environment (if applicable)
pip install -r requirements.txt  # Install new dependencies
python manage.py migrate  # Apply database migrations
python manage.py collectstatic --noinput  # Collect static files
deactivate  # Exit virtual environment
```

3. Restart
```
sudo supervisorctl start all
sudo supervisorctl start django celery celery-beat
```

```
sudo systemctl start postgresql
sudo systemctl start redis
```


```
sudo systemctl restart nginx
sudo systemctl status nginx
```



4. Check
```
sudo supervisorctl status
sudo systemctl status redis
sudo systemctl status postgresql
```




### Full
update.sh

```
#!/bin/bash

echo "Stopping all services..."
sudo supervisorctl stop all
sudo systemctl stop redis
sudo systemctl stop postgresql

echo "Pulling latest updates..."
cd /path/to/your/django/project
git pull origin master

echo "Applying migrations and installing dependencies..."
source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py collectstatic --noinput
deactivate

echo "Restarting services..."
sudo systemctl start postgresql
sudo systemctl start redis
sudo supervisorctl start all

echo "Checking service status..."
sudo supervisorctl status
sudo systemctl status redis
sudo systemctl status postgresql

echo "Update completed!"
```

Run with:
```
chmod +x update.sh
./update.sh
```



# Restart





# Backup

Export all data	
```
python manage.py dumpdata --exclude auth.permission --exclude contenttypes --exclude admin.logentry --indent 2 > /home/user/backup_data.json
python manage.py dumpdata --exclude auth.permission --exclude contenttypes --indent 2 > /home/user/backup_data.json
python manage.py dumpdata --indent 2 > /home/user/backup_data.json
python manage.py dumpdata --indent 2 > backup_data.json
```
Export data from an app	
```
python manage.py dumpdata django_app --indent 2 > dev/backup_data.json
```
Export a specific model	
```
python manage.py dumpdata django_app.My_Model --indent 2 > model_data.json
```
Import saved data	
```
python manage.py loaddata dev/backup_data.json
```


## SystemCTL
sudo systemctl stop gunicorn
sudo systemctl stop postgresql  # (If needed)
git fetch origin
git reset --hard origin/master  # (Or use git pull origin master)
source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py collectstatic --noinput
sudo systemctl start postgresql  # (If stopped)
sudo systemctl restart gunicorn
sudo systemctl restart nginx
sudo systemctl status gunicorn
sudo systemctl status nginx
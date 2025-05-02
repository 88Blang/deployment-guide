

# Docker

## Docker Setup

BUILD

# docker build -f Dockerfile -t project .
# -f for file, -t tage, . for path to file



```
Purpose	Command
Build dev	
docker-compose build
Build prod	
docker-compose -f docker-compose.prod.yml build
Rebuild dev	
docker-compose build --no-cache
Rebuild prod	
docker-compose -f docker-compose.prod.yml build --no-cache
Run dev	
docker-compose up
Run prod	
docker-compose -f docker-compose.prod.yml up
Build manually	
docker build -f Dockerfile.prod -t myapp:prod .

```


# Run django only



RUN
-p for port, -it for iteractive terminal
```
# docker run project
docker run -p local:container project
```

Using compose
build and run from compose.yaml
```
docker compose up
docker compose up -d #in background
#stop all services in compose
docker compose down

# get in terminal server=service_name
docker compose exec -it server /bin/bash
```



STOP
```
docker ps
docker stop $contianer_id

```


## Celery

```
celery -A project worker --loglevel=info --pool=solo
celery -A project beat --loglevel=info
```


Check process:
```
Get-Process | Where-Object {$_.ProcessName -match "celery"}
Stop-Process -Name "celery" -Force
Stop-Process -Id <PID> -Force
```




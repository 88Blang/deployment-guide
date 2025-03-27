



## Docker



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




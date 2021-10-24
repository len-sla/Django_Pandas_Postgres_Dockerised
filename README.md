

## Dockerised Pandas, Jango Postgres for processing Mozilla Voice Data Sets Finnish
 [pipeline notebook](pipeline_Finish_Post_SQL.ipynb)
---


Most promising and very good described dockerising pandas jango and postgress was the way Davide Provasoli did this taskl the details are in the article https://medium.com/star-gazers/data-workflow-with-django-pandas-postgresql-and-docker-56fbf2bc1105. There were couple of small byt important preparations. Whole dockerisation was done in on local lubuntu machine.

![Dockerized Jango exporting to Postgres ](docker-jango-postgres.gif)
 


---


There couple of things one need to take care when installing Docker on Ubuntu machines( details under this address): (https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04).

Next step was installing docker-compose latest version.
Handy way of doing that is to check latest version with 



```
# curl + grep
VERSION=$(curl --silent https://api.github.com/repos/docker/compose/releases/latest | grep -Po '"tag_name": "\K.*\d')
```
and using then CURL



```
DESTINATION=/usr/local/bin/docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m) -o $DESTINATION
sudo chmod 755 $DESTINATION

```





---
There are plenty of places where mistakes are causig that jango server 
is not up and running. Still I dont know why. Last column with audio duration seconds integer was causing nasty error. 





```
# Create your models here.
class CsvData(models.Model):
    client_id = models.CharField(max_length=130)
    path = models.CharField(max_length=130, primary_key=True)
    sentence = models.CharField(max_length=130)
    age = models.CharField(max_length=10)
    gender = models.CharField(max_length=10)
    accent = models.CharField(max_length=10)
    locale = models.CharField(max_length=10)
    validation_status = models.CharField(max_length=10)
    #audio_duration= = models.IntegerField() #this line is making problems
```

You need to be careful to which column you assign primery key as first time I diddnt checked and client_id was used which crashed export to postgress because there ar eplenty of records with where only path, mp3 fileis unique.
to make quicker whole debuging(DEBUG=True in settings.py) VSCode was used as it has also docker extension. Effect is good though buy there are a lot places for improvement


_Created by:_ [@len-sla]


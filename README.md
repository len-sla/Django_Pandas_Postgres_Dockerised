

## Dockerised Pandas, Django Postgres for processing Mozilla Voice Data Sets Finnish
 [pipeline notebook](pipeline_Finish_Post_SQL.ipynb)
---
Reason for preparig notebook above  was requirement to present proof of concept pipeline where particular tasks typical for pandas 
were required and filtered and processed output should be stored in Postgres

To  do dockerising constelation of pandas Django and Postgres.
Django is a high-level Python web framework that encourages rapid development and clean, pragmatic design. Built to  take care of much of the hassle of web development, so you can focus on writing your app without needing to reinvent the wheel
proceeding presented by Davide Provasoli  was used.
He presented all the details in  the [article](https://medium.com/star-gazers/data-workflow-with-django-pandas-postgresql-and-docker-56fbf2bc1105).
Additionally very informative is also the  [post](https://testdriven.io/dockerizing-django-with-postgres-gunicorn-and-nginx) prepared by Michael Herman
Only word about Django:
 Get started with Django
Meet Django


---


There was need to do couple of settings  in  local lubuntu machine.

![Dockerized Jango exporting to Postgres ](docker-jango-postgres.gif)
 


---


All the  things one need to take care about when installing Docker on Ubuntu machines are under this address: (https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04).

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

You need to be careful to which column you assign primery key as first time I diddnt check and client_id was used which crashed export to postgress because there are plenty of records  where only path, mp3 fileis unique.
to make quicker whole debuging(DEBUG=True in settings.py) VSCode was used as it has also docker extension. 
Effect is good though there are a lot places for improvement


_Created by:_ [lencz.sla@gmail.com]


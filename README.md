

## Dockerised Pandas, Django Postgres for processing Mozilla Voice Data Sets Finnish
 [pipeline notebook](pipeline_Finish_Post_SQL.ipynb)
---
Reason for preparig notebook above  was requirement to present proof of concept pipeline where particular tasks typical for pandas 
were required and filtered and processed output should be stored in Postgres

To  do dockerising constelation of pandas Django and Postgres.
Django is a high-level Python web framework that encourages rapid development and clean, pragmatic design. Built to  take care of much of the hassle of web development, so you can focus on writing your app without needing to reinvent the wheel.
Proceeding presented by Davide Provasoli  was used.
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

## Using ready structure from cookiecutter to create you django environment in minutes


you need to install it
First, get Cookiecutter. Trust me, it's awesome:

If you have docker and docker-compose installed  as mkvirtualenv tool
next step is to create structure of projects etc.
One of the easiest way is to use tool which will 
create ready to use structure. Tool is provided by 
https://github.com/cookiecutter/cookiecutter-django

Afer preapring new environment  and activating,  lets call it sound_pipe
```
mkvirtualenv sound_pipe
```
lets generate structure of our project answering questions on the way:

```
cookiecutter gh:cookiecutter/cookiecutter-django
```
like 
```
project_name [My Awesome Project]: sound_pipe
project_slug [sound_pipe]: 
description [Behold My Awesome Project!]: sound_pipe
author_name [Daniel Roy Greenfeld]: John.Doe
domain_name [example.com]: 
email [john.doe@example.com]: 
version [0.1.0]: 
Select open_source_license:
1 - MIT
2 - BSD
3 - GPLv3
4 - Apache Software License 2.0
5 - Not open source
Choose from 1, 2, 3, 4, 5 [1]: 
timezone [UTC]: 
windows [n]: 
use_pycharm [n]: 
use_docker [n]: yes
Select postgresql_version:
1 - 14.1
2 - 13.5
3 - 12.9
4 - 11.14
5 - 10.19
Choose from 1, 2, 3, 4, 5 [1]: 
Select js_task_runner:
1 - None
2 - Gulp
Choose from 1, 2 [1]: 
Select cloud_provider:
1 - AWS
2 - GCP
3 - None
Choose from 1, 2, 3 [1]: 
Select mail_service:
1 - Mailgun
2 - Amazon SES
3 - Mailjet
4 - Mandrill
5 - Postmark
6 - Sendgrid
7 - SendinBlue
8 - SparkPost
9 - Other SMTP
Choose from 1, 2, 3, 4, 5, 6, 7, 8, 9 [1]: 
use_async [n]: 
use_drf [n]: 
custom_bootstrap_compilation [n]: 
use_compressor [n]: 
use_celery [n]: 
use_mailhog [n]: 
use_sentry [n]: 
use_whitenoise [n]: 
use_heroku [n]: 
Select ci_tool:
1 - None
2 - Travis
3 - Gitlab
4 - Github
Choose from 1, 2, 3, 4 [1]: 
keep_local_envs_in_vcs [y]: 
debug [n]: 
 [SUCCESS]: Project initialized, keep up the good work!
```
as I need  docker so instead of default n I gave yes
A syou see ther eare a kot of usefull options to choose and the tool take care about generating appropiate setup
Structure was created 
so you could check and build locally initial image( based on local.yml) to check if everything is working
```
docker-compose -f local.yml build
```
everything was ok now lets place ectra app ie pandas and numpy for manipulation of the input data
lets look for the appropiate ready to use image as 
I see something like that
https://adamtheautomator.com/python-data-science-libraries/

```
ARG PYTHON_VERSION=3.9-slim-bullseye

# define an alias for the specfic python version used in this file.
FROM python:${PYTHON_VERSION} as python

# Image descriptions
LABEL maintainer="SL"
LABEL version="0.1"
LABEL description = "pandas part image"

# Specifies the working directory
WORKDIR /data

# Installs the Python data science libraries
RUN pip install nbterm numpy matplotlib seaborn pandas
#zostawmy to aby byl tam ten image  i mozliwosc odpalenia
```


In the local.yml
I added service pandas like below
```
  pandas:
    stdin_open: true
    tty: true
    build:
      context: .
      dockerfile: ./compose/local/pandas/Dockerfile
    image: sound_pipe_loc_postgres
    container_name: sound_pipe_pandas_min
    volumes:
      - ./pandas_data:/data:Z
 ```   
    in the compos/local/pandas/start
    is simply
    ```
    !/bin/bash
    ```
_Created by:_ [lencz.sla@gmail.com]


# Docker Compose Tutorial (Flask)
Docker Compose tutorial for running a flask application with gunicorn and exposing it to the internet with nginx reverse_proxy

## Creating Flask application

Flask application is located under `app` directory. To start building the application create a python virtual environment.
```powershell
python -m venv venv 
```
Where `venv` is your virtual environment. Then activate the environment.
```
venv\Scripts\activate
```
Now install the required python libraries.

```
pip install gunicorn flask
```
Write these libraries to the `requirements.txt`
```
pip freeze > requirements.txt
```
Please, make sure that all of these files are located under `app` directory.
Following this generate applocation file `app.py` which will contains the `hello world ` example.
The contents file is `app.py`:
```python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello_world():
    return "Hello World!"
```
Now create a `wsgi.py` file, which will start the server or application `app`.
To start the app instance:
```python
from app import app

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=8000)
```
To start the server instance:
```python
from app import server

if __name__ == "__main__":
    server.run(host='0.0.0.0', port=8000)
```

To test the application, run the following in command prompt or terminal.
```
gunicorn -w 1 -b 0.0.0.0:8000 wsgi:app
```
This will start the service at `localhost` port 8000. To see the output go to the following: http://localhost:8000

## Dockerizing the app

Generate a docker file:
```Dockerfile
# using a python small basic image
FROM python:alpine

# creates a dir for our application
WORKDIR /app
# copy our requirements.txt file and install dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt
# copy the rest of our application
COPY . .
# exposing our app port in docker internal network
EXPOSE 5000
# run the application
CMD gunicorn --bind 0.0.0.0:8000 wsgi:app
```

Create `.dockerignore` file to reduce the size of the docker image.
```dockerignore
venv
Dockerfile
```

## Configuring Nginx

Create a `nginx` directory in the main directory and generate a nginx configuration file `nginx.conf`. This file will contains all the fundamental nginx information and variables.

```bash
# Define the user that will own and run the Nginx server
user  nginx;
# Define the number of worker processes; recommended value is the number of
# cores that are being used by your server
worker_processes  1;
# Define the location on the file system of the error log, plus the minimum
# severity to log messages for
error_log  /var/log/nginx/error.log warn;
# Define the file that will store the process ID of the main NGINX process
pid        /var/run/nginx.pid;

# events block defines the parameters that affect connection processing.
events {
    # Define the maximum number of simultaneous connections that can be opened by a worker proce$
    worker_connections  1024;
}

# http block defines the parameters for how NGINX should handle HTTP web traffic
http {
    # Include the file defining the list of file types that are supported by NGINX
    include       /etc/nginx/mime.types;
    # Define the default file type that is returned to the user
    default_type  text/html;
    # Define the format of log messages.
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
                          # Define the location of the log of access attempts to NGINX
    access_log  /var/log/nginx/access.log  main;
    # Define the parameters to optimize the delivery of static content
    sendfile        on;
    tcp_nopush     on;
    tcp_nodelay    on;
    # Define the timeout value for keep-alive connections with the client
    keepalive_timeout  65;
    # Define the usage of the gzip compression algorithm to reduce the amount of data to transmit
    #gzip  on;
    # Include additional parameters for virtual host(s)/server(s)
    include /etc/nginx/conf.d/*.conf;
}
```

Following this create a second configuration file `project.conf` which will contains the basic proxy pass information.

```bash
server {
    listen 80;
    location / {
        proxy_pass http://app:8000;

        # Do not change this
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    location /static {
        rewrite ^/static(.*) /$1 break;
        root /static;
    }
}

```
This will allow nginx to listen on port 80 and proxy pass will point it to flask application. Here, `app` is the container name hence be carefull in changing (it should always be the name of flask app in your docker compose file).

### Dockerizing Nginx

A docker file to build the nginx image with custom configuration files.
```Dockerfile
FROM nginx:1.15.8

RUN rm /etc/nginx/nginx.conf
COPY nginx.conf /etc/nginx/
RUN rm /etc/nginx/conf.d/default.conf
COPY project.conf /etc/nginx/conf.d/
```


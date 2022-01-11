# Docker Compose Tutorial (Flask + Gunicorn + Nginx)

![GitHub](https://img.shields.io/github/license/mdsa3d/Tutorial-flask_gunicorn_nginx_docker-compose?style=for-the-badge)
![GitHub Workflow Status (branch)](https://img.shields.io/github/workflow/status/mdsa3d/Tutorial-flask_gunicorn_nginx_docker-compose/pages%20build%20and%20deployment/gh-pages?style=for-the-badge)
[![](https://img.shields.io/badge/docs-stable-blue?style=for-the-badge)](https://mdsa3d.github.io/Tutorial-flask_gunicorn_nginx_docker-compose/)

This repository shows a simple demonstration of configuring docker compose service for flask application running on WSGI server (Gunicorn) and exposed using nginx as reverse proxy web server.

## Test flask app

To test the flask application, open the terminal and go to the `app` directory and run `wsgi.py` file.

```sh
cd ~/app
gunicorn -w 1 -b 0.0.0.0:8000 wsgi:app
```
A WSGI server will be initialized at http://localhost:8000

## Run docker compose

This tool will allow us to start multiple containers with single command. To initiate the containers run following command:

*Linux (Ubuntu)*         `sudo docker-compose up --build` 

*Windows*                `docker compose up --build`

This should start your application at `http://localhost:80`.

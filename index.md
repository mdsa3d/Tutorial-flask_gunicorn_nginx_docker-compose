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

##Dockerizing the app

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
CMD gunicorn --bind 0.0.0.0:5000 wsgi:app
```

Create `.dockerignore` file to reduce the size of the docker image.
```dockerignore
venv
Dockerfile
```

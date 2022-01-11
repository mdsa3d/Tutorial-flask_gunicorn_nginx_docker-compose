# Docker Compose Tutorial
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

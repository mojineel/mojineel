+-----------------+        +-----------------+
|    API Gateway   | <----> |  Authentication  |
+-----------------+        +-----------------+
         |                           |
         |                           |
         v                           v
+-----------------+        +-----------------+
|    Service A    | <----> |    Service B    |
+-----------------+        +-----------------+
         |                           |
         v                           v
+-----------------+        +-----------------+
|    Database A   |        |    Database B   |
+-----------------+        +-----------------+
# Dockerfile for the service
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
from flask import Flask, jsonify, request
from flask_restful import Api, Resource
import os

app = Flask(__name__)
api = Api(app)

class HealthCheck(Resource):
    def get(self):
        return jsonify({"status": "healthy"})

class DataService(Resource):
    def get(self, key):
        value = os.getenv(key, "Not Found")
        return jsonify({key: value})

    def post(self, key):
        value = request.json.get("value")
        os.environ[key] = value
        return jsonify({key: value})

api.add_resource(HealthCheck, '/health')
api.add_resource(DataService, '/data/<string:key>')

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
    from flask import Flask, request, jsonify
import requests

app = Flask(__name__)
from flask import Flask, jsonify
from flask_restful import Api, Resource

app = Flask(__name__)
api = Api(app)

class HelloWorld(Resource):
    def get(self):
        return jsonify({"message": "Hello from Service A!"})

api.add_resource(HelloWorld, '/')

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5001)
from flask import Flask, jsonify
from flask_restful import Api, Resource

app = Flask(__name__)
api = Api(app)

class GoodbyeWorld(Resource):
    def get(self):
        return jsonify({"message": "Goodbye from Service B!"})

api.add_resource(GoodbyeWorld, '/')
version: '3'

services:
  api_gateway:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5000:5000"

  service_a:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5001:5001"

  service_b:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5002:5002"*******/********************

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5002)
    
    

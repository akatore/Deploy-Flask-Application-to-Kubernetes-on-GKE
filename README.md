# Deploy-Flask-Application-to-Kubernetes-on-GKE
```py
cat > app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def main_page():
    return "<h1>Welcome to the Main Page</h1><p>This is the homepage of our Flask app.</p>"

@app.route('/about')
def about_page():
    return "<h1>About Page</h1><p>This is the about page of our Flask app.</p>"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)

```

```Dockerfile
cat > Dockerfile
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 8080 available to the world outside this container
EXPOSE 8080

# Define environment variable for Flask to run in production mode
ENV FLASK_ENV=production

# Run app.py when the container launches
CMD ["python", "app.py"]
```

```
cat > requirements.txt
Flask==2.3.3
```

Create Artifact registry, copy the registry path 

![image](https://github.com/user-attachments/assets/7153c7db-a116-4f6a-bb6f-9cd72e9515ef)

```shell
docker build -t flaskapp:v1 -f Dockerfile .
[+] Building 13.0s (9/9) FINISHED                                                                     docker:default
 => [internal] load build definition from Dockerfile                                                            0.0s
 => => transferring dockerfile: 595B                                                                            0.0s
 => [internal] load metadata for docker.io/library/python:3.9-slim                                              2.7s
 => [internal] load .dockerignore                                                                               0.0s
 => => transferring context: 2B                                                                                 0.0s
 => [1/4] FROM docker.io/library/python:3.9-slim@sha256:7a9cd42706c174cdcf578880ab9ae3b6551323a7ddbc2a89ad6e5b  4.8s
 => => resolve docker.io/library/python:3.9-slim@sha256:7a9cd42706c174cdcf578880ab9ae3b6551323a7ddbc2a89ad6e5b  0.0s
 => => sha256:151089ffef3f9093a349049321fa9a4668c29b122d05224e443c5e996fb60da5 14.92MB / 14.92MB                0.9s
 => => sha256:7a9cd42706c174cdcf578880ab9ae3b6551323a7ddbc2a89ad6e5b20a28fbfbe 10.41kB / 10.41kB                0.0s
 => => sha256:e9cf9c2f800532238969770769696b30e2b270f36289aefbc4d807406d8d198f 1.75kB / 1.75kB                  0.0s
 => => sha256:b9b3c02da6c33a199501e9e4cf8da859d8065718b084ce9ee333e12cfc3b4482 5.43kB / 5.43kB                  0.0s
 => => sha256:a480a496ba95a197d587aa1d9e0f545ca7dbd40495a4715342228db62b67c4ba 29.13MB / 29.13MB                1.1s
 => => sha256:99b8d55c8acd10aa3901ad6f43d5998b882c1f4acaca51f005625b23893f0367 3.51MB / 3.51MB                  0.6s
 => => sha256:277f520eee4a7406e307add15a461fa57bfe184f595671f364066ab24264cb1a 250B / 250B                      0.9s
 => => extracting sha256:a480a496ba95a197d587aa1d9e0f545ca7dbd40495a4715342228db62b67c4ba                       2.0s
 => => extracting sha256:99b8d55c8acd10aa3901ad6f43d5998b882c1f4acaca51f005625b23893f0367                       0.2s
 => => extracting sha256:151089ffef3f9093a349049321fa9a4668c29b122d05224e443c5e996fb60da5                       1.1s
 => => extracting sha256:277f520eee4a7406e307add15a461fa57bfe184f595671f364066ab24264cb1a                       0.0s
 => [internal] load build context                                                                               0.0s
 => => transferring context: 1.05kB                                                                             0.0s
 => [2/4] WORKDIR /app                                                                                          0.7s
 => [3/4] COPY . /app                                                                                           0.0s
 => [4/4] RUN pip install --no-cache-dir -r requirements.txt                                                    4.4s
 => exporting to image                                                                                          0.2s 
 => => exporting layers                                                                                         0.2s 
 => => writing image sha256:da7493fdab6cb68e1d8e5424aae73ea6946d556ced5909d32f020aa5e9bdceb4                    0.0s 
 => => naming to docker.io/library/flaskapp:v1                                                                  0.0s
```
```shell
docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
flaskapp     v1        da7493fdab6c   10 minutes ago   136MB
```
```shell
docker tag flaskapp:v1 asia-south1-docker.pkg.dev/gke-project-439604/flask-app/flaskapp:v1   
```
```
docker images
REPOSITORY                                                         TAG       IMAGE ID       CREATED          SIZE
asia-south1-docker.pkg.dev/gke-project-439604/flask-app/flaskapp   v1        da7493fdab6c   18 minutes ago   136MB
flaskapp                                                           v1        da7493fdab6c   18 minutes ago   136MB

```
```
docker push asia-south1-docker.pkg.dev/gke-project-439604/flask-app/flaskapp:v1 
The push refers to repository [asia-south1-docker.pkg.dev/gke-project-439604/flask-app/flaskapp]
eef8f0d896cc: Pushed 
01b13bbe0346: Pushed 
406df587f6fc: Pushed 
d86feaf80e98: Pushed 
19f5accf4683: Pushed 
0300a07ea341: Pushed 
98b5f35ea9d3: Pushing [===================================>               ]  53.28MB/74.78MB
.
.
98b5f35ea9d3: Pushed 
v1: digest: sha256:acc745eb59c5c9c5853c31a121f67391b276eb4a206e6600bbaf7c6830cb884c size: 1783
```
![image](https://github.com/user-attachments/assets/3ab35532-effa-4075-8871-8eff5edecf64)

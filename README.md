# Deploy Django application in AWS App Runner


## Summary

* [Development Architecture](#development-architecture)
* [Docker Container](#docker-container)
* [AWS App Runner](#aws-app-runner)
* [About me](#about-me)



## Development Architecture

Important information about the development architecture of this project.

| | Technology | Version |
| --- | --- | --- |
| Language | Python | 3.9.5 |
| Framework | Django | 3.2.4 |
| Database | PostgreSql | 13.2 | 
| IDE | PyCharm | 2021.1 |
| Container | Docker | 3.4.0 |



## Docker Container


### Creating Dockerfile

Create the `Dockerfile` file in your project's root folder with the following instructions.

```dockerfile
FROM ubuntu:18.04

COPY . /app

WORKDIR /app

RUN apt-get update \
    && apt-get -y upgrade \
    && apt-get install -y python3-pip build-essential manpages-dev libpq-dev postgresql-client

RUN python3 -m pip install -U pip \
    && pip3 install --upgrade setuptools \
    && pip3 install -r /app/requirements.txt

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "--workers", "3", "--timeout", "120", "--max-requests", "600", "--log-file", "-", "<myapplication>.wsgi:application"]
```

:warning: Change the text `<myapplication>` to the name of your application.


### Creating a local Docker image

In the root folder of your project execute the command to create the local image of your application.

```sh
docker image build -t <myapplication>:latest .
```

You can change the `latest` by your application version.


### Uploading image to AWS Elastic Container Registry

After the build completes, tag your image so you can push the image to this repository.

```sh
docker tag <myapplication>:latest <aws-container-register-url>.amazonaws.com/<myapplication>:latest
```

Run the following command to push this image to your newly created AWS repository.

```sh
docker push <aws-container-register-url>.amazonaws.com/<myapplication>:latest
```



## AWS App Runner

On the AWS AppRunner page, select "create service", then under "source" link to AWS Elastic Container Registry.

### Important tips

- Create your `environment variables` on the Configure service page;

- Modify the Port to `8000` (or the port that Gunicorn is configured);

- In additional settings, search for "Start command" and enter the `migrate` command: 

```sh
python3 /app/manager.py migrate
```


### Custom domains

Once the deployment is complete, you can go to the `Custom domains` folder and parameterize your application's domain, following the AWS instructions.



## About me

Please feel free to contact me. :sunglasses: 

[Alexandre Nicolau](https://www.linkedin.com/in/alexandrenicolau) :herb:

:beers:
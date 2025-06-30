# jenikns-ocean-test
Repor for ocean jenkins class


## Instalar o jenkins com docker 

```bash
docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts-jdk11
```


### Acessar o container e instalar o python
 ```bash
 docker exec -it -u root {CONTAINER_ID}  bash
```

 ```bash
 apt-get update
 apt-get install -y python3 python3-pip python3-venv
```

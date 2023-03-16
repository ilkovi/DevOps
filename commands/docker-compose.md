
===== HOW YML Works

app_deployed:
  stdin_open: true
  tty: true
  image: 'ivan_docker_image:v1'
  command: '/bin/bash'
  container_name: app
  user: jenkins
  ports:
    - "8181:8181"
    - "8081:8080"
    - "8001:8001"

This is the equivalent to :
docker run -user jenkins -d -p 8181:8181 -p 8081:8080 -p 8001:8001 -it ivan_docker_image:v1 /bin/bash name app

stdin_open: true
tty: true
The first corresponds to -i in docker run and the second to -t.







====== Start APP by entrypoint and file inside the container

# YAML file

app_deployed:
  stdin_open: true
  tty: true
  image: 'ivan_docker_image:v2'
  command: '/bin/bash'
  entrypoint: /home/jenkins/entrypoint-docker.sh
  container_name: app
  user: jenkins
  ports:
    - "8181:8181"
    - "8081:8080"
    - "8001:8001"

# Inside the container we have:

vi /home/jenkins/entrypoint-docker.sh     # executable permissions
#!/bin/bash
/usr/share/APP/bin/start
exec "$@"


# Explain

You can use entrypoint here.
entrypoint in docker is executed before the command while command is the default command that
should be run when container starts. So most of the applications generally carry setup procedure
in entrypoint file and in the last they allow command to run.

make a shell script file may be as docker-entrypoint.sh
(name does not matter) with following contents in it.

#!/bin/bash
python manage.py migrate
exec "$@"

in docker-compose.yml file use it with entrypoint: /docker-entrypoint.sh and register command as 
command: python manage.py runserver 0.0.0.0:8000 
P.S : do not forget to copy docker-entrypoint.sh along with your code.







======= Start APP, second way TOP 

# create the entryoint file together with YAML file

$ ls
docker-compose.yml
entrypoint-docker.sh

# give it owner and permission as it will be in the container.

# cat entrypoint-docker.sh
#!/bin/bash
/usr/share/APP/bin/start
exec "$@"


# add that file as a volume to a place where the container user can access

app_deployed:
  stdin_open: true
  tty: true
  image: 'ivan_docker_image:v1'
  command: '/bin/bash'
  volumes:
    - ./entrypoint-docker.sh:/usr/share/jenkins/entrypoint-docker.sh
  entrypoint: /usr/share/jenkins/entrypoint-docker.sh
  container_name: app
  user: jenkins
  ports:
    - "8181:8181"
    - "8081:8080"
    - "8001:8001"



--- DOCKER-COMPOSE

docker-compose.yml
production.json

Backend:
  stdin_open: true
  tty: true
  image: backend
  command: "/bin/bash"
  volumes:
    - ./production.json:/opt/backend_app/be/config/production.json
  entrypoint: "node /opt/backend_app/be/server/server.js"
  container_name: backend
  ports:
    - "3001:3001"
Selfcare:
  stdin_open: true
  tty: true
  image: fe-selfcare
  command: "/bin/bash"
  container_name: fe-selfcare
  extra_hosts:
    - "be:APPSERVER"
  ports:
    - "8090:8080"
CSR:
  stdin_open: true
  tty: true
  image: fe-csr
  command: "/bin/bash"
  container_name: fe-csr
  extra_hosts:
    - "be:APPSERVER"
  ports:
    - "8091:8080"

	
	
	
	
# cat production.json
{
    "ConnectionToapp": {
        "protocol": "http:",
        "IP": "10.100.200.200",
        "port": 8001,
        "prefix": "/rest/v1",
        "suffix": ""
    },
    "ConnectionToACC": {
        "IP": "Production - ACC IP"
    },
    "serverConfig": {
        "Logs": {},
        "port": 3001
    },
    "logConfiguration": {
        "loggerExpress": {
            "level": "error",
            "appenders": "production"
        }
    }
}

	
	
	




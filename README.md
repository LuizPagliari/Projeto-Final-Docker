# Projeto-Final-Docker
Primeiro Passo - Instalações
sudo apt update

sudo apt install -y [docker.io](http://docker.io/) docker-compose

sudo systemctl start docker

sudo systemctl enable docker

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/6c7dffd4-16ad-4460-86a7-f7863635d88c/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/07005146-4129-42ef-bfbc-61fd6e7fc354/Untitled.png)

Passo 2 - Iniciar o Swarm

sudo docker swarm init

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/35426021-82ac-4bed-bc79-898fa58d30a9/Untitled.png)

Passo 3- Criar o projeto e ir na pasta

mkdir projeto_final
cd projeto_final

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/fb2f1bb8-ae47-4bd2-b7e4-af953aab8ebe/Untitled.png)

Passo 4- Criar o arquivo docker-compose.yml:
nano docker-compose.ym

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/51e42739-6783-4b42-906f-29671e502847/Untitled.png)

Passo 5- Configure dessa forma:

version: '3.8'
services:
wordpress:
image: wordpress:latest
ports:
- "8080:80"
environment:
WORDPRESS_DB_HOST: db:3306
WORDPRESS_DB_USER: wordpress
WORDPRESS_DB_PASSWORD: wordpress_password
WORDPRESS_DB_NAME: wordpress
volumes:
- wordpress_data:/var/www/html
deploy:
replicas: 1
restart_policy:
condition: on-failure

db:
image: mysql:5.7
environment:
MYSQL_DATABASE: wordpress
MYSQL_USER: wordpress
MYSQL_PASSWORD: wordpress_password
MYSQL_ROOT_PASSWORD: root_password
volumes:
- db_data:/var/lib/mysql
deploy:
replicas: 1
restart_policy:
condition: on-failure

redis:
image: redis:latest
ports:
- "6379:6379"
volumes:
- redis_data:/data
deploy:
replicas: 1
restart_policy:
condition: on-failure

prometheus:
image: prom/prometheus:latest
ports:
- "9090:9090"
volumes:
- ./prometheus.yml:/etc/prometheus/prometheus.yml
deploy:
replicas: 1
restart_policy:
condition: on-failure

grafana:
image: grafana/grafana:latest
ports:
- "3000:3000"
environment:
- GF_SECURITY_ADMIN_PASSWORD=admin
volumes:
- grafana_data:/var/lib/grafana
deploy:
replicas: 1
restart_policy:
condition: on-failure

volumes:
wordpress_data:
db_data:
redis_data:
grafana_data:

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/08f8409a-fbbd-4f15-8ceb-b3aba5510476/Untitled.png)

Passo 6- Criar e configurar o Prometheus:

nano prometheus.yml

global:
scrape_interval: 15s

scrape_configs:

- job_name: 'prometheus'
static_configs:
    - targets: ['localhost:9090']
- job_name: 'wordpress'
static_configs:
    - targets: ['wordpress:80']
- job_name: 'mysql'
static_configs:
    - targets: ['db:3306']
- job_name: 'redis'
static_configs:
    - targets: ['redis:6379']
- job_name: 'docker'
static_configs:
    - targets: ['docker:9323']

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/42e0ccd7-78ec-495b-850b-86a63c454101/Untitled.png)

Passo 7- Iniciar o Docker

sudo systemctl start docker

Passo 8- Iniciar o Docker Swarm

sudo docker swarm init

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/4fabfa02-5f3a-4f36-8b88-d5c40f28d086/Untitled.png)

Passo 9- Colocar os serviços:
sudo docker stack deploy -c docker-compose.yml swarmservice

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/be10275a-5e28-423e-a17a-4ea3be522c3f/Untitled.png)

Passo 10- Verificando:
sudo docker stack services swarmservice

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a17620bb-e02d-4513-bcb4-bca42ed756d0/ff1c187c-638e-454e-8b45-840096deba38/Untitled.png)


## Exercícios Propostos para treinar a elaboração e contrução de conteiners, compose files e Dockerfiles

### Exercícios: 
**Exercícios Docker**
 
## 🟢 Fácil

### 1 Rodando um container básico

🔹Execute um container usando a imagem do Nginx e acesse a página padrão no navegador.

Exemplo de aplicação: Use a [landing page do TailwindCSS](https://github.com/tailwindtoolbox/Landing-Page) como site estático dentro do container.

-------------------------------------------------------------------------------------------------------------

### 2 Criando e rodando um container interativo

🔹Inicie um container Ubuntu e interaja com o terminal dele.

Exemplo de aplicação: Teste um script Bash que imprime logs do sistema ou instala pacotes de forma interativa.

-------------------------------------------------------------------------------------------------------------
### 3 Listando e removendo containers

🔹Liste todos os containers em execução e parados, pare um container em execução e remova um container específico.

Exemplo de aplicação: Gerenciar containers de testes criados para verificar configurações ou dependências.

-------------------------------------------------------------------------------------------------------------
### 4 Criando um Dockerfile para uma aplicação simples em Python

🔹Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.

Exemplo de aplicação: Use a API de exemplo [Flask Restful API Starter](https://github.com/gothinkster/flask-realworld-example-app) para criar um endpoint de teste.

-------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------
## 🟡 Médio

### 5 Criando e utilizando volumes para persistência de dados

🔹Execute um container MySQL e configure um volume para armazenar os dados do banco de forma persistente.

Exemplo de aplicação: Use o sistema de login e cadastro do Laravel Breeze, que usa MySQL.

-------------------------------------------------------------------------------------------------------------

### 6 Criando e rodando um container multi-stage

🔹Utilize um multi-stage build para otimizar uma aplicação Go, reduzindo o tamanho da imagem final.

Exemplo de aplicação: Compile e rode a API do Go Fiber Example dentro do container.

-------------------------------------------------------------------------------------------------------------

### 7 Construindo uma rede Docker para comunicação entre containers

🔹Crie uma rede Docker personalizada e faça dois containers, um Node.js e um MongoDB, se comunicarem.

Exemplo de aplicação: Utilize o projeto MEAN Todos para criar um app de tarefas usando Node.js + MongoDB.

-------------------------------------------------------------------------------------------------------------

### 8 Criando um compose file para rodar uma aplicação com banco de dados

🔹Utilize Docker Compose para configurar uma aplicação Django com um banco de dados PostgreSQL.

Exemplo de aplicação: Use o projeto Django Polls App para criar uma pesquisa de opinião integrada ao banco.

-------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------
## 🔴 Difícil


### 9 Criando uma imagem personalizada com um servidor web e arquivos estáticos

🔹Construa uma imagem baseada no Nginx ou Apache, adicionando um site HTML/CSS estático.

Exemplo de aplicação: Utilize a landing page do Creative Tim para criar uma página moderna hospedada no container.

# Resolução:

## 1º Exercício:
Esse exercício mesmo que muito básico e estár classificado como fácil ele trás um reforço de como se inicia um container, para quem não está familiarizado com a tecnologia é algo excelente para treinar o entendimento da sintaxe a acrescentar na familiarização com a tecnologia.
### comandos utilizados:
```
mkdir nginx-projeto
cd nginx-projeto
nano Dockerfile
```
Aqui nós configuramos o nosso container com a imagem nginx, na ultima versão e copiamos o arquivo index.html para o destino `/usr/share/nginx/html/inde.html` e já de cara expomos a porta 80 para acesso via http e adicionamos o comando `nginx -g daemon off` para que o nginx rode a página em primeiro plano, não sendo executado como um daemon.
```
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx","-g", "daemon off;"]
```
A pagina html foi retirada do repositório [Landing-Page](https://github.com/tailwindtoolbox/Landing-Page/blob/master/index.html).

--------------------------------------------------------------------------

## 2º Exercício:
Este exercício trouxe um pouco de criatividade na questão de se criar algo interativo dentro do container porém nada muito novo também. Porém, como eu no momento de fazer este exercício não havia estudado tanto acabei fazendo do " caminho mais longo " e criei o container com Ubuntu e criei um script de instalação interativa dentro dele.
```
docker pull ubuntu
```
### Após a instalação
```
docker run -ti --name ubuntu-projeto ubuntu /bin/sh
```
### Dentro do container
```
cd /
mkdir app
cd app
nano install-packages.sh
```
### Insira este código
```
#!/bin/bash

# Cores para melhorar a visualização
GREEN='\033[0;32m'
RED='\033[0;31m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # Sem cor

# Função para verificar se o script está sendo executado como root
check_root() {
    if [ "$EUID" -ne 0 ]; then
        echo -e "${RED}Este script precisa ser executado como root (sudo).${NC}"
        echo -e "Por favor, execute novamente com: ${YELLOW}sudo $0${NC}"
        exit 1
    fi
}

# Função para detectar o gerenciador de pacotes
detect_package_manager() {
    if command -v apt &> /dev/null; then
        PKG_MANAGER="apt"
        INSTALL_CMD="apt install -y"
        UPDATE_CMD="apt update"
    elif command -v dnf &> /dev/null; then
        PKG_MANAGER="dnf"
        INSTALL_CMD="dnf install -y"
        UPDATE_CMD="dnf check-update"
    elif command -v yum &> /dev/null; then
        PKG_MANAGER="yum"
        INSTALL_CMD="yum install -y"
        UPDATE_CMD="yum check-update"
    elif command -v pacman &> /dev/null; then
        PKG_MANAGER="pacman"
        INSTALL_CMD="pacman -S --noconfirm"
        UPDATE_CMD="pacman -Sy"
    elif command -v zypper &> /dev/null; then
        PKG_MANAGER="zypper"
        INSTALL_CMD="zypper install -y"
        UPDATE_CMD="zypper refresh"
    else
        echo -e "${RED}Não foi possível detectar um gerenciador de pacotes suportado.${NC}"
        echo -e "Este script suporta: apt, dnf, yum, pacman e zypper."
        exit 1
    fi
    
    echo -e "${BLUE}Gerenciador de pacotes detectado: ${GREEN}$PKG_MANAGER${NC}"
}

# Função para instalar pacotes
install_package() {
    local package=$1
    echo -e "\n${YELLOW}Instalando $package...${NC}"
    
    if eval "$INSTALL_CMD $package"; then
        echo -e "${GREEN}✓ $package instalado com sucesso!${NC}"
        return 0
    else
        echo -e "${RED}✗ Falha ao instalar $package.${NC}"
        return 1
    fi
}

# Função para atualizar a lista de pacotes
update_packages() {
    echo -e "\n${BLUE}Atualizando listas de pacotes...${NC}"
    if eval "$UPDATE_CMD"; then
        echo -e "${GREEN}✓ Listas de pacotes atualizadas com sucesso!${NC}"
    else
        echo -e "${YELLOW}⚠ Houve um problema ao atualizar as listas de pacotes.${NC}"
        echo -e "${YELLOW}⚠ Continuando com a versão atual das listas.${NC}"
    fi
}

# Função para exibir o menu de pacotes
show_menu() {
    clear
    echo -e "${BLUE}=============================================${NC}"
    echo -e "${BLUE}     INSTALADOR INTERATIVO DE PACOTES       ${NC}"
    echo -e "${BLUE}=============================================${NC}"
    echo -e "${YELLOW}Gerenciador de pacotes: $PKG_MANAGER${NC}\n"
    
    echo -e "${GREEN}1.${NC} Utilitários básicos (htop, neofetch, curl, wget)"
    echo -e "${GREEN}2.${NC} Ferramentas de desenvolvimento (git, build-essential/gcc, make)"
    echo -e "${GREEN}3.${NC} Servidores web (nginx, apache2)"
    echo -e "${GREEN}4.${NC} Bancos de dados (postgresql, mariadb/mysql)"
    echo -e "${GREEN}5.${NC} Editores de texto (vim, nano, emacs)"
    echo -e "${GREEN}6.${NC} Instalação personalizada (informe o pacote)"
    echo -e "${GREEN}7.${NC} Atualizar lista de pacotes"
    echo -e "${GREEN}8.${NC} Sair\n"
    
    echo -n -e "${YELLOW}Selecione uma opção [1-8]:${NC} "
}

# Função principal
main() {
    check_root
    detect_package_manager
    
    while true; do
        show_menu
        read -r option
        
        case $option in
            1)
                echo -e "\n${BLUE}Instalando utilitários básicos...${NC}"
                update_packages
                
                if [ "$PKG_MANAGER" = "pacman" ]; then
                    install_package "htop neofetch curl wget"
                else
                    install_package "htop"
                    install_package "neofetch"
                    install_package "curl" 
                    install_package "wget"
                fi
                
                echo -e "\n${GREEN}Pressione Enter para continuar...${NC}"
                read -r
                ;;
            2)
                echo -e "\n${BLUE}Instalando ferramentas de desenvolvimento...${NC}"
                update_packages
                
                install_package "git"
                
                # Instalar pacotes específicos de compilação baseados na distribuição
                if [ "$PKG_MANAGER" = "apt" ]; then
                    install_package "build-essential"
                elif [ "$PKG_MANAGER" = "dnf" ] || [ "$PKG_MANAGER" = "yum" ]; then
                    install_package "gcc gcc-c++ make"
                elif [ "$PKG_MANAGER" = "pacman" ]; then
                    install_package "base-devel"
                elif [ "$PKG_MANAGER" = "zypper" ]; then
                    install_package "gcc gcc-c++ make"
                fi
                
                echo -e "\n${GREEN}Pressione Enter para continuar...${NC}"
                read -r
                ;;
            3)
                echo -e "\n${BLUE}Instalando servidores web...${NC}"
                update_packages
                
                echo -e "\n${YELLOW}Qual servidor web você deseja instalar?${NC}"
                echo -e "${GREEN}1.${NC} Nginx"
                echo -e "${GREEN}2.${NC} Apache"
                echo -e "${GREEN}3.${NC} Ambos"
                echo -e "${GREEN}4.${NC} Voltar"
                
                echo -n -e "${YELLOW}Selecione uma opção [1-4]:${NC} "
                read -r web_option
                
                case $web_option in
                    1) install_package "nginx" ;;
                    2) 
                        if [ "$PKG_MANAGER" = "apt" ]; then
                            install_package "apache2"
                        else
                            install_package "httpd"
                        fi
                        ;;
                    3)
                        install_package "nginx"
                        if [ "$PKG_MANAGER" = "apt" ]; then
                            install_package "apache2"
                        else
                            install_package "httpd"
                        fi
                        ;;
                    4) continue ;;
                    *) echo -e "${RED}Opção inválida!${NC}" ;;
                esac
                
                echo -e "\n${GREEN}Pressione Enter para continuar...${NC}"
                read -r
                ;;
            4)
                echo -e "\n${BLUE}Instalando bancos de dados...${NC}"
                update_packages
                
                echo -e "\n${YELLOW}Qual banco de dados você deseja instalar?${NC}"
                echo -e "${GREEN}1.${NC} PostgreSQL"
                echo -e "${GREEN}2.${NC} MariaDB/MySQL"
                echo -e "${GREEN}3.${NC} Ambos"
                echo -e "${GREEN}4.${NC} Voltar"
                
                echo -n -e "${YELLOW}Selecione uma opção [1-4]:${NC} "
                read -r db_option
                
                case $db_option in
                    1) install_package "postgresql postgresql-client" ;;
                    2) 
                        if [ "$PKG_MANAGER" = "apt" ] || [ "$PKG_MANAGER" = "pacman" ]; then
                            install_package "mariadb-server"
                        else
                            install_package "mysql-server"
                        fi
                        ;;
                    3)
                        install_package "postgresql postgresql-client"
                        if [ "$PKG_MANAGER" = "apt" ] || [ "$PKG_MANAGER" = "pacman" ]; then
                            install_package "mariadb-server"
                        else
                            install_package "mysql-server"
                        fi
                        ;;
                    4) continue ;;
                    *) echo -e "${RED}Opção inválida!${NC}" ;;
                esac
                
                echo -e "\n${GREEN}Pressione Enter para continuar...${NC}"
                read -r
                ;;
            5)
                echo -e "\n${BLUE}Instalando editores de texto...${NC}"
                update_packages
                
                echo -e "\n${YELLOW}Qual editor você deseja instalar?${NC}"
                echo -e "${GREEN}1.${NC} Vim"
                echo -e "${GREEN}2.${NC} Nano"
                echo -e "${GREEN}3.${NC} Emacs"
                echo -e "${GREEN}4.${NC} Todos"
                echo -e "${GREEN}5.${NC} Voltar"
                
                echo -n -e "${YELLOW}Selecione uma opção [1-5]:${NC} "
                read -r editor_option
                
                case $editor_option in
                    1) install_package "vim" ;;
                    2) install_package "nano" ;;
                    3) install_package "emacs" ;;
                    4)
                        install_package "vim"
                        install_package "nano"
                        install_package "emacs"
                        ;;
                    5) continue ;;
                    *) echo -e "${RED}Opção inválida!${NC}" ;;
                esac
                
                echo -e "\n${GREEN}Pressione Enter para continuar...${NC}"
                read -r
                ;;
            6)
                echo -e "\n${BLUE}Instalação personalizada${NC}"
                echo -n -e "${YELLOW}Digite o nome do pacote que deseja instalar:${NC} "
                read -r custom_package
                
                if [ -n "$custom_package" ]; then
                    update_packages
                    install_package "$custom_package"
                else
                    echo -e "${RED}Nome de pacote inválido!${NC}"
                fi
                
                echo -e "\n${GREEN}Pressione Enter para continuar...${NC}"
                read -r
                ;;
            7)
                update_packages
                echo -e "\n${GREEN}Pressione Enter para continuar...${NC}"
                read -r
                ;;
            8)
                echo -e "\n${GREEN}Saindo do instalador. Até mais!${NC}"
                exit 0
                ;;
            *)
                echo -e "\n${RED}Opção inválida! Por favor, tente novamente.${NC}"
                echo -e "\n${GREEN}Pressione Enter para continuar...${NC}"
                read -r
                ;;
        esac
    done
}

# Iniciar o script
main
```
### Após a criação do script de as permissões e o execute
```
chmod +x install-packages.sh
sudo ./install-packages.sh
```
--------------------------------------------------------------------------
## 3º Exercicio:
Neste exercício os comandos deveriam ser executados durante a execução de um container, logo há duas formas de o faze-lo, sendo executar o container com `-d` para configurar como detached ou separado e a outra forma é abrir um novo terminal e executar o comando de para do container.
`docker ps -a`
### Exibe todos os containers, em execução ou já parados
`docker stop {id do container}`
### Para um container em expecifico
`docker rm {id do container}`
### Remove/exclui um determinado container

## 4º Exercício:
Este exercício requisitou bastante de mim em comparação aos outros, mas mesmo assim a resolução era relativamente simples, somente tive dificuldade com a API pois a documentação dela estava descontinuada mas ainda funcionava.
Em primeiro momento tive que criar um arquivo python para chamar a API, também criei um arquivo requirements.txt para colocar os arquivos necessários no momentos da instalação do python no container e tudo isso foi configurado no Dockerfile

#### Um programa básico apenas para fazer uso da API 
```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return  "Hello World!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

#### Mesmo que foi apenas um pacote informado no requirements pesquisei e vi que era a forma mais segura e garantida de que fosse baixar a versão correta
```
flask==2.3.3
```


#### Este Dockerfile configura toda a parte da conexão da API além de expor a porta 5000 para acesso, copia o arquivo requirements para dentro do container e faz as devidas configurações
```
# Use a imagem oficial do Python como base
FROM python:3.9-slim

# Define o diretório de trabalho dentro do container
WORKDIR /app

# Copia os arquivos de requisitos primeiro para aproveitar o cache de camad>COPY requirements.txt .

# Instala as dependências
RUN pip install --no-cache-dir -r requirements.txt

# Copia o resto do código da aplicação
COPY . .

# Expõe a porta que a aplicação vai usar
EXPOSE 5000

# Define variáveis de ambiente
ENV FLASK_APP=app.py
ENV FLASK_ENV=development

# Comando para executar a aplicação
CMD ["flask","run","--host=0.0.0.0"]
```

Por fim a criação da imagem do container e feita atráves do comando `docker build -t {nome da imagem} .` que cria a imagem com base no Dockerfile (deve ser executado dentro da mesma pasta que se encontra o Dockerfile) e para executar o container com esta imagem utilize o comando `docker run -p 5000:5000 {nome da imagem}` ápos isso será necessário acessar o endereço `http://localhost:5000` para ter a visualização da pagina com o Hello World!.

## 5º Exercício
Aqui começamos os exercícios de nível médio, neste exercício eu tive muita dificuldade pois não entendia muito bem como funcionava a tecnologia do Laravel Breeze, e por tanto eu acabei tendo muita dificuldade, tanto que nem sei si consegui atingir o resultado esperado ao finalizar tal exercício:
#### Criação e configuração dos arquivos do projeto (docker-compose, Dockerfile e default.conf)
```
mkdir laravel-breeze-mysql
cd laravel-breeze-mysql
touch docker-compose.yml Dockerfile default.conf
```
#### Configurando docker-compose.yml
```
version: '3'

services:
  # Aplicação Laravel
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: laravel_app
    restart: unless-stopped
    volumes:
      - ./src:/var/www/html
    depends_on:
      - mysql
    networks:
      - laravel-network

  # Servidor Web Nginx
  nginx:
    image: nginx:alpine
    container_name: laravel_nginx
    restart: unless-stopped
    ports:
      - "8000:80"
    volumes:
      - ./src:/var/www/html
      - ./nginx/conf.d:/etc/nginx/conf.d
    depends_on:
      - app
    networks:
      - laravel-network

  # Banco de dados MySQL
  mysql:
    image: mysql:8.0
    container_name: laravel_mysql
    restart: unless-stopped
    environment:
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: secret
      MYSQL_ROOT_PASSWORD: secret_root
      SERVICE_TAGS: dev
      SERVICE_NAME: mysql
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - laravel-network

networks:
  laravel-network:
    driver: bridge

volumes:
  mysql_data:
    driver: local
```
#### Configurando Dockerfile
```
FROM php:8.2-fpm

# Instala dependências do sistema
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    zip \
    unzip \
    libpq-dev \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath intl zip \
    && docker-php-ext-configure gd \
    && docker-php-ext-install gd

# Limpa cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Instala o Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Define o diretório de trabalho
WORKDIR /var/www/html

# Copia o projeto Laravel
COPY ./src /var/www/html

# Ajusta permissões
RUN chown -R www-data:www-data /var/www/html

# Expõe a porta 9000 para o PHP-FPM
EXPOSE 9000

# Inicia o PHP-FPM
CMD ["php-fpm"]
```
#### Configurando default.conf
```
server {
    listen 80;
    index index.php index.html;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /var/www/html/public;
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```
Após a configuração de cada um dos arquivos criasse a estrutura de diretórios onde utilizamos o comando `mkdir -p src nginx/conf.d` e movemos o arquivo default.conf para este diretório. Após isso inicializamos o projeto Laravel com o comando `docker-compose up -d` e executamos mais 2 comandos, o comando `docker exec -it laravel_app bash` para entrarmos no container e termos acesso ao bash dele e o comando `composer create-project laravel/laravel .` que irá criar um novo projeto Laravel. Ainda dentro do container fazemos a sequência de comandos para instalar o Laravel Breeze e suas depêndencias:
```
composer require laravel/breeze --dev
php artisan breeze:install blade
npm install
npm run build
```
aproveitamos e configuramos o arquivo .env dentro do Laravel para termos acesso e usarmos o MySQL:
```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=secret
```
Executamos a migração para a criação da tabela de usuários `php artisan migrate` e por final para testar fazemos o acesso via `http://localhost:8000`
Como dito anteriormente, a idéia era utilizar o Laravel Breeze como um sistema de registro e o MySQL faria o armazenamento destes dados porém não consegui chegar num resultado que aparentava estabelecer esta solução mas em decorrência disto notasse que foi usado ambos os serviços tanto quanto o Laravel quanto o MySQL unicamente a parte de registro e feita, entretanto em demasia de tentativas não fora encontrada uma solução para tal ocorrência.

## 6º Exercício
Este exercício não foi tão complicado quanto o anterior pelo fato de eu já ter visto uma aplicabilidade semelhante a requisitada, portanto foi mais tranquilo e sem muitas dificuldades aplicar os conceitos vistos anteriormente. 
Neste exercício como todos os demais em que envolve-se uma aplicação fora criada um diretório para a organização da aplicação.
```
mkdir go
cd go
```
Já adianto que o repositório do GoFiber está descontinuado porém aparentemente ainda é possivel utilizar de algumas tecnologias dele, então eu fiz a clonagem do [repositorio](https://github.com/gofiber/fiber.git) para visualizar e utilizar do código fornecido e apenas configurar a compilação e execução dentro do container. Criando o arquivo app.go `nano app.go`

## Código do app.go
```
package main

import (
        "log"

        "github.com/gofiber/fiber/v2"                    // Importing the fiber package for handling HTTP requests
        "github.com/gofiber/fiber/v2/middleware/cors"    // Middleware for handling Cross-Origin Resource Sharing (CORS)
        "github.com/gofiber/fiber/v2/middleware/favicon" // Middleware for serving favicon
        "github.com/gofiber/fiber/v2/middleware/logger"  // Middleware for logging HTTP requests
        // Package for logging errors
)

func main() {
        app := fiber.New() // Initialize a new Fiber instance
        // register middlewares
        app.Use(favicon.New()) // Use favicon middleware to serve favicon
        app.Use(cors.New())    // Use CORS middleware to allow cross-origin requests
        app.Use(logger.New())  // Use logger middleware to log HTTP requests

        // Define a GET route for the path '/hello'
        app.Get("/hello", func(c *fiber.Ctx) error {
                return c.SendString("World!") // Send a response when the route is accessed
        })

        log.Fatal(app.Listen(":5000")) // Start the server on port 5000 and log any errors
}
```
## Criando arquivos go.mod e go.sum para requisições a outros repositórios
### go.mod
```
module yendoapi

go 1.20

require github.com/gofiber/fiber/v2 v2.52.5

require (
        github.com/andybalholm/brotli v1.0.5 // indirect
        github.com/google/uuid v1.5.0 // indirect
        github.com/klauspost/compress v1.17.0 // indirect
        github.com/mattn/go-colorable v0.1.13 // indirect
        github.com/mattn/go-isatty v0.0.20 // indirect
        github.com/mattn/go-runewidth v0.0.15 // indirect
        github.com/rivo/uniseg v0.2.0 // indirect
        github.com/valyala/bytebufferpool v1.0.0 // indirect
        github.com/valyala/fasthttp v1.51.0 // indirect
        github.com/valyala/tcplisten v1.0.0 // indirect
        golang.org/x/sys v0.15.0 // indirect
)

``` 
### go.sum
```
github.com/andybalholm/brotli v1.0.5 h1:8uQZIdzKmjc/iuPu7O2ioW48L81FgatrcpfFmiq/cCs=
github.com/andybalholm/brotli v1.0.5/go.mod h1:fO7iG3H7G2nSZ7m0zPUDn85XEX2GTukHGRSepvi9Eig=
github.com/gofiber/fiber/v2 v2.52.5 h1:tWoP1MJQjGEe4GB5TUGOi7P2E0ZMMRx5ZTG4rT+yGMo=
github.com/gofiber/fiber/v2 v2.52.5/go.mod h1:KEOE+cXMhXG0zHc9d8+E38hoX+ZN7bhOtgeF2oT6jrQ=
github.com/google/uuid v1.5.0 h1:1p67kYwdtXjb0gL0BPiP1Av9wiZPo5A8z2cWkTZ+eyU=
github.com/google/uuid v1.5.0/go.mod h1:TIyPZe4MgqvfeYDBFedMoGGpEw/LqOeaOT+nhxU+yHo=
github.com/klauspost/compress v1.17.0 h1:Rnbp4K9EjcDuVuHtd0dgA4qNuv9yKDYKK1ulpJwgrqM=
github.com/klauspost/compress v1.17.0/go.mod h1:ntbaceVETuRiXiv4DpjP66DpAtAGkEQskQzEyD//IeE=
github.com/mattn/go-colorable v0.1.13 h1:fFA4WZxdEF4tXPZVKMLwD8oUnCTTo08duU7wxecdEvA=
github.com/mattn/go-colorable v0.1.13/go.mod h1:7S9/ev0klgBDR4GtXTXX8a3vIGJpMovkB8vQcUbaXHg=
github.com/mattn/go-isatty v0.0.16/go.mod h1:kYGgaQfpe5nmfYZH+SKPsOc2e4SrIfOl2e/yFXSvRLM=
github.com/mattn/go-isatty v0.0.20 h1:xfD0iDuEKnDkl03q4limB+vH+GxLEtL/jb4xVJSWWEY=
github.com/mattn/go-isatty v0.0.20/go.mod h1:W+V8PltTTMOvKvAeJH7IuucS94S2C6jfK/D7dTCTo3Y=
github.com/mattn/go-runewidth v0.0.15 h1:UNAjwbU9l54TA3KzvqLGxwWjHmMgBUVhBiTjelZgg3U=
github.com/mattn/go-runewidth v0.0.15/go.mod h1:Jdepj2loyihRzMpdS35Xk/zdY8IAYHsh153qUoGf23w=
github.com/rivo/uniseg v0.2.0 h1:S1pD9weZBuJdFmowNwbpi7BJ8TNftyUImj/0WQi72jY=
github.com/rivo/uniseg v0.2.0/go.mod h1:J6wj4VEh+S6ZtnVlnTBMWIodfgj8LQOQFoIToxlJtxc=
github.com/valyala/bytebufferpool v1.0.0 h1:GqA5TC/0021Y/b9FG4Oi9Mr3q7XYx6KllzawFIhcdPw=
github.com/valyala/bytebufferpool v1.0.0/go.mod h1:6bBcMArwyJ5K/AmCkWv1jt77kVWyCJ6HpOuEn7z0Csc=
github.com/valyala/fasthttp v1.51.0 h1:8b30A5JlZ6C7AS81RsWjYMQmrZG6feChmgAolCl1SqA=
github.com/valyala/fasthttp v1.51.0/go.mod h1:oI2XroL+lI7vdXyYoQk03bXBThfFl2cVdIA3Xl7cH8g=
github.com/valyala/tcplisten v1.0.0 h1:rBHj/Xf+E1tRGZyWIWwJDiRY0zc1Js+CV5DqwacVSA8=
github.com/valyala/tcplisten v1.0.0/go.mod h1:T0xQ8SeCZGxckz9qRXTfG43PvQ/mcWh7FwZEA7Ioqkc=
golang.org/x/sys v0.0.0-20220811171246-fbc7d0a398ab/go.mod h1:oPkhp1MJrh7nUepCBck5+mAzfO9JrbApNNgaTdGDITg=
golang.org/x/sys v0.6.0/go.mod h1:oPkhp1MJrh7nUepCBck5+mAzfO9JrbApNNgaTdGDITg=
golang.org/x/sys v0.15.0 h1:h48lPFYpsTvQJZF4EKyI4aLHaev3CxivZmv7yZig9pc=
golang.org/x/sys v0.15.0/go.mod h1:/VUhepiaJMQUp4+oa/7Zr1D23ma6VTLIYjOOTFZPUcA=
```
Após a criação dos arquivos .go e .sum e .mod faremos a criação e configuração dos arquivos Dockerfile e docker-compose.yml
### Dockerfile
```
FROM golang:alpine AS build

RUN adduser -D -g '' appuser
RUN mkdir /app

WORKDIR /app

COPY go.mod .
COPY go.sum .

RUN go mod download

COPY app.go .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -a -installsuffix cgo -ldflags="-w -s" -o app .

FROM alpine:latest

WORKDIR /app

RUN adduser -D -g '' appuser

COPY --from=build /app/app /app/

RUN chown -R appuser:appuser /app

USER appuser

EXPOSE 5000

CMD ["/app/app"]
```
### docker-compose.yml
```
services:
  api:
    build: "."
    ports:
      - "5000"
    restart: "always"
    networks:
      - "api.network"
    deploy:
      replicas: 5

  nginx:
    image: nginx:stable-alpine
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/api.conf:ro
    depends_on:
      - api
    ports:
      - "8080:8080"
    networks:
      - "api.network"
networks:
  api.network:
```
além de claro necessitar da criação de um diretório nginx e dentro dele o nginx.conf codo apresenta no repositório da Fiber:
```
mkdir -p nginx
mkdir -p ./nginx/nginx.conf
```
Gostaria de salientar também que fiz algumas alterações no código do repositório da Fiber para que a execução fosse feita com sucesso. Ao final utilizasse do comando `docker-compose up --build` e assim tendo uma página sendo acessada atráves do `http://localhost:8080` e as 5 instâncias do serviço operam sem conflitos de porta e fazendo o redirecionamento semelhantemente a um load balance

## 7º Exercício
Neste exercício onde o reposítório do [MEAN todo](https://github.com/luanphandinh/mean-todo) também encontrava-se forá do ar neste caso optei por executar o exercício de manieira a utilizar as tecnologias porém ao invés de uma aplicação de anotação de tarefas fora uma aplicação de registro de livros, podendo retira-los, atualiza-los e até busrca unitariamente atráves do ID.
Para tal criei primeiramente o arquivo docker-compose.yml:
```
version: '3.8'

services:
  mongodb:
    image: mongo:6.0
    container_name: meu_mongodb
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: admin
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

  nodejs:
    build: .
    container_name: meu_nodejs
    environment:
      MONGODB_URL: mongodb://admin:admin@mongodb:27017/meu_db?authSource=admin
    ports:
      - "3000:3000"
    depends_on:
      - mongodb
    volumes:
      - .:/app
      - /app/node_modules

volumes:
  mongo-data:
```
### Logo depois eu criei o arquivo package.json:
```
{
  "name": "meu-projeto-node-mongo",
  "version": "1.0.0",
  "description": "Projeto Node.js com MongoDB em Docker",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^6.13.0"
  }
}
```
### E logo após o arquivo index.js
```
const express = require('express');
const mongoose = require('mongoose');

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware para analisar corpos de requisição JSON
app.use(express.json());

// Conexão com o MongoDB
const MONGODB_URL = process.env.MONGODB_URL;

mongoose.connect(MONGODB_URL)
  .then(() => console.log('Conectado ao MongoDB com sucesso!'))
  .catch(err => console.error('Erro ao conectar ao MongoDB:', err));

const { Schema, model } = mongoose;

const LivroSchema = new Schema({
  titulo: { type: String, required: true },
  autor: { type: String, required: true },
  ano: { type: Number, required: true }
});

const Livro = model('Livro', LivroSchema);

// Middleware de tratamento de erros centralizado
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Ocorreu um erro interno!' });
});

// Validação de dados de entrada
const validarLivro = (req, res, next) => {
  const { titulo, autor, ano } = req.body;
  if (!titulo || !autor || !ano) {
    return res.status(400).json({ error: 'Dados do livro incompletos!' });
  }
  next();
};

app.post('/livros', validarLivro, async (req, res) => {
  try {
    const livro = new Livro(req.body);
    await livro.save();
    res.status(201).json(livro);
  } catch (err) {
    next(err);
  }
});

app.get('/livros', async (req, res) => {
  try {
    const livros = await Livro.find();
    res.json(livros);
  } catch (err) {
    next(err);
  }
});

app.get('/livros/:id', async (req, res) => {
  try {
    const livro = await Livro.findById(req.params.id);
    if (!livro) return res.status(404).json({ error: 'Livro não encontrado' });
    res.json(livro);
  } catch (err) {
    next(err);
  }
});

app.put('/livros/:id', validarLivro, async (req, res) => {
  try {
    const livro = await Livro.findByIdAndUpdate(req.params.id, req.body, { new: true, runValidators: true });
    if (!livro) return res.status(404).json({ error: 'Livro não encontrado' });
    res.json(livro);
  } catch (err) {
    next(err);
  }
});

app.delete('/livros/:id', async (req, res) => {
  try {
    const livro = await Livro.findByIdAndDelete(req.params.id);
    if (!livro) return res.status(404).json({ error: 'Livro não encontrado' });
    res.json({ message: 'Livro deletado com sucesso' });
  } catch (err) {
    next(err);
  }
});

// Definir um modelo simples
const TestSchema = new Schema({ name: String });
const TestModel = model('Test', TestSchema);

// Rota para testar a conexão
app.get('/', async (req, res) => {
  try {
    const testDoc = new TestModel({ name: 'Teste Docker' });
    await testDoc.save();
    res.send('Documento salvo no MongoDB com sucesso!');
  } catch (err) {
    next(err);
  }
});

// Iniciar o servidor
app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```
### Para finalizar criasse o arquivo Dockerfile
```
FROM node:18.19.1-buster-slim
EXPOSE 3000

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

CMD ["npm","start"]
```
Referente ao exercício eu mudei bastante a ideia de resolução porém utilizei as tecnologias como haviam sido empregadas e objetificadas, único porém é que para utilizar do cadastro, da listagem, busca e deletagem de livros tive que fazer uso de um gerenciador de API para colocar em uso o endereço `http://localhost:3000/livros` e fazendo POST, GET e DELETE gerenciado de API, funcionou porém creio que não foi com está ideia que foi proposto o exercício.

## 8º Exercício
Este exercício em comparação aos outros foi até que tranquilo porém exigiu bastante entendimento sobre as demais aplicações utilizadas fora o Docker.
```
mkdir Django
cd Django
touch docker-compose.yml Dockerfile requirements.txt settings.py
```
### Finalizada a pré configuração dos arquivos começasse a inserção das configurações de cada arquivo especificamente
##### docker-compose.yml
```
services:

  postgresql:
    image: postgres:latest
    container_name: PostgresSQL_container
    environment:
      - POSTGRES_USER=root
      - POSTGRES_PASSWORD=root
      - POSTGRES_DB=django_db
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data/
    healthcheck:
      test: ["CMD-SHELL","pg_isready -U root -d django_db"]
      interval: 5s
      timeout: 5s
      retries: 5
  web:
      build: .
      command: python manage.py runserver 0.0.0.0:8000
      environment:
        -  DATABASE_URL=postgres://root:root@db:5432/django_db
      volumes:
        - .:/app
      ports:
        - "8000:8000"
      depends_on:
          postgresql:
            condition: service_healthy

volumes:
   postgres_data:

```
##### Dockerfile
```
FROM python:3.9-slim-buster

WORKDIR /app

RUN apt-get update && apt-get install -y\
    libpq-dev\
    gcc

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PYTHONUNBUFFERED=1
ENV DATABASE_URL=postgres://root:root@postgresql:5432/django_db

CMD ["python","manage.py","runserver","0.0.0.0:8000"]
```
##### requirements.txt
```
Django==4.2
psycopg2-binary==2.9.6
dj-database-url==1.3.0
```
##### settings.py
```
import os
import dj_database_url

DATABASES = {
        'default': dj_database_url.config(default=os.environ.get('DATABASE_URL'))
}
```
E para finalizar a configuração dos arquivos executamos o comando `django-admin startproject core .` que fará a inserção dos arquivos restantes do django. E por fim utilizamos o comando `docker-compose up --build` e acessamos a aplicação via `http://localhost:8000`

## 9º Exercício
Finalizando a lista de exercícios, este exercício mesmo sendo classificado como díficil fora mais fácil do que outros no nível médio que tinha a documentação descontinuada.
### Pré configuração da estrutura do diretório
```
mkdir estatic
cd estatic
touch Dockerfile docker-compose.yml
mkdir site
```
Neste exercício foi requisitado o uso de uma Landing Page do Creative Tim, logo utilizei do [repositório deles](https://github.com/creativetimofficial/material-kit?tab=readme-ov-file) para fazer isto.
##### Dockerfile
```
FROM nginx:alpine

COPY site/ /usr/share/nginx/html/

EXPOSE 80
```
##### docker-compose.yml
```
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8080:80"
```
Sim, os arquivos foram demasiadamente menores do que em comparação com o restante dos exercícios. Finalizando o exercício utilizamos o comando `docker-compose build` e logo em seguida `docker-compose up`, podendo se fazer o acesso ao container através do comando `docker exec -it {nome do projeto} /bin/sh` e verificar a integridade dos arquivos e ver o error.log em caso de algum problema. Além e claro da utilização do acesso via `http://localhost:8080` para verificar a pagina estatica.

## Conclusão

Mesmo tendo vários repositórios descontinuados foi muito desafiador buscar alternativas para contemplar o objetivo dos exercícios, admito que tamanho desafio me deixou mais entusiasmado com a tecnologia de conteineirização e suas várias possibilidades de aplicação.

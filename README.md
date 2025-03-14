
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

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return  "Hello World!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
### Um programa básico apenas para fazer uso da API 
```
flask==2.3.3
```
### Mesmo que foi apenas um pacote informado no requirements pesquisei e vi que era a forma mais segura e garantida de que fosse baixar a versão correta
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
### Este Dockerfile configura toda a parte da conexão da API além de expor a porta 5000 para acesso, copia o arquivo requirements para dentro do container e faz as devidas configurações

Por fim a criação da imagem do container e feita atráves do comando `docker build -t {nome da imagem} .` que cria a imagem com base no Dockerfile (deve ser executado dentro da mesma pasta que se encontra o Dockerfile) e para executar o container com esta imagem utilize o comando `docker run -p 5000:5000 {nome da imagem}` ápos isso será necessário acessar o endereço `http://localhost:5000` para ter a visualização da pagina com o Hello World!.



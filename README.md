# Docker_Exercises

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
a pagina html foi retirada do repositório [Landing-Page](https://github.com/tailwindtoolbox/Landing-Page/blob/master/index.html).

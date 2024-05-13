---
title: "SQL Server com Docker Compose"
date: 2024-05-12T19:08:44-03:00
draft: false
toc: false
images:
tags:
  - sqlserver
  - sql
  - docker
  - docker-compose
  - shell
  - tutorial
---

Olá a Todos. Como vocês estão?

Espero que bem.

Hoje, temos mais um tutorial e será sobre algo que utilizei e ainda utilizo bastante quando preciso testar novas funcionalidades ou conteúdo relacionado a bancos de dados.

Será algo mais enxuto, caso queiram um tutorial mais detalhado e completo, podem me mandar uma DM no link que deixarei no final do post.

Vamos lá!

---

### 1. Docker e Docker Compose

Como no título do post mostra, utilizaremos o *docker-compose* para a criação do nosso ambiente.

Então, caso não tenha na sua máquina, acesse o seguinte link para instalar:
[Instalando Docker Compose](https://docs.docker.com/compose/install/)

Após, verifique se está funcionando corretamente.

```shell
# Execute
docker compose --version

# Resultado
Docker version 24.0.5, build 24.0.5-0ubuntu1~22.04.1
```

Com o Docker instalado e em pleno funcionamento, vamos começar a escrever nosso *docker-compose.yml*.

### 2. Criando e configurando compose.yml

O Docker utiliza o arquivo compose.yml como um arquivo de configuração, uma ferramenta que facilita a criação e execução dos contêineres.

Crie o arquivo utilizando o editor de texto ou terminal de sua preferência. 
```shell
# Comando para criar um arquivo vazio
echo "" > docker-compose.yml
```

Com o arquivo criado, vamos as configurações.

```yml
version: "3"

  services:
    sql-server-db:
      container_name: sql-server-db
      image: mcr.microsoft.com/mssql/server:2019-latest
      ports:
        - "1433:1433"
      environment:
        SA_PASSWORD: ${PASSWORD}
        ACCEPT_EULA: "Y"
      volumes:
        - sql-data:/var/opt/mssql

  volumes:
    sqldata:

```

Vamos entender o que acabamos de criar.


> version: "3"

Especifica a versão do formato do arquivo Docker Compose.

> services:

Define os serviços que serão criados ao executar o arquivo. Cada serviço representa um contêiner Docker.

> sql-server-db:

O nome do primeiro serviço. Nome utilizado para referenciar o serviço e como um alias ao executar comandos Docker Compose.

> container_name: sql-server-db

Define o nome do contêiner para esse serviço.

> image: 

Especifica a imagem Docker utilizada pelo contêiner. Nesse caso é utilizado a imagem oficial do SQL Server 2019 da Microsoft.

> ports:

Utilizado para mapear as portas do host e do contêiner. *"1433:1433"* significa que a porta *1433* do contêiner será mapeada para a porta *1433* do host. Lembrando apenas que *host* significa a sua máquina. Permite o acesso direto ao banco de dados.

> environment: 

Define as variáveis de ambiente que são passadas para o contêiner.

> SA_PASSWORD:

Variável que define a senha do usuário *"sa"* que é o usuário de maior prioridade no SQL Server. O valor dessa variável está definido em outro arquivo, mais a frente explico.

> ACCEPT_EULA:

Aceita os Termos de Licença do Endereço do Usuário (EULA) do SQL Server. Isso é necessário para que o SQL Server seja iniciado corretamente.

> volumes:

Define volumes Docker que são montados no contêiner. Aqui, um volume chamado sql-data é montado no caminho /var/opt/mssql dentro do contêiner. Isso é útil para persistir os dados do banco de dados entre reinicializações do contêiner.

> volumes:

Na identação do arquivo, esse comando está em um nível superior. Nesse caso, define volumes globais que podem ser utilizados pelos serviços, persistindo seus dados.

### 3. Criando a variável de ambiente

Como informei na explicação do *"SA_PASSWORD"*, o mesmo está utilizando uma senha que foi definida em um arquivo externo. Esse padrão é utilizado para manter a segurança das variáveis e senhas utilizados no seu ambiente.

Para definir o valor dessa variável, crie o arquivo *".env"*.

```shell
# Cria um arquivo de texto vazio
echo "" > .env
```

Após, abra com o seu editor de texto de preferência e digite:

```shell
PASSWORD=DigiteSuaSenhaDePreferenciaAquiTaBom?
```

### 4. Iniciando nosso ambiente.

Com tudo criado e configurado, vamos a execução.

```shell
# Cria e inicia o contêiner
docker-compose build && docker-compose up -d
```

O parâmetro *"build"* irá puxar as imagens da internet e irá construir o serviço e o parâmetro *"up -d"* irá subir/iniciar os serviços em segundo plano, para não travar o terminal.

Teremos algo como:

```shell
# Comando
docker-compose build && docker-compose up -d

# Resultado
sql-server-db uses an image, skipping
Pulling sql-server-db (mcr.microsoft.com/mssql/server:2019-latest)...
2019-latest: Pulling from mssql/server
bbca08248480: Pull complete
99ed8d3c45a0: Pull complete
cac19e7e89db: Pull complete
Digest: sha256:22ac1bcc7aa4582c93050a1231fa4817df2c62fa7e6d68096b2b9e1737be25ac
Status: Downloaded newer image for mcr.microsoft.com/mssql/server:2019-latest
Creating sql-server-db ... done
```

Para acessar é só utilizar o seu gerenciador de banco de dados de preferência, passar o usuário *"sa"* que é o padrão de administração e a senha que foi passada na variável *"PASSWORD"*.

Exemplo utilizando o software *DBeaver*:
![dbeaver-conf](/images/dbeaver-conf.png)

---

Com isso, finalizamos o rápido tutorial de hoje.

*Obrigado por lerem até aqui.*

Para entrar em contato comigo, utilize qualquer um dos links abaixo:

[Instagram](https://instagram.com/in/pablodeas)
.
[Whatsapp](https://api.whatsapp.com/send?phone=5521966916139)
.
[E-mail](mailto:pablodeas@gmail.com)
# Docker - Wordpress

**Nginx**: Servidor web usado para servir o aplicativo Laravel e encaminhar solicitações para o PHP-FPM.

**PHP-FPM**: Process Manager para PHP, responsável por executar scripts PHP.

**Composer**: Gerenciador de dependências para PHP, usado para instalar e gerenciar pacotes PHP.

**MySQL**: Banco de dados relacional MySQL para armazenar dados do aplicativo.

**Supervisor**: Processo de controle de sistema usado para gerenciar e monitorar os processos do Nginx e do PHP-FPM.

**Wp CLI**: Interface de linha de comando para WordPress, usada para gerenciar o WordPress a partir do terminal.

## Requisitos

- Docker instalado
- Docker Compose instalado

## Instalação - site.wordpress.test

Acesse a pasta e faça download das imagens e construa os containers com o comando:

```sh
docker-compose build
```

Suba a primera vez os containers com o comando:

```sh
docker-compose up -d
```

🔹 No Windows:
Abra um terminal como administrador e execute:

```sh
notepad C:\Windows\System32\drivers\etc\hosts
```

🔹 No Linux/macOS:
Abra um terminal e execute:

```sh
sudo nano /etc/hosts
```

Verifique se existe a linha:

```sh
127.0.0.1 site.wordpress.test
```

Faça uma copia do certificado SSL para importar no seu sistema operacional

```sh
sh run ssl
```

Você pode fazer o download do wordpress com o comando:

```sh
sh run install:wordpress
```

Reinicie os containers:

```sh

docker-compose down

docker-compose up -d

```

Faça o clone do tema do projeto na pasta `./themes`

```sh
sudo rm -rf ./themes

git clone git@github.com:caiobarilli/wordpress-themes.git ./themes
```

Atribuindo permissões:

```sh
sh run set:permissions
```

Acesse o site em `https://site.wordpress.test`

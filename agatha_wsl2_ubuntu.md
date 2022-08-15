talação do Ágatha
*Versão baixada do Portal do Software Público Brasileiro em julho de 2022.*

    Ao decompactar o arquivo baixado teremos uma pasta chamada *"agatha.git"* de dentro dela um arquivo *".gitignore"* e as pastas *"docker"*, *"codigo-fonte"* e *"documentacao"*.
    Na pasta *documentacao* tem um manual de instalação que é a base para este método de instalação.

## Sistema operacional Ubuntu 20.04.4 x86_64 WSL2 on Windows 10
___

### Atualizando o sistema

```sh
    $ sudo apt update && apt upgrade -y

```

### Instalando python 2.7 e linkando para padrão

```sh
   $ sudo apt install python2.7

   $ sudo ln -sf /usr/bin/python2.7 /usr/bin/python

   $ python --version

```

###  Instalação Docker
1. Instalando dependencias
```sh
   $ sudo apt install lsb-release ca-certificates apt-transport-https software-properties-common -y

```

2. Adicionando repositório
```sh
   $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

   $ sudo echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```

3. Instalando Docker
```sh
   $ sudo apt update

   $ sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

   $ docker version
     Client: Docker Engine - Community
      Version:           20.10.17
      API version:       1.41
    ...

    $ sudo usermod -aG docker $USER

```
    Caso necessário incluir grupo: ```$ sudo groupadd docker```

4. iniciando e testando o Docker
```sh
    $ sudo service docker start
    * Starting Docker: docker

   $ docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

   $ docker run hello-world
    Unable to find image 'hello-world:latest' locally
    latest: Pulling from library/hello-world
    ...

```

### Instalação Docker-compose
```sh
   $ sudo apt install docker-compose -y

   $ docker-compose version
    docker-compose version 1.25.0, build unknown
    docker-py version: 4.1.0
    CPython version: 3.8.10
    OpenSSL version: OpenSSL 1.1.1f  31 Mar 2020

```

### Instalação JDK Java 8
```sh
   $ sudo apt install openjdk-8-jdk-headless -y
    ...
    done.
    done.

   $ javac -version
    javac 1.8.0_342

```

### Instalação Maven
```sh
   $ sudo apt install maven -y

   $ mvn --version
    Apache Maven 3.6.3
    Maven home: /usr/share/maven
    ...

```

### Instalação Node JS
1. Instalação __nvm__ (gerenciador de versões)

    ```sh
        $ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | sudo bash

        $ export NVM_DIR="$HOME/.nvm"
        $ [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
        $ [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
        $ nvm ls
                        N/A
            iojs -> N/A (default)
            node -> stable (-> N/A) (default)
            unstable -> N/A (default)

    ```
2. Instalação __node__ e __npm__

    ```sh
   $ sudo nvm install 8.10.0
    Downloading and installing node v8.10.0...
    ...

   $ node --version
    v8.10.0

   $ npm --version
    5.6.0

    ```

### Instalação Agatha (builds)
    Primeiro copie os arquivos descompactados do arquivo baixado para a pasta elegida para trabalhar.
    São necessárias as pastas *"docker" e *"codigo-fonte"* ou se preferir pode descompactar todo arquivo.
    Precisamos entrar na pasta *"docker"*:

```sh
   $ cd docker/

   $ ls

```

    Dentro da pasta *"docker"* temos as pastas *"nginx"*, *"postgresql"* e *"spring"*, o arquivo docker-compose.yml, que faz a orquestração dos containers de banco, backend e frontend, e os arquivos de *"build"*.

1. Vamos dar permissão de execução para os scripts de build:

```sh
    $ chmod +x *.sh

```

2. Vamos dar permissão de execução do npm pelo root:

```sh
   $ sudo npm config set user 0

```

3. Antes de rodar o build do backend tem que incluir um repositório.
    Necessário alterar o arquivo *"pom.xml"* no caminho *"codigo-fonte/servico/"*.

    Entre os blocos \<properties\> e \<dependencies\> incluir o bloco \<repositories\> que segue:

```xml
    <repositories>
        <repository>
            <id>jaspersoft-third-party</id>
            <url>https://jaspersoft.jfrog.io/jaspersoft/third-party-ce-artifacts/</url>
        </repository>
        <repository>
            <id>jr-ce-snapshots</id>
            <name>JasperReports CE Snapshots</name>
            <url>https://jaspersoft.jfrog.io/jaspersoft/jr-ce-snapshots</url>
        </repository>
        <repository>
            <id>jr-ce-releases</id>
            <name>JasperReports CE Releases</name>
            <url>https://jaspersoft.jfrog.io/jaspersoft/jr-ce-releases</url>
        </repository>
    </repositories>
```

4. Vamos fazer o build do backend:
```sh
   $ ./build-backend.sh
    ...
    [INFO] -------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] -------------------------------------
    ...
```
    Um novo pacote *"app.jar"*, será gerado dentro de *"spring/"*.



5. Vamos fazer o build do frontend:
```sh
   $ ./build-frontend.sh

```
    Um novo pacote *"dist.tar.gz"*, será gerado dentro de *"nginx/"*.


6. Vamos editar o arquivo *"application.yaml"* no caminho *"spring/config/application.yaml"* e trocar as ocorrências de *"dns_definido"* para *"localhost"*. Neste momento se tiver de posse das credeniais de *client-id* e *client-secret*, pode também substituir onde diz *"SOLICITAR-MP"*.

```sh
   $ nano spring/config/application.yaml 

```

    Caso tenha problemas ao acessar diretamente em  http://localhost, pode alterar o arquivo docker-compose.yml colocando, por exemplo a porta 88 como padrão.
```sh
    ports:
      - "80:80"
```
Muda para:
```sh
    ports:
      - "88:80"
```

Pronto para ulilizar.

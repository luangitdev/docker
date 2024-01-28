# CONTAINERS

- Quando se fala de conteiner está se referindo a isolamento.
- Um container não enxerga os processos do outro.
- Conseguimos determinar um limite de recursos do host para os containers.


### Instalação do Docker:

```sh
curl -fsSL https://get.docker.com | bash
```

### Verifica a versão:

```sh
docker version
```

### Listar os conteiners criados(e em execução):

```sh
docker ps
docker container ls
```
### Listar todos os containers (inclusive os parados):

```sh
docker ps -a
docker container ls -a
```

### Executar um container docker:

```sh
docker run -ti hello-world
docker container run -ti hello-world
```

### Outro exemplo:

```sh
docker container run -ti ubuntu
```

- O comando acima cria um container do ubuntu e já me traz o terminal do sistema em tela.
- Se eu der um Ctrl + d e sair do container do ubuntu o processo do bash é encerrado e o container também.
- Para sair sem matar o processo (e encerrar o container) é necessário usar o atalho ctrl + p + q.

# PROCESSAMENTO, LOGS E REDE

### 1) Definir limites de CPU e memória ao container

##### Executar o comando abaixo para ver os dados de consumo do container:

```sh
docker stats nome-container
```

##### Para definir limites ao container (CPU, Memória e etc...)

```sh
docker update nome-container -m 128M --cpus 0.2
```

Explicação do comando acima:
- -m 128M      (Define a memória com um máximo de 128MB)
- --cpus 0.2   (Define que somente 20% do núcleo de processamento será utilizado para esse container)



##### Posso definir esses limites diretamente na criação do container, exemplo:

```sh
docker run --name nome-container -dti -m 128M --cpus 0.2 nome-imagem
```

### 2) Informações, logs e processos.

##### Informações gerais sobre o servidor do docker.

```sh
docker info
```

##### Visualizar os logs de execução do container.

```sh
docker logs nome-container
```

##### Visualizar os processos em execução dentro do container.

```sh
docker top nome-container
```

### 3) Redes

##### Listar as redes disponíveis ao docker.

```sh
docker network ls
```

- O comando acima apresenta basicamente duas redes (Host e Bridge)
- Se não especificarmos a rede, todo container criado, é adicionado na rede Bridge por padrão.
- A rede Bridge consegue se comunicar com a rede Host.


##### Ver quais containers estão na rede bridge:

```sh
docker network inspect bridge
```

- Uma das informações apresentadas com o comando acima são os ips dos containers na rede.


##### Posso validar a comunicação entre dois containers usando o ping.

```sh
docker exec -ti nome-container bash
apt get update
apt get install -y iputils-ping
ping ip-outro-container
```

##### Para isolar dois containers em uma rede.

```sh
docker network create minha-rede
```

- O comando acima cria uma rede com o nome especificado
- Após isso posso definir em qual rede ficará o tomcat criado.

```sh
docker run -dti --name nome-container-a --network minha-rede nome-imagem
docker run -dti --name nome-container-b --network minha-rede nome-imagem
```

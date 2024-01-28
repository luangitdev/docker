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

### Para voltar ao terminal do Container após sair:

```sh
docker container attach container_id
docker container attach container_nome
```

### Executar/Criar container em segundo plano sem o terminal interativo:

```sh
docker container run -d nginx
```

### Para executar um comando em um container que está em execução no segundo plano:

```sh
docker container exec -ti container_id ls
docker container exec -ti container_id bash
```

### Parar a execução de um container:

```sh
docker container stop container_id
docker container stop container_nome
```

### Reiniciar um container:

```sh
docker container restart container_id
docker container restart container_nome
```

### Para ver os detalhes do container:

```sh
docker container inspect container_id
```

### Existe a opção de apenas pausar o container:

```sh
docker container pause container_id
```

### Para apagar/remover um container:

```sh
docker container rm
docker container rm -f #(Força a parada do container e o apaga) 
```

### Ver o quanto o container está usando de recursos:

```sh
docker container stats container_id
```

### Ver os processos em execução dentro do container:

```sh
docker container top container_id
```

### Criar um container definindo a capacidade máxima de memória:

```sh
docker container -d -m 128M nginx
```

### Atualizar um container em execução:

```sh
docker container update recurso_a_ser_atualizado container_id
docker container update --cpus 0.2 container_id #(Define que será usado no máximo 20% do core de cpu disponível)
```

### Criar persistência de dados no container(Caso já exista uma pasta criada no Host):

```sh
docker container run -ti --mount type=bind,src=/pasta/no/host,dst=/pasta/no/container nome_distro
docker container run -ti --mount type=bind,source=/opt/meuvolume,destination=/volumecontainer ubuntu
docker container run -ti --mount type=bind,src=/opt/meuvolume,dst=/volumecontainer,ro ubuntu  #(ro = read only)
docker container run -ti -v /pasta/no/host:/pasta/no/container --name dbdados centos #(-v sintaxe antiga para gerir volumes)
```

### Criar persistência de dados no container (Usando Volume):

```sh
docker volume create nome_volume
docker volume ls
docker volume inspect nome_volume #(Olhar o "Mountpoint")
docker container run -ti --mount type=volume,src=nome_volume,dst=/pasta/no/container debian
```

### Container Data Only:

>O Data only é um termo que se refere a um tipo de container que é usado apenas para armazenar dados persistentes de outros containers1. O Data only não executa nenhum processo, mas apenas possui um ou mais volumes que podem ser compartilhados >com outros containers usando a opção --volumes-from.
>
>A vantagem de usar o Data only é que ele permite gerenciar dados em docker de forma isolada e reutilizável, sem depender do diretório do host ou do filesystem do container3. O Data only também facilita o backup e a migração dos dados, pois eles >estão armazenados em um container separado4.
>
>No entanto, o Data only também tem algumas desvantagens, como a necessidade de criar e gerenciar um container adicional para cada conjunto de dados, e a dificuldade de especificar opções de montagem, como permissões ou flags5.
>
>O --mount é uma forma mais moderna e explícita de montar volumes no docker6. O --mount permite especificar vários parâmetros, como o tipo, a origem, o destino e as opções do volume7. O --mount também suporta o uso de drivers de volume, que >permitem armazenar volumes em hosts remotos ou provedores de nuvem, criptografar o conteúdo dos volumes ou adicionar outras funcionalidades8.
>
>A vantagem de usar o --mount é que ele oferece mais flexibilidade e controle sobre os volumes, além de ser mais fácil de entender e manter. O --mount também tem um melhor desempenho do que o --volumes-from, pois ele evita a sobrecarga de criar e >gerenciar containers data only.
>

#### Exemplo de criação de um container Data Only:

```sh
docker container create -v /data --name dbdados centos
```

#### Exemplo de como apontar para um container Data Only (É necessário usar o --volumes-from para referenciar o container Data Only):

```sh
docker container run -d -p 5432:5432 --name pgsql1 --volumes-from dbdados -e POSTGRESQL_USER=docker -e POSTGRESQL_PASS=docker -e POSTGRESQL_DB=docker kamui/postgresql
```



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

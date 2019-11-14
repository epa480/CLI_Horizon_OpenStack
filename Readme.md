# Minicurso - OpenStack #

## 1. Agenda

- Instalação
- **Operando via Horizon** 
  - **Visão Geral**
  - Instanciar Imagem
  - Criar Flavor
  - Criar Redes
    - Subredes
    - IPs Flutuantes
  - Instanciar VMs
- Usando a CLI
- Desenvolvimento com APIs


## 2. Introdução ##

O minicurso de instalação e operação do OpenStack está voltado para os módulos básicos do OpenStack, Keystone, Nova, Neutron e Glance.

- Keystone - É o serviço de identidade usado pelo OpenStack par autenticação (authN) e autorização de alto nível (authZ). Ele oferece suporte a authN baseado em token e autorização de serviço do usuário. 
- Nova - É o serviço que faz o provisionamento de instancias de computação. Suporta a criação de VMs, servidores baremetais (através do uso de ironic) e possui suporte limitado para contêineres do sistema. Ele depende de alguns serviços, tais como, Keystone, Glance, Neutron, Placement.
- Neutron - É responsável pelo gerencimanto dos recursos de rede do OpenStack. Ele cria as vNICs para as VMs, redes, subnets etc.
- Glance - É responsável por gerir as imagens que são usadas nas VMs.

## 3. Operação do OpenStack via Horizon ##

### 3.1 Visão Geral do OpenStack ###

![](/Figuras/tela_inicial.png)
Figura 1 - Visão Geral

Aba Projeto - Mostra todos os recursos de um projeto para gerência e execução. Os recursos são divididos em duas partes: Computação (Instâncias, Imagens, Pares de Chave,) e Rede (Redes, Roteadores, Grupos de Segurança e IPs Flutuantes).

Aba Admin - (Apenas para usuários compermissão de Administrador) Mostra uma visão geral dos recursos no OpenStack. Pode-se visualizar, criar snapshot, migrar, Ligar/Pausar/Desligar/Excluir instâncias; criar/modificar flavors; instanciar/modificar imagens; Criar/Modificar redes; Criar/Modificar subredes; Editar Roteadores; Alocar/Desalocar IPs Flutuantes para projetos; Verificar o pool de recursos disponíveis quotas de computação e rede etc.

Aba Identidade
### ###





## Via CLI ##

### Pré-Requisitos ###

O Openstack utiliza os plugins de autenticação python-keystoneclient que oferece suporte a vários métodos de autenticação.

#### Autenticação ####

##### via senha #####
Esse é o tipo de autenticação mais comum. São requisitados: 
``` bash
$ export OS_IDENTITY_API_VERSION=3                
$ export OS_AUTH_URL=http://<IP>:5000/v3   
$ export OS_DEFAULT_DOMAIN=default               
$ export OS_USERNAME=<USER>                      
$ export OS_PASSWORD=<PASSWORD>                     
$ export OS_PROJECT_NAME=<PROJECT>                  
```

##### via token #####
Nesse tipo de autenticação, são necessários apenas dois parâmetros.
```bash
# Parâmetros obrigatórios
--os-auth-url
--os-token
```
Essa autenticação é utilizada quando é utilizada o curl. Exemplo de requisição de um token:
``` curl
curl -i \
  -H "Content-Type: application/json" \
  -d '
{ "auth": {
    "identity": {
      "methods": ["password"],
      "password": {
        "user": {
          "name": "<USUÁRIO>",
          "domain": { "id": "default" },
          "password": "<SENHA>"
        }
      }
    }
  }
}' \
  "http://192.168.0.65:5000/v3/auth/tokens" ; echo
```

Resposta da requisição
```
HTTP/1.1 201 Created
X-Subject-Token: MIIFvgY...
Vary: X-Auth-Token
Content-Type: application/json
Content-Length: 1025
Date: Tue, 10 Jun 2014 20:55:16 GMT

{
  "token": {
    "methods": ["password"],
    "roles": [{
      "id": "9fe2ff9ee4384b1894a90878d3e92bab",
      "name": "_member_"
    }, {
      "id": "c703057be878458588961ce9a0ce686b",
      "name": "admin"
    }],
    "expires_at": "2014-06-10T2:55:16.806001Z",
    "project": {
      "domain": {
        "id": "default",
        "name": "Default"
      },
      "id": "8538a3f13f9541b28c2620eb19065e45",
      "name": "admin"
    },
...
```
O token é retornado na variável 'X-Subject-Token', localizado na segunda linha da resposta

O trecho de código abaixo apresenta uma requisição ao OpenStack para verificar as informações dos recursos de rede.
```curl
OS_TOKEN='gAAAAABb10ZSi7Eu7LxOJySkahFPIaCa8VgpPQ2OqpqBTB7HjB-dId0-4JSTTIvKfVIHIEy1iKhsRuJLzMKgMzxsU8NvRaxxO7xAr0zpdvsTHx-iqR0ziPtCvcFKMZGKZoecnbtw2m1c4vymvZcpsxE7aT2KQWqNWFeKA4A51Ab0DKTaW1zWCqc'
curl -s \
  -H "X-Auth-Token: $OS_TOKEN" \
  "http://192.168.0.65:9696/v2.0/" | python -mjson.tool
```

É retornado os recursos de rede, com as URLs para cada recurso: **subnets**, **subnetpools**, **networks**, **ports**.
```
{
    "resources": [
        {
            "collection": "subnets",
            "links": [
                {
                    "href": "http://192.168.0.65:9696/v2.0/subnets",
                    "rel": "self"
                }
            ],
            "name": "subnet"
        },
        {
            "collection": "subnetpools",
            "links": [
                {
                    "href": "http://192.168.0.65:9696/v2.0/subnetpools",
                    "rel": "self"
                }
            ],
            "name": "subnetpool"
        },
        {
            "collection": "networks",
            "links": [
                {
                    "href": "http://192.168.0.65:9696/v2.0/networks",
                    "rel": "self"
                }
            ],
            "name": "network"
        },
        {
            "collection": "ports",
            "links": [
                {
                    "href": "http://192.168.0.65:9696/v2.0/ports",
                    "rel": "self"
                }
            ],
            "name": "port"
        }
    ]
}
```

### Comandos Básicos ###
Depois da autenticação, podemos operar o OpenStack via CLI utilizando o python-keystoneclient.

Para listar todos os comandos que podem ser utilizados no OpenStack, utilizamos o seguinte comando.
``` bash
$ openstack command list
```
É retornado todos os comandos para cada tipo de serviço dentro do OpenStack. Por exemplo, para o serviço de computação, é listado todos os comandos para instanciar uma VM, verificar status dos hypervisor etc. 

Em caso de dúvidas em como utilizar algum comando ou deseja saber quais são os parâmetros obrigatórios e opcionais, podemos utilizar o seguinte comando.
```bash
$ openstack <COMANDO DESEJADO> -h
```
Pode-se digitar o comando todo ou parte dele. Por exemplo:
```bash
# comando parcial
$ openstack server -h
# retorna todos os comandos que se iniciam com 'openstack server'

# comando completo
$ openstack server create -h
# retorna a forma de utilização do comando 'openstack server create'
```

Escrever aqui em baixo

##### Compute #####
```bash
openstack agregate
compute compute agent
openstack compute service
openstack flavor
openstack host
openstack hypervisor
openstack keypair
openstack server
openstack usage
```

##### Identity #####
```bash
openstack access token 
openstack application credential
openstack catalog
openstack consumer
openstack credential
openstack group
openstack project
openstack request token
openstack user
openstack region
```

##### Image #####
```bash
openstack image
```

##### Metric #####
```bash
openstack metrick resource <list or show <ID>>
```

##### Network ##### 
```bash
openstack floating ip
openstack ip availability
openstack network 
openstack network agent
openstack network flavor
openstack router
openstack subnet
```

##### Volume #####
```bash
openstack backup
openstack snapshot
openstack volume
openstack volume backup
openstack volume snapshot
openstack volume type
```

## Operação do OpenStack ##

### via Horizon ###

### via CLI ###

#### Pré-Requisitos ####

O Openstack utiliza os plugins de autenticação python-keystoneclient que oferece suporte a vários métodos de autenticação.

##### Autenticação #####

###### via senha ######
Esse é o tipo de autenticação mais comum. São requisitados: 
``` bash
# Parâmetros obrigatórios
--os-auth-url    	# URL para autenticação com o serviço de identidade
--os-project-name	# Um projeto
--os-username		# Usuário

No minicurso, é usado para autenticação via CLI
# Parâmetros opcionais
--os-password		# Senha do usuário 
```

###### via token ######
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

O trecho de código abaixo apresenta uma requisição ao OpenStack para verificar as informações da topologia de redes.
```curl
OS_TOKEN='gAAAAABb10ZSi7Eu7LxOJySkahFPIaCa8VgpPQ2OqpqBTB7HjB-dId0-4JSTTIvKfVIHIEy1iKhsRuJLzMKgMzxsU8NvRaxxO7xAr0zpdvsTHx-iqR0ziPtCvcFKMZGKZoecnbtw2m1c4vymvZcpsxE7aT2KQWqNWFeKA4A51Ab0DKTaW1zWCqc'
curl -s \
  -H "X-Auth-Token: $OS_TOKEN" \
  "http://192.168.0.65:9696/v2.0/" | python -mjson.tool
```

É retornado os recursos de rede, com as URLs para cada recurso: subnets, subnetpools, networks, ports.
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


#### Comando Geral #### 
```bash
openstack command list # retorna todos comandos relacionados a cada grupo ou módulo

# Em caso de dúvidas nos parâmetros de um comando, utiliza-se o "-h" no final do comando, como é apresentado abaixo
openstack <comando> -h 
```

#### Comandos Comuns #### 
```bash
openstack --version
openstack availability zone list # 
openstack configuration show
openstack extension list
openstack extension show
openstack versions show
```

#### Compute ####
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

#### Identity ####
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

#### Image ####
```bash
openstack image
```

#### Metric ####
```bash
openstack metrick resource <list or show <ID>>
```

#### Network #### 
```bash
openstack floating ip
openstack ip availability
openstack network 
openstack network agent
openstack network flavor
openstack router
openstack subnet
```

#### Volume ####
```bash
openstack backup
openstack snapshot
openstack volume
openstack volume backup
openstack volume snapshot
openstack volume type
```

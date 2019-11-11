## Operação do OpenStack ##

### via Horizon ###

### via CLI ###

#### Pré-Requisitos ####

Falta escrever sobre os pré-requisitos

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

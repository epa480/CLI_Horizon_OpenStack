## Operação do OpenStack ##

### via Horizon ###

### via CLI ###

#### Pré-Requisitos ####

Falta escrever sobre os pré-requisitos

#### Dados Gerais #### 
```bash
openstack --version
openstack -v  # --verbose
openstack -h  # --help
```

##### Versão #####

```bash
# comando
$ openstack --version

# retorno
openstack 3.16.2
```

#### Network #### 

```bash
#Rede
openstack network -h

#subnet
openstack subnet -h

#IP Flutuante
openstack floating ip -h

openstack router -h
```




#### Cinder ####
```bash
openstack image -h
```

#### Keypair #### 
```bash
openstack keypair -h
```

#### Flavor (nova) #### 
```bash
openstack flavor create
openstack flavor delete
openstack flavor list
openstack flavor set
openstack flavor show
openstack flavor unset
```

```bash
# comando
$ openstack flavor list # Lista os flavors

# Parâmetros opcionais
# -f {csv, json, table, value, yaml}    Formato de saida, o padrão é table
# --public                              Lista todos os flavors que são públicos
# --private                             Lista todos os flavors que são privados ao projeto 
# --all                                 Lista todos os flavors que o projeto pode utilizar
# --long                                Lista campos adicionais na saida
# --limit <num-flavors>

# retorno
+--------------------------------------+------------+------+------+-----------+-------+-----------+
| ID                                   | Name       |  RAM | Disk | Ephemeral | VCPUs | Is Public |
+--------------------------------------+------------+------+------+-----------+-------+-----------+
| 2fbecf9b-e341-4c94-a023-9e595632ebf4 | ds1G       | 1024 |  100 |         0 |     1 | True      |
| 3df9e0ab-6587-4f33-9044-f2ceb9257a86 | generica   |  512 |   10 |         0 |     1 | True      |
| 977b4f83-bc50-45b2-86e8-4145afb7f196 | ds1G-large | 1024 |  500 |         0 |     1 | True      |
| b2347587-ac18-4aa1-82b2-677477e258e3 | ds1G-small | 1024 |   25 |         0 |     1 | True      |
+--------------------------------------+------------+------+------+-----------+-------+-----------+
```

```bash
# comando
$ openstack flavor show <name ou ID> # Mostra as configurações de um flavor específico

# Parâmetros opcionais
# -f {csv, json, table, value, yaml}    Formato de saida, o padrão é table
# -c COLUMN, --max-width <integer>, --noident, entre outros

# Comando
$ openstack flavor show ds1G -f json

{
  "name": "ds1G", 
  "ram": 1024, 
  "properties": "", 
  "OS-FLV-DISABLED:disabled": false, 
  "vcpus": 1, 
  "access_project_ids": null, 
  "os-flavor-access:is_public": true, 
  "rxtx_factor": 1.0, 
  "OS-FLV-EXT-DATA:ephemeral": 0, 
  "disk": 100, 
  "id": "2fbecf9b-e341-4c94-a023-9e595632ebf4", 
  "swap": ""
}

# Comando
$ openstack flavor show ds1G  

+----------------------------+--------------------------------------+
| Field                      | Value                                |
+----------------------------+--------------------------------------+
| OS-FLV-DISABLED:disabled   | False                                |
| OS-FLV-EXT-DATA:ephemeral  | 0                                    |
| access_project_ids         | None                                 |
| disk                       | 100                                  |
| id                         | 2fbecf9b-e341-4c94-a023-9e595632ebf4 |
| name                       | ds1G                                 |
| os-flavor-access:is_public | True                                 |
| properties                 |                                      |
| ram                        | 1024                                 |
| rxtx_factor                | 1.0                                  |
| swap                       |                                      |
| vcpus                      | 1                                    |
+----------------------------+--------------------------------------+
```

```bash
# comando
openstack flavor create <flavor-name>

# Parâmetros adicionais
# --id                                  (Default: auto)
# --ram <size-MB>                       Tamanho da memória (Default 256 MB)
# --disk <size-GB>                      Tamanho do disco (Default 0 GB)
# --ephemeral <size-GB>                 Tamanho do disco efemero (Default 0 GB)
# --swap <size-MB>                      Tamanho do swap (Default 0 MB)
# --vcpus <vcpus>                       Número de vCPUs (Default 1)
# --rxtx-factor <factor>                (Default 1.0)
# --public                              Flavor disponível para os demais projetos
# --private                             Flavor não disponível para os demais projetos
# --project <project>                   Permite que o projeto <project> acesse ao flavor <flavor-name> privado
# -- description <description>          Descrição do Flavor
# -- project-domain <project-domain>    Alguma coisa

# comando
openstack flavor create teste

# retorna
+----------------------------+--------------------------------------+
| Field                      | Value                                |
+----------------------------+--------------------------------------+
| OS-FLV-DISABLED:disabled   | False                                |
| OS-FLV-EXT-DATA:ephemeral  | 0                                    |
| disk                       | 0                                    |
| id                         | 699e9b16-660c-42ca-88e6-d8a888edf979 |
| name                       | teste                                |
| os-flavor-access:is_public | True                                 |
| properties                 |                                      |
| ram                        | 256                                  |
| rxtx_factor                | 1.0                                  |
| swap                       |                                      |
| vcpus                      | 1                                    |
+----------------------------+--------------------------------------+

# comando
openstack flavor create teste2 --ram 512 --disk 10 --vcpus 2 --private

#retorna
+----------------------------+--------------------------------------+
| Field                      | Value                                |
+----------------------------+--------------------------------------+
| OS-FLV-DISABLED:disabled   | False                                |
| OS-FLV-EXT-DATA:ephemeral  | 0                                    |
| disk                       | 10                                   |
| id                         | d50392c6-c858-43f7-b213-da2ed419f417 |
| name                       | teste2                               |
| os-flavor-access:is_public | False                                |
| properties                 |                                      |
| ram                        | 512                                  |
| rxtx_factor                | 1.0                                  |
| swap                       |                                      |
| vcpus                      | 2                                    |
+----------------------------+--------------------------------------+
```

```bash
# comando
$ openstack flavor delete <Flavor name or ID>

# comando
$ openstack flavor delete teste
# não possui resposta
```


```bash
# comando
$ openstack flavor set <Flavor name or ID> # Adiciona propriedades de CPU, Memória, Disco, etc ao Flavor
                                           # Adicionar descrição e outros projetos para visualizar este flavor (--projeto <name or ID>)
                                           # Remover todas as propriedades adicionadas (--no-property)
# comando
$ openstack flavor set teste \
    --property quota:read_bytes_sec=10240000 \
    --property quota:write_bytes_sec=10240000

# Verificando as configurações do flavor teste
+----------------------------+-------------------------------------------------------------------+
| Field                      | Value                                                             |
+----------------------------+-------------------------------------------------------------------+
| OS-FLV-DISABLED:disabled   | False                                                             |
| OS-FLV-EXT-DATA:ephemeral  | 0                                                                 |
| access_project_ids         | None                                                              |
| disk                       | 0                                                                 |
| id                         | 05fbda94-2458-4d54-977e-9a732a2dfb57                              |
| name                       | teste                                                             |
| os-flavor-access:is_public | True                                                              |
| properties                 | quota:read_bytes_sec='10240000', quota:write_bytes_sec='10240000' |
| ram                        | 512                                                               |
| rxtx_factor                | 1.0                                                               |
| swap                       |                                                                   |
| vcpus                      | 1                                                                 |
+----------------------------+-------------------------------------------------------------------+
```

```bash
# comando
openstack flavor unset <Flavor name or ID>  # Remove os dados cadastrados no campo propriedades
                                            # Remove o acesso de projetos ao flavor
# comando
$ openstack flavor unset teste --property quota:read_bytes_sec

# verificando as configurações do flavor teste
+----------------------------+--------------------------------------+
| Field                      | Value                                |
+----------------------------+--------------------------------------+
| OS-FLV-DISABLED:disabled   | False                                |
| OS-FLV-EXT-DATA:ephemeral  | 0                                    |
| access_project_ids         | None                                 |
| disk                       | 0                                    |
| id                         | 05fbda94-2458-4d54-977e-9a732a2dfb57 |
| name                       | teste                                |
| os-flavor-access:is_public | True                                 |
| properties                 | quota:write_bytes_sec='10240000'     |
| ram                        | 512                                  |
| rxtx_factor                | 1.0                                  |
| swap                       |                                      |
| vcpus                      | 1                                    |
+----------------------------+--------------------------------------+
```



#### Compute ####
```bash
openstack server -h

openstack snapshot -h
```

#### Métricas ####
```bash
openstack metric -h
```

#### Projetos ####
```bash
openstack project -h
```

```bash
openstack user -h
```

#### Adicionais ####
```bash
#Testar antes
openstack container -h
```

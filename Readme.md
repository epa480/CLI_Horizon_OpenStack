# Minicurso - OpenStack #

## 1. Agenda

- Instalação
- **Operando via Horizon** 
  - **Visão Geral**
  - **Criar Imagem**
  - **Criar Flavor**
  - **Criar Redes**
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

![Visão Geral do OpenStack](/Figuras/tela_inicial.png) 
**Figura 1 - Visão Geral**

Aba Projeto - Mostra todos os recursos de um projeto para gerência e execução. Os recursos são divididos em duas partes: Computação (Instâncias, Imagens, Pares de Chave,) e Rede (Redes, Roteadores, Grupos de Segurança e IPs Flutuantes).

Aba Admin - (Apenas para usuários compermissão de Administrador) Mostra uma visão geral dos recursos no OpenStack. Pode-se visualizar, criar snapshot, migrar, ligar/pausar/desligar/excluir instâncias; criar/modificar flavors; instanciar/modificar imagens; criar/modificar redes; criar/modificar subredes; editar Roteadores; alocar/Desalocar IPs flutuantes para projetos; verificar o pool de recursos disponíveis de computação e rede.

Aba Identidade - (Algumas funcionalides só aparecem quando o usuário é Admininstrador). Como administrador, é possível: criar/modificar projetos, adicionar/remover membros à projetos; criar/remover usuários, trocar usuários de projetos primários; criação/modificação de grupos de usuários ; e por fim a criação de credencias de aplicação (autenticação via CLI) podendo limitar o tempo de utilização das credenciais.
Como membro, é possível: visualizar o projetos que o usuário está cadastrado, visualizar os dados do seu usuário e criar credenciais de aplicação (lembrando que essa credencial está limitada a realizar certas operações que só o admin pode fazer).

### 3.2. Imagens ###

O módulo **Cinder** tem como objetivo ser o repositório de imagens que serão usadas nas VMs.

Para verificar o conjunto de imagens que o projeto que estamos utilizando possui visibilidade, na **Aba Projeto > Computação > Imagens**, conforme é mostrado na Figura abaixo.
![Imagens](Figuras/imagem_01.png)
**Figura 2 - Imagens**

É possível visualizar todas as imagens que estão visiveis ao projeto que estamos usando. Para adicionar uma nova imagem no projeto, clicamos no botão **Criar Imagem**.
![Criar Imagem](Figuras/imagem_03.png)
**Figura 3 - Criar imagem**

Irá aparecer uma nova página pedindo algumas informações, sendo algumas obrigatórias e outras opcicionais, para a criação de uma imagem no OpenStack. As informações obrigatórias são: **Nome da Imagem**, **Arquivo** e **Formato**. As informações opcionais são utilizadas quando a imagem que será carregada para o OpenStack necessita de algum pré-requisito como tamanho mínimo de Disco, memória ou metadado. Por padrão, já estão selecionadas as opções da nova imagem estar visível ao público e desprotegida, mas pode-se alterar essas opções.

Para o minicurso, baixamos a imagem cirros do site de [repositórios de imagens para o OpenStack](https://docs.openstack.org/image-guide/obtain-images.html). Esse site, contém um conjunto de imagens ou direcionamento para as imagens: CentOS, CirrOS, Debian, Fedora, Windows, Ubuntu, openSUSE, Red Hat. Pode-se também criar imagens customizadas para adicionar no repositório do OpenStack, uma das maneirais é preparar levantar uma imagem no Virtual Box, adicinar todas as customizações e por fim instalar o **cloud-init**.

O OpenStack suporta os formatos: ISO, OVA, FLOOP, QCOW2, Raw, VDI, VMDK, AKI, AMI, ARI, Docker.

Colocamos o nome da imagem como 'cirros', selecionamos o caminho da imagem e marcamos o formato da imagem cirros como QCOW2 e por fim clicamos no botão **Criar Imagem**.

### 3.3. Criação de Flavor ###

O serviço de repositório de flavor faz parte do módulo **Nova**. 

Para a visualização e criação dos flavors, é preciso o acesso de administrador. Na **Aba Admin > Computação > Sabores**, é possível visualizar todos os flavors existentes no OpenStack, conforme é ilustrado na Figura abaixo

![Flavor](Figuras/flavor_01.png)
**Figura 4 - Flavor**

Para adicionar um novo flavor, clicamos no botão **Criar Sabor**.
![Flavor](Figuras/flavor_02.png)
**Figura 5 - Criar Flavor**

Irá aparecer uma nova página pedindo algumas informações para a criação de um flavor no OpenStack. As informações obrigatórias são: **Nome**, **vCPUs**, **RAM**, **Risco Raíz**. Por padrão o Fator RX/TX é 1, mas pode ser alterado para qualquer número inteiro maior ou igual a 1. As informações adicionais são Disco Efemero e Disco Swap que por padrão tem valor 0. Outra informação 
que é opcional está na **sub-aba Acesso ao Sabor** que indica quais projetos que podem ou não visualizar o flavor, basta selecionar os projetos que o usuário deseja que o flavor esteja visível. Caso não marque nenhum, o flavor será visível para todos os projetos. 

### 3.4. Criar Redes ###
Quando a nuvem é criada, ela não tem nenhuma rede criada internamente, necessitando fazer a criação da **rede provider** (Acesso externo), **redes internas** (para instanciar as VMs) e os **roteadores** para a interconexão entre elas.

A _**rede provider**_ é criada em modo administrador na **Aba Administrador > Rede > Redes**. Nela podemos observar todas as redes conforme é ilustrado na Figura abaixo.

![Redes](Figuras/network_00.png)
**Figura 6 - Network**

Para criar uma rede, clicamos no botão **Criar Rede** que irá aparecer uma nova página conforme a Figura Abaixo.

![Redes](Figuras/network_01.png)
**Figura 7 - Criar Rede externa**

A página de criação de redes possui 3 sub-abas: Rede, Sub-rede e Detalhes da sub-rede. A sub-aba Rede é responsável pela definição do nome da rede, em qual projeto essa rede vai estar conectada, o tipo de provedor da rede podendo ser: Local, Flat, VLAN, GRE, VXLAN e Geneve. Ela possui outros atributos como compartilhado, rede externa e sub-rede que indicam, respectivamente, em qual  projeto primário a rede vai estar alocada, indica se a rede criada será a provider, e caso seja marcada esta opção, as  demais sub-abas para a criação de sub-redes são disponibilizadas.

Na sub-aba Sub-rede colocamos o nome da sub-rede, endereçamento de rede, a versão do IP, e o IP do Gateway. Caso não seja adicionado o IP do Gateway, o OpenStack pega o primeiro endereço da sub-rede e atribui para o IP do Gateway.

A sub-aba Detalhes da Sub-Rede o DHCP estará habilitado e podemos adicionar manualmente o pool de alocação insindo "start_ip_address, end_ip_address", lista de servidores DNS adicionando um por linha e as rotas de host.

Após a criaçao da rede externa, podemos criar as redes internas tanto pela aba Admin quanto pela aba Projetos. Via aba projetos existem duas maneiras. A primeira maneira é na aba **Projeto > Rede > Topologia de Redes**. Nesta aba é apresentada a topologia de rede em forma de gráficos, ao qual podemos visualizar todas as redes disponíveis no OpenStack, porém só podemos observar o endereçamento e a mascara de rede das redes que estão compartilhadas. Podemos também observar todos os elementos, sejam eles roteadores e/ou máquinas virtuais, que estão no projeto em questão. Nesta aba é possível: levantar uma instância, criar rede e criar roteador.

![Topologia de Rede](Figuras/network_07.png)
**FIGURA 8 - Topologia de Redes**

A segunda maneira é através da aba **Projeto > Rede > Redes**. Nesta aba observamos melhor as redes disponíveis no OpenStack, podendo visualizar o nome, subredes e o seu endereçamento caso estejam compartilhado, conforme é ilustrado na Figura abaixo.

![Rede](Figuras/network_04.png)
**FIGURA 9 - Redes**

Para criar uma nova rede, vamos utilizar a segunda maneira. Clicamos no botão **Criar Rede**, localizado no canto superior à direita da Figura acima.

![Criar Rede interna](Figuras/network_05.png)
**FIGURA 10 - Criar Rede interna**

Irá aparecer uma nova página para a criação de redes. É semelhante à forma da criação de redes em modo Administrador, porém mais resumido. Na sub-aba Rede só precisamos do nome da rede e das opções Compartilhado, Criar sub-rede. Por padrão, todas as redes internas criadas via aba projeto serão do tipo vxlan que o OpenStack irá atribuir de forma automática o ID de segmentação. Caso a opção Criar sub-rede estiver marcada, serão acrescentadas duas sub-abas para a configuração de endereçamento e pool de recusos, como os possíveis endereçamentos, DNS, Gateway, de forma semelhante à criação da rede provider.


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

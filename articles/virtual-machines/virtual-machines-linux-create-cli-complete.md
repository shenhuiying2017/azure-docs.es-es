
<properties
   pageTitle="Creación de un entorno de Linux completo mediante la CLI de Azure | Microsoft Azure"
   description="Cree almacenamiento, una máquina virtual Linux, una red virtual y subred, un equilibrador de carga, una NIC, una dirección IP pública, un grupo de seguridad de red, todo desde el principio mediante la CLI de Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="06/10/2016"
   ms.author="iainfou"/>

# Creación de un entorno de Linux completo mediante la CLI de Azure

En este artículo, crearemos una red sencilla con un equilibrador de carga y un par de máquinas virtuales útiles para simplificar el desarrollo y los procesos. Le guiaremos por el proceso comando a comando, hasta que tenga dos máquinas virtuales Linux seguras funcionando a las que se pueda conectar desde cualquier parte de Internet. Luego, podrá pasar a redes y entornos más complejos.

Tras su lectura, conocerá la jerarquía de dependencias que ofrece el modelo de implementación de Resource Manager y la potencia que proporciona. Cuando vea cómo se compila el sistema, puede recompilarlo mucho más rápido mediante [plantillas de Azure Resource Manager](../resource-group-authoring-templates.md). Además, cuando vea cómo encajan las distintas partes del entorno, la creación de plantillas para automatizarlas se volverá más fácil.

El entorno contiene:

- Dos máquinas virtuales dentro de un conjunto de disponibilidad.
- Un equilibrador de carga con una regla de equilibrio de carga en el puerto 80.
- Reglas de grupo de seguridad de red (NSG) para proteger la máquina virtual del tráfico no deseado.

![Información general del entorno básico](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Para crear este entorno personalizado, deberá tener la versión más reciente de la [CLI de Azure](../xplat-cli-install.md) en modo de Resource Manager (`azure config mode arm`). También necesitará una herramienta de análisis JSON. En este ejemplo se usa [jq](https://stedolan.github.io/jq/).

## Comandos rápidos
Puede usar los siguientes comandos rápidos para crear su entorno personalizado. Para más información sobre lo que hace cada comando mientras crea el entorno, examine los [pasos detallados del tutorial a continuación](#detailed-walkthrough).

Creación del grupo de recursos:

```bash
azure group create TestRG -l westeurope
```

Compruebe el grupo de recursos mediante el analizador JSON:

```bash
azure group show TestRG --json | jq '.'
```

Cree la cuenta de almacenamiento:

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

Compruebe la cuenta de almacenamiento mediante el analizador JSON:

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

Creación de la red virtual.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Cree la subred:

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Compruebe la red virtual y la subred mediante el analizador JSON:


```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Cree una dirección IP pública:

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Cree el equilibrador de carga:

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Cree un grupo de direcciones IP front-end para el equilibrador de carga y asocie la dirección IP pública:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Cree el grupo de direcciones IP back-end para el equilibrador de carga:

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Cree reglas NAT de entrada SSH para el equilibrador de carga:

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Cree las reglas NAT de entrada web para el equilibrador de carga:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Cree el sondeo de estado del equilibrador de carga:

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Compruebe el equilibrador de carga, los grupos de direcciones IP y las reglas NAT mediante el analizador JSON:

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Cree la primera tarjeta de interfaz de red (NIC):

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Cree la segunda NIC:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Compruebe las NIC mediante el analizador JSON:

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Cree el NSG:

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Agregue las reglas de entrada para el NSG:

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Compruebe el NSG y las reglas de entrada mediante el analizador JSON:

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Enlace el NSG a las NIC:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Cree el conjunto de disponibilidad:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Cree la primera máquina virtual Linux:

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Cree la segunda máquina virtual Linux:

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Use el analizador JSON para comprobar todo lo que se creó:

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

Exporte el entorno compilado a una plantilla para volver a crear rápidamente nuevas instancias:

```bash
azure resource export TestRG
```

## Tutorial detallado
A través de los pasos detallados que aparecen a continuación se explica lo que hace cada comando mientras crea su entorno. Estos conceptos le ayudarán mientras crea sus propios entornos personalizados con fines de desarrollo o producción.

## Creación de grupos de recursos y elección de las ubicaciones para la implementación

Los grupos de recursos de Azure son entidades de implementación lógicas que contienen información de configuración y metadatos que permiten la administración lógica de las implementaciones de recursos.

```bash
azure group create TestRG westeurope
```

Salida:

```bash                        
info:    Executing command group create
+ Getting resource group TestRG
+ Creating resource group TestRG
info:    Created resource group TestRG
data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Crear una cuenta de almacenamiento

Las cuentas de almacenamiento son necesarias tanto para los discos de su máquina virtual como para cualquier otro disco de datos adicionales que desee agregar. Las cuentas de almacenamiento se crean casi inmediatamente después de crear los grupos de recursos.

Aquí se usa el comando `azure storage account create`, que pasa la ubicación de la cuenta, el grupo de recursos que la controla y el tipo de soporte de almacenamiento que quiere.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

Salida:

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
ahmet•~/workspace/keygen» azure group show testrg
info:    Executing command group show
+ Listing resource groups
+ Listing resources for the group
data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    Resources:
data:
data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
data:      Name    : computeteststore
data:      Type    : storageAccounts
data:      Location: westeurope
data:      Tags    :
data:
data:    Permissions:
data:      Actions: *
data:      NotActions:
data:
info:    group show command OK
```

Vamos a usar la herramienta [jq](https://stedolan.github.io/jq/) junto con la opción `--json` de la CLI de Azure para examinar nuestro grupo de recursos mediante el comando `azure group show`. (Para analizar el código JSON se puede usar **jsawk** o cualquier biblioteca de idioma que prefiera).

```bash
azure group show TestRG --json | jq                                                                                      
```


Salida:

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Para investigar la cuenta de almacenamiento mediante la CLI, tendrá que establecer primero los nombres y claves de la cuenta con una variación del comando siguiente. Reemplace el nombre de la cuenta de almacenamiento en el siguiente ejemplo por un nombre de su elección:

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Luego podrá ver la información de almacenamiento fácilmente:

```bash
azure storage container list
```

Salida:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Creación de una red virtual y una subred

Después va a necesitar crear una red virtual que se ejecute en Azure y una subred en la que pueda instalar sus máquinas virtuales.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Salida:

```bash
info:    Executing command network vnet create
+ Looking up virtual network "TestVNet"
+ Creating virtual network "TestVNet"
+ Loading virtual network state
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
data:    Name                            : TestVNet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Una vez más, vamos a usar la opción --json de `azure group show` y **jq** para ver cómo creamos nuestros recursos. Ya tenemos un recurso de `storageAccounts` y un recurso de `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Salida:

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Ahora vamos a crear una subred en la red virtual `TestVnet` en la que se van a implementar las máquinas virtuales. Usaremos el comando `azure network vnet subnet create`, junto con los recursos que ya hemos creado: el grupo de recursos `TestRG` y la red virtual `TestVNet`. Agregaremos el nombre de subred `FrontEnd` y el prefijo de dirección de subred `192.168.1.0/24` de la forma siguiente:

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Salida:

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "FrontEnd"
+ Creating subnet "FrontEnd"
+ Looking up the subnet "FrontEnd"
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : FrontEnd
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Dado que la subred está lógicamente dentro de la red virtual, buscaremos la información de la subred con un comando un poco diferente. El comando que usaremos es `azure network vnet show`, pero continuaremos examinando la salida JSON mediante **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Salida:

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "FrontEnd",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
  "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
  "name": "TestVNet",
  "location": "westeurope"
}
```

## Creación de una dirección IP pública (PIP)

Ahora, vamos a crear la dirección IP pública (PIP) que asignaremos a su equilibrador de carga. Le permite conectarse a sus máquinas virtuales desde Internet mediante el comando `azure network public-ip create`. Dado que la dirección predeterminada es dinámica, crearemos una entrada DNS con nombre en el dominio **cloudapp.azure.com** con la opción `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Salida:

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "TestPIP"
+ Creating public ip address "TestPIP"
+ Looking up the public ip "TestPIP"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
data:    Name                            : TestPIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Este recurso también es de nivel superior, por lo que se puede ver con `azure group show`.

```bash
azure group show TestRG --json | jq '.'
```

Salida:

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/TestRG",
"name": "TestRG",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
    "name": "TestNIC",
    "type": "networkInterfaces",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
    "name": "testpip",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
    "name": "TestVNet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
    "name": "computeteststore",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Puede investigar más detalles del recurso, incluido el nombre de dominio completo (FQDN) del subdominio, mediante el comando `azure network public-ip show`, que es más completo. Tenga en cuenta que el recurso de la dirección IP pública se ha asignado de forma lógica, pero aún no hay ninguna dirección específica asignada. Para ello, va a necesitar un equilibrador de carga, que todavía no se ha creado.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Salida:

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "testsubdomain",
    "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
"id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
"name": "testpip",
"location": "westeurope"
}
```

## Creación de un equilibrador de carga y grupos de direcciones IP
Al crear un equilibrador de carga, puede distribuir el tráfico en varias máquinas virtuales. Puede hacerlo al ejecutar aplicaciones web, por ejemplo. También proporciona redundancia a la aplicación mediante la ejecución de varias máquinas virtuales que responden a las solicitudes de los usuarios en caso de que haya tareas de mantenimiento o cargas elevadas.

Creamos nuestro equilibrador de carga con:

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Salida:

```bash
info:    Executing command network lb create
+ Looking up the load balancer "TestLB"
+ Creating load balancer "TestLB"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB
data:    Name                            : TestLB
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```
Nuestro equilibrador de carga está bastante vacío, así que vamos a crear algunos grupos de direcciones IP. Queremos crear dos grupos de direcciones IP para nuestro equilibrador de carga: uno para el front-end y otro para el back-end. El grupo de direcciones IP front-end se verá de forma pública. También es la ubicación donde asignaremos la PIP creada anteriormente. A continuación, usaremos el grupo back-end como ubicación de conexión para nuestras máquinas virtuales. De este modo, el tráfico puede fluir a través del equilibrador de carga hacia las máquinas virtuales.

En primer lugar, vamos a crear nuestro grupo IP front-end:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Salida:

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "TestLB"
+ Looking up the public ip "TestLBPIP"
+ Updating load balancer "TestLB"
data:    Name                            : TestFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP
info:    network lb frontend-ip create command OK
```

Tenga en cuenta cómo se usa el conmutador `--public-ip-name` para pasar el TestLBPIP creado anteriormente. De esta forma, se asigna la dirección IP pública al equilibrador de carga para que podemos comunicarnos con nuestras máquinas virtuales a través de Internet.

Después, vamos a crear nuestro segundo grupo IP, esta vez para el tráfico de back-end:

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Salida:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos ver cómo está el equilibrador de carga buscando la salida JSON con `azure network lb show` y examinándolo:

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Salida:

```bash
{
  "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## Creación de reglas NAT del equilibrador de carga
Para que el tráfico fluya a través de nuestro equilibrador de carga, hay que crear reglas NAT que especifiquen acciones entrantes o salientes. Puede determinar el protocolo que va a usarse y, luego, asignar los puertos externos a los internos que desee. Para nuestro entorno, vamos a crear algunas reglas que habiliten SSH en nuestras máquinas virtuales a través de nuestro equilibrador de carga. Vamos a configurar los puertos TCP 4222 y 4223 para dirigir al puerto TCP 22 de nuestras máquinas virtuales (que crearemos más adelante):

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Salida:

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "TestLB"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default frontend IP configuration "TestFrontEndPool"
+ Updating load balancer "TestLB"
data:    Name                            : VM1-SSH
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Repita el procedimiento con la segunda regla NAT para SSH:

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Continuemos y creemos una regla NAT para el puerto TCP 80, que enlaza la regla a nuestros grupos IP. Si lo hacemos, en lugar de enlazar la regla a nuestras máquinas virtuales individualmente, basta con que agreguemos o quitemos máquinas virtuales del grupo de direcciones IP. A continuación, el equilibrador de carga ajusta automáticamente el flujo de tráfico:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Salida:

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "TestLB"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "TestLB"
data:    Name                            : WebRule
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
info:    network lb rule create command OK
```

## Creación del sondeo de estado de un equilibrador de carga

Una acción de sondeo de estado comprueba periódicamente las máquinas virtuales que están detrás de nuestro equilibrador de carga para asegurarse de que funcionan y responden a las solicitudes, tal como se define. De lo contrario, se quitarán de la operación para asegurarse de que no se redirigen los usuarios a dichas máquinas virtuales. Puede definir comprobaciones personalizadas para el sondeo de estado, junto con intervalos y valores de tiempo de espera. Para más información sobre los sondeos de estado, consulte [Sondeos del Equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Salida:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Http
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

En este caso, especificamos un intervalo de 15 segundos para nuestras comprobaciones de mantenimiento; se pueden ignorar un máximo de cuatro sondeos (un minuto) antes de que el equilibrador de carga considere que el host no volverá a funcionar.

## Comprobación del equilibrador de carga
Ahora se ha realizado la configuración del equilibrador de carga. Estos son los pasos adoptados:

1. Primero creó un equilibrador de carga.
2. A continuación, creó un grupo de direcciones IP front-end y asignó una dirección IP pública al mismo.
3. Luego, creó un grupo de direcciones IP back-end al que pueden conectarse las máquinas virtuales.
4. Después, creó reglas NAT que permiten SSH en las máquinas virtuales con fines de administración, junto con una regla que permite el puerto TCP 80 en nuestra aplicación web.
5. Por último, agregó un sondeo de estado para comprobar periódicamente las máquinas virtuales. Esto garantiza que los usuarios no intenten obtener acceso a una máquina virtual que ya no funciona ni proporciona contenido.

Revisemos cuál es ahora el aspecto del equilibrador de carga:

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Salida:

```bash
{
  "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM1-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM2-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "WebRule",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "frontendPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe",
      "protocol": "Http",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 4,
      "requestPath": "healthprobe.aspx",
      "provisioningState": "Succeeded",
      "name": "HealthProbe"
    }
  ]
}
```

## Creación de una NIC para usarla con la máquina virtual Linux

 Las NIC están disponibles de manera programática porque puede aplicar reglas a su uso. También puede tener más de una. Tenga en cuenta que en el siguiente comando `azure network nic create` se enlaza la NIC al grupo de direcciones IP back-end de carga y se asocia a la regla NAT para permitir el tráfico SSH. Para ello, debe especificar el id. de la suscripción de Azure en lugar de `<GUID>`:

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

Salida:

```bash
info:    Executing command network nic create
+ Looking up the subnet "FrontEnd"
+ Looking up the network interface "LB-NIC1"
+ Creating network interface "LB-NIC1"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1
data:    Name                            : LB-NIC1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
data:
info:    network nic create command OK
```

Para ver los detalles, examine directamente el recurso. Para ello, use el comando `azure network nic show`:

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Salida:

```bash
{
  "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1",
  "name": "LB-NIC1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Ahora crearemos la segunda NIC, que, de nuevo, se enlaza a nuestro grupo de direcciones IP back-end. Esta vez, la segunda regla NAT permite el tráfico SSH:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Creación de un grupo de seguridad de red (NSG) y las reglas

Ahora creamos el NSG y las reglas de entrada que rigen el acceso a la NIC.

```bash
azure network nsg create TestRG TestNSG westeurope
```

Vamos a agregar la regla de entrada del NSG para permitir conexiones entrantes en el puerto 22 (para habilitar SSH):

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow TestRG TestNSG SSHRule
```

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

> [AZURE.NOTE] La regla de entrada es un filtro para las conexiones de red entrantes. En este ejemplo, enlazamos el NSG a la NIC virtual de las máquinas virtuales, lo que significa que cualquier solicitud que se realice al puerto 22 atravesará la NIC de nuestra máquina virtual. Se trata de una regla sobre una conexión de red y no sobre un punto de conexión, como en el caso de las implementaciones clásicas. Esto significa que, para abrir un puerto, debe establecer `--source-port-range` en '*' (el valor predeterminado) para aceptar las solicitudes entrantes de **cualquier** puerto solicitante. Los puertos suelen ser dinámicos.

## Enlace a la NIC

Enlace el NSG a las NIC:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## Crear un conjunto de disponibilidad
Los conjuntos de disponibilidad ayudan a propagar las máquinas virtuales a los dominios de error y dominios de actualización. Vamos a crear un conjunto de disponibilidad para sus máquinas virtuales:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Los dominios de error definen un grupo de máquinas virtuales que comparten una fuente de alimentación común y un conmutador de red. De manera predeterminada, las máquinas virtuales configuradas dentro de su conjunto de disponibilidad se separan en hasta tres dominios de error. La idea es que un problema de hardware en uno de estos dominios de error no afecte a cada máquina virtual que ejecute la aplicación. Azure distribuye automáticamente las máquinas virtuales en los dominios de error al incluirlos en un conjunto de disponibilidad.

Los dominios de actualización indican grupos de máquinas virtuales y hardware físico subyacente que se pueden reiniciar al mismo tiempo. Es posible que el orden de reinicio de los dominios de actualización no sea secuencial durante el mantenimiento planeado, pero solo se reiniciará una actualización cada vez. De nuevo, Azure distribuye automáticamente las máquinas virtuales en los dominios de actualización al incluirlos en un sitio de disponibilidad.

Lea más sobre cómo [administrar la disponibilidad de las máquinas virtuales](./virtual-machines-linux-manage-availability.md).

## Creación de las máquinas virtuales Linux

Ha creado los recursos de almacenamiento y de red necesarios para dar soporte a máquinas virtuales con acceso a Internet. Ahora crearemos dichas máquinas virtuales y las protegeremos con una clave SSH sin contraseña. En este caso, vamos a crear una máquina virtual con Ubuntu basada en la LTM más reciente. Buscaremos esa información de la imagen mediante `azure vm image list`, tal como se describe en el artículo sobre cómo [buscar imágenes de máquina virtual de Azure](virtual-machines-linux-cli-ps-findimage.md).

Hemos seleccionado una imagen mediante el comando `azure vm image list westeurope canonical | grep LTS`. En este caso, usaremos `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`. Para el último campo pasaremos `latest` para que en el futuro siempre obtengamos la compilación más reciente. (La cadena que usaremos será `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`).

El siguiente paso resultará familiar para quien ya haya creado un par de claves pública y privada ssh-rsa en Linux o Mac mediante **ssh-keygen -t rsa -b 2048**. Si no tiene ningún par de claves de certificado en el directorio `~/.ssh`, puede crearlas:

- Automáticamente, mediante la opción `azure vm create --generate-ssh-keys`.
- Manualmente, mediante [las instrucciones para crearlas usted mismo](virtual-machines-linux-ssh-from-linux.md).

Como alternativa, puede usar el método --admin-password para autenticar sus conexiones SSH una vez creada la máquina virtual. Este método suele ser menos seguro.

Creamos la máquina virtual, para lo que reunimos toda nuestra información y recursos con el comando `azure vm create`:

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Salida:

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ifoulds/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the availability set "TestAvailSet"
info:    Found an Availability set "TestAvailSet"
+ Looking up the NIC "LB-NIC1"
info:    Found an existing NIC "LB-NIC1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "LB-NIC1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://computeteststore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Puede conectarse de inmediato a la máquina virtual con las claves SSH predeterminadas. Asegúrese de especificar el puerto adecuado, ya que las estamos pasando a través del equilibrador de carga. (Para nuestra primera máquina virtual, configuramos la regla NAT para reenviar el puerto 4222 a nuestra máquina virtual):

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Salida:

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Apr 27 23:44:06 UTC 2016

  System load: 0.37              Memory usage: 5%   Processes:       81
  Usage of /:  37.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@TestVM1:~$
```

Continúe y cree su segunda máquina virtual de la misma manera:

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Ya puede usar el comando `azure vm show testrg testvm` para examinar lo que ha creado. Llegados a este punto, ejecuta las máquinas virtuales con Ubuntu detrás de un equilibrador de carga en Azure en las que solo puede iniciar sesión con el par de claves SSH (porque las contraseñas están deshabilitadas). Puede instalar nginx o httpd, implementar una aplicación web y ver cómo fluye el tráfico a las dos máquinas virtuales a través del equilibrador de carga.

```bash
azure vm show TestRG TestVM1
```

Salida:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/8fa5cd83-7fbb-431a-af16-4a20dede8802/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli1cca1d20a1dcf56c-os-1461800591317
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli1cca1d20a1dcf56c-os-1461800591317.vhd
data:
data:    OS Profile:
data:      Computer Name                 :TestVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-20-F8-8B
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK
```


## Exportación del entorno como una plantilla
Ahora que ha creado este entorno, ¿y si quiere crear un entorno de desarrollo adicional con los mismos parámetros o un entorno de producción correspondiente? Resource Manager usa plantillas JSON que definen todos los parámetros de su entorno. Esto significa que puede crear entornos enteros haciendo referencia a esta plantilla JSON. Puede [compilar plantillas JSON manualmente](../resource-group-authoring-templates.md), o simplemente exportar un entorno existente para crear la plantilla JSON automáticamente:

```bash
azure group export TestRG
```

De esta manera, se crea el archivo `TestRG.json` en el directorio de trabajo actual. Al crear un nuevo entorno a partir de esta plantilla, se le pedirán todos los nombres de recursos, incluidos los del equilibrador de carga, las interfaces de red, las maquinas virtuales, etc. Para rellenar estos datos en el archivo de plantilla, agregue `-p` o `--includeParameterDefaultValue` al comando `azure group export` mostrado anteriormente. Edite su plantilla JSON para especificar los nombres de recursos o [cree un archivo parameters.json](../resource-group-authoring-templates.md#parameters) que especifique los nombres de recursos.

Para crear un nuevo entorno a partir de la plantilla:

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

Es posible que quiera leer [más sobre cómo realizar implementaciones desde plantillas](../resource-group-template-deploy-cli.md). Obtenga información sobre cómo actualizar los entornos de manera incremental, usar el archivo de parámetros y tener acceso a las plantillas desde una única ubicación de almacenamiento.

## Pasos siguientes

Ya está listo para empezar a trabajar con varios componentes de red y máquinas virtuales. Puede usar este entorno de ejemplo para crear la aplicación con los componentes principales aquí presentados.

<!---HONumber=AcomDC_0810_2016-->
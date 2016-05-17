<properties
   pageTitle="Creación de una máquina virtual con Linux desde cero con la CLI de Azure | Microsoft Azure"
   description="Creación de una máquina virtual con Linux, almacenamiento, red virtual y subred, NIC, IP pública, grupo de seguridad de red desde cero con la CLI de Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/29/2016"
   ms.author="iainfou"/>

# Creación de una máquina virtual con Linux desde cero con la CLI de Azure

Para crear una máquina virtual de Linux necesitará [la CLI de Azure](../xplat-cli-install.md) en modo administrador de recursos (`azure config mode arm`) y una herramienta de análisis de JSON. Para este documento se está usando [jq](https://stedolan.github.io/jq/).

## Comandos rápidos

Crear el grupo de recursos

```bash
azure group create TestRG -l westeurope
```

Comprobar el grupo de recursos mediante el analizador JSON

```bash
azure group show TestRG --json | jq '.'
```

Crear la cuenta de almacenamiento

```bash
azure storage account create -g TestRG -l westeurope --type GRS computeteststore
```

Comprobar el almacenamiento usando el analizador JSON

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

Crear la red virtual

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Crear la subred

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Comprobar la red virtual y subred usando el analizador JSON

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Crear una dirección IP pública

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Crear el equilibrador de carga

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Crear un grupo IP front-end para el equilibrador de carga y asociar la dirección IP pública

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Crear nuestro grupo IP de back-end para el equilibrador de carga

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Crear reglas NAT de entrada SSH para el equilibrador de carga

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Crear nuestras reglas NAT de entrada web para el equilibrador de carga

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Crear nuestro sondeo de estado del equilibrador de carga

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Comprobar el equilibrador de carga, los grupos IP y las reglas NAT usando el analizador JSON

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Crear la primera NIC

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Crear la segunda NIC

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Comprobar las NIC usando el analizador JSON

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Crear el NSG

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Agregar las reglas de entrada para el NSG

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Comprobar el NSG y las reglas de entrada usando el analizador JSON

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Enlazar el NSG a las NIC

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Crear el conjunto de disponibilidad

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Crear la primera máquina virtual Linux

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

Crear la segunda máquina virtual Linux

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

Comprobar que se han integrado todos los elementos mediante el analizador JSON

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

## Tutorial detallado

### Introducción

En este artículo se crea una implementación con dos máquinas virtuales Linux detrás de un equilibrador de carga. Aquí se le guiará a través de toda la implementación básica, comando a comando, hasta que tenga una máquina virtual con Linux segura en funcionamiento a la que pueda conectarse desde cualquier lugar de Internet.

Tras su lectura, conocerá la jerarquía de dependencias que ofrece el modelo de implementación del Resource Manager y la potencia que proporciona. Cuando vea cómo se crea el sistema, podrá volver a generarlo más rápidamente con comandos más directos de la CLI de Azure (consulte [este artículo](virtual-machines-linux-quick-create-cli.md) para ver más o menos la misma implementación con el comando `azure vm quick-create`) o bien podrá pasar a aprender a diseñar y automatizar todas las implementaciones de aplicaciones y redes, así como a actualizarlas mediante [plantillas de Azure Resource Manager](../resource-group-authoring-templates.md). Una vez que vea cómo encajan las distintas partes de la implementación, la creación de plantillas para automatizarlas resulta más fácil.

Vamos a crear un equilibrador de carga y una red sencillos con una máquina virtual útil para el desarrollo y el proceso simple, y explicaremos el proceso sobre la marcha. Luego, podrá pasar a redes y implementaciones más complejas.

## Creación de un grupo de recursos y elección de las ubicaciones para la implementación

Los grupos de recursos de Azure son entidades de implementación lógicas que contienen la configuración y otros metadatos que permiten la administración lógica de las implementaciones de recursos.

```bash
azure group create TestRG westeurope
```

Salida

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

Las cuentas de almacenamiento son necesarias tanto para los discos de su máquina virtual como para cualquier otro disco de datos adicionales que desee agregar, entre otros escenarios. En resumen, las cuentas de almacenamiento siempre se van a crear casi inmediatamente después de crear los grupos de recursos.

Aquí se usa el comando `azure storage account create`, que pasa la ubicación de la cuenta, el grupo de recursos que la controlará y el tipo de soporte de almacenamiento que se quiere.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore
```

Salida

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

Vamos a usar la herramienta [jq](https://stedolan.github.io/jq/) (para analizar el JSON se puede usar **jsawk** o cualquier biblioteca de idioma que prefiera) junto con la opción `--json` de la CLI de Azure para examinar nuestro grupo de recursos mediante el comando `azure group show`.

```bash
azure group show TestRG --json | jq                                                                                      
```


Salida

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

Para investigar la cuenta de almacenamiento mediante la CLI, primero es preciso establecer los nombres y claves de la cuenta mediante una variación del comando siguiente, es preciso reemplazar el nombre de cuenta de almacenamiento de información de este artículo por el suyo.

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Luego podrá ver la información de almacenamiento fácilmente:

```bash
azure storage container list
```

Salida

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Creación de una red virtual y una subred

Va a necesitar crear una Red virtual de Azure y una subred en la que pueda instalar su máquina virtual.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Salida

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

Una vez más, vamos a ver cómo creamos nuestros recursos mediante la opción --json de `azure group show` y **jq**. Ya tenemos un recurso de `storageAccounts` y un recurso de `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Salida

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

Ahora vamos a crear una subred en la red virtual `TestVnet` en la que se van a implementar las máquinas virtuales. Usamos el comando `azure network vnet subnet create`, junto con los recursos que ya hemos creado: el grupo de recursos `TestRG`, la red virtual `TestVNet`, y agregamos el nombre de subred `FrontEnd` y el prefijo de dirección de subred `192.168.1.0/24` de la forma siguiente.

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Salida

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

Dado que la subred está lógicamente dentro de la red virtual, buscaremos la información de la subred con un comando un poco diferente, -- `azure network vnet show`, aunque la salida de JSON se sigue examinando mediante **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Salida

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

Ahora vamos a crear la dirección IP pública (PIP) que asignaremos al equilibrador de carga y que permitirá conectarse a la máquina virtual desde Internet con el comando `azure network public-ip create`. Dado que el valor predeterminado es una dirección dinámica, creamos una entrada DNS con nombre en el dominio **cloudapp.azure.com** con la opción `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Salida

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

Salida

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

Además, como siempre, puede investigar más detalles del recurso, incluido el nombre de dominio completo (FQDN) del subdominio, con el comando `azure network public-ip show`, que es más completo. Tenga en cuenta que el recurso de la dirección IP pública se ha asignado de forma lógica, pero aún no hay ninguna dirección específica asignada. Para ello, va a necesitar un equilibrador de carga, que todavía no se ha creado.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Salida

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

## Creación del equilibrador de carga y los grupos IP
Si crea un equilibrador de carga, podrá distribuir el tráfico entre varias máquinas virtuales; por ejemplo, ejecutar aplicaciones web. También proporciona redundancia a la aplicación mediante la ejecución de varias máquinas virtuales que responden a las solicitudes de los usuarios en caso de que haya tareas de mantenimiento o una carga elevada.

Creamos nuestro equilibrador de carga con:

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Salida

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
El equilibrador de carga está bastante vacío, así que vamos a crear algunos grupos IP. Queremos crear dos grupos IP para nuestro equilibrador de carga: uno para el front-end y otro para el back-end. El grupo IP front-end es el que haremos visible públicamente y donde asignaremos la PIP creada anteriormente. El grupo de back-end es el que usaremos para conectarlo en orden a nuestras máquinas virtuales con el fin de que el tráfico fluya a través del equilibrador de carga.

En primer lugar, vamos a crear nuestro grupo IP front-end:

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Salida

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

Salida

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos comprobar cómo el equilibrador de carga está buscando el resultado JSON con `azure network lb show` y examinándolo:

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Salida

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

## Creación de reglas NAT de equilibrador de la carga
Para que el tráfico fluya a través de nuestro equilibrador de carga, hay que crear reglas NAT que especifiquen acciones entrantes o salientes. Puede determinar el protocolo que va a usarse y, luego, asignar los puertos externos a los internos que desee. Para nuestro entorno, vamos a crear algunas reglas que habiliten SSH en nuestras máquinas virtuales a través de nuestro equilibrador de carga. Vamos a configurar los puertos TCP 4222 y 4223 para dirigir al puerto TCP 22 de nuestras máquinas virtuales (que crearemos más adelante):

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Salida

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

Continuemos y creemos una regla NAT para el puerto TCP 80, que enlaza la regla a nuestros grupos IP. En lugar de enlazar la regla a nuestras máquinas virtuales individualmente, podremos agregar o quitar máquinas virtuales del grupo IP y que el equilibrador de carga ajuste automáticamente el flujo de tráfico:

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Salida

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

## Creación del sondeo de estado del equilibrador de carga

Una acción de sondeo de estado comprobará periódicamente las máquinas virtuales que están detrás de nuestro equilibrador de carga para asegurarse de que están operativas y responde a las solicitudes, tal y como se define. De lo contrario, se quitarán de la operación para asegurarse de no que se redirijan los usuarios a dichas máquinas virtuales. Puede definir comprobaciones personalizadas para el sondeo de estado, junto con intervalos y valores de tiempo de espera. Para obtener más información sobre los sondeos de estado, puede leer el artículo [Sondeos del Equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Salida

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

En este caso, especificamos un intervalo de 15 segundos para nuestras comprobaciones de mantenimiento; se pueden ignorar un máximo de 4 sondeos (1 minuto) antes de que el equilibrador de carga considere que el host no volverá a estar operativo.

## Comprobación del equilibrador de carga
Ya hemos terminado de configurar el equilibrador de carga. Ha creado un equilibrador de carga, un grupo IP front-end y ha asignado una dirección IP pública; después, creó un grupo IP de back-end al que se conectarán las máquinas virtuales. Después, creó reglas NAT que permitirán SSH en las máquinas virtuales con fines de administración, junto con una regla que permite el puerto TCP 80 en nuestra aplicación web. Finalmente, para asegurarse de que nuestros usuarios no traten de acceder una máquina virtual que ya no funciona ni ofrece contenido, agregó un sondeo de estado para comprobar periódicamente las máquinas virtuales.

Revisemos cuál es ahora el aspecto del equilibrador de carga.

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Salida

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

## Creación de una NIC para usará con la máquina virtual con Linux

Hasta las NIC están disponibles mediante programación, ya que es posible aplicar reglas a su uso y tener más de una. Tenga en cuenta que en el siguiente comando `azure network nic create` se enlaza la NIC al grupo IP de back-end de carga y se asocia a la regla NAT para permitir el tráfico SSH. Para ello, debe especificar el id. de la suscripción de Azure en lugar de `<GUID>`:

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

Salida

```bash 
/subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
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

Para ver los detalles, examine directamente el recurso mediante el comando `azure network nic show`.

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Salida

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

Continuemos y creemos la segunda NIC, que, de nuevo, se enlaza a nuestro grupo IP de back-end y, esta vez, a la segunda de nuestras reglas NAT para permitir el tráfico SSH:

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd -d
```

Salida

```bash
 /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Creación de un grupo de seguridad de red y las reglas

Ahora creamos el grupo de seguridad de red (NSG) y las reglas de entrada que rigen el acceso a la NIC.

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

> [AZURE.NOTE] La regla de entrada es un filtro para las conexiones de red entrantes. En este ejemplo, enlazaremos el NSG a la tarjeta de interfaz de red (NIC) virtual de las máquinas virtuales, lo que significa que cualquier solicitud que se realice al puerto 22 atravesará la NIC de nuestra máquina virtual. Dado que se trata de una regla acerca de una conexión de red (no de un punto de conexión como en las implementaciones clásicas) para abrir un puerto, debe dejar `--source-port-range` establecido en "*" (el valor predeterminado) para aceptar las solicitudes entrantes de **cualquier** puerto solicitante, que suelen ser dinámicas.

## Enlace a la NIC

Enlace el NSG a las NIC:

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## Crear un conjunto de disponibilidad
Los conjuntos de disponibilidad ayudan a propagar las máquinas virtuales a los denominados "dominios de error" y "dominios de actualización". Vamos a crear un conjunto de disponibilidad para sus máquinas virtuales:

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Los dominios de error definen un grupo de máquinas virtuales que comparten una fuente de alimentación común y un conmutador de red. De manera predeterminada, las máquinas virtuales configuradas dentro de su conjunto de disponibilidad se separan en hasta tres dominios de error. La idea es que un problema de hardware en uno de estos dominios de error no afecte a cada máquina virtual que ejecute la aplicación. Azure distribuirá automáticamente las máquinas virtuales en los dominios de error cuando se coloquen en un conjunto de disponibilidad.

Los dominios de actualización indican grupos de máquinas virtuales y hardware físico subyacente que se pueden reiniciar al mismo tiempo. El orden en que los dominios de error se reiniciarán tal vez no siga una secuencia durante un mantenimiento planeado, pero solo se reiniciarán uno por uno. De nuevo, Azure distribuirá automáticamente las máquinas virtuales a los dominios de actualización cuando se colocan en un sitio de disponibilidad.

Puede obtener más información sobre cómo [administrar la disponibilidad de las máquinas virtuales](./virtual-machines-linux-manage-availability.md).

## Creación de máquinas virtuales Linux

Ha creado los recursos de almacenamiento y de red necesarios para dar soporte a una máquina virtual con acceso a Internet. Ahora crearemos dicha máquina virtual y la protegeremos con una clave SSH sin contraseña. En este caso, vamos a crear una máquina virtual con Ubuntu basada en la LTM más reciente. Buscaremos esa información de la imagen mediante `azure vm image list`, tal y como se describe en el artículo sobre cómo [buscar imágenes de máquina virtual de Azure](virtual-machines-linux-cli-ps-findimage.md). Seleccionamos una imagen con el comando `azure vm image list westeurope canonical | grep LTS` y, en este caso, usaremos `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`, pero para el último campo pasaremos `latest` para que en el futuro siempre obtengamos la compilación más reciente (la cadena que usemos será `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`).

> [AZURE.NOTE] El siguiente paso resultará familiar para quien ya haya creado un par de claves privadas y públicas SSH RSA en Linux o Mac mediante **ssh-keygen -t rsa -b 2048**. Si no tiene ningún par de claves de certificado en el directorio `~/.ssh`, puede crearlas: <br /> 1. automáticamente, con la opción `azure vm create --generate-ssh-keys` 2. manualmente, con [las instrucciones para crearlas por uno mismo](virtual-machines-linux-ssh-from-linux.md). <br /> También puede usar las opciones `azure vm create --admin-username --admin-password` para usar el método de nombre de usuario y contraseña, que suele ser menos seguro, de autenticación de sus conexiones SSH una vez que se haya creado la máquina virtual.

Creamos la máquina virtual, para lo que reunimos toda nuestra información y recursos con el comando `azure vm create`.

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

Salida

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

Inmediatamente, puede conectarse a la máquina virtual con sus claves SSH predeterminadas, pero asegúrese de especificar el puerto adecuado, ya que las estamos pasando a través del equilibrador de carga (para nuestra primera máquina virtual, configuramos la regla NAT para reenviar el puerto 4222 a nuestra máquina virtual):

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Salida

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

Ya puede usar el comando `azure vm show testrg testvm` para examinar lo que ha creado. Ya tiene las máquinas virtuales con Ubuntu ejecutándose detrás de un equilibrador de carga en Azure en las que solo puede iniciar sesión con el par de claves SSH que tiene; las contraseñas están deshabilitadas. Puede instalar nginx o httpd, así como implementar una aplicación web y ver cómo fluye el tráfico a las dos máquinas virtuales a través del equilibrador de carga.

```bash
azure vm show TestRG TestVM1
```

Salida

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

## Pasos siguientes

Ya está listo para comenzar con varios componentes de red y máquinas virtuales.

<!---HONumber=AcomDC_0504_2016-->
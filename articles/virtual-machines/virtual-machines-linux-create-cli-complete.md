<properties
   pageTitle="Creación de una máquina virtual con Linux desde cero con la CLI de Azure | Microsoft Azure"
   description="Creación de una máquina virtual con Linux, almacenamiento, red virtual y subred, NIC, IP pública, grupo de seguridad de red desde cero con la CLI de Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/10/2016"
   ms.author="v-livech"/>

# Creación de una máquina virtual con Linux desde cero con la CLI de Azure


## Objetivo

- Implementar un grupo de recursos
- Implementar una cuenta de almacenamiento
- Implementar una red y subred virtual
- Configurar un grupo de seguridad de red y reglas de entrada
- Asignar una dirección IP pública a la NIC
- Asignar el NSG a la NIC
- Implementar una máquina virtual con Ubuntu 14.04 LTS

## Requisitos previos

- Cuenta de Azure
  - [Obtención de una evaluación gratuita.](https://azure.microsoft.com/pricing/free-trial/) 
  - [Portal de Azure](https://portal.azure.com)
- Una herramienta de análisis de JSON: este ejemplo utiliza [jq](https://stedolan.github.io/jq/)


## Introducción

En este artículo se crea una implementación similar a la implementación de un servicio en la nube con una máquina virtual con Linux en una subred de una red virtual. Aquí se le guiará a través de toda la implementación básica, comando a comando, hasta que tenga una máquina virtual con Linux segura en funcionamiento a la que pueda conectarse desde cualquier lugar de Internet.

Tras su lectura, conocerá la jerarquía de dependencias que ofrece el modelo de implementación del Resource Manager y la potencia que proporciona. Cuando vea cómo se crea el sistema, podrá volver a generar el sistema mucho más rápidamente con comandos de la CLI de Azure más directos (consulte [este artículo](virtual-machines-linux-quick-create-cli.md) si desea saber cómo se realiza más o menos la misma implementación mediante el comando `azure vm quick-create`), o bien podrá pasar a aprender a diseñar y automatizar todas las implementaciones de aplicaciones y redes, así como a actualizarlas mediante las [plantillas de Azure Resource Manager](../resource-group-authoring-templates.md). Una vez que vea cómo encajan las distintas partes de la implementación, la creación de plantillas para automatizarlas resulta más fácil.

Vamos a crear una red sencilla con una máquina virtual útil para el desarrollo y el proceso simple, y explicaremos el proceso sobre la marcha. Luego, podrá pasar a redes y implementaciones más complejas.

## Comandos rápidos

_La nomenclatura de esta sección de comandos rápidos tiene varios ejemplos que puede reemplazar por su propia configuración, así que edítela según sea necesario._

```bash
# Create the Resource Group
azure group create TestRG westeurope

# Create the Storage Account
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
azure group show testrg --json | jq '.'

# Create the Virtual Network
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
azure group show testrg --json | jq '.'

# Create the Subnet
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
azure network nic show testrg testnic --json | jq '.'

# Create the NSG
azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
azure network nic set --public-ip-name testpip testrg testnic

# Bind the NSG to the NIC
azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
    
# Verify everything built
azure vm show testrg testvm 

```


## Tutorial detallado

### Creación de un grupo de recursos y elección de las ubicaciones para la implementación 

Los grupos de recursos de Azure son entidades de implementación lógicas que contienen la configuración y otros metadatos que permiten la administración lógica de las implementaciones de recursos.

    azure group create TestRG westeurope                        
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

### Crear una cuenta de almacenamiento 

Las cuentas de almacenamiento son necesarias tanto para los discos de su máquina virtual como para cualquier otro disco de datos adicionales que desee agregar, entre otros escenarios. En resumen, las cuentas de almacenamiento siempre se van a crear casi inmediatamente después de crear los grupos de recursos.

Aquí se usa el comando `azure storage account create` y se pasa la ubicación de la cuenta, el grupo de recursos que la controlará y el tipo de soporte de almacenamiento que se desea.

    azure storage account create \  
    --location westeurope \
    --resource-group TestRG \
    --type GRS \
    computeteststore
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK
    rasquill•~/workspace/keygen» azure group show testrg 
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
    
Vamos a usar la herramienta [jq](https://stedolan.github.io/jq/) (para analizar el JSNO se puede usar **jsawk**, o cualquier biblioteca de idioma que desee), junto con la opción `--json` de la CLI de Azure, para examinar nuestro grupo de recursos mediante el comando `azure group show`.

    azure group show testrg --json | jq '.'                                                                                        
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

Para investigar la cuenta de almacenamiento mediante la CLI, primero es preciso establecer los nombres y claves de la cuenta mediante una variación del comando siguiente, es preciso reemplazar el nombre de cuenta de almacenamiento de información de este artículo por el suyo.

        AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"

Luego podrá ver la información de almacenamiento fácilmente:

        azure storage container list 
        info:    Executing command storage container list
        + Getting storage containers
        data:    Name  Public-Access  Last-Modified
        data:    ----  -------------  -----------------------------
        data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
        info:    storage container list command OK

### Creación de una red virtual y una subred 

Va a necesitar crear una Red virtual de Azure y una subred en la que pueda instalar su máquina virtual.

    azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
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

Una vez más, vamos a ver cómo creamos nuestros recursos mediante la opción --json de `azure group show` y **jq**. Ahora tenemos un recurso de `storageAccounts` y un recurso de `virtualNetworks`.

    azure group show testrg --json | jq '.'
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

Ahora vamos a crear una subred en la red virtual `TestVnet` en la que se va a implementar la máquina virtual. Usamos el comando `azure network vnet subnet create`, junto con los recursos que hemos creado: el grupo de recursos `TestRG`, la red virtual `TestVNet` y agregaremos el nombre de subred `FrontEnd` y el prefijo de dirección de subred `192.168.1.0/24`, como sigue.

    azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
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
  
Dado que la subred está lógicamente dentro de la red virtual, buscaremos la información de la subred con un comando un poco diferente-- `azure network vnet show`, pero se sigue examinando la salida de JSON mediante **jq**.

    azure network vnet show testrg testvnet --json | jq '.'
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

### Creación de una NIC para usará con la máquina virtual con Linux

Hasta las NIC están disponibles mediante programación, ya que es posible aplicar reglas a su uso y tener más de una.

        azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
        info:    Executing command network nic create
        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

Dado que el recurso NIC está asociado tanto a una máquina virtual como a un grupo de seguridad de red, puede aparecer en forma de recurso de nivel superior cuando examine el grupo de recursos `TestRG`:

        azure group show testrg --json | jq '.'
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

Para ver los detalles, examine directamente el recurso mediante el comando `azure network nic show`.

        azure network nic show testrg testnic --json | jq '.'
        {
        "ipConfigurations": [
            {
            "loadBalancerBackendAddressPools": [],
            "loadBalancerInboundNatRules": [],
            "privateIpAddress": "192.168.1.101",
            "privateIpAllocationMethod": "Static",
            "subnet": {
                "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
            },
            "provisioningState": "Succeeded",
            "name": "NIC-config",
            "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
            }
        ],
        "tags": {},
        "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
        },
        "enableIPForwarding": false,
        "provisioningState": "Succeeded",
        "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
        "name": "TestNIC",
        "location": "westeurope"
        }

### Creación de un grupo de seguridad de red y las reglas

Ahora creamos el grupo de seguridad de red (NSG) y las reglas de entrada que rigen el acceso a la NIC.

        azure network nsg create testrg testnsg westeurope

Vamos a agregar la regla de entrada del NSG para permitir conexiones entrantes en el puerto 22 (para dar soporte a SSH):

        azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

> [AZURE.NOTE] La regla de entrada es un filtro para las conexiones de red entrantes. En este ejemplo, enlazaremos el NSG a la tarjeta de interfaz de red (NIC) virtual de las máquinas virtuales, lo que significa que cualquier solicitud que se realice al puerto 22 atravesará la NIC de nuestra máquina virtual. Dado que se trata de una regla acerca de una conexión de red (no un punto de conexión como en las implementaciones clásicos) para abrir un puerto, debe dejar `--source-port-range` establecido en ' *' (el valor predeterminado) para aceptar las solicitudes entrantes de **cualquier** puerto solicitante, que suelen ser dinámicas.

### Creación de una dirección IP pública (PIP)

Ahora vamos a crear la dirección IP pública (PIP) que le permitirá conectarse a la máquina virtual desde Internet mediante el comando `azure network public-ip create`. Dado que el valor predeterminado es una dirección dinámica, creamos una entrada DNS con nombre en el dominio **cloudapp.azure.com** mediante la opción `-d testsubdomain`.

        azure network public-ip create -d testsubdomain testrg testpip westeurope
        info:    Executing command network public-ip create
        + Looking up the public ip "testpip"
        + Creating public ip address "testpip"
        + Looking up the public ip "testpip"
        data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
        data:    Name                            : testpip
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        data:    Domain name label               : testsubdomain
        data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
        info:    network public-ip create command OK

Este recurso también es de nivel superior, por lo que se puede ver con `azure group show`.

        azure group show testrg --json | jq '.'
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

Y, como siempre, puede investigar más detalles de recursos, incluido el nombre de dominio completo (FQDN) del subdominio con el comando `azure network public-ip show`, que es más completo. Tenga en cuenta que el recurso de la dirección IP pública se ha asignado de forma lógica, pero aún no hay ninguna dirección específica asignada. Para ello, va a necesitar una máquina virtual, que todavía no se ha creado.

        azure network public-ip show testrg testpip --json | jq '.'
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

### Asociación de la dirección IP pública y el grupo de seguridad de red a la NIC

        azure network nic set --public-ip-name testpip testrg testnic

Asignación del NSG a la NIC:

        azure network nic set --network-security-group-name testnsg testrg testnic

### Creación de una máquina virtual con Linux

Ha creado los recursos de almacenamiento y de red necesarios para dar soporte a una máquina virtual con acceso a Internet. Ahora crearemos dicha máquina virtual y la protegeremos con una clave SSH sin contraseña. En este caso, vamos a crear una máquina virtual con Ubuntu basada en la LTM más reciente. Buscaremos esa información de la imagen mediante `azure vm image list`, como se describe en la [búsqueda de imágenes de máquina virtual de Azure](virtual-machines-linux-cli-ps-findimage.md). Seleccionamos una imagen mediante el comando `azure vm image list westeurope canonical | grep LTS`, y en este caso usaremos `canonical:UbuntuServer:14.04.3-LTS:14.04.201509080`, pero para el último campo pasaremos `latest` para que en el futuro siempre obtengamos la compilación más reciente (la cadena usaremos será `canonical:UbuntuServer:14.04.3-LTS:latest`).

> [AZURE.NOTE] El próximo paso es familiar para quien haya creado un par de claves privadas y públicas ssh rsa en Linux o Mac mediante **ssh-keygen -t rsa -b 2048**. Si no tiene ningún par de claves de certificado en el directorio `~/.ssh`, puede crearlas: <br /> 1. automáticamente, mediante el uso de la opción `azure vm create --generate-ssh-keys` 2. manualmente, mediante el uso de [las instrucciones para crearlas automáticamente](virtual-machines-linux-ssh-from-linux.md) <br /> Como alternativa, puede usar las opciones `azure vm create --admin-username --admin-password` para usar el método de nombre de usuario y contraseña, que suele ser menos seguro, de autenticación de sus conexiones ssh una vez que se crea la máquina virtual.

Creamos la máquina virtual, para lo que reunimos toda nuestra información y recursos con el comando `azure vm create`.

        azure vm create \            
        --resource-group testrg \
        --name testvm \
        --location westeurope \
        --os-type linux \
        --nic-name testnic \
        --vnet-name testvnet \
        --vnet-subnet-name FrontEnd \
        --storage-account-name computeteststore \
        --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
        --ssh-publickey-file ~/.ssh/id_rsa.pub \
        --admin-username ops
        info:    Executing command vm create
        + Looking up the VM "testvm"
        info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account computeteststore
        + Looking up the NIC "testnic"
        info:    Found an existing NIC "testnic"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
        info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
        + Creating VM "testvm"
        info:    vm create command OK

Puede conectarse de inmediato a la máquina virtual con las claves SSH predeterminadas.

        ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
        The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
        RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
        Are you sure you want to continue connecting (yes/no)? yes
        Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
        Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)
        
        * Documentation:  https://help.ubuntu.com/
        
        System information as of Mon Sep 28 18:45:02 UTC 2015
        
        System load: 0.64              Memory usage: 5%   Processes:       81
        Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0
        
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
        
        ops@testvm:~$

Y ahora puede usar el comando `azure vm show testrg testvm` para examinar lo que ha creado. Ya tiene una máquina virtual con Ubuntu ejecutándose en Azure en la que solo se puede iniciar sesión con el par de claves SSH que tiene; las contraseñas están deshabilitadas.

        azure vm show testrg testvm 
        info:    Executing command vm show
        + Looking up the VM "testvm"
        + Looking up the NIC "testnic"
        + Looking up the public ip "testpip"
        data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
        data:    ProvisioningState               :Succeeded
        data:    Name                            :testvm
        data:    Location                        :westeurope
        data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
        data:    Type                            :Microsoft.Compute/virtualMachines
        data:
        data:    Hardware Profile:
        data:      Size                          :Standard_A1
        data:
        data:    Storage Profile:
        data:      Image reference:
        data:        Publisher                   :canonical
        data:        Offer                       :UbuntuServer
        data:        Sku                         :14.04.3-LTS
        data:        Version                     :latest
        data:
        data:      OS Disk:
        data:        OSType                      :Linux
        data:        Name                        :cli4eecdddc349d6015-os-1443465824206
        data:        Caching                     :ReadWrite
        data:        CreateOption                :FromImage
        data:        Vhd:
        data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
        data:
        data:    OS Profile:
        data:      Computer Name                 :testvm
        data:      User Name                     :ops
        data:      Linux Configuration:
        data:        Disable Password Auth       :true
        data:        SSH Public Keys:
        data:          Public Key #1:
        data:            Path                    :/home/ops/.ssh/authorized_keys
        data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
        <snip>
        data:
        data:    Network Profile:
        data:      Network Interfaces:
        data:        Network Interface #1:
        data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
        data:          Primary                   :true
        data:          MAC Address               :00-0D-3A-21-8E-AE
        data:          Provisioning State        :Succeeded
        data:          Name                      :testnic
        data:          Location                  :westeurope
        data:            Private IP alloc-method :Dynamic
        data:            Private IP address      :192.168.1.101
        data:            Public IP address       :40.115.48.189
        data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
        data:
        data:    Diagnostics Instance View:
        info:    vm show command OK

### Pasos siguientes

Ya está listo para comenzar con varios componentes de red y máquinas virtuales.

<!---HONumber=AcomDC_0330_2016-->
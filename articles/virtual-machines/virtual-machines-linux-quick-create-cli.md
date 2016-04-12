<properties
   pageTitle="Creación de una máquina virtual con Linux en Azure mediante la CLI | Microsoft Azure"
   description="Creación de una máquina virtual con Linux en Azure mediante la CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>


# Creación de una máquina virtual con Linux en Azure mediante la CLI

Este artículo muestra cómo crear rápidamente una máquina virtual con Linux en Azure mediante el comando `azure vm quick-create` de la CLI de Azure, que crea una máquina virtual con una infraestructura básica que se puede utilizar para crear un prototipo o probar un concepto muy rápidamente. El artículo requiere una cuenta de Azure ([obtenga una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)) y [la CLI de Azure](../xplat-cli-install.md) en modo de administrador de recursos (`azure config mode arm`).

## Resumen rápido de comandos

```
# One command to quickly the VM that prompts for arguments
chrisL@fedora$ azure vm quick-create
```

## Tutorial detallado

### Crear la máquina virtual con Linux

En el siguiente comando, puede usar cualquier imagen que desee, pero en este ejemplo se usa `canonical:ubuntuserver:14.04.2-LTS:latest` para crear una máquina virtual de forma rápida. (Para encontrar una imagen en Marketplace, [busque una imagen](virtual-machines-linux-cli-ps-findimage.md) o [cargue su propia imagen personalizada](virtual-machines-linux-create-upload-generic.md)). Tendrá un aspecto similar al siguiente.

En los siguientes ejemplos de comandos, reemplace los valores entre &lt; y &gt; con los valores de su propio entorno.

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: exampleResourceGroup
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "exampleVMname"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Ahora puede usar SSH en el puerto 22 de SSH predeterminado de su máquina virtual.

`azure vm quick-create` crea rápidamente una máquina virtual para que pueda iniciar sesión y ponerse a trabajar. Sin embargo, no tiene un entorno complejo, por lo que si desea personalizar su entorno, puede [usar una plantilla de Azure Resource Manager para crear una implementación específica rápidamente](virtual-machines-linux-cli-deploy-templates.md), o puede [crear su propio entorno personalizado para una máquina virtual con Linux mediante los comandos de la CLI de Azure directamente](virtual-machines-linux-cli-deploy-templates.md).

En el ejemplo anterior, se crea:

- un grupo de recursos de Azure en el que implementar la máquina virtual
- una cuenta de almacenamiento de Azure para incluir el archivo .vhd que es la imagen de la máquina virtual
- una red virtual de Azure y una subred para proporcionar conectividad a la máquina virtual
- una tarjeta de interfaz de red (NIC) virtual para asociar la máquina virtual a la red
- una dirección IP pública y un prefijo de subdominio para proporcionar una dirección de Internet para uso externo y, luego, la máquina virtual de Linux dentro de ese entorno.

Esta máquina virtual se expone directamente a Internet y solo está protegida mediante un nombre de usuario y una contraseña.

## Pasos siguientes

Ahora ha creado una máquina virtual con Linux rápidamente para usarla con fines de prueba o demostración. Puede crear un entorno de ejecución más seguro con una máquina virtual con Linux en Azure mediante una de las acciones siguientes:

- [Create a Linux VM in Azure using Azure Templates (Crear una máquina virtual con Linux en Azure mediante plantillas de Azure)](virtual-machines-linux-cli-deploy-templates.md)
- [Create an SSH-Secured Linux VM in Azure using Azure Templates (Crear una máquina virtual con Linux protegida por SSH en Azure mediante plantillas de Azure)](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Create a Linux VM in Azure using the Azure CLI and customizing the infrastructure (Crear una máquina virtual con Linux en Azure mediante la CLI de Azure y la personalización de la infraestructura)](virtual-machines-linux-create-cli-complete.md)

así como cualquier número de herramientas de implementación, configuración y orquestación de infraestructuras patentadas y de código abierto.

<!---HONumber=AcomDC_0406_2016-->
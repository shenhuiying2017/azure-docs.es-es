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
   ms.date="04/08/2016"
   ms.author="v-livech"/>


# Creación de una máquina virtual con Linux en Azure mediante la CLI

En este se artículo muestra cómo crear rápidamente una máquina virtual de Linux en Azure mediante el comando `azure vm quick-create` de la CLI de Azure. El comando `quick-create` crea una máquina virtual con una infraestructura básica que puede usar para crear un prototipo o hacer una prueba de un concepto rápidamente. Considere que este es el camino más fácil a un shell de bash de Linux. Para los pasos de este artículo se requiere una cuenta de Azure ([obtenga una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)) y [la CLI de Azure](../xplat-cli-install.md) en modo de Resource Manager (`azure config mode arm`).

## Resumen rápido de comandos

```
# One command to quickly the VM that prompts for arguments
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## Tutorial detallado

## Crear la máquina virtual con Linux

En el siguiente comando, puede usar cualquier imagen que desee, pero en este ejemplo se usa `canonical:ubuntuserver:14.04.2-LTS:latest` para crear una máquina virtual de forma rápida. (Para encontrar una imagen en Marketplace, [busque una imagen](virtual-machines-linux-cli-ps-findimage.md) o [cargue su propia imagen personalizada](virtual-machines-linux-create-upload-generic.md)). Tendrá un aspecto similar al siguiente.

En el siguiente tutorial de comandos, reemplace los mensajes por valores de su propio entorno. En este artículo usaremos valores de "ejemplo".

```bash
# Create the Linux VM using prompts
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
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
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Ahora puede usar SSH en la máquina virtual en el puerto SSH 22 predeterminado y la dirección IP pública que aparece en el resultado anterior.

```
ahmet@fedora$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

El comando `azure vm quick-create` es la forma más rápida de crear una máquina virtual para que pueda iniciar sesión en un shell de bash y empezar a trabajar. El uso de `vm quick-create` no le proporciona los beneficios adicionales de un entorno complejo; sin embargo, si quiere personalizar su entorno, puede [usar una plantilla de Azure Resource Manager para crear una implementación específica rápidamente](virtual-machines-linux-cli-deploy-templates.md), o puede [crear su propio entorno personalizado para una máquina virtual de Linux usando directamente comandos de la CLI de Azure](virtual-machines-linux-cli-deploy-templates.md).

En el ejemplo anterior, se crea:

- un grupo de recursos de Azure en el que implementar la máquina virtual
- una cuenta de almacenamiento de Azure para incluir el archivo .vhd que es la imagen de la máquina virtual
- una red virtual de Azure y una subred para proporcionar conectividad a la máquina virtual
- una tarjeta de interfaz de red (NIC) virtual para asociar la máquina virtual a la red
- una dirección IP pública y un prefijo de subdominio para proporcionar una dirección de Internet para uso externo y, luego, la máquina virtual de Linux dentro de ese entorno.

## Pasos siguientes

Ahora ha creado una máquina virtual con Linux rápidamente para usarla con fines de prueba o demostración. Para crear una máquina virtual de Linux personalizada para su infraestructura, puede seguir cualquiera de los artículos que se indican a continuación.

- [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creación de una máquina virtual con Linux desde cero con la CLI de Azure](virtual-machines-linux-create-cli-complete.md)

Estos artículos le introducirán en la creación de una infraestructura de Azure, así como de un número cualquiera de herramientas de orquestación, configuración e implementación de infraestructura de código abierto de su propiedad.

<!---HONumber=AcomDC_0420_2016-->
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
   ms.date="04/29/2016"
   ms.author="v-livech"/>


# Creación de una máquina virtual con Linux en Azure mediante la CLI

En este artículo se muestra cómo implementar rápidamente una máquina virtual de Linux en Azure mediante el comando `azure vm quick-create` de la CLI de Azure. El comando `quick-create` implementa una máquina virtual con una infraestructura básica que la rodea y que puede usar para crear un prototipo o hacer una prueba de un concepto rápidamente. Considere que este es el camino más fácil a un shell de bash de Linux. Para los pasos de este artículo se requiere una cuenta de Azure ([obtenga una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)) y [la CLI de Azure](../xplat-cli-install.md), la sesión iniciada en (`azure login`) y en modo de Resource Manager (`azure config mode arm`). Puede implementar rápidamente una máquina virtual de Linux mediante el [Portal de Azure](virtual-machines-linux-quick-create-portal.md).

## Resumen rápido de comandos

Un comando para implementar la máquina virtual y adjuntar la clave SSH

```
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## Implementación de la máquina virtual con Linux

Con el mismo comando anterior, mostraremos cada aviso junto con la salida que debería ver.

Para ImageURN, usaremos `canonical:ubuntuserver:14.04.2-LTS:latest` para implementar una máquina virtual de Ubuntu 14.04. (Para encontrar una imagen en Marketplace, [busque una imagen](virtual-machines-linux-cli-ps-findimage.md) o [cargue su propia imagen personalizada](virtual-machines-linux-create-upload-generic.md)).

En el siguiente tutorial de comandos, reemplace los avisos por valores de su propio entorno. Estamos usando valores de "ejemplo". La salida debe tener un aspecto similar al siguiente bloque de salida:

Siga las indicaciones y escriba sus propios nombres

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

Salida

```bash
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

Ahora puede usar SSH en la máquina virtual en el puerto SSH 22 predeterminado y la dirección IP pública (o el nombre de dominio completo) que aparece en la salida anterior.

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

## Pasos siguientes

El comando `azure vm quick-create` es la forma más rápida de implementar una máquina virtual para poder iniciar sesión en un shell de bash y empezar a trabajar. Si usa `vm quick-create`, no le proporcionará las ventajas adicionales de un entorno complejo. Para implementar una máquina virtual de Linux personalizada para su infraestructura, puede seguir cualquiera de los artículos que se indican a continuación.

- [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md)
- [Creación de un entorno personalizado para una máquina virtual con Linux mediante el uso de comandos de la CLI de Azure directamente](virtual-machines-linux-cli-deploy-templates.md).
- [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creación de una máquina virtual con Linux desde cero con la CLI de Azure](virtual-machines-linux-create-cli-complete.md)

Estos artículos le introducirán en la creación de una infraestructura de Azure, así como de un número cualquiera de herramientas de orquestación, configuración e implementación de infraestructura de código abierto de su propiedad.

<!---HONumber=AcomDC_0504_2016-->
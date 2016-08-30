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
   ms.date="08/18/2016"
   ms.author="v-livech"/>


# Creación de una máquina virtual con Linux en Azure mediante la CLI

> [AZURE.NOTE] Si tiene unos momentos, ayúdenos a mejorar la documentación para máquinas virtuales Linux de Azure respondiendo a esta [encuesta rápida](https://aka.ms/linuxdocsurvey) sobre sus experiencias. Cada respuesta nos ayuda a facilitarle el trabajo.

En este artículo se muestra cómo implementar rápidamente una máquina virtual Linux en Azure mediante el comando `azure vm quick-create` de la CLI de Azure. El comando `quick-create` implementa una máquina virtual con una infraestructura básica que la rodea y que puede usar para crear un prototipo o hacer una prueba de concepto rápidamente. Para los pasos de este artículo se requiere una cuenta de Azure ([obtenga una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)) y haber iniciado sesión en [la CLI de Azure](../xplat-cli-install.md) (`azure login`) en modo de Resource Manager (`azure config mode arm`). También puede implementar rápidamente una máquina virtual Linux mediante el [Portal de Azure](virtual-machines-linux-quick-create-portal.md).

## Resumen rápido de comandos

Un comando para implementar una máquina virtual con CoreOS y adjuntar la clave SSH

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Implementación de la máquina virtual con Linux

Ahora vamos a explicar cada paso del comando usando Red Hat Enterprise Linux 7.2.

## Uso de un alias ImageURN

El comando `quick-create` de la CLI de Azure tiene alias asignados a las distribuciones de SO más habituales. La siguiente tabla enumera los alias de distribución (desde la versión 0.10 de la CLI de Azure) Todas las implementaciones con `quick-create` utilizan de manera predeterminada máquinas virtuales con almacenamiento SSD, lo que ofrece una experiencia de alto rendimiento.

| Alias | Publicador | Oferta | SKU | Versión |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | CentOS | 7,2 | más reciente |
| CoreOS | CoreOS | CoreOS | Stable | más reciente |
| Debian | credativ | Debian | 8 | más reciente |
| openSUSE | SUSE | openSUSE | 13\.2 | más reciente |
| RHEL | Redhat | RHEL | 7,2 | más reciente |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | más reciente |



Para la opción **ImageURN** (`-Q`), vamos a usar `RHEL` para implementar una máquina virtual con Red Hat Enterprise Linux 7.2. Estos alias `quick-create` representan una mínima parte del sistema operativo disponible en Azure. Para encontrar más imágenes en Marketplace, [busque una imagen](virtual-machines-linux-cli-ps-findimage.md) o [cargue su propia imagen personalizada](virtual-machines-linux-create-upload-generic.md).

En el siguiente tutorial de comandos, reemplace los mensajes por valores de su propio entorno.

Siga las indicaciones y escriba sus propios nombres

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

La salida debe tener un aspecto similar al siguiente bloque de salida:

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

SSH en la máquina virtual en el puerto 22 y la dirección IP pública que aparece en la salida. (También se puede usar el FQDN que aparece).

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
El proceso de inicio de sesión debe tener un aspecto similar al siguiente:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Pasos siguientes

El comando `azure vm quick-create` es la forma más rápida de implementar una máquina virtual para poder iniciar sesión en un shell de Bash y empezar a trabajar. `vm quick-create` no proporcionará las ventajas adicionales de un entorno complejo. Para implementar una máquina virtual Linux personalizada para su infraestructura, puede seguir cualquiera de estos artículos.

- [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md)
- [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](virtual-machines-linux-create-cli-complete.md).
- [Creación de una máquina virtual Linux protegida con SSH en Azure mediante plantillas](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

<!---HONumber=AcomDC_0824_2016-->
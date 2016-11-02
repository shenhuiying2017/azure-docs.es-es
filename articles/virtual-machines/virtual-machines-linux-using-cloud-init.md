<properties
    pageTitle="Uso de cloud-init para personalizar una VM de Linux durante la creación | Microsoft Azure"
    description="Uso de cloud-init para personalizar una VM de Linux durante la creación."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# Uso de cloud-init para personalizar una VM de Linux durante la creación

Este artículo muestra cómo crear un script de cloud-init para establecer el nombre de host, actualizar los paquetes instalados y administrar cuentas de usuario. Los scripts de cloud-init se llaman durante la creación de la VM desde la CLI de Azure.

## Requisitos previos

Requisitos previos: [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/), [claves pública y privada de SSH](virtual-machines-linux-mac-create-ssh-keys.md) y [la CLI de Azure](../xplat-cli-install.md), que se debe cambiar al modo de Azure Resource Manager mediante `azure config mode arm`.

## Comandos rápidos

Cree un script cloud-init.txt que establezca el nombre de host, actualice todos los paquetes y agregue un usuario de sudo a Linux.

```bash
#cloud-config
hostname: exampleServerName
apt_upgrade: true
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Cree una VM Linux con cloud-init para configurarla durante el arranque.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

## Introducción

Cuando inicia una nueva VM de Linux, obtiene una VM Linux estándar con nada personalizado o preparada para sus necesidades. [Cloud-init](https://cloudinit.readthedocs.org) es una forma estándar de insertar un script u opciones de configuración en esa VM Linux cuando arranca por primera vez.

En Azure hay tres formas distintas de realizar cambios en una VM Linux cuando se está implementando o arrancando.

- Inserte scripts con cloud-init.
- Inserte scripts con la [extensión VMAccess](virtual-machines-linux-using-vmaccess-extension.md) de Azure.
- Una plantilla de Azure con cloud-init.
- Una plantilla de Azure con [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Para insertar scripts en cualquier momento después del arranque:

- SSH para ejecutar directamente los comandos.
- Inserte scripts con la [extensión VMAccess](virtual-machines-linux-using-vmaccess-extension.md) de Azure, ya sea de manera imperativa como en una plantilla de Azure.
- Herramientas de administración de la configuración, como Ansible, Salt, Chef y Puppet.

>[AZURE.NOTE]La extensión VMAccess ejecuta un script como raíz del mismo modo que podría hacerlo con SSH. Sin embargo, con la extensión de VM se habilitan varias características que Azure ofrece y que pueden ser útiles según el escenario.

### Disponibilidad de cloud-init en los alias de imagen de creación rápida de VM de Azure:

| Alias | Publicador | Oferta | SKU | Versión | cloud-init |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS | OpenLogic | CentOS | 7,2 | más reciente | no |
| CoreOS | CoreOS | CoreOS | Stable | más reciente | yes |
| Debian | credativ | Debian | 8 | más reciente | no |
| openSUSE | SUSE | openSUSE | 13\.2 | más reciente | no |
| RHEL | Redhat | RHEL | 7,2 | más reciente | no |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | más reciente | yes |

Microsoft trabaja con nuestros asociados para que cloud-init se incluya y funcione en las imágenes que proporcionan a Azure.


## Tutorial detallado

### Incorporación de un script de cloud-init a la creación de VM con la CLI de Azure

Para iniciar un script de cloud-init al crear una VM en Azure, especifique el archivo de cloud-init mediante el modificador `--custom-data` de la CLI de Azure.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

### Creación de un script de cloud-init para establecer el nombre de host de una VM de Linux

Una de las configuraciones más sencillas y más importantes para cualquier VM de Linux sería el nombre de host. Podemos establecer esto fácilmente mediante cloud-init con este script.

#### Script de cloud-init de ejemplo denominado `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: exampleServerName
```

Durante el primer inicio de la VM, este script de cloud-init establece el nombre de host en `exampleServerName`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_hostname.txt

```

Inicie sesión y compruebe el nombre de host de la nueva VM.

```bash
ssh exampleVM
hostname
exampleServerName
```

### Creación de un script de cloud-init para actualizar Linux

Por seguridad, desea que la VM de Ubuntu se actualice en el primer arranque. Mediante cloud-init podemos hacerlo con el siguiente script, dependiendo de la distribución de Linux que se use.

#### Script de cloud-init de ejemplo `cloud_config_apt_upgrade.txt` para la familia Debian

```bash
#cloud-config
apt_upgrade: true
```

Después de iniciarse Linux, se actualizan todos los paquetes instalados a través de `apt-get`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_apt_upgrade.txt
```

Inicie sesión y compruebe que todos los paquetes se han actualizado.

```bash
ssh exampleVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Creación de un script de cloud-init para agregar un usuario a Linux

Una de las primeras tareas en cualquier VM Linux nueva es agregar un usuario para usted mismo o para evitar el uso de `root`. Las claves SSH son el procedimiento recomendado para la seguridad y la facilidad de uso; además, se agregan al archivo `~/.ssh/authorized_keys` con este script de cloud-init.

#### Script de cloud-init de ejemplo `cloud_config_add_users.txt` para la familia Debian

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Una vez que se inicia Linux, todos los usuarios de la lista se crean y se agregan al grupo de sudo.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_add_users.txt
```

Inicie sesión y compruebe el usuario recién creado.

```bash
cat /etc/group
```

Salida

```bash
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

## Pasos siguientes

Cloud-init se convierte en la manera estándar de modificar la VM Linux en el arranque. Azure también tiene extensiones de VM, que le permiten modificar su VM Linux en el arranque o mientras está en ejecución. Por ejemplo, puede usar la extensión VMAccessExtension de Azure para restablecer SSH o la información del usuario mientras la VM está en ejecución. Con cloud-init, necesitaría reiniciar para restablecer la contraseña.

[Acerca de las características y extensiones de las máquinas virtuales](virtual-machines-linux-extensions-features.md)

[Administración de usuarios, SSH y comprobar o reparar discos en máquinas virtuales de Linux de Azure con la extensión VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0831_2016-->
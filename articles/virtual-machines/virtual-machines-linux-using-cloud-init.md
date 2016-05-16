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
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# Uso de cloud-init para personalizar una VM de Linux durante la creación

Este artículo muestra cómo crear un script de cloud-init para establecer el nombre de host, actualizar los paquetes instalados y administrar cuentas de usuario. Los scripts de cloud-init se usarán durante la creación de la máquina virtual de [la CLI de Azure](../xplat-cli-install.md).

## Requisitos previos

Requisitos previos: [Una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/), [claves públicas y privadas de SSH](virtual-machines-linux-mac-create-ssh-keys.md), un grupo de recursos de Azure en los que iniciar las VM de Linux y la CLI de Azure instalada, que se debe cambiar al modo ARM mediante `azure config mode arm`.

## Introducción

Cuando inicia una nueva VM de Linux, obtiene una VM de Linux estándar con nada personalizado o preparada para sus necesidades. [Cloud-init](https://cloudinit.readthedocs.org) es una forma estándar de inyectar un script u opciones de configuración en esa VM de Linux cuando arranca por primera vez.

En Azure hay tres formas diferentes de realizar cambios en una VM de Linux cuando está arrancando.

- Puede inyectar scripts con cloud-init.
- Puede inyectar scripts mediante [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) de Azure.
- Puede especificar una configuración personalizada en una plantilla de Azure y usarla para iniciar y personalizar la VM de Linux, que incluye compatibilidad con cloud-init, así como la extensión de VM CustomScript, entre otros.

Para inyectar scripts en cualquier momento, puede:

- usar SSH para ejecutar comandos directamente, usar [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) de Azure bien imperativamente o en una plantilla de Azure o usar herramientas de administración de configuración comunes como Ansible, Salt, Chef y Puppet, que funcionan sobre SSH después de que la VM ha completado el arranque.

NOTA: Aunque [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) simplemente ejecuta un script como raíz de la misma manera con SSH, el uso de la extensión de VM habilita varias características que ofrece Azure que pueden ser útiles en función del escenario.

## Comandos rápidos

Creación de un script de cloud-init de nombre de host

```bash
#cloud-config
hostname: exampleServerName
```

Creación de una actualización de Linux en el primer script de cloud-init de arranque para la familia Debian

```bash
#cloud-config
apt_upgrade: true
```

Creación y adición de un script cloud-init de usuario

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

## Tutorial detallado

### Incorporación de un script de cloud-init a la creación de VM con la CLI de Azure

Para iniciar un script de cloud-init al crear una VM en Azure, especifique el archivo de cloud-init mediante el modificador `--custom-data` de la CLI de Azure.

NOTA: aunque en este artículo se describe el uso del modificador `--custom-data` para archivos de cloud-init, también puede pasar código arbitrario o archivos utilizando este modificador. Si la VM de Linux ya sabe qué hacer con estos archivos, se ejecutan automáticamente.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_init_script.txt
```

### Creación de un script de cloud-init para establecer el nombre de host de una VM de Linux

Una de las configuraciones más sencillas y más importantes para cualquier VM de Linux sería el nombre de host. Podemos establecer esto fácilmente mediante cloud-init con este script.

#### Script de cloud-ini de ejemplo denominado `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: exampleServerName
```

Durante el primer inicio de la VM, este script de cloud-init establece el nombre de host en `exampleServerName`.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
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

#### Script de cloud-ini de ejemplo `cloud_config_apt_upgrade.txt` para la familia Debian

```bash
#cloud-config
apt_upgrade: true
```

Después de iniciarse la nueva VM de Linux, actualizará inmediatamente todos los paquetes instalados a través de `apt-get`.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
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

Una de las primeras tareas en cualquier VM de Linux nueva es agregar un usuario para usted mismo o para evitar el uso de `root`. Esto es fantástico por motivos de seguridad y su usabilidad una vez que agregue su clave pública de SSH para el archivo `~/.ssh/authorized_keys` de ese usuario archivo para inicios de sesión SSH sin contraseña y seguros.

#### Script de cloud-ini de ejemplo `cloud_config_add_users.txt` para la familia Debian

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

Después de iniciarse la nueva VM de Linux, creará el usuario nuevo y lo agregará al grupo sudo.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
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

<!---HONumber=AcomDC_0504_2016-->
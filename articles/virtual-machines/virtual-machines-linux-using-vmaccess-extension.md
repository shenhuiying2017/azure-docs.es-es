---
title: Restablecimiento del acceso en las máquinas virtuales de Linux de Azure con la extensión VMAccess | Microsoft Docs
description: Restablezca el acceso en las máquinas virtuales de Linux de Azure con la extensión VMAccess.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: v-livech

---
# Administración de usuarios, SSH y comprobar o reparar discos en máquinas virtuales de Linux de Azure con la extensión VMAccess
En este artículo se muestra cómo usar la extensión VMAccess de Azure para comprobar o reparar un disco, restablecer el acceso de usuario, administrar cuentas de usuario o restablecer la configuración de SSHD en Linux.

Requisitos previos: [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/), [claves pública y privada de SSH](virtual-machines-linux-mac-create-ssh-keys.md) y la CLI de Azure instalada, que se debe cambiar al modo de Azure Resource Manager mediante `azure config mode arm`.

## Comandos rápidos
Existen dos maneras de usar VMAccess en las máquinas virtuales Linux:

* Con la CLI de Azure y los parámetros necesarios.
* Con archivos JSON sin formato que procesa VMAccess para luego realizar acciones en ellos.

Para ver la sección de comandos rápidos, usaremos el método `azure vm reset-access` de la CLI de Azure. En los siguientes ejemplos de comandos, reemplace los valores que contienen "example" por los valores de su propio entorno.

## Creación de un grupo de recursos y una VM Linux
```bash
azure group create resourcegroupexample westus
```

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u userexample \
-g resourcegroupexample \
-l westus \
-y Linux \
-n debianexamplevm \
-Q Debian
```

## Restablecimiento de la contraseña raíz
Para restablecer la contraseña raíz:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u root -p examplenewPassword
```

## Restablecimiento de la clave SSH
Para restablecer la clave SSH de un usuario no raíz:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -M ~/.ssh/id_rsa.pub
```

## Creación de un usuario
Para crear un usuario:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -p examplePassword
```

## Eliminación de un usuario
```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -R userexample
```

## Restablecer SSHD
Para restablecer la configuración de SSHD:

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -r
```


## Tutorial detallado
### VMAccess definido:
El disco de la máquina virtual de Linux muestra errores. De alguna forma, restableció la contraseña raíz de la máquina virtual de Linux o eliminó por accidente la clave privada SSH. Si esto sucedió en el centro de datos, deberá ir ahí y, luego, abrir el conmutador KVM para llegar a la consola del servidor. Piense en la extensión VMAccess de Azure como ese conmutador KVM que le permite tener acceso a la consola para restablecer el acceso a Linux o realizar el mantenimiento de nivel de disco.

En el tutorial detallado vamos a usar el formato largo de VMAccess, que usa archivos JSON sin formato. Estos archivos JSON de VMAccess también se pueden llamar desde las plantillas de Azure.

### Uso de VMAccess para comprobar o reparar el disco de una máquina virtual de Linux
Con VMAccess, puede ejecutar fsck en el disco de la máquina virtual de Linux. También puede hacer una comprobación y reparación de disco con VMAccess.

Para comprobar y luego reparar el disco, use este script de VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Ejecute el script VMAccess con:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### Uso de VMAccess para restablecer el acceso de usuario a Linux
Si perdió el acceso a la raíz de la máquina virtual Linux, puede iniciar un script de VMAccess para restablecer la contraseña raíz.

Para restablecer la contraseña raíz, use este script de VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"exampleNewPassword",   
}
```

Ejecute el script VMAccess con:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Para restablecer la clave SSH de un usuario no raíz, use este script de VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"exampleUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",   
}
```

Ejecute el script VMAccess con:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### Uso de VMAccess para administrar cuentas de usuario en Linux
VMAccess es un script de Python que se puede usar para administrar usuarios en la máquina virtual de Linux sin iniciar sesión y mediante la cuenta raíz o sudo.

Para crear un usuario, use este script de VMAccess:

`create_new_user.json`

```json
{
"username":"exampleNewUserName",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",
"password":"examplePassword",
}
```

Ejecute el script VMAccess con:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Para crear un usuario:

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

Ejecute el script VMAccess con:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### Uso de VMAccess para restablecer la configuración SSHD
Si realiza cambios en la configuración SSHD de máquinas virtuales de Linux y cerrar la conexión SSH antes de comprobar los cambios, es posible que se le impida volver a realizar la conexión SSH. VMAccess se puede usar para restablecer la configuración SSHD nuevamente a una buena configuración conocida sin tener que iniciar sesión a través de SSH.

Para restablecer la configuración SSHD, use este script de VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Ejecute el script VMAccess con:

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## Pasos siguientes
Actualizar Linux con las extensiones VMAccess de Azure es un método para hacer cambios en una VM de Linux en ejecución. También puede usar herramientas como cloud-init y plantillas de Azure para modificar la VM de Linux en el arranque.

[Acerca de las características y extensiones de las máquinas virtuales](virtual-machines-linux-extensions-features.md)

[Creación de plantillas de Azure Resource Manager con extensiones de máquina virtual de Linux](virtual-machines-linux-extensions-authoring-templates.md)

[Uso de cloud-init para personalizar una VM de Linux durante la creación](virtual-machines-linux-using-cloud-init.md)

<!---HONumber=AcomDC_0831_2016-->
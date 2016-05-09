<properties
    pageTitle="Restablecimiento del acceso en las máquinas virtuales de Linux de Azure con la extensión VMAccess | Microsoft Azure"
    description="Restablezca el acceso en las máquinas virtuales de Linux de Azure con la extensión VMAccess."
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
    ms.date="04/26/2016"
    ms.author="v-livech"
/>

# Administración de usuarios, SSH y comprobar o reparar discos en máquinas virtuales de Linux de Azure con la extensión VMAccess

En este artículo se muestra cómo usar la extensión VMAccess de máquina virtual [(GitHub)](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) para comprobar o reparar el disco, restablecer el acceso de usuario, administrar cuentas de usuario o restablecer la configuración de SSHD en Linux. Este artículo necesita [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/), [claves SSH](virtual-machines-linux-mac-create-ssh-keys.md), una máquina virtual de Linux de Azure y la CLI de Azure instalada y cambiada al modo ARM con `azure config mode arm`.

## Comandos rápidos

Existen dos maneras de usar VMAccess en las máquinas virtuales de Linux. La primera es usar la CLI de Azure con `azure vm reset-access` con la marca correcta. La segunda manera de usar VMAccess es con archivos JSON sin procesar que VMAccess procesará y, luego, actuará en ellos. Para la sección de comandos rápidos, usaremos el método `azure vm reset-access`.

En los siguientes ejemplos de comandos, reemplace los valores entre &lt; y &gt; con los valores de su propio entorno.

## Restablecer contraseña raíz

Para restablecer la contraseña raíz:

```bash
ahmet@fedora$ azure vm reset-access -g <resource group> -n <vm name> -u root -p <examplePassword>
```

## Restablecimiento de la clave SSH

Para restablecer la clave SSH de un usuario no raíz:

```bash
ahmet@fedora$ azure vm reset-access -g <resource group> -n <vm name> -u <exampleUser> -M <~/.ssh/azure_id_rsa.pub>
```

## Crear un usuario

Para crear un usuario nuevo:

```bash
ahmet@fedora$ azure vm reset-access -g <resource group> -n <vm name> -u <exampleNewUserName> -p <examplePassword>
```

## Eliminar un usuario

```bash
ahmet@fedora$ azure vm reset-access -g <resource group> -n <vm name> -R <exampleNewUserName>
```

## Restablecer SSHD

Para restablecer la configuración de SSHD:

```bash
ahmet@fedora$ azure vm reset-access -g <resource group> -n <vm name> -r
```


## Tutorial detallado

### VMAccess definido:

El disco de la máquina virtual de Linux muestra errores. De alguna forma, restableció la contraseña raíz de la máquina virtual de Linux o eliminó por accidente la clave privada SSH. Si eso hubiese sucedido en los tiempos oscuros del centro de datos, vendría aquí, desbloquearía la puerta con su huella, entraría en la jaula y abriría el KVM para llegar a la consola del servidor. Piense en la extensión VMAccess de Azure como ese conmutador de KVM que le permite tener acceso a la consola para restablecer el acceso a Linux o realizar el mantenimiento de nivel de disco.

En el tutorial detallado usaremos el formato largo de VMAccess, el que usa archivos JSON sin procesar. Estos archivos JSON de VMAccess también se pueden llamar desde las plantillas de Azure.

### Uso de VMAccess para comprobar o reparar el disco de una máquina virtual de Linux

Con VMAccess, puede ejecutar fsck en el disco de la máquina virtual de Linux. Puede hacer una comprobación del disco y, luego, repararlo si encuentra algún error.

Para comprobar y reparar el disco, use este script de VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Ejecute el script VMAccess con:

```bash
ahmet@fedora$ azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### Uso de VMAccess para restablecer el acceso de usuario a Linux

Si perdió el acceso a la raíz de la máquina virtual de Linux, puede iniciar un script de VMAccess para restablecer la contraseña raíz y, así, desbloquear Linux.

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
ahmet@fedora$ azure vm extension set exampleResourceGruop exampleVM \
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
ahmet@fedora$ azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### Uso de VMAccess para administrar cuentas de usuario en Linux

VMAccess es un script de Python que se puede usar para administrar usuarios en la máquina virtual de Linux sin iniciar sesión y mediante la cuenta raíz o sudo.

Para crear un usuario nuevo, use este script de VMAccess:

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
ahmet@fedora$ azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Para crear un usuario nuevo, use este script de VMAccess:

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

Ejecute el script VMAccess con:

```bash
ahmet@fedora$ azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### Uso de VMAccess para restablecer la configuración SSHD en Linux

Si realiza cambios en la configuración SSHD de máquinas virtuales de Linux y cerrar la conexión SSH antes de comprobar los cambios, es posible que se le impida volver a realizar la conexión SSH. VMAccess se puede usar para restablecer la configuración SSHD nuevamente a una buena configuración conocida.

Para restablecer la configuración SSHD, use este script de VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Ejecute el script VMAccess con:

```bash
ahmet@fedora$ azure vm extension set exampleResourceGruop exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

<!---HONumber=AcomDC_0427_2016-->
---
title: "Restablecimiento del acceso con la extensión VMAccess y la CLI de Azure 2.0 (versión preliminar) | Microsoft Docs"
description: "Procedimiento para administrar usuarios y restablecer el acceso en máquinas virtuales Linux con la extensión VMAccess y la CLI de Azure 2.0 (versión preliminar)"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 98646daf5a4d2c9aca7dfc02a36f39d12f749443
ms.openlocfilehash: 7752b486bda4a68b14ff3e8aaf1a369a649c83b5
ms.lasthandoff: 02/17/2017


---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20-preview"></a>Administración de usuarios, SSH, y comprobación o reparación de discos en máquinas virtuales Linux con la extensión VMAccess y la CLI de Azure 2.0 (versión preliminar)
El disco de la máquina virtual de Linux muestra errores. De alguna forma, restableció la contraseña raíz de la máquina virtual de Linux o eliminó por accidente la clave privada SSH. Si esto sucedió en el centro de datos, deberá ir ahí y, luego, abrir el conmutador KVM para llegar a la consola del servidor. Piense en la extensión VMAccess de Azure como ese conmutador KVM que le permite tener acceso a la consola para restablecer el acceso a Linux o realizar el mantenimiento de nivel de disco.

En este artículo se muestra cómo usar la extensión VMAccess de Azure para comprobar o reparar un disco, restablecer el acceso de usuario, administrar cuentas de usuario o restablecer la configuración de SSHD en Linux.


## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](virtual-machines-linux-using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](#ways-to-use-the-vmaccess-extension): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo)


## <a name="ways-to-use-the-vmaccess-extension"></a>Formas de usar la extensión VMAccess
Hay dos formas de usar la extensión VMAccess en las máquinas virtuales Linux:

* Usando la CLI de Azure 2.0 (versión preliminar) y los parámetros obligatorios
* [Usando archivos sin formato JSON que procese la extensión VMAccess](#use-json-files-and-the-vmaccess-extension) para realizar acciones en ellos

Los ejemplos siguientes emplean [az vm access](/cli/azure/vm/access) con los parámetros adecuados. Para realizar estos pasos, necesita tener instalada la [CLI de Azure 2.0 (versión preliminar)](/cli/azure/install-az-cli2) más reciente y haber iniciado sesión en una cuenta de Azure mediante [az login](/cli/azure/#login).

## <a name="reset-ssh-key"></a>Restablecimiento de la clave SSH
En el ejemplo siguiente se restablece la clave SSH del usuario `azureuser` en la máquina virtual denominada "`myVM`":

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Restablecer contraseña
En el ejemplo siguiente se restablece la contraseña del usuario `azureuser` en la máquina virtual denominada "`myVM`":

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="reset-sshd"></a>Restablecer SSHD
En el ejemplo siguiente se restablece la configuración SSHD en una máquina virtual denominada "`myVM`":

```azurecli
az vm access reset-linux-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-a-user"></a>Creación de un usuario
En el ejemplo siguiente se crea un usuario denominado "`myNewUser`" con una clave SSH para realizar la autenticación en la máquina virtual denominada "`myVM`":

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="deletes-a-user"></a>Eliminación de un usuario
En el ejemplo siguiente se elimina un usuario denominado "`myNewUser`" en la máquina virtual llamada "`myVM`":

```azurecli
az vm access delete-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Uso de archivos JSON y de la extensión VMAccess
En los ejemplos siguientes se emplean archivos sin formato JSON. Use [az vm extension set](/cli/azure/vm/extension#set) y, luego, llame a los archivos JSON. Estos archivos JSON también se pueden llamar desde las plantillas de Azure. 

### <a name="reset-user-access"></a>Restablecimiento del acceso de un usuario
Si perdió el acceso a la raíz de la máquina virtual Linux, puede iniciar un script de VMAccess para restablecer la contraseña de un usuario.

Para restablecer la clave SSH de un usuario, cree un archivo denominado "`reset_ssh_key.json`" y agregue el siguiente contenido:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Ejecute el script VMAccess con:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Para restablecer la contraseña de un usuario, cree un archivo denominado "`reset_user_password.json`" y agregue el siguiente contenido:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Ejecute el script VMAccess con:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="reset-ssh"></a>Restablecimiento de SSH
Si realiza cambios en la configuración SSHD de máquinas virtuales de Linux y cerrar la conexión SSH antes de comprobar los cambios, es posible que se le impida volver a realizar la conexión SSH.  VMAccess se puede usar para restablecer la configuración SSHD nuevamente a una buena configuración conocida sin tener que iniciar sesión a través de SSH.

Para restablecer la configuración SSHD, cree un archivo denominado "`reset_sshd.json`" y agregue el siguiente contenido:

```json
{
  "reset_ssh": true
}
```

Ejecute el script VMAccess con:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-users"></a>Administrar usuarios
VMAccess es un script de Python que se puede usar para administrar usuarios en la máquina virtual de Linux sin iniciar sesión y mediante la cuenta raíz o sudo.

Para crear un usuario, cree un archivo denominado "`create_new_user.json`" y agregue el siguiente contenido:

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Ejecute el script VMAccess con:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Para eliminar un usuario, cree un archivo denominado"`delete_user.json`" y agregue el siguiente contenido:

```json
{
  "remove_user":"myDeleteUser"
}
```

Ejecute el script VMAccess con:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Comprobación o reparación del disco
Con VMAccess, puede ejecutar fsck en el disco de la máquina virtual de Linux. También puede hacer una comprobación y reparación de disco con VMAccess.

Para comprobar y luego reparar el disco que usa este script de VMAccess, cree un archivo denominado "`disk_check_repair.json`" y agregue el siguiente contenido:

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Ejecute el script VMAccess con:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>Pasos siguientes
Actualizar Linux con la extensión VMAccess de Azure constituye un método para hacer cambios en una máquina virtual Linux que esté ejecutándose. También puede usar herramientas como cloud-init y las plantillas de Azure Resource Manager para modificar la máquina virtual Linux en el arranque.

[Acerca de las características y extensiones de las máquinas virtuales](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Creación de plantillas de Azure Resource Manager con extensiones de máquina virtual de Linux](virtual-machines-linux-extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Uso de cloud-init para personalizar una VM de Linux durante la creación](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



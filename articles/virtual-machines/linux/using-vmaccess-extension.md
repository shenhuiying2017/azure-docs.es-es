---
title: "Restablecimiento del acceso a una máquina virtual Linux de Azure | Microsoft Docs"
description: "Administración de usuarios administrativos y restablecer el acceso en máquinas virtuales Linux con la extensión VMAccess y la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 5fb3941e0b55f8b5d79c9fc794ec984e074caafe
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Administración de usuarios administrativos, SSH y comprobación o reparación de discos en máquinas virtuales Linux con la extensión VMAccess y la CLI de Azure 2.0
El disco de la máquina virtual de Linux muestra errores. De alguna forma, restableció la contraseña raíz de la máquina virtual de Linux o eliminó por accidente la clave privada SSH. Si esto sucedió en el centro de datos, deberá ir ahí y, luego, abrir el conmutador KVM para llegar a la consola del servidor. Piense en la extensión VMAccess de Azure como ese conmutador KVM que le permite tener acceso a la consola para restablecer el acceso a Linux o realizar el mantenimiento de nivel de disco.

En este artículo se muestra cómo usar la extensión VMAccess de Azure para comprobar o reparar un disco, restablecer el acceso de usuarios administrativos, administrar cuentas de usuario o restablecer la configuración de SSH en Linux. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Formas de usar la extensión VMAccess
Hay dos formas de usar la extensión VMAccess en las máquinas virtuales Linux:

* Usando la CLI de Azure 2.0 y los parámetros necesarios
* [Usando archivos sin formato JSON que procese la extensión VMAccess](#use-json-files-and-the-vmaccess-extension) para realizar acciones en ellos

En los ejemplos siguientes se utilizan comandos [az vm user](/cli/azure/vm/user). Para realizar estos pasos, necesita tener instalada la [CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente y haber iniciado sesión en una cuenta de Azure mediante [az login](/cli/azure/#az_login).

## <a name="reset-ssh-key"></a>Restablecimiento de la clave SSH
En el ejemplo siguiente se restablece la clave SSH del usuario `azureuser` en la máquina virtual denominada "`myVM`":

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Restablecimiento de contraseña
En el ejemplo siguiente se restablece la contraseña del usuario `azureuser` en la máquina virtual denominada "`myVM`":

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Reinicio de SSH
En el ejemplo siguiente se reinicia el demonio de SSH y se restablece la configuración de SSH a los valores predeterminados en una máquina virtual denominada "`myVM`":

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Creación de un usuario administrativo/sudo
En el ejemplo siguiente se crea un usuario denominado `myNewUser` con permisos **sudo**. La cuenta utiliza una clave SSH para la autenticación en la máquina virtual denominada `myVM`. Este método está diseñado para ayudarle a recuperar el acceso a una máquina virtual en caso de que las credenciales actuales se pierdan o se olviden. Se recomienda limitar las cuentas con permisos **sudo**.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>Eliminar un usuario
En el ejemplo siguiente se elimina un usuario denominado "`myNewUser`" en la máquina virtual llamada "`myVM`":

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Uso de archivos JSON y de la extensión VMAccess
En los ejemplos siguientes se emplean archivos sin formato JSON. Use [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) y, luego, llame a los archivos JSON. Estos archivos JSON también se pueden llamar desde las plantillas de Azure. 

### <a name="reset-user-access"></a>Restablecimiento del acceso de un usuario
Si perdió el acceso a la raíz de la máquina virtual Linux, puede iniciar un script de VMAccess para restablecer la contraseña o la clave SSH de un usuario.

Para restablecer la clave pública SSH de un usuario, cree un archivo denominado "`reset_ssh_key.json`" y agregue la configuración en el siguiente formato. Sustituya sus propios valores por los parámetros `username` y `ssh_key`:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
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

Para restablecer la contraseña de un usuario, cree un archivo denominado "`reset_user_password.json`" y agregue la configuración en el siguiente formato. Sustituya sus propios valores por los parámetros `username` y `password`:

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

### <a name="restart-ssh"></a>Reinicio de SSH
Para reiniciar el demonio de SSH y restablecer la configuración de SSH a los valores predeterminados, cree un archivo denominado "`reset_sshd.json`". Agregue el siguiente contenido:

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

### <a name="manage-administrative-users"></a>Administración de usuarios administrativos

Con el objetivo de crear un usuario con permisos **sudo** que utilice una clave SSH para la autenticación, cree un archivo denominado `create_new_user.json` y agregue la configuración en el siguiente formato. Sustituya sus propios valores por los parámetros `username` y `ssh_key`. Este método está diseñado para ayudarle a recuperar el acceso a una máquina virtual en caso de que las credenciales actuales se pierdan o se olviden. Se recomienda limitar las cuentas con permisos **sudo**.

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

Para eliminar un usuario, cree un archivo denominado"`delete_user.json`" y agregue el siguiente contenido. Sustituya su propio valor para el parámetro `remove_user`:

```json
{
  "remove_user":"myNewUser"
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
Al utilizar VMAccess, también se puede comprobar y reparar un disco que haya agregado a la VM con Linux.

Para comprobar y reparar después el disco, cree un archivo denominado "`disk_check_repair.json`" y agregue la configuración en el siguiente formato. Sustituya su propio valor para el nombre de `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
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

## <a name="next-steps"></a>pasos siguientes
Actualizar Linux con la extensión VMAccess de Azure constituye un método para hacer cambios en una máquina virtual Linux que esté ejecutándose. También puede usar herramientas como cloud-init y las plantillas de Azure Resource Manager para modificar la máquina virtual Linux en el arranque.

[Características y extensiones de las máquinas virtuales para Linux](extensions-features.md)

[Creación de plantillas de Azure Resource Manager con extensiones de máquina virtual de Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Uso de cloud-init para personalizar una VM de Linux durante la creación](using-cloud-init.md)


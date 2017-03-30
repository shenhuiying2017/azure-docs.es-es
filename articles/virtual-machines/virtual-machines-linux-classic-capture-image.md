---
title: "Captura de una imagen de una máquina virtual Linux | Microsoft Docs"
description: "Obtenga información acerca de cómo capturar una imagen de una máquina virtual de Azure (VM) basada en Linux creada con el modelo de implementación clásico."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 59a88a9f2db745db22007f6b528f8d41016bde16
ms.lasthandoff: 03/21/2017


---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Captura de una máquina virtual clásica con Linux para usarla como imagen
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Obtenga información sobre cómo [realizar estos pasos con el modelo de Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

En este artículo se muestra cómo puede capturar una máquina virtual (VM) clásica de Azure con Linux para usarla como imagen en la creación de otras máquinas virtuales. Esta imagen incluye el disco del sistema operativo y cualquier otro disco de datos conectado a la VM. No incluye la configuración de redes, por lo que debe configurarla usted mismo al crear la otra VM a partir de la imagen.

Azure almacena la imagen en **Images**(Imágenes), junto con las demás que haya cargado. Para obtener más información sobre las imágenes, consulte [Acerca de las imágenes para las máquinas virtuales Linux][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Antes de empezar
En estos pasos se supone que ya ha creado un VM de Azure con el modelo de implementación clásica y que ha configurado el sistema operativo, lo que incluye adjuntar cualquier disco de datos. Si tiene que crear una VM, lea [Creación de una máquina virtual con Linux personalizada][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Captura de la máquina virtual
1. [Conéctese a la VM](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mediante un cliente SSH que elija.
2. En la ventana SSH, escriba el siguiente comando. El resultado de `waagent` puede variar ligeramente según la versión de esta utilidad:

    ```bash
    sudo waagent -deprovision+user
    ```

    El comando anterior intenta limpiar el sistema y dejarlo adecuado para un reaprovisionamiento. Esta operación ejecuta las siguientes tareas:

   * Quita las claves de host de SSH (si Provisioning.RegenerateSshHostKeyPair es "y" en el archivo de configuración)
   * Borra la configuración de Nameserver en /etc/resolv.conf
   * Quita la contraseña del usuario `root` desde /etc/shadow (si Provisioning.DeleteRootPassword es "y" en el archivo de configuración)
   * Quita concesiones del cliente de DHCP en caché
   * Restablece el nombre de host a localhost.localdomain
   * Elimina la última cuenta de usuario aprovisionada (obtenida desde /var/lib/waagent) **y los datos asociados**.

     > [!NOTE]
     > El desaprovisionamiento elimina los archivos y los datos para "generalizar" la imagen. Ejecutar este comando solamente en una VM que quiera capturar como nueva plantilla de imagen. No garantiza que se haya borrado toda información sensible de la imagen o que sea adecuada para su redistribución a terceros.

3. Escriba **y** para continuar. Puede agregar el parámetro `-force` para evitar este paso de confirmación.
4. Escriba **Salir** para cerrar el cliente SSH.

   > [!NOTE]
   > En los demás pasos se supone que ya ha [instalado la CLI de Azure](../cli-install-nodejs.md) en el equipo cliente. Todos los pasos siguientes también pueden realizarse en el [Portal de Azure clásico][Azure classic portal].

5. Desde el equipo cliente, abra la CLI de Azure e inicie sesión en su suscripción de Azure. Para detalles, lea [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).
6. Asegúrese de que está en modo de administración de servicios:

    ```azurecli
    azure config mode asm
    ```

7. Apague la VM que ya está desaprovisionada. En el ejemplo siguiente se apaga la VM denominada `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```

   > [!NOTE]
   > Puede ver una lista de todas las VM creadas en su suscripción mediante `azure vm list`.

8. Cuando la VM se detenga, capture la imagen. En el ejemplo siguiente se captura la VM denominada `myVM` y se crea una imagen generalizada denominada `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    El subcomando `-t` elimina la máquina virtual original.

9. La nueva imagen ahora está disponible en la lista de imágenes que pueden usarse para configurar nuevas VM. Puede visualizarla mediante el comando:

   ```azurecli
   azure vm image list
   ```

   En [Azure Portal](http://portal.azure.com), la nueva imagen aparece en las **imágenes de máquina virtual (clásicas)** que pertenece a los servicios de **Proceso**. Puede tener acceso a **imágenes de máquina virtual (clásicas)** haciendo clic en _Más servicios_en la parte inferior de la lista de servicios de Azure y, a continuación, busque los servicios de **Proceso**.   

   ![Captura correcta de la imagen](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Pasos siguientes
La imagen está lista para usarse para crear VM. Puede usar el comando de la CLI de Azure `azure vm create` y proporcionar el nombre de la imagen que ha creado. Para más información, consulte [Comandos CLI de Azure en modo de Administración de servicios de Azure (asm)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

También puede usar el [Portal de Azure clásico][Azure classic portal] para crear una VM personalizada mediante el método **De la galería** y seleccionar la imagen que ha creado. Para más información, consulte [Creación de una máquina virtual con Linux personalizada][How to Create a Custom Virtual Machine].

**Consulte también:** [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[Azure classic portal]: http://manage.windowsazure.com
[About Virtual Machine Images in Azure]: virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]:windows/classic/attach-disk.md
[How to Create a Linux Virtual Machine]: virtual-machines-linux-classic-create-custom.md


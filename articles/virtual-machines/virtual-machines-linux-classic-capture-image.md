---
title: Captura de una imagen de una máquina virtual de Linux | Microsoft Docs
description: Obtenga información acerca de cómo capturar una imagen de una máquina virtual de Azure (VM) basada en Linux creada con el modelo de implementación clásico.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: iainfou

---
# Captura de una máquina virtual clásica con Linux para usarla como imagen
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Obtenga información acerca de cómo [realizar estos pasos con el modelo de Resource Manager](virtual-machines-linux-capture-image.md).

En este artículo se muestra cómo puede capturar una máquina virtual clásica de Azure con Linux para usarla como imagen en la creación de otras máquinas virtuales. Esta imagen incluye el disco del sistema operativo y cualquier otro disco de datos conectado a la máquina virtual. No incluye la configuración de redes, por lo que debe configurarla usted mismo al crear las otras máquinas virtuales a partir de la imagen.

Azure almacena la imagen en **Images** (Imágenes), junto con las demás que haya cargado. Para obtener más información sobre las imágenes, consulte [Acerca de las imágenes de máquina virtual en Azure][Acerca de las imágenes de máquina virtual en Azure].

## Antes de empezar
En estos pasos se supone que ya ha creado un máquina virtual de Azure con el modelo de implementación clásica y que ha configurado el sistema operativo, lo que incluye adjuntar cualquier disco de datos. Si tiene que crear una máquina virtual, lea [Creación de una máquina virtual Linux personalizada][Creación de una máquina virtual Linux personalizada].

## Captura de la máquina virtual
1. [Conéctese a la máquina virtual](virtual-machines-linux-mac-create-ssh-keys.md) mediante un cliente SSH de su elección.
2. En la ventana SSH, escriba el siguiente comando. El resultado de `waagent` puede variar ligeramente según la versión de esta utilidad:
   
    `sudo waagent -deprovision+user`
   
    Este comando intenta limpiar el sistema y dejarlo adecuado para un reaprovisionamiento. Esta operación ejecuta las siguientes tareas:
   
   * Quita las claves de host de SSH (si Provisioning.RegenerateSshHostKeyPair es "y" en el archivo de configuración)
   * Borra la configuración de Nameserver en /etc/resolv.conf
   * Quita la contraseña del usuario `root` desde /etc/shadow (si Provisioning.DeleteRootPassword es "y" en el archivo de configuración)
   * Quita concesiones del cliente de DHCP en caché
   * Restablece el nombre de host a localhost.localdomain
   * Elimina la última cuenta de usuario aprovisionada (obtenida desde /var/lib/waagent) **y los datos asociados**.
     
     > [!NOTE]
     > El desaprovisionamiento elimina los archivos y los datos para "generalizar" la imagen. Solo puede ejecutar este comando en máquinas virtuales que quiera capturar como nueva plantilla de imagen. No garantiza que se haya borrado toda información sensible de la imagen o que sea adecuada para su redistribución a terceros.
     > 
     > 
3. Escriba **y** para continuar. Puede agregar el parámetro `-force` para evitar este paso de confirmación.
4. Escriba **Salir** para cerrar el cliente SSH.
   
   > [!NOTE]
   > En los demás pasos se supone que ya ha [instalado la CLI de Azure](../xplat-cli-install.md) en el equipo cliente. Todos los pasos siguientes también pueden realizarse en el [Portal de Azure clásico][Portal de Azure clásico].
   > 
   > 
5. Desde el equipo cliente, abra la CLI de Azure e inicie sesión en su suscripción de Azure. Para detalles, lea [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).
6. Asegúrese de que está en modo de administración de servicios:
   
    `azure config mode asm`
7. Apague la máquina virtual desaprovisionada en los pasos anteriores mediante:
   
    `azure vm shutdown <your-virtual-machine-name>`
   
   > [!NOTE]
   > Puede encontrar todas las máquinas virtuales creadas en su suscripción mediante `azure vm list`
   > 
   > 
8. Cuando se detenga la máquina virtual, capture la imagen con el comando:
   
    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`
   
    Escriba el nombre de la imagen que desee en lugar de *nuevo-nombre-imagen*. Este comando crea una imagen de sistema operativo generalizada. El subcomando `-t` elimina la máquina virtual original.
9. La nueva imagen ahora está disponible en la lista de imágenes que pueden usarse para configurar nuevas máquinas virtuales. Puede visualizarla mediante el comando:
   
   `azure vm image list`
   
   En el [Portal de Azure clásico][Portal de Azure clásico], aparecerá en la lista **IMAGES** (IMÁGENES).
   
   ![Captura correcta de la imagen](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)

## Pasos siguientes
La imagen está lista para usarse para crear máquinas virtuales. Puede usar el comando de la CLI de Azure `azure vm create` y proporcionar el nombre de la imagen que ha creado. Para más información acerca del comando, consulte el artículo sobre el [uso de la CLI de Azure con el modelo de implementación clásico](../virtual-machines-command-line-tools.md). También puede usar el [Portal de Azure clásico][Portal de Azure clásico] para crear una máquina virtual personalizada mediante el método **De la galería** y seleccionar la imagen que ha creado. Vea [Creación de una máquina virtual personalizada][Creación de una máquina virtual personalizada] para obtener más detalles.

**Consulte también:**[Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md)

[Portal de Azure clásico]: http://manage.windowsazure.com
[Acerca de las imágenes de máquina virtual en Azure]: virtual-machines-linux-classic-about-images.md
[Creación de una máquina virtual personalizada]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Creación de una máquina virtual Linux personalizada]: virtual-machines-linux-classic-create-custom.md

<!---HONumber=AcomDC_0907_2016-->
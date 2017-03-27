---
title: "Inicio rápido de Azure: creación de máquinas virtuales con el Portal | Microsoft Docs"
description: "Inicio rápido de Azure: creación de máquinas virtuales con el Portal"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 348407b57bbb3329d7d27a6f38623e052aecc58b
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Creación de máquinas virtuales Linux con Azure Portal

Las máquinas virtuales de Azure pueden crearse a través de Azure Portal. Este método proporciona una interfaz de usuario basada en el explorador para crear y configurar máquinas virtuales y todos los recursos de Azure asociados.

Antes de empezar, necesita una clave SSH pública y privada. Si quiere obtener información detallada sobre cómo crear claves SSH para Azure, consulte [este artículo](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-virtual-machine"></a>Create virtual machine

2. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

3. Seleccione **Proceso** en la hoja **Nuevo**, seleccione **Ubuntu Server 16.04 LTS** en la hoja **Proceso** y, luego, haga clic en el botón **Crear**.

4. Rellene el formulario **Datos básicos** de la máquina virtual. En **Tipo de autenticación**, se recomienda SSH. Al pegar la **clave SSH pública**, tenga cuidado de no quitar los espacios en blanco finales o iniciales. En **Grupo de recursos**, cree uno. Un grupo de recursos es un contenedor lógico en el que se administran y crean los recursos de Azure. Cuando haya terminado, haga clic en **Aceptar**.

    ![Especificación de la información básica de la máquina virtual en la hoja del Portal](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

5. Elija un tamaño de máquina virtual y haga clic en **Seleccionar**. 

    ![Selección de un tamaño para la máquina virtual en la hoja del Portal](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

6. En la hoja Configuración, seleccione **Sí** en **Usar discos administrados**, conserve los valores predeterminados en el resto de la configuración y haga clic en **Aceptar**.

7. En la página Resumen, haga clic en **Aceptar** para iniciar la implementación de máquina virtual.

## <a name="connect-to-virtual-machine"></a>Conexión a la máquina virtual

Una vez finalizada la implementación, cree una conexión SSH con la máquina virtual.

1. Haga clic en la máquina virtual. Encontrará la máquina virtual en la pantalla principal de Azure Portal, o bien seleccionando **Máquinas virtuales** en el menú izquierdo.

2. Haga clic en el botón **Conectar** . El botón Conectar muestra una cadena de conexión SSH que se puede usar para conectarse a la máquina virtual.

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. Ejecute el comando siguiente para crear una sesión SSH. Reemplace la cadena de conexión con la que copió desde Azure Portal.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Eliminación de máquinas virtuales

Cuando ya no los necesite, elimine el grupo de recursos, la máquina virtual y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la hoja de la máquina virtual y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

[Tutorial de creación de máquinas virtuales de alta disponibilidad](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Ejemplos de la CLI de implementación de máquinas virtuales](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


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
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 15d4f81f8a7a835ea61a9056d83d8337d529c7cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Creación de máquinas virtuales Linux con Azure Portal

Las máquinas virtuales de Azure pueden crearse a través de Azure Portal. Este método proporciona una interfaz de usuario basada en el explorador para crear y configurar máquinas virtuales y todos los recursos asociados. Esta guía de inicio rápido le lleva paso a paso por la creación de una máquina virtual y la instalación de un servidor web en ella.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-ssh-key-pair"></a>Creación del par de claves SSH

Necesita un par de claves SSH para completar este inicio rápido. Si ya tiene un par de claves SSH, puede omitir este paso.

Desde un shell de Bash, ejecute este comando y siga las instrucciones en pantalla. La salida del comando incluye el nombre del archivo de clave pública. Copie el contenido del archivo de clave pública en el Portapapeles.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-virtual-machine"></a>Create virtual machine

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Seleccione **Compute**y, después, seleccione **Ubuntu Server 16.04 LTS**. 

3. Escriba la información de la máquina virtual. En **Tipo de autenticación**, seleccione **Clave pública SSH**. Al pegar la clave pública SSH, tenga cuidado de no quitar los espacios en blanco finales o iniciales. Cuando haya terminado, haga clic en **Aceptar**.

    ![Especificación de la información básica de la máquina virtual en la hoja del Portal](./media/quick-create-portal/create-vm-portal-basic-blade.png)

4. Seleccione un tamaño para la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). 

    ![Captura de pantalla que muestra los tamaños de máquina virtual](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. En **Configuración**, conserve los valores predeterminados y haga clic en **Aceptar**.

6. En la página Resumen, haga clic en **Aceptar** para iniciar la implementación de máquina virtual.

7. La máquina virtual se anclará al panel de Azure Portal. Una vez completada la implementación, se abrirá automáticamente el resumen de la máquina virtual.


## <a name="connect-to-virtual-machine"></a>Conexión a la máquina virtual

Cree una conexión SSH con la máquina virtual.

1. Haga clic en el botón **Conectar** en las propiedades de la máquina virtual. El botón Conectar muestra una cadena de conexión SSH que se puede usar para conectarse a la máquina virtual.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Ejecute el comando siguiente para crear una sesión SSH. Reemplace la cadena de conexión con la que copió desde Azure Portal.

```bash 
ssh azureuser@40.112.21.50
```

## <a name="install-nginx"></a>Instalación de NGINX

Use el siguiente script de bash para actualizar los orígenes de paquetes e instalar el paquete NGINX más reciente. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Cuando haya finalizado, salga de la sesión de SSH y vuelva a las propiedades de máquina virtual en Azure Portal.


## <a name="open-port-80-for-web-traffic"></a>Apertura del puerto 80 para el tráfico web 

Los grupos de seguridad de red (NSG) protegen el tráfico entrante y saliente. Cuando se crea una máquina virtual desde Azure Portal, se crea una regla de entrada en el puerto 22 para las conexiones SSH. Dado que esta máquina virtual hospeda un servidor web, es preciso crear una regla de NSG para el puerto 80.

1. En la máquina virtual, haga clic en el nombre del **grupo de recursos**.
2. Seleccione el **grupo de seguridad de red**. Los NSG pueden identificarse mediante la columna **Tipo**. 
3. En el menú de la izquierda, en Configuración, haga clic en **Reglas de seguridad de entrada**.
4. Haga clic en **Agregar**.
5. En **Nombre**, escriba **http**. Asegúrese de que **Intervalo de puertos** esté establecido en 80 y **Acción** esté establecido en **Permitir**. 
6. Haga clic en **Aceptar**.


## <a name="view-the-nginx-welcome-page"></a>Visualización de la página de bienvenida de NGINX

Con NGINX instalado y el puerto 80 abierto para la máquina virtual, se puede acceder ahora al servidor web desde Internet. Abra un explorador web y escriba la dirección IP pública de la máquina virtual. La dirección IP pública puede encontrarse en las propiedades de la máquina virtual en Azure Portal.

![Sitio predeterminado de NGINX](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, elimine el grupo de recursos, la máquina virtual y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la máquina virtual y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado una máquina virtual simple y una regla de grupo de seguridad de red, y ha instalado un servidor web. Para más información acerca de las máquinas virtuales de Azure, continúe con el tutorial de máquinas virtuales Linux.

> [!div class="nextstepaction"]
> [Tutoriales de máquinas virtuales Linux de Azure](./tutorial-manage-vm.md)

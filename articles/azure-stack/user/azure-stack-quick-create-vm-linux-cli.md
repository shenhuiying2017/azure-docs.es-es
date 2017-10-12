---
title: "Creación de una máquina virtual Linux con la CLI de Azure en Azure Stack | Microsoft Docs"
description: "Creación de una máquina virtual Linux con la CLI en Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: de2ff697c083493b43ab0d1b5bcde532c28684e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Creación de una máquina virtual Linux con la CLI de Azure en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

La CLI de Azure se usa para crear y administrar recursos de Azure Stack desde la línea de comandos. Esta guía de inicio rápido detalla el uso de la CLI de Azure para crear una máquina virtual de Linux en Azure Stack.  Una vez creada la VM, se instala un servidor web y se abre el puerto 80 para permitir el tráfico web.

## <a name="prerequisites"></a>Requisitos previos 

* Asegúrese de que el operador de Azure Stack haya agregado la imagen "Ubuntu Server 16.04 LTS" a Marketplace de Azure Stack. 

* Azure Stack requiere una versión específica de la CLI de Azure para crear y administrar los recursos. Si no tiene la CLI de Azure configurada para Azure Stack, inicie sesión en el [kit de desarrollo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o en un cliente externo basado en Windows si está [conectado a través de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn), y siga los pasos para [instalar y configurar la CLI de Azure](azure-stack-connect-cli.md).

* Se debe crear una clave SSH pública con el nombre id_rsa.pub en el directorio .ssh del perfil de usuario de Windows. Para más información sobre la creación de claves SSH, consulte [Creación de claves SSH en Windows](../../virtual-machines/linux/ssh-from-windows.md). 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure Stack. Desde el kit de desarrollo o el sistema integrado de Azure Stack, ejecute el comando [az group create](/cli/azure/group#create) para crear un grupo de recursos. Hemos asignado los valores para todas las variables en este documento, puede usarlas tal cual o asignar un valor diferente. En el ejemplo siguiente, se crea un grupo de recursos denominado myResourceGroup en la ubicación local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>de una máquina virtual

Cree una máquina virtual con el comando [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada myVM. En este ejemplo se usa Demouser como el nombre de usuario administrativo y Demouser@123 como la contraseña. Actualice estos valores a un valor apropiado para su entorno. Estos valores son necesarios al conectarse a la máquina virtual.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Una vez que haya finalizado, el comando generará parámetros para la máquina virtual.  Anote el valor de *PublicIPAddress*, ya que se utiliza para conectarse y administrar la máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Apertura del puerto 80 para el tráfico web

De forma predeterminada, solo se permiten conexiones mediante SSH con las máquinas virtuales Linux implementadas en Azure. Si esta máquina virtual va a ser un servidor web, debe abrir el puerto 80 desde Internet. Use el comando [az vm open-port](/cli/azure/vm#open-port) para abrir el puerto deseado.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Conexión SSH con la máquina virtual

En un sistema con SSH instalado, usa el comando siguiente para conectarse a la máquina virtual. Si trabaja en Windows, puede usar [Putty](http://www.putty.org/) para crear la conexión. Asegúrese de reemplazar por la dirección IP pública correcta de la máquina virtual. En el ejemplo anterior, la dirección IP era 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Instalación de NGINX

Use el siguiente script de bash para actualizar los orígenes de paquetes e instalar el paquete NGINX más reciente. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visualización de la página de bienvenida de NGINX

Con NGINX instalado y el puerto 80 abierto en la máquina virtual desde Internet, puede usar el explorador web que elija para ver la página principal de NGINX. Asegúrese de utilizar el valor de *publicIpAddress* que ha anotado antes para visitar la página predeterminada. 

![Sitio predeterminado de NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado una máquina virtual Linux sencilla. Para aprender más sobre las máquina virtuales de Azure Stack, continúe con el artículo [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Consideraciones acerca de máquinas virtuales de Azure Stack).


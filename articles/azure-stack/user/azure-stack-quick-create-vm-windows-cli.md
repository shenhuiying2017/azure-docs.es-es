---
title: "Creación de una máquina virtual Windows en Azure Stack con la CLI de Azure | Microsoft Docs"
description: "Aprenda a crear una máquina virtual Windows en Azure Stack con la CLI de Azure."
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
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 3a4d6f23bd8824636b3babe208add92db6aab537
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Creación de una máquina virtual Windows en Azure Stack con la CLI de Azure

La CLI de Azure se usa para crear y administrar recursos de Azure Stack desde la línea de comandos. En esta guía se detalla cómo usar la CLI de Azure para crear una máquina virtual Windows Server 2016 en Azure Stack. Después de crear la máquina virtual, se conectará con Escritorio remoto, instalará IIS y luego verá el sitio web predeterminado. 

Antes de comenzar, asegúrese de que el operador de Azure Stack haya agregado la imagen "Windows Server 2016" a Marketplace de Azure Stack.  

Azure Stack requiere una versión específica de la CLI de Azure para crear y administrar los recursos. Si no tiene la CLI de Azure configurada para Azure Stack, siga los pasos que se indican para [instalar y configurarla](azure-stack-connect-cli.md).


## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure Stack. Para crear un grupo de recursos, use el comando [az group create](/cli/azure/group#create). Hemos asignado los valores para todas las variables en este documento, puede usarlas tal cual o asignar un valor diferente. En el ejemplo siguiente, se crea un grupo de recursos denominado myResourceGroup en la ubicación local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>de una máquina virtual

Cree una máquina virtual con el comando [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada myVM. En este ejemplo se usa Demouser como el nombre de usuario administrativo y Demouser@123 como la contraseña. Actualice estos valores a un valor apropiado para su entorno. Estos valores son necesarios al conectarse a la máquina virtual.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Una vez creada la máquina virtual, anote el parámetro *PublicIPAddress* que se genera, ya que lo usará para acceder a ella.
 
## <a name="open-port-80-for-web-traffic"></a>Apertura del puerto 80 para el tráfico web

De forma predeterminada, solo se permiten conexiones RDP con las máquinas virtuales Windows implementadas en Azure Stack. Si esta máquina virtual va a ser un servidor web, debe abrir el puerto 80 desde Internet. Use el comando [az vm open-port](/cli/azure/vm#open-port) para abrir el puerto deseado.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Conexión a la máquina virtual

Ejecute el comando siguiente para crear una sesión del Escritorio remoto con la máquina virtual. Reemplace la dirección IP con la dirección IP pública de la máquina virtual. Cuando se le solicite, escriba las credenciales usadas al crear la máquina virtual.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalación de IIS mediante PowerShell

Ahora que ha iniciado sesión en la máquina virtual de Azure, puede usar una sola línea de PowerShell para instalar IIS y habilitar la regla de firewall local para permitir el tráfico web. Abra una ventana de PowerShell y ejecute el siguiente comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Página principal de IIS

Con IIS instalado y el puerto 80 abierto en la máquina virtual desde Internet, puede usar el explorador web que elija para ver la página principal de IIS. Asegúrese de utilizar la dirección IP pública que ha anotado antes para visitar la página predeterminada. 

![Sitio predeterminado de IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

[Creación de una máquina virtual mediante una contraseña almacenada en el almacén de claves](azure-stack-kv-deploy-vm-with-secret.md)

[Más información sobre Storage en Azure Stack](azure-stack-storage-overview.md)


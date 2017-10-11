---
title: "Administración de nodos de proceso del clúster de HPC Pack | Microsoft Docs"
description: "Obtenga información sobre las herramientas de script de PowerShell para agregar, quitar, iniciar y detener los nodos de proceso del clúster de HPC Pack 2012 R2 en Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: dc9f354191b9e80ff6a01bd401a874c6998bda79
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Administrar el número y la disponibilidad de los nodos de ejecución en un clúster de HPC Pack en Azure
Si creó un clúster de HPC Pack 2012 R2 en las máquinas virtuales de Azure, puede que desee agregar, quitar, iniciar (aprovisionar) o detener (desaprovisionar) fácilmente algunas máquinas virtuales de nodo de ejecución en el clúster. Para realizar estas tareas, ejecute los scripts de Azure PowerShell que están instalados en el nodo principal de la máquina virtual. Estos scripts le ayudan a controlar el número y la disponibilidad de los recursos de clúster de HPC Pack para que pueda controlar los costos.

> [!IMPORTANT] 
> La información de este artículo solo se aplica a los clústeres de HPC Pack 2012 R2 en Azure que se hayan creado con el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.
> Además, los scripts de PowerShell que se describen en este artículo no están disponibles en HPC Pack 2016.

## <a name="prerequisites"></a>Requisitos previos
* **Clúster de HPC Pack 2012 R2 en máquinas virtuales de Azure**: cree un clúster de HPC Pack 2012 R2 en el modelo de implementación clásico. Por ejemplo, puede automatizar la implementación mediante la imagen de la máquina virtual de HPC Pack 2012 R2 en Azure Marketplace y un script de Azure PowerShell. Para más información y ver los requisitos previos, consulte [Creación de un clúster de HPC con el script de implementación de HPC Pack IaaS](hpcpack-cluster-powershell-script.md).
  
    Después de la implementación, busque los scripts de administración de nodo en la carpeta % CCP\_HOME %bin en el nodo principal. Ejecute cada uno de estos scripts como administrador.
* **Archivo de configuración de publicación o certificado de administración de Azure**: debe llevar a cabo una de las siguientes acciones en el nodo principal:
  
  * **Importar el archivo de configuración de publicación de Azure**. Para ello, ejecute los siguientes cmdlets de Azure PowerShell en el nodo principal:
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Configure el certificado de administración de Azure en el nodo principal**. Si tiene el archivo .cer, impórtelo en el almacén CurrentUser\My certificate y luego ejecute el siguiente cmdlet de Azure PowerShell para su entorno de Azure (AzureCloud o AzureChinaCloud):
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Agregar máquinas virtuales de nodo de ejecución
Agregue nodos de ejecución con el script **Add-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintaxis
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>parameters
* **ServiceName**: nombre del servicio en la nube al que se agregan las nuevas máquinas virtuales de nodos de proceso.
* **ImageName**: nombre de la imagen de máquina virtual de Azure, que puede obtenerse mediante el Portal de Azure clásico o el cmdlet de Azure PowerShell **Get-AzureVMImage**. La imagen debe cumplir los siguientes requisitos:
  
  1. Debe haber instalado un sistema operativo Windows.
  2. HPC Pack debe estar instalado en el rol del nodo de ejecución.
  3. La imagen debe ser una imagen privada en la categoría de usuario, no una imagen de máquina virtual de Azure pública.
* **Quantity**: número de máquinas virtuales de nodo de ejecución que se van a agregar.
* **InstanceSize**: tamaño de las máquinas virtuales de nodos de proceso.
* **DomainUserName**: nombre de usuario de dominio, que se usa para unir las nuevas máquinas virtuales al dominio.
* **DomainUserPassword**: contraseña del usuario de dominio.
* **NodeNameSeries** (opcional): modelo de nomenclatura para los nodos de proceso. El formato debe ser &lt;*Raíz\_Nombre*&gt;&lt;*Inicio\_Número*&gt;%. Por ejemplo, MyCN%10% se refiere a una serie de nombres de nodos de ejecución a partir de MyCN11. Si no se especifica, el script usa la serie de nomenclatura de nodos configurados clúster de HPC.

### <a name="example"></a>Ejemplo
En el ejemplo siguiente se agregan 20 máquinas virtuales grandes de nodos de proceso de gran tamaño en el servicio en la nube *hpcservice1*, según la imagen de máquina virtual *hpccnimage1*.

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Quitar máquinas virtuales de nodo de ejecución
Quite nodos de ejecución con el script **Remove-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintaxis
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>parameters
* **Name**: nombres de los nodos de clúster que se van a quitar. Se admite caracteres comodín. El nombre del conjunto de parámetros es Name. No se pueden especificar los dos parámetros, **Name** y **Node**.
* **Node**: objeto HpcNode para los nodos que se van quitar, que se puede obtener a través del cmdlet de HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). El nombre del conjunto de parámetro es Node. No se pueden especificar los dos parámetros, **Name** y **Node**.
* **DeleteVHD** (opcional): configuración para eliminar los discos asociados para las máquinas virtuales que se quitan.
* **Force** (opcional): configuración para forzar nodos HPC sin conexión antes de quitarlos.
* **Confirm** (opcional): solicitud de confirmación antes de ejecutar el comando.
* **WhatIf**: configuración para describir lo que sucedería si ejecutara el comando sin ejecutarlo realmente.

### <a name="example"></a>Ejemplo
En el ejemplo siguiente se fuerzan los nodos sin conexión con nombres que comienzan por *HPCNode-CN-* y luego quita los nodos y sus discos asociados.

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Iniciar máquinas virtuales de nodos de ejecución
Inicie nodos de ejecución con el script **Start-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintaxis
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>parameters
* **Name**: nombres de los nodos de clúster que se van a iniciar. Se admite caracteres comodín. El nombre del conjunto de parámetros es Name. No se pueden especificar los dos parámetros, **Name** y **Node**.
* **Node**: objeto HpcNode para los nodos que se van a iniciar, que se puede obtener mediante el cmdlet de HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). El nombre del conjunto de parámetro es Node. No se pueden especificar los dos parámetros, **Name** y **Node**.

### <a name="example"></a>Ejemplo
En el ejemplo siguiente se inician nodos con nombres que comienzan por *HPCNode-CN-*.

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Detener máquinas virtuales de nodo de ejecución
Detenga los nodos de proceso con el script **Stop-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintaxis
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parámetros
* **Name**: nombres de los nodos de clúster que se van a detener. Se admite caracteres comodín. El nombre del conjunto de parámetros es Name. No se pueden especificar los dos parámetros, **Name** y **Node**.
* **Node**: objeto HpcNode para los nodos que se van a detener, que se puede obtener mediante el cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx)de HPC PowerShell. El nombre del conjunto de parámetro es Node. No se pueden especificar los dos parámetros, **Name** y **Node**.
* **Force** (opcional): configuración para forzar los nodos HPC sin conexión antes de detenerlos.

### <a name="example"></a>Ejemplo
En el ejemplo siguiente se fuerzan nodos sin conexión con nombres que comienzan por *HPCNode-CN-* y luego se detienen los nodos.

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>Pasos siguientes
* Para aumentar o reducir automáticamente los nodos de clúster según la carga de trabajo actual de los trabajos y las tareas en el clúster, consulte [Aumento y reducción automáticos de los recursos de clúster de HPC Pack en Azure según la carga de trabajo de clúster](hpcpack-cluster-node-autogrowshrink.md).


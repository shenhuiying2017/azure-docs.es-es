---
title: Montaje de Azure File Storage en una VM de Windows de Azure | Microsoft Docs
description: "Almacene archivos en la nube con Azure File Storage y monte un recurso compartido de archivos de nube en una máquina virtual (VM) de Azure."
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 8d537bdc882487784baef9f693e4677c76d3bd8d
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Uso de recursos compartidos de archivos de Azure con máquinas virtuales de Windows 

Puede usar recursos compartidos de archivos de Azure como una forma de almacenar archivos y acceder a ellos desde la VM. Por ejemplo, puede almacenar un script o un archivo de configuración de la aplicación que quiera que todas las máquinas virtuales compartan. En este artículo, se muestra cómo crear un recurso compartido de archivos de Azure y cómo cargar y descargar archivos.

## <a name="connect-to-a-file-share-from-a-vm"></a>Conexión a un recurso compartido desde una VM

En esta sección se da por supuesto que ya tiene un recurso compartido de archivos al que quiere conectarse. Si tiene que crear uno, vea [Creación de un recurso compartido de archivos](#create-a-file-share) más adelante en este artículo.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Cuentas de almacenamiento**.
3. Elija la cuenta de almacenamiento.
4. En la página **Información general**, en **Servicios**, seleccione **Archivos**.
5. Seleccione un recurso compartido de archivos o haga clic en **+ Recurso compartido de archivos** para crear uno nuevo que se va a usar.
6. Haga clic en **Conectar** para abrir una página que muestra la sintaxis de línea de comandos para el montaje de recursos compartidos de archivos de Windows o Linux.
7. En **Letra de unidad**, seleccione la letra que desea usar para identificar la unidad.
8. Elija la sintaxis que se usará y seleccione el botón Copiar que aparece a la derecha para copiarla en el Portapapeles. Péguela en algún lugar donde pueda accederla sin problemas. 
8. Conéctese a la máquina virtual y abra un símbolo del sistema.
9. Pegue la sintaxis de conexión modificada y presione **Entrar**.
10. Una vez creada la conexión, se recibe el mensaje **El comando se ha completado correctamente.**
11. Compruebe la conexión escribiendo la letra de unidad para cambiar a esa unidad de disco y escriba **dir** para ver el contenido del recurso compartido de archivos.



## <a name="create-a-file-share"></a>Creación de un recurso compartido de archivos 
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Cuentas de almacenamiento**.
3. Elija la cuenta de almacenamiento.
4. En la página **Información general**, en **Servicios**, seleccione **Archivos**.
5. En la página Servicio de archivos, haga clic en **+ Recurso compartido de archivos**.
6. Rellene el nombre del recurso compartido de archivos. Pueden usarse minúsculas, números y guiones individuales en los nombres de recurso compartido de archivos. El nombre no puede empezar por un guion y no se pueden usar varios guiones consecutivos. 
7. Rellene un límite para el tamaño máximo del archivo, hasta 5120 GB.
8. Haga clic en **Aceptar** para crear el recurso compartido de archivos.
   
## <a name="upload-files"></a>Carga de archivos
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Cuentas de almacenamiento**.
3. Elija la cuenta de almacenamiento.
4. En la página **Información general**, en **Servicios**, seleccione **Archivos**.
5. Seleccione un recurso compartido de archivos.
6. Haga clic en **Cargar** para abrir la página **Cargar archivos**.
7. Haga clic en el icono de carpeta para buscar el archivo que quiere cargar en el sistema de archivos local.   
8. Haga clic en **Cargar** para cargar el archivo en el recurso compartido de archivos.

## <a name="download-files"></a>Descarga de archivos
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Cuentas de almacenamiento**.
3. Elija la cuenta de almacenamiento.
4. En la página **Información general**, en **Servicios**, seleccione **Archivos**.
5. Seleccione un recurso compartido de archivos.
6. Haga clic con el botón derecho en un archivo y elija **Descargar** para descargarlo en una máquina local.
   

## <a name="next-steps"></a>pasos siguientes

También puede crear y administrar recursos compartidos de archivos con PowerShell. Para más información, vea [Introducción a Azure File Storage en Windows](../../storage/files/storage-dotnet-how-to-use-files.md).

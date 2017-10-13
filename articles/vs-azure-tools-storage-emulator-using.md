---
title: "Configuración y uso del emulador de Storage con Visual Studio | Microsoft Docs"
description: "Configuración y uso del emulador de almacenamiento con Visual Studio"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: c8e7996f-6027-4762-806e-614b93131867
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/17/2017
ms.author: kraigb
ms.openlocfilehash: f4cd8ccc3b186cf2b4178b7d8a98d8928c705cbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configuración y uso del emulador de almacenamiento con Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Información general
El entorno de desarrollo SDK de Azure incluye el emulador de almacenamiento, una utilidad que simula los servicios de almacenamiento de blobs, colas y tablas disponibles en Azure, en el equipo de desarrollo local. Si está creando un servicio en la nube que emplea los servicios de almacenamiento de Azure o escribiendo una aplicación externa que llama a los servicios de almacenamiento, puede probar el código localmente con el emulador de almacenamiento. Azure Tools para Microsoft Visual Studio integra la administración del emulador de almacenamiento en Visual Studio. Azure Tools inicializa la base de datos del emulador de almacenamiento cuando se usa por primera vez, inicia el servicio del emulador de almacenamiento cuando ejecuta o depura el código desde Visual Studio y proporciona acceso de solo lectura a los datos del emulador de almacenamiento mediante el Explorador de almacenamiento de Azure.

Para información detallada sobre el emulador de almacenamiento, incluidos los requisitos del sistema e instrucciones de configuración personalizada, consulte [Uso del emulador de Azure Storage para desarrollo y pruebas](storage/common/storage-use-emulator.md).

> [!NOTE]
> Existen algunas diferencias de funcionalidad entre el emulador de almacenamiento y los servicios de almacenamiento de Azure. Consulte [Diferencias entre el emulador de Storage y los servicios de Azure Storage](storage/common/storage-use-emulator.md) en la documentación del SDK de Azure para información sobre las diferencias concretas.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configuración de una cadena de conexión para el emulador de almacenamiento
Para acceder al emulador de almacenamiento desde el código dentro de un rol, deberá configurar una cadena de conexión que señale al emulador de almacenamiento y que se pueda cambiar después para que señale a una cuenta de almacenamiento de Azure. Una cadena de conexión es una opción de configuración que el rol puede leer en tiempo de ejecución para conectarse a una cuenta de almacenamiento. Para obtener más información sobre cómo crear cadenas de conexión, consulte el artículo sobre cómo [configurar la aplicación de Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

> [!NOTE]
> Puede devolver una referencia a la cuenta del emulador de almacenamiento desde el código mediante la propiedad **DevelopmentStorageAccount** . Este enfoque funciona correctamente si desea acceder al emulador de almacenamiento desde el código, pero, si piensa publicar su aplicación en Azure, necesitará crear una cadena de conexión para acceder a su cuenta de almacenamiento de Azure y modificar el código para que use esa cadena de conexión antes de publicarla. Si cambia entre la cuenta del emulador de almacenamiento y una cuenta de almacenamiento de Azure con frecuencia, una cadena de conexión simplificará el proceso.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicialización y ejecución del emulador de almacenamiento
Puede especificar que, cuando ejecute o depure el servicio en Visual Studio, Visual Studio inicie automáticamente el emulador de almacenamiento. En el Explorador de soluciones, abra el menú contextual de su proyecto de **Azure** y elija **Propiedades**. En la pestaña **Desarrollo**, en la lista **Iniciar emulador de Azure Storage**, elija **True** (si todavía no está establecido en ese valor).

La primera vez que ejecute o depure el servicio desde Visual Studio, el emulador de almacenamiento inicia un proceso de inicialización. Este proceso reserva los puertos locales para el emulador de almacenamiento y crea la base de datos del emulador de almacenamiento. Una vez finalizado, no es necesario volver a ejecutar este proceso a menos que se elimine la base de datos del emulador de almacenamiento.

> [!NOTE]
> A partir de la versión de junio de 2012 de Azure Tools, el emulador de almacenamiento se ejecuta, de forma predeterminada, en SQL Express LocalDB. En versiones anteriores de Azure Tools, el emulador de almacenamiento se ejecuta en una instancia predeterminada de SQL Express 2005 o 2008, que debe instalar para poder instalar el SDK de Azure. También puede ejecutar el emulador de almacenamiento en una instancia con nombre de SQL Express o en una instancia con nombre o predeterminada de Microsoft SQL Server. Si necesita configurar el emulador de Storage para que se ejecute en una instancia distinta de la predeterminada, consulte [Uso del emulador de Azure Storage para desarrollo y pruebas](storage/common/storage-use-emulator.md).
> 
> 

El emulador de almacenamiento proporciona una interfaz de usuario para ver el estado de los servicios de almacenamiento local y para iniciarlos, detenerlos y restablecerlos. Una vez que se ha iniciado el servicio del emulador de almacenamiento, puede mostrar la interfaz de usuario o iniciar o detener el servicio haciendo clic con el botón derecho en el icono del área de notificación para el Emulador de Microsoft Azure en la barra de tareas de Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visualización de los datos del emulador de almacenamiento en el Explorador de servidores
El nodo Almacenamiento de Azure en el Explorador de servidores permite ver los datos y cambiar la configuración para los datos de blob y tabla en sus cuentas de almacenamiento, incluido el emulador de almacenamiento. Consulte [Administración de recursos de Azure Blob Storage con el Explorador de Storage (versión preliminar)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) para más información.


---
title: "Incorporación del conector de Azure Blob Storage en Logic Apps | Microsoft Docs"
description: "Muestra cómo empezar a trabajar y configurar el conector de Azure Blob Storage en una aplicación lógica"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: mandia; ladocs
ms.openlocfilehash: bc7908868828bd1628633cf9e57f8c44f8000827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>Uso del conector de Azure Blob Storage en una aplicación lógica
Use el conector de Azure Blob Storage para cargar, actualizar, obtener y eliminar blobs en la cuenta de almacenamiento, todo dentro de una aplicación lógica.  

Con Almacenamiento de blobs de Azure:

* Cree un flujo de trabajo cargando nuevos proyectos u obteniendo archivos recientemente actualizados.
* Use acciones para obtener metadatos de archivos, eliminar un archivo, copiar archivos y muchas otras cosas. Por ejemplo, cuando se actualiza una herramienta en un sitio web de Azure (desencadenador), se actualiza un archivo en Blob Storage (acción). 

En este tema se muestra cómo usar el conector de Azure Blob Storage en una aplicación lógica.

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-what-are-logic-apps.md) y [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-what-are-logic-apps.md) y [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-blob-storage"></a>Conexión con el almacenamiento de blobs de Azure
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a una cuenta de almacenamiento, debe crear primero una *conexión* con Blob Storage. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que se está conectando. En el caso de Almacenamiento de Azure, para crear la conexión, deberá escribir las credenciales de la cuenta de almacenamiento. 

#### <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>Uso de un desencadenador
Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica; por ejemplo, un desencadenador de periodicidad, un desencadenador HTTP webhook, los desencadenadores disponibles con otros conectores, etc. En [Creación de una nueva aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) se puede ver un ejemplo.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica.

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Elija **Add an action**(Agregar una acción).
3. En el cuadro de texto, escriba "blob" para obtener una lista de todas las acciones disponibles.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. En nuestro ejemplo, elija **AzureBlob - Obtener metadatos de archivo mediante la ruta de acceso**. Si ya existe una conexión, seleccione el botón **...** (Mostrar selector) para seleccionar un archivo.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Si se le solicita la información de conexión, escriba los detalles para crear la conexión. Estas propiedades se describen en la sección [Creación de la conexión](connectors-create-api-azureblobstorage.md#create-the-connection) de este tema. 
   
   > [!NOTE]
   > En este ejemplo, obtenemos los metadatos de un archivo. Para ver los metadatos, agregue otra acción que cree un archivo nuevo mediante otro conector. Por ejemplo, agregue una acción de OneDrive que cree un nuevo archivo de "prueba" basándose en los metadatos. 


5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

> [!TIP]
> [Explorador de Storage](http://storageexplorer.com/) es una excelente herramienta para administrar varias cuentas de almacenamiento.

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/azureblobconnector/). 

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).


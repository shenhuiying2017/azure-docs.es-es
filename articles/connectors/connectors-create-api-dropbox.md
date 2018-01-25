---
title: Conector de Dropbox en Azure Logic Apps | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a Dropbox para administrar los archivos. En Dropbox, puede realizar diversas acciones, como cargar, actualizar, obtener y eliminar archivos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 3fecdc33a08c82d9ad13226e75d0d704a5557f98
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-dropbox-connector"></a>Introducción al conector de Dropbox
Conéctese a Dropbox para administrar los archivos. En Dropbox, puede realizar diversas acciones, como cargar, actualizar, obtener y eliminar archivos.

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Puede empezar [creando una aplicación lógica ahora](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-dropbox"></a>Conexión con Dropbox
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para poder conectarse a Dropbox, primero necesita crear una *conexión* a Dropbox. Para ello, tendrá que especificar las credenciales que usa habitualmente para acceder al servicio al que desea conectarse. Por tanto, en el ejemplo de Dropbox, deberá escribir las credenciales de la cuenta de Dropbox para crear la conexión con este servicio. [Más información sobre las conexiones]()

### <a name="create-a-connection-to-dropbox"></a>Creación de una conexión con Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Uso de un desencadenador de Dropbox
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../logic-apps/logic-apps-overview.md#logic-app-concepts).

En este ejemplo, vamos a usar el desencadenador **When a file is created (Cuando se crea un archivo)**. Cuando se active este desencadenador, se invocará la acción de Dropbox **Get file content using path (Obtener contenido del archivo mediante la ruta de acceso)**. 

1. Escriba *dropbox* en el cuadro de búsqueda del diseñador de Logic Apps y seleccione el desencadenador **Dropbox - When a file is created (Dropbox - Cuando se cree un archivo)**.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Seleccione la carpeta en la que se va a supervisar la creación de archivos. Seleccione la opción … (marcada con un cuadro rojo) y busque la carpeta que quiere seleccionar para la entrada del desencadenador.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Uso de una acción de Dropbox
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Ahora que se ha agregado el desencadenador, siga estos pasos para incorporar una acción que obtenga el contenido del nuevo archivo.

1. Seleccione **+ Nuevo paso** para agregar la acción que quiere que se ejecute cuando se cree un archivo.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Seleccione **Add an action**(Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Escriba *dropbox* para buscar las acciones relacionadas con Dropbox.  
4. Seleccione **Dropbox - Get file content using path (Dropbox: obtener el contenido del archivo mediante la ruta de acceso)**. Esta será la acción que se ejecutará cuando se cree un archivo en la carpeta de Dropbox seleccionada. Se abre el bloque de control de acción. Si no lo ha hecho previamente, se le pedirá que autorice a la aplicación lógica para que pueda acceder a la cuenta de Dropbox.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Seleccione la opción … (la encontrará a la derecha del control **Ruta de archivo**) y busque la ruta de archivo que quiere usar. También puede usar el token de la **ruta de archivo** para crear más rápido la aplicación lógica.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Guarde el trabajo y cree un nuevo archivo en Dropbox para activar el flujo de trabajo.  

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/dropbox/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).
---
title: "Adición del conector de OneDrive a Logic Apps | Microsoft Docs"
description: "Información general del conector de OneDrive con parámetros de la API de REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 63bd33bf4e09b98aa53dcfec9fcc4a0109204952
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-onedrive-connector"></a>Introducción al conector de OneDrive
Conéctese a OneDrive para administrar los archivos, incluyendo las tareas de carga, obtención y eliminación de archivos, y muchas más. 

Con OneDrive, puede: 

* Crear un flujo de trabajo almacenando archivos en OneDrive o actualizar las archivos que ya tenga en OneDrive. 
* Usar desencadenadores para iniciar el flujo de trabajo cuando se crea o se actualiza un archivo en OneDrive.
* Usar acciones para crear o eliminar un archivo, entre otras muchas cosas. Por ejemplo, cuando se reciba un nuevo correo electrónico de Office 365 con datos adjuntos (desencadenador), cree un nuevo archivo en OneDrive (acción).

En este tema se muestra cómo usar el conector de OneDrive en una aplicación lógica, y se enumeran los desencadenadores y las acciones.

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-what-are-logic-apps.md) y [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-onedrive"></a>Conexión a OneDrive
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a OneDrive, primero necesita una *conexión* de OneDrive. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que desea conectarse. Por lo tanto, con OneDrive, escriba las credenciales de la cuenta de OneDrive para crear la conexión.

### <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Uso de un desencadenador
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. Los desencadenadores "sondean" el servicio en el intervalo y la frecuencia que desee. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. En la aplicación lógica, escriba "onedrive" para obtener una lista de los desencadenadores:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Seleccione **Cuando se modifica un archivo**. Si ya existe una conexión, seleccione el botón Mostrar Selector para seleccionar una carpeta.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Si se le solicita que inicie sesión, escriba los datos de inicio de sesión para crear la conexión. En la sección [Creación de la conexión](connectors-create-api-onedrive.md#create-the-connection) de este tema se enumeran los pasos. 
   
   > [!NOTE]
   > En este ejemplo, la aplicación lógica se ejecuta cuando un archivo de la carpeta que elija se actualiza. Para ver los resultados de este desencadenador, agregue otra acción que envíe un correo electrónico. Por ejemplo, agregue la acción *Enviar un correo electrónico* de Office 365 Outlook que le envía un correo electrónico cuando se actualiza un archivo. 

3. Seleccione el botón **Editar** y defina los valores de **Frecuencia** e **Intervalo**. Por ejemplo, si desea que el desencadenador sondee cada 15 minutos, establezca el valor de **Frecuencia** en **Minuto** y el de **Intervalo** en **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Elija **Add an action**(Agregar una acción).
3. En el cuadro de texto, escriba "onedrive" para obtener una lista de todas las acciones disponibles.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. En nuestro ejemplo, elija **OneDrive - Crear archivo**. Si ya existe una conexión, seleccione la **Ruta de la carpeta** en la que se incluirá el archivo, escriba el **Nombre de archivo** y elija el **Contenido de archivo** que desee:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Si se le solicita la información de conexión, escriba los detalles para crear la conexión. Estas propiedades se describen en la sección [Creación de la conexión](connectors-create-api-onedrive.md#create-the-connection) de este tema. 
   
   > [!NOTE]
   > En este ejemplo, creamos un nuevo archivo en una carpeta de OneDrive. Puede utilizar la salida de otro desencadenador para crear el archivo de OneDrive. Por ejemplo, agregue el desencadenador *Cuando llega un nuevo correo electrónico* de Office 365 Outlook. A continuación, agregue la acción *Crear archivo* de OneDrive, que usa los campos Attachments y Content-Type de una instrucción ForEach para crear el nuevo archivo en OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.


## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).
---
title: "Llamada a los puntos de conexión REST con el conector HTTP + Swagger para Azure Logic Apps | Microsoft Docs"
description: "Conexión a los puntos de conexión REST desde las aplicaciones lógicas a través de Swagger con el conector HTTP + Swagger"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 0487dbedddee684c75420bd66effe2c963a18624
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-http--swagger-action"></a>Introducción a la acción HTTP + Swagger

Puede crear un conector de primera categoría para cualquier punto de conexión REST a través de un [documento Swagger](https://swagger.io) al usar la acción HTTP + Swagger en el flujo de trabajo de la aplicación lógica. También puede ampliar las aplicaciones lógicas para llamar a cualquier punto de conexión REST con una experiencia de Diseñador de aplicación lógica de primer nivel.

Para aprender a crear aplicaciones lógicas con conectores, consulte [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Uso de HTTP + Swagger como desencadenador o acción

El desencadenador y la acción HTTP + Swagger funcionan igual que la [acción HTTP](connectors-native-http.md), pero ofrecen una experiencia en el Diseñador de aplicación lógica mejor al exponer la estructura de la API y los resultados desde los [metadatos de Swagger](https://swagger.io). También puede usar el conector HTTP + Swagger como desencadenador. Si desea implementar un desencadenador de sondeo, siga el modelo de sondeo que se describe en [Creación de API personalizadas para llamar a otras API, servicio y sistemas de aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Más información sobre [las acciones y los desencadenadores de aplicaciones lógicas](connectors-overview.md).

A continuación se muestra un ejemplo de cómo utilizar la operación HTTP + Swagger como una acción en un flujo de trabajo de una aplicación lógica.

1. Seleccione el botón **Nuevo paso** .
2. Seleccione **Add an action**(Agregar una acción).
3. En el cuadro de búsqueda de acciones, escriba **swagger** para mostrar la acción HTTP + Swagger.
   
    ![Acción de selección de HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)
4. Escriba la dirección URL de un documento Swagger:
   
   * Para poder utilizarse en el diseñador de aplicaciones lógicas, la dirección URL debe ser un punto de conexión HTTPS y debe tener habilitado CORS.
   * Si el documento Swagger no cumple este requisito, puede usar el [Azure Storage con CORS habilitado](#hosting-swagger-from-storage) para almacenar el documento.
5. Haga clic en **Siguiente** para realizar operaciones de lectura y procesamiento en el documento Swagger.
6. Agregue cualquier parámetro necesario para la llamada HTTP.
   
    ![Completar acción HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Para guardar y publicar la aplicación lógica, haga clic en **Guardar** en la barra de herramientas del diseñador.

### <a name="host-swagger-from-azure-storage"></a>Hospedar Swagger desde Azure Storage
Puede hacer referencia a un documento de Swagger que no está hospedado, o que no cumple los requisitos de seguridad y de origen cruzado para el diseñador. Para resolver este problema, puede almacenar el documento Swagger en Azure Storage y habilitar CORS para hacer referencia al documento.  

Estos son los pasos necesarios para crear, configurar y almacenar documentos Swagger en Azure Storage:

1. [Crear una cuenta de Azure Storage con Azure Blob Storage](../storage/common/storage-create-storage-account.md). Para este paso, establezca los permisos en **Acceso público**.

2. Habilite CORS en el blob. 

   Puede usar [este script de PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) para configurar automáticamente esta opción.

3. Cargue el archivo de Swagger en el blob. 

   Puede realizar este paso desde [Azure Portal](https://portal.azure.com) o con una herramienta como el [Explorador de Azure Storage](http://storageexplorer.com/).

4. Haga referencia a un vínculo HTTPS en el documento de Azure Blob Storage. 

   El vínculo utiliza este formato:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Detalles técnicos
A continuación se muestran los detalles de los desencadenadores y las acciones que admite el conector HTTP + Swagger.

## <a name="http--swagger-triggers"></a>Desencadenadores HTTP + Swagger
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores.](connectors-overview.md) El conector HTTP + Swagger tiene un desencadenador.

| Desencadenador | DESCRIPCIÓN |
| --- | --- |
| HTTP + Swagger |Realizar una llamada HTTP y devolver el contenido de la respuesta. |

## <a name="http--swagger-actions"></a>Acciones HTTP + Swagger
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones.](connectors-overview.md) El conector HTTP + Swagger tiene una acción posible.

| . | DESCRIPCIÓN |
| --- | --- |
| HTTP + Swagger |Realizar una llamada HTTP y devolver el contenido de la respuesta. |

### <a name="action-details"></a>Detalles de la acción
El conector HTTP + Swagger incluye una acción posible. A continuación, se incluye información acerca de cada una de las acciones, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

#### <a name="http--swagger"></a>HTTP + Swagger
Realice una solicitud HTTP saliente con ayuda de los metadatos de Swagger.
Un asterisco (*) significa un campo obligatorio.

| Nombre para mostrar | Nombre de propiedad | DESCRIPCIÓN |
| --- | --- | --- |
| Método* |estático |Verbo HTTP que se va a usar |
| URI* |uri |Identificador URI de la solicitud HTTP |
| encabezados |encabezados |Objeto JSON de los encabezados HTTP que se va a incluir |
| Cuerpo |Cuerpo |Cuerpo de la solicitud HTTP |
| Autenticación |Autenticación |Autenticación que se utiliza para la solicitud. Para más información, consulte el artículo [Conector HTTP](connectors-native-http.md#authentication). |

**Detalles de salida**

Respuesta HTTP

| Nombre de propiedad | Tipo de datos | DESCRIPCIÓN |
| --- | --- | --- |
| encabezados |objeto |Encabezados de respuesta |
| Cuerpo |objeto |Objeto de respuesta |
| Código de estado |int |Código de estado HTTP |

### <a name="http-responses"></a>Respuestas HTTP
Al realizar llamadas a diversas acciones, es posible que obtenga determinadas respuestas. A continuación se incluye una tabla que describe las respuestas y descripciones correspondientes.

| NOMBRE | DESCRIPCIÓN |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Solicitud incorrecta |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor Error desconocido. |

- - -
## <a name="next-steps"></a>pasos siguientes

* [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Buscar otros conectores](apis-list.md)
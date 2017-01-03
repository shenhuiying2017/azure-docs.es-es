
---
title: "Adición de la acción HTTP + Swagger a Logic Apps | Microsoft Docs"
description: "Información general de las operaciones y la acción HTTP + Swagger"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 04b081d265c53ddcf77c79bf604296a0d44be4ed


---
# <a name="get-started-with-the-http--swagger-action"></a>Introducción a la acción HTTP + Swagger
Con la acción HTTP + Swagger puede crear un conector de primera categoría para cualquier punto de conexión REST a través de un [documento Swagger](https://swagger.io). También puede ampliar una aplicación lógica para llamar a cualquier punto de conexión REST con una experiencia de diseñador de aplicaciones lógicas de primer nivel.

Para empezar con la acción HTTP + Swagger en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

- - -
## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Uso de HTTP + Swagger como desencadenador o acción
El desencadenador y la función de acción de HTTP + Swagger funcionan igual que la [acción HTTP](connectors-native-http.md), pero ofrecen una experiencia de diseño más eficaz, ya que muestran la forma de la API y los resultados en el diseñador desde los [metadatos de Swagger](https://swagger.io). Además, puede utilizar HTTP + Swagger como desencadenador. Si desea implementar un desencadenador de sondeo, debería seguir el modelo de sondeo que se describe en [Creación de una API personalizada para usar con aplicaciones lógicas](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[más información sobre las acciones y los desencadenadores de aplicaciones lógicas.](connectors-overview.md)

A continuación se muestra un ejemplo de cómo utilizar la operación HTTP + Swagger como una acción en un flujo de trabajo de una aplicación lógica.

1. Seleccione el botón **Nuevo paso** .
2. Seleccione **Add an action**(Agregar una acción).
3. En el cuadro de búsqueda de acciones, escriba **swagger** para mostrar la acción HTTP + Swagger.
   
    ![Acción de selección de HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)
4. Escriba la dirección URL de un documento Swagger:
   
   * Para poder utilizarse en el diseñador de aplicaciones lógicas, la dirección URL debe ser un punto de conexión HTTPS y debe tener habilitado CORS.
   * Si el documento Swagger no cumple este requisito, puede usar el [Almacenamiento de Azure con CORS habilitado](#hosting-swagger-from-storage) para almacenar el documento.
5. Haga clic en **Siguiente** para realizar operaciones de lectura y procesamiento en el documento Swagger.
6. Agregue cualquier parámetro necesario para la llamada HTTP.
   
    ![Completar acción HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Haga clic en **Guardar** en la esquina superior izquierda de la barra de herramientas; la aplicación lógica se guardará y se publicará (activará).

### <a name="host-swagger-from-azure-storage"></a>Hospedar Swagger desde Almacenamiento de Azure
Puede hacer referencia a un documento de Swagger que no está hospedado, o que no cumple los requisitos de seguridad y de origen cruzado para el diseñador. Para resolver este problema, puede almacenar el documento Swagger en Almacenamiento de Azure y habilitar CORS para hacer referencia al documento.  

Estos son los pasos necesarios para crear, configurar y almacenar documentos Swagger en Almacenamiento de Azure:

1. [Crear una cuenta de Almacenamiento de Azure con Almacenamiento de blobs de Azure](../storage/storage-create-storage-account.md). (Para ello, establezca los permisos en **acceso público**).
2. Habilite CORS en el blob. Puede usar [este script de PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) para configurar automáticamente esta opción.
3. Cargue el archivo de Swagger en el blob. Puede hacerlo desde [Azure Portal](https://portal.azure.com) o con una herramienta como [Azure Storage Explorer](http://storageexplorer.com/).
4. Haga referencia a un vínculo HTTPS en el documento de Almacenamiento de blobs de Azure. (El vínculo sigue el formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`).

## <a name="technical-details"></a>Detalles técnicos
A continuación se muestran los detalles de los desencadenadores y las acciones que admite el conector HTTP + Swagger.

## <a name="http--swagger-triggers"></a>Desencadenadores HTTP + Swagger
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores.](connectors-overview.md)  El conector HTTP + Swagger tiene un desencadenador.

| Desencadenador | Description |
| --- | --- |
| HTTP + Swagger |Realizar una llamada HTTP y devolver el contenido de la respuesta. |

## <a name="http--swagger-actions"></a>Acciones HTTP + Swagger
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones.](connectors-overview.md)  El conector HTTP + Swagger tiene una acción posible.

| Acción | Description |
| --- | --- |
| HTTP + Swagger |Realizar una llamada HTTP y devolver el contenido de la respuesta. |

### <a name="action-details"></a>Detalles de la acción
El conector HTTP + Swagger incluye una acción posible. A continuación, se incluye información acerca de cada una de las acciones, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

#### <a name="http--swagger"></a>HTTP + Swagger
Realice una solicitud HTTP saliente con ayuda de los metadatos de Swagger.
Un asterisco (*) significa un campo obligatorio.

| Nombre para mostrar | Nombre de propiedad | Description |
| --- | --- | --- |
| Método* |estático |Verbo HTTP que se va a usar |
| URI* |uri |Identificador URI de la solicitud HTTP |
| Encabezados |Encabezados |Objeto JSON de los encabezados HTTP que se va a incluir |
| Cuerpo |Cuerpo |Cuerpo de la solicitud HTTP |
| Autenticación |Autenticación |Autenticación que se utiliza para la solicitud. [Para más información, vea HTTP](connectors-native-http.md#authentication). |

**Detalles de salida**

Respuesta HTTP

| Nombre de propiedad | Tipo de datos | Description |
| --- | --- | --- |
| encabezados |objeto |Encabezados de respuesta |
| Cuerpo |objeto |Objeto de respuesta |
| Código de estado |int |Código de estado HTTP |

### <a name="http-responses"></a>Respuestas HTTP
Al realizar llamadas a diversas acciones, es posible que obtenga determinadas respuestas. A continuación se incluye una tabla que describe las respuestas y descripciones correspondientes.

| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Solicitud incorrecta |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor Error desconocido. |

- - -
## <a name="next-steps"></a>Pasos siguientes
Pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Puede explorar los demás conectores disponibles en aplicaciones lógicas consultando nuestra [lista de API](apis-list.md).




<!--HONumber=Jan17_HO1-->



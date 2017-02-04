---
title: "Incorporación del conector de Dynamics CRM Online a Logic Apps | Microsoft Docs"
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El proveedor de conexión de Dynamics CRM Online proporciona una API para trabajar con entidades de Dynamics CRM Online."
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: a1ea6c09621aeeb1e98bbbf5edf1d5deb5e4b721


---
# <a name="get-started-with-the-dynamics-crm-online-connector"></a>Introducción al conector de Dynamics CRM Online
Conéctese a Dynamics CRM Online para crear un nuevo registro, actualizar un elemento y mucho más. Con CRM Online, puede hacer lo siguiente:

* Compilar el flujo de negocio en función de los datos que obtiene de CRM Online. 
* Usar acciones para eliminar registros, obtener entidades y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se actualice un elemento en CRM, puede enviar un correo electrónico mediante Office 365.

En este tema se muestra cómo usar el conector de Dynamics CRM Online en una aplicación lógica y también se enumeran los desencadenadores y las acciones.

> [!NOTE]
> Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas.
> 
> 

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-what-are-logic-apps.md) y [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-dynamics-crm-online"></a>Conexión a Dynamics CRM Online
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a Dynamics, primero necesita una *conexión* de Dynamics CRM Online. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que desea conectarse. Por lo tanto, con Dynamics CRM Online, escriba las credenciales de la cuenta de Dynamics CRM Online para crear la conexión.

### <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Steps to create a connection to Dynamics CRM Online Connection Provider](../../includes/connectors-create-api-crmonline.md)]
> 
> 

## <a name="use-a-trigger"></a>Uso de un desencadenador
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. Los desencadenadores "sondean" el servicio en el intervalo y la frecuencia que desee. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. En la aplicación lógica, escriba "dynamics" para obtener una lista de los desencadenadores:  
   
    ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Seleccione **Dynamics CRM Online - When a record is created**. Si ya existe una conexión, seleccione una organización y una entidad de la lista desplegable.
   
    ![](./media/connectors-create-api-crmonline/select-organization.png)
   
    Si se le solicita que inicie sesión, escriba los datos de inicio de sesión para crear la conexión. En la sección [Creación de la conexión](connectors-create-api-crmonline.md#create-the-connection) de este tema se enumeran los pasos. 
   
   > [!NOTE]
   > En este ejemplo, la aplicación lógica se ejecuta cuando se crea un evento. Para ver los resultados de este desencadenador, agregue otra acción que envíe un correo electrónico. Por ejemplo, agregue la acción *Send an email* (Enviar un correo electrónico) en Office 365 que le envía un correo electrónico cuando se agrega un nuevo registro. 
   > 
   > 
3. Seleccione el botón **Editar** y defina los valores de **Frecuencia** e **Intervalo**. Por ejemplo, si desea que el desencadenador sondee cada 15 minutos, establezca el valor de **Frecuencia** en **Minuto** y el de **Intervalo** en **15**. 
   
    ![](./media/connectors-create-api-crmonline/edit-properties.png)
4. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.
   
    ![](./media/connectors-create-api-crmonline/add-action.png)
2. Elija **Add an action**(Agregar una acción).
3. En el cuadro de texto, escriba "dynamics" para obtener una lista de todas las acciones disponibles.
   
    ![](./media/connectors-create-api-crmonline/dynamics-actions.png)
4. En nuestro ejemplo, elija **Dynamics CRM Online - Update a record** (Dynamics CRM Online: actualizar un registro). Si ya existe una conexión, elija las propiedades **Organization name** (Nombre de la organización), **Entity Name** (Nombre de la entidad) y otras propiedades:  
   
    ![](./media/connectors-create-api-crmonline/sample-action.png)
   
    Si se le solicita la información de conexión, escriba los detalles para crear la conexión. Estas propiedades se describen en la sección [Creación de la conexión](connectors-create-api-crmonline.md#create-the-connection) de este tema. 
   
   > [!NOTE]
   > En este ejemplo se actualiza un registro existente en CRM Online. Para actualizar el registro puede utilizar la salida de otro desencadenador. Por ejemplo, agregue el desencadenador SharePoint *When an existing item is modified* (SharePoint: cuando se modifica un elemento ya existente). A continuación, agregue la acción CRM Online *Update a record* (CRM Online: actualizar un registro) que utiliza los campos de SharePoint para actualizar el registro existente en CRM Online. 
   > 
   > 
5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="technical-details"></a>Detalles técnicos
## <a name="triggers"></a>Desencadenadores
| Desencadenador | Descripción |
| --- | --- |
| [When a record is created](connectors-create-api-crmonline.md#when-a-record-is-created) |Desencadena un flujo cuando se crea un objeto en CRM. |
| [When a record is updated](connectors-create-api-crmonline.md#when-a-record-is-updated) |Desencadena un flujo cuando se modifica un objeto en CRM. |
| [When a record is deleted](connectors-create-api-crmonline.md#when-a-record-is-deleted) |Desencadena un flujo cuando se elimina un objeto en CRM. |

## <a name="actions"></a>Acciones
| Acción | Descripción |
| --- | --- |
| [List records](connectors-create-api-crmonline.md#list-records) |Esta operación obtiene los registros de una entidad. |
| [Create a new record](connectors-create-api-crmonline.md#create-a-new-record) |Esta operación crea un nuevo registro de una entidad. |
| [Get record](connectors-create-api-crmonline.md#get-record) |Esta operación obtiene el registro especificado de una entidad. |
| [Delete a record](connectors-create-api-crmonline.md#delete-a-record) |Esta operación elimina un registro de una colección de entidades. |
| [Update a record](connectors-create-api-crmonline.md#update-a-record) |Esta operación actualiza un registro existente de una entidad. |

### <a name="trigger-and-action-details"></a>Detalles de los desencadenadores y las acciones
En esta sección podrá consultar los detalles específicos acerca de los desencadenadores y las acciones, como las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### <a name="when-a-record-is-created"></a>When a record is created
Desencadena un flujo cuando se crea un objeto en CRM. 

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para obtener (valor predeterminado = 256) |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-updated"></a>When a record is updated
Desencadena un flujo cuando se modifica un objeto en CRM. 

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para obtener (valor predeterminado = 256) |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-deleted"></a>When a record is deleted
Desencadena un flujo cuando se elimina un objeto en CRM. 

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para obtener (valor predeterminado = 256) |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="list-records"></a>List records
Esta operación obtiene los registros de una entidad. 

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para obtener (valor predeterminado = 256) |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="create-a-new-record"></a>Crear un nuevo registro
Esta operación crea un nuevo registro de una entidad. 

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="get-record"></a>Get record
Esta operación obtiene el registro especificado de una entidad. 

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| id* |Identificador de elementos |Especifica el identificador del registro |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="delete-a-record"></a>Delete a record
Esta operación elimina un registro de una colección de entidades. 

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| id* |Identificador de elementos |Especifica el identificador del registro |

Un asterisco (*) significa que la propiedad es obligatoria.

#### <a name="update-a-record"></a>Actualización de un registro
Esta operación actualiza un registro existente de una entidad. 

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| id* |Identificador del registro |Especifica el identificador del registro |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

## <a name="http-responses"></a>Respuestas HTTP
Las acciones y los desencadenadores pueden devolver uno o varios de los siguientes códigos de estado HTTP: 

| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido. |
| default |Error en la operación. |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).




<!--HONumber=Jan17_HO3-->



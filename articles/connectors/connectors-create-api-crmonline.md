---
title: Incorporación del conector de Dynamics CRM Online a las aplicaciones lógicas | Microsoft Docs
description: Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El proveedor de conexión de Dynamics CRM Online proporciona una API para trabajar con entidades de Dynamics CRM Online.
services: logic-apps
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/15/2016
ms.author: mandia

---
# Introducción al conector de Dynamics CRM Online
Conéctese a Dynamics CRM Online para crear un nuevo registro, actualizar un elemento y mucho más. Con CRM Online, puede hacer lo siguiente:

* Compilar el flujo de negocio en función de los datos que obtiene de CRM Online.
* Usar acciones para eliminar registros, obtener entidades y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se actualice un elemento en CRM, puede enviar un correo electrónico mediante Office 365.

En este tema se muestra cómo usar el conector de Dynamics CRM Online en una aplicación lógica y también se enumeran los desencadenadores y las acciones.

> [!NOTE]
> Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas.
> 
> 

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../app-service-logic/app-service-logic-what-are-logic-apps.md) y [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión a Dynamics CRM Online
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a Dynamics, primero necesita una *conexión* de Dynamics CRM Online. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que desea conectarse. Por lo tanto, con Dynamics CRM Online, escriba las credenciales de la cuenta de Dynamics CRM Online para crear la conexión.

### Creación de la conexión
> [!INCLUDE [Pasos para crear una conexión al proveedor de conexión de Dynamics CRM Online](../../includes/connectors-create-api-crmonline.md)]
> 
> 

## Uso de un desencadenador
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. Los desencadenadores "sondean" el servicio en el intervalo y la frecuencia que desee. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. En la aplicación lógica, escriba "dynamics" para obtener una lista de los desencadenadores:
   
    ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Seleccione **Dynamics CRM Online - When a record is created**. Si ya existe una conexión, seleccione una organización y una entidad de la lista desplegable.
   
    ![](./media/connectors-create-api-crmonline/select-organization.png)
   
    Si se le solicita que inicie sesión, escriba los datos de inicio de sesión para crear la conexión. En este tema, en [Creación de la conexión](connectors-create-api-crmonline.md#create-the-connection) se enumeran los pasos.
   
   > [!NOTE]
   > En este ejemplo, la aplicación lógica se ejecuta cuando se crea un evento. Para ver los resultados de este desencadenador, agregue otra acción que envíe un correo electrónico. Por ejemplo, agregue la acción *Send an email* (Enviar un correo electrónico) en Office 365 que le envía un correo electrónico cuando se agrega un nuevo registro.
   > 
   > 
3. Seleccione el botón **Editar** y defina los valores para **Frecuencia** e **Intervalo**. Por ejemplo, si desea que el desencadenador sondee cada 15 minutos, establezca el valor **Frecuencia** en **Minuto** y el de **Intervalo** en **15**.
   
    ![](./media/connectors-create-api-crmonline/edit-properties.png)
4. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Seleccione el signo más. Aparecen varias opciones: **Add an action** (Agregar una acción), **Add a condition** (Agregar una condición) o una de las opciones de **More** (Más).
   
    ![](./media/connectors-create-api-crmonline/add-action.png)
2. Elija **Agregar una acción**.
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

## Detalles técnicos
## Desencadenadores
| Desencadenador | Description |
| --- | --- |
| [When a record is created](connectors-create-api-crmonline.md#when-a-record-is-created) |Desencadena un flujo cuando se crea un objeto en CRM. |
| [When a record is updated](connectors-create-api-crmonline.md#when-a-record-is-updated) |Desencadena un flujo cuando se modifica un objeto en CRM. |
| [When a record is deleted](connectors-create-api-crmonline.md#when-a-record-is-deleted) |Desencadena un flujo cuando se elimina un objeto en CRM. |

## Acciones
| Acción | Description |
| --- | --- |
| [List records](connectors-create-api-crmonline.md#list-records) |Esta operación obtiene los registros de una entidad. |
| [Crear un nuevo registro](connectors-create-api-crmonline.md#create-a-new-record) |Esta operación crea un nuevo registro de una entidad. |
| [Get record](connectors-create-api-crmonline.md#get-record) |Esta operación obtiene el registro especificado de una entidad. |
| [Delete a record](connectors-create-api-crmonline.md#delete-a-record) |Esta operación elimina un registro de una colección de entidades. |
| [Actualización de un registro](connectors-create-api-crmonline.md#update-a-record) |Esta operación actualiza un registro existente de una entidad. |

### Detalles de los desencadenadores y las acciones
En esta sección podrá consultar los detalles específicos acerca de los desencadenadores y las acciones, como las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### When a record is created
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

##### Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### When a record is updated
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

##### Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### When a record is deleted
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

##### Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### List records
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

##### Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### Crear un nuevo registro
Esta operación crea un nuevo registro de una entidad.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Get record
Esta operación obtiene el registro especificado de una entidad.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| id* |Identificador de elementos |Especifica el identificador del registro |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Delete a record
Esta operación elimina un registro de una colección de entidades.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| id* |Identificador de elementos |Especifica el identificador del registro |

Un asterisco (*) significa que la propiedad es obligatoria.

#### Actualización de un registro
Esta operación actualiza un registro existente de una entidad.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización en CRM como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| id* |Identificador del registro |Especifica el identificador del registro |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

## Respuestas HTTP
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

## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0817_2016-->
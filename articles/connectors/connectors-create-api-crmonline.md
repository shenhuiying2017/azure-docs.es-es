---
title: "Incorporación del conector de Dynamics 365 (en línea) a Azure Logic Apps | Microsoft Docs"
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El proveedor de conexión Dynamics 365 (en línea) proporciona una API para trabajar con entidades de Dynamics 365 (en línea)."
services: logic-apps
cloud: Azure Stack
documentationcenter: 
author: Mattp123
manager: anneta
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp
translationtype: Human Translation
ms.sourcegitcommit: fa426f474f4efd4023da5dfd9954dacf96f635ab
ms.openlocfilehash: 99d5379ad1e6965dd9ed88de456cc850d7e40d5a
ms.lasthandoff: 02/14/2017


---
# <a name="create-a-logic-app-with-the-dynamics-365-connector"></a>Creación de una aplicación lógica con el conector Dynamics 365

Con Logic Apps puede conectarse a Dynamics 365 (en línea) y crear flujos de negocio útiles que crean nuevos registros, actualizan elementos o devuelven una lista de registros. Con el conector de Dynamics 365, puede:

* Compilar el flujo de negocio en función de los datos que obtiene de Dynamics 365 (en línea).
* Usar acciones que obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se actualice un elemento en Dynamics 365 (en línea), puede enviar un correo electrónico mediante Office 365.

En este tema se muestra cómo crear una aplicación lógica que crea una tarea en Dynamics 365 cada vez que se crea un nuevo cliente potencial en Dynamics 365.

## <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure.
* Una cuenta de Dynamics 365 (en línea).

## <a name="walkthrough-create-a-task-whenever-a-new-lead-is-created-in-dynamics-365"></a>Tutorial: Creación de una tarea cuando se crea un nuevo cliente potencial en Dynamics 365
1.    [Inicie de sesión en Azure](https://portal.azure.com).
2.    Escriba*Logic Apps* en el cuadro de **Búsqueda** y, a continuación, presione ENTRAR.
3.    En el área de servicio de Logic App, haga clic en **Agregar**.

  ![Agregar LogicApp](./media/connectors-create-api-crmonline/add-logic-app.png)

4.    Completar los campos **Nombre**, **Suscripción**, **Grupo de recursos**, y **Ubicación** para crear el objeto de aplicación lógica y, a continuación, haga clic en **crear**.

5.    Seleccione la nueva aplicación lógica. Cuando reciba la notificación **Implementación correcta**, haga clic en **Actualizar**.

6.    En Herramientas de desarrollo, haga clic en **Diseñador de aplicación lógica** y, a continuación, en la lista de plantillas disponibles haga clic en **LogicApp en blanco**.

7.    Escriba *Dynamics 365*. En la lista hay varios desencadenadores de Dynamics 365, haga clic en **Dynamics 365 – Al crear un registro**.

8.    Si se le pide que inicie sesión en Dynamics 365, hágalo ahora.

9.    Escriba la información a continuación en los detalles del desencadenador.

  * **Nombre de la organización**. Seleccione la instancia de Dynamics 365 a la que desea que la aplicación lógica escuche.

  * **Nombre de entidad**. Seleccione la entidad a la que desea escuchar, que actuará como un desencadenador para iniciar la aplicación lógica. En este tutorial está seleccionado **Leads**.

  * **¿Con qué frecuencia quiere comprobar elementos?** Estos valores configuran la frecuencia con la que la aplicación lógica busca actualizaciones relacionadas con el desencadenador. El valor predeterminado es comprobar si hay actualizaciones cada tres minutos.

    * **Frecuencia**. Seleccione segundos, minutos, horas o días.

    * **Intervalo**. Escriba un número que indica el número de segundos, minutos, horas o días a pasar antes de la siguiente comprobación.

    ![Detalles del desencadenador de aplicación lógica](./media/connectors-create-api-crmonline/trigger-details.png)

10.    Haga clic en **Nuevo paso** y, a continuación, en **Agregar una acción**.

11.    Escriba *Dynamics 365* y en la lista, haga clic en **Dynamics 365 – crear un nuevo registro**.

12.    Escriba la siguiente información.
  * **Nombre de la organización**. Seleccione la instancia de Dynamics 365 en la que desea que el flujo cree un registro. Tenga en cuenta que no tiene que ser la misma instancia desde la que se desencadena el evento.
  * **Nombre de entidad**. Seleccione la entidad que desea que cree un registro cuando se desencadene el evento. En este tutorial está seleccionado **Tasks**.

13.    Aparece un cuadro de Asunto. Al hacer clic en el cuadro, aparece un panel de contenido dinámico donde puede seleccionar cualquiera de los siguientes campos.
  * **Apellido**. Al seleccionar este campo, se inserta el apellido del cliente potencial en el campo de Asunto de la tarea, cuando se cree el registro de tareas.
  * **Tema**. Al seleccionar este campo, se inserta el campo Tema del cliente potencial en el campo de Asunto de la tarea, cuando se cree el registro de tareas.
Haga clic en **Tema** para agregarlo al cuadro **Asunto**.

  ![Creación de detalles del nuevo registro en la aplicación lógica](./media/connectors-create-api-crmonline/create-record-details.png)

14.    Haga clic en **Guardar** en la barra de herramientas del Diseñador de aplicación lógica.

  ![Guardado en la barra de herramientas del Diseñador de aplicación lógica](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15.    Para iniciar la aplicación lógica, haga clic en **Ejecutar**.

  ![Guardado en la barra de herramientas del Diseñador de aplicación lógica](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Ahora cree un registro de cliente potencial en Dynamics 365 para Ventas y podrá ver el flujo en acción.

## <a name="using-advanced-options"></a>Uso de las opciones avanzadas
Cuando agregue un paso a una aplicación lógica, hacer clic en **Mostrar opciones avanzadas** permite controlar cómo se filtran los datos en el paso mediante la incorporación de una consulta de filtro o de organizar por.

Por ejemplo, puede usar una consulta de filtro para recuperar solo cuentas activas y organizar por el nombre de cuenta. Para ello, escriba la consulta de filtro OData **statuscode eq 1** y seleccione **Nombre de cuenta** desde el panel de contenido dinámico. Para más información consulte: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) y [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

  ![Opciones avanzadas de LogicApp](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Procedimientos recomendados al usar las opciones avanzadas
Tenga en cuenta que cuando agregue un valor a un campo, el tipo de campo tiene que coincidir, tanto si escribe el valor como si lo selecciona en el contenido dinámico que se muestra.

Tipo de campo  |Modo de uso  |Dónde encontrarlo  |Nombre  |Tipo de datos  
---------|---------|---------|---------|---------
Campos de texto|Los campos de texto requieren una sola línea de texto o contenido dinámico que sea un campo de tipo texto. Algunos ejemplos son los campos de categoría y subcategoría.|Configuración > Personalizaciones > Personalizar el sistema > Entidades > Tarea > Campos |categoría |Línea de texto única.       
Campos numéricos enteros | Algunos campos requieren un número entero o un contenido dinámico que sea un campo de tipo numérico entero. Algunos ejemplos son Porcentaje completado y Duración. |Configuración > Personalizaciones > Personalizar el sistema > Entidades > Tarea > Campos |percentcomplete |Número entero         
Campos de fecha | Algunos campos, requieren una fecha escrita en formato mm/dd/aaaa o contenido dinámico que sea un campo de tipo de fecha. Algunos ejemplos son fecha de creación, fecha de inicio, inicio real, último periodo de retención, finalización real y fecha de vencimiento. | Configuración > Personalizaciones > Personalizar el sistema > Entidades > Tarea > Campos |createdon |Fecha y hora         
Campos que requieren un identificador de registro y un tipo de búsqueda |Algunos campos que hacen referencia a otro registro de entidad requieren el identificador de registro y el tipo de búsqueda. |Configuración > Personalizaciones > Personalizar el sistema > Entidades > Cuenta > Campos  | accountid   | Clave principal

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Más ejemplos de campos que requieren tanto un identificador de registro como un tipo de búsqueda
Ampliando la tabla anterior, aquí tiene más ejemplos de campos que no funcionan con valores seleccionados de la lista de contenido dinámica. En su lugar, estos campos requieren tanto un identificador de registro como un tipo de búsqueda especificados en los campos en PowerApps.  
*  Propietario y Tipo de propietario. El campo Propietario tiene que ser un usuario válido o un identificador de registro de equipo. El Tipo de propietario tiene que ser **systemusers** o **equipos**.
* Cliente y Tipo de cliente. El campo Cliente tiene que ser una cuenta válida o un identificador de registro de contacto. El Tipo de propietario tiene que ser **cuentas** o **contactos**.
* Referente a y Tipo referente a. El campo Referente a tiene que ser un identificador de registro válido, como una cuenta o un identificador de registro de contacto. El Tipo referente a tiene que ser el tipo de búsqueda para el registro, como **cuentas** o **contactos**.

El siguiente ejemplo de acción de creación de tarea agrega un registro de cuenta que corresponde al identificador de registro, agregándolo al campo Referente a de la tarea.

  ![Identificador de registro de flujo y tipo de cuenta](./media/connectors-create-api-crmonline/recordid-type-account.png)

Este ejemplo también asigna la tarea a un usuario específico basado en el identificador de registro. del usuario.
  ![Identificador de registro de flujo y tipo de cuenta](./media/connectors-create-api-crmonline/recordid-type-user.png)

Para obtener un identificador de registro, consulte la sección *Localización del identificador de registro* a continuación.

## <a name="find-the-record-id"></a>Localización del identificador de registro
1. Abra un registro, como un registro de cuenta.

2. En la barra de herramientas de acciones, haga clic en el **emergente** ![registro emergente](./media/connectors-create-api-crmonline/popout-record.png).
O bien, en la barra de herramientas de acciones, haga clic en **ENVIAR UN VÍNCULO** para copiar la dirección URL completa en el programa de correo electrónico predeterminado.

3. El identificador de registro se muestra entre los caracteres codificados %7b y %7d de la dirección URL.

  ![Identificador de registro de flujo y tipo de cuenta](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Solución de problemas
Para solucionar problemas de un paso con errores en una aplicación de lógica, vea los detalles de estado del evento.

1. En el área de Logic Apps, haga clic en su aplicación lógica y, a continuación, en **Información general**. Se muestra el área de resumen, que proporciona el estado de ejecución de la aplicación lógica. Si hay ejecuciones que hayan fallado, haga clic en el evento con error para el que desea ver más información.

  ![Paso 1 de solución de problemas de LogicApp](./media/connectors-create-api-crmonline/tshoot1.png)

2. Haga clic en el paso con errores para expandirlo.

  ![Paso 2 de solución de problemas de LogicApp](./media/connectors-create-api-crmonline/tshoot2.png)

3. Se muestran los detalles del paso que pueden ayudar a solucionar la causa del error.

    ![Paso 2 de solución de problemas de LogicApp](./media/connectors-create-api-crmonline/tshoot3.png)

Para más información sobre cómo solucionar problemas de las aplicaciones lógicas, consulte [Diagnóstico de errores de aplicaciones lógicas](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="technical-details"></a>Detalles técnicos
## <a name="triggers"></a>Desencadenadores
| Desencadenador | Descripción |
| --- | --- |
| When a record is created |Desencadena un flujo cuando se crea un objeto en Dynamics 365. |
| When a record is updated |Desencadena un flujo cuando se modifica un objeto en Dynamics 365. |
| When a record is deleted |Desencadena un flujo cuando se elimina un objeto en Dynamics 365. |

## <a name="actions"></a>Acciones
| Acción | Descripción |
| --- | --- |
| List records |Esta operación obtiene los registros de una entidad. |
| Crear un nuevo registro |Esta operación crea un nuevo registro de una entidad. |
| Get record |Esta operación obtiene el registro especificado de una entidad. |
| Delete a record |Esta operación elimina un registro de una colección de entidades. |
| Actualización de un registro |Esta operación actualiza un registro existente de una entidad. |

### <a name="trigger-and-action-details"></a>Detalles de los desencadenadores y las acciones
En esta sección podrá consultar los detalles específicos acerca de los desencadenadores y las acciones, como las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### <a name="when-a-record-is-created"></a>When a record is created
Desencadena un flujo cuando se crea un objeto en Dynamics 365.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización de Dynamics 365 como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-updated"></a>When a record is updated
Desencadena un flujo cuando se modifica un objeto en Dynamics 365.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización de Dynamics 365 como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-deleted"></a>When a record is deleted
Desencadena un flujo cuando se elimina un objeto en Dynamics 365.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización de Dynamics 365 como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |


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
| dataset* |Nombre de la organización |Nombre de la organización de Dynamics 365 como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
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
| dataset* |Nombre de la organización |Nombre de la organización de Dynamics 365 como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="get-record"></a>Get record
Esta operación obtiene el registro especificado de una entidad.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización de Dynamics 365 como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| id* |Identificador de elementos |Especifica el identificador del registro |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="delete-a-record"></a>Delete a record
Esta operación elimina un registro de una colección de entidades.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización de Dynamics 365 como Contoso |
| table* |Nombre de entidad |Nombre de la entidad |
| id* |Identificador de elementos |Especifica el identificador del registro |

Un asterisco (*) significa que la propiedad es obligatoria.

#### <a name="update-a-record"></a>Actualización de un registro
Esta operación actualiza un registro existente de una entidad.

| Nombre de propiedad | Nombre para mostrar | Description |
| --- | --- | --- |
| dataset* |Nombre de la organización |Nombre de la organización de Dynamics 365 como Contoso |
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
Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).


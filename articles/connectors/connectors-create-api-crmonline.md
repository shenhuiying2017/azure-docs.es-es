---
title: "Conexión a Dynamics 365 (en línea) desde Azure Logic Apps | Microsoft Docs"
description: "Creación de flujos de trabajo de aplicación lógica para administrar entidades de Dynamics 365 (en línea) a través de la API proporcionada por el conector de Dynamics 365"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Conexión a Dynamics 365 desde flujos de trabajo de aplicación lógica

Con Logic Apps puede conectarse a Dynamics 365 (en línea) y crear flujos de negocio útiles que creen registros, actualicen elementos o devuelvan una lista de registros. Con el conector de Dynamics 365, puede:

* Compilar el flujo de negocio en función de los datos que obtiene de Dynamics 365 (en línea).
* Usar acciones que obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se actualice un elemento en Dynamics 365 (en línea), puede enviar un correo electrónico mediante Office 365.

En este tema se muestra cómo crear una aplicación lógica que crea una tarea en Dynamics 365 cada vez que se crea un nuevo cliente potencial en Dynamics 365.

## <a name="prerequisites"></a>requisitos previos
* Una cuenta de Azure.
* Una cuenta de Dynamics 365 (en línea).

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Creación de una tarea cuando se crea un nuevo cliente potencial en Dynamics 365

1.  [Inicie de sesión en Azure](https://portal.azure.com).

2.  En el cuadro de búsqueda de Azure, escriba `Logic apps` y presione ENTRAR.

      ![Búsqueda de Logic Apps](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  En **Logic Apps**, haga clic en **Agregar**.

      ![Agregar LogicApp](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  Para crear la aplicación lógica, rellene los campos **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**, y haga clic en **Crear**.

5.  Seleccione la nueva aplicación lógica. Cuando reciba la notificación **Implementación correcta**, haga clic en **Actualizar**.

6.  En **Herramientas de desarrollo**, haga clic en **Diseñador de aplicación lógica**. En la lista de plantillas, haga clic en **Aplicación lógica en blanco**.

7.  En el cuadro de búsqueda, escriba `Dynamics 365`. En la lista de desencadenadores de Dynamics 365, seleccione **Dynamics 365: Al crear un registro**.

8.  Si se le pide que inicie sesión en Dynamics 365, hágalo.

9.  Escriba la información a continuación en los detalles del desencadenador:

  * **Nombre de la organización**. Seleccione la instancia de Dynamics 365 a la que desea que la aplicación lógica escuche.

  * **Nombre de entidad**. Seleccione la entidad que desea escuchar. Este evento actúa como desencadenador para iniciar la aplicación lógica. 
  En este tutorial está seleccionado **Leads**.

  * **¿Con qué frecuencia quiere comprobar elementos?** Estos valores configuran la frecuencia con la que la aplicación lógica busca actualizaciones relacionadas con el desencadenador. El valor predeterminado es comprobar si hay actualizaciones cada tres minutos.

    * **Frecuencia**. Seleccione segundos, minutos, horas o días.

    * **Intervalo**. Escriba el número de segundos, minutos, horas o días que deben pasar para la siguiente comprobación.

      ![Detalles del desencadenador de aplicación lógica](./media/connectors-create-api-crmonline/trigger-details.png)

10. Haga clic en **Nuevo paso** y, a continuación, en **Agregar una acción**.

11. En el cuadro de búsqueda, escriba `Dynamics 365`. En la lista de acciones, seleccione **Dynamics 365: Crear un nuevo registro**.

12. Escriba la siguiente información:

    * **Nombre de la organización**. Seleccione la instancia de Dynamics 365 en la que desea que el flujo cree el registro. 
    Tenga en cuenta que no tiene que ser la misma instancia desde la que se desencadena el evento.

    * **Nombre de entidad**. Seleccione la entidad que desea que cree un registro cuando se desencadene el evento. 
    En este tutorial está seleccionado **Tasks**.

13. Haga clic en el cuadro **Asunto** que aparece. En la lista de contenido dinámica que aparece, puede seleccionar cualquiera de estos campos:

    * **Apellido**. Al seleccionar este campo, se inserta el apellido del cliente potencial en el campo de Asunto de la tarea, cuando se cree el registro de tareas.
    * **Tema**. Al seleccionar este campo, se inserta el campo Tema del cliente potencial en el campo de Asunto de la tarea, cuando se cree el registro de tareas. 
    Haga clic en **Tema** para agregarlo al cuadro **Asunto**.

      ![Creación de detalles del nuevo registro en la aplicación lógica](./media/connectors-create-api-crmonline/create-record-details.png)

14. Haga clic en **Guardar** en la barra de herramientas del Diseñador de aplicación lógica.

    ![Guardado en la barra de herramientas del Diseñador de aplicación lógica](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. Para iniciar la aplicación lógica, haga clic en **Ejecutar**.

    ![Guardado en la barra de herramientas del Diseñador de aplicación lógica](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Ahora cree un registro de cliente potencial en Dynamics 365 para Ventas y podrá ver el flujo en acción.

## <a name="set-advanced-options-for-a-logic-app-step"></a>Establecimiento de opciones avanzadas para un paso de aplicación lógica

Para especificar cómo filtrar datos en un paso de la aplicación de lógica, haga clic en **Mostrar opciones avanzadas** en ese paso y agregue un filtro u ordenar por consulta.

Por ejemplo, puede usar una consulta de filtro para recuperar solo cuentas activas y ordenar según el nombre de la cuenta. Para realizar esta tarea, escriba la consulta de filtro OData `statuscode eq 1` y seleccione **Nombre de cuenta** en la lista de contenido dinámica. Para más información consulte: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) y [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Opciones avanzadas de la aplicación lógica](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Procedimientos recomendados al usar las opciones avanzadas

Cuando agregue un valor a un campo, el tipo de campo tiene que coincidir, tanto si escribe el valor como si lo selecciona de la lista de contenido dinámica.

Tipo de campo  |Modo de uso  |Dónde encontrarlo  |NOMBRE  |Tipo de datos  
---------|---------|---------|---------|---------
Campos de texto|Los campos de texto requieren una sola línea de texto o contenido dinámico que sea un campo de tipo texto. Algunos ejemplos son los campos de categoría y subcategoría.|Configuración > Personalizaciones > Personalizar el sistema > Entidades > Tarea > Campos |categoría |Línea de texto única        
Campos numéricos enteros | Algunos campos requieren un número entero o un contenido dinámico que sea un campo de tipo numérico entero. Algunos ejemplos son Porcentaje completado y Duración. |Configuración > Personalizaciones > Personalizar el sistema > Entidades > Tarea > Campos |percentcomplete |Número entero         
Campos de fecha | Algunos campos, requieren una fecha escrita en formato mm/dd/aaaa o contenido dinámico que sea un campo de tipo de fecha. Algunos ejemplos son fecha de creación, fecha de inicio, inicio real, último periodo de retención, finalización real y fecha de vencimiento. | Configuración > Personalizaciones > Personalizar el sistema > Entidades > Tarea > Campos |createdon |Fecha y hora
Campos que requieren un identificador de registro y un tipo de búsqueda |Algunos campos que hacen referencia a otro registro de entidad requieren el identificador de registro y el tipo de búsqueda. |Configuración > Personalizaciones > Personalizar el sistema > Entidades > Cuenta > Campos  | accountid  | Clave principal

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Más ejemplos de campos que requieren tanto un identificador de registro como un tipo de búsqueda
Ampliando la tabla anterior, aquí tiene más ejemplos de campos que no funcionan con valores seleccionados de la lista de contenido dinámica. En su lugar, estos campos requieren tanto un identificador de registro como un tipo de búsqueda especificados en los campos en PowerApps.  
* Propietario y Tipo de propietario. El campo Propietario tiene que ser un usuario válido o un identificador de registro de equipo. El Tipo de propietario tiene que ser **systemusers** o **equipos**.
* Cliente y Tipo de cliente. El campo Cliente tiene que ser una cuenta válida o un identificador de registro de contacto. El Tipo de propietario tiene que ser **cuentas** o **contactos**.
* Referente a y Tipo referente a. El campo Referente a tiene que ser un identificador de registro válido, como una cuenta o un identificador de registro de contacto. El Tipo referente a tiene que ser el tipo de búsqueda para el registro, como **cuentas** o **contactos**.

El siguiente ejemplo de acción de creación de tarea agrega un registro de cuenta que corresponde al identificador de registro, agregándolo al campo Referente a de la tarea.

![Identificador de registro de flujo y tipo de cuenta](./media/connectors-create-api-crmonline/recordid-type-account.png)

Este ejemplo también asigna la tarea a un usuario específico basado en el identificador de registro. del usuario.

![Identificador de registro de flujo y tipo de cuenta](./media/connectors-create-api-crmonline/recordid-type-user.png)

Para obtener un identificador de registro, consulte la siguiente sección *Localización del identificador de registro*.

## <a name="find-the-record-id"></a>Localización del identificador de registro

1. Abra un registro, como un registro de cuenta.

2. En la barra de herramientas de acciones, haga clic en el **emergente** ![registro emergente](./media/connectors-create-api-crmonline/popout-record.png).
O bien, en la barra de herramientas de acciones, haga clic en **ENVIAR UN VÍNCULO POR CORREO ELECTRÓNICO** para copiar la dirección URL completa en el programa de correo electrónico predeterminado.

   El identificador de registro se muestra entre los caracteres codificados %7b y %7d de la dirección URL.

   ![Identificador de registro de flujo y tipo de cuenta](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>solución de problemas
Para solucionar problemas de un paso con errores en una aplicación de lógica, vea los detalles de estado del evento.

1. En **Logic Apps**, seleccione la aplicación lógica y haga clic en **Información general**. 

   Se muestra el área de resumen y se proporciona el estado de ejecución de la aplicación lógica. 

   ![Estado de ejecución de la aplicación lógica](./media/connectors-create-api-crmonline/tshoot1.png)

2. Para ver más información sobre cualquier ejecución que haya fallado, haga clic en el evento con error. Para expandir un paso con errores, haga clic en ese paso.

   ![Expansión del paso con errores](./media/connectors-create-api-crmonline/tshoot2.png)

   Los detalles del paso aparecen y pueden ayudar a solucionar la causa del error.

   ![Detalles del paso con errores](./media/connectors-create-api-crmonline/tshoot3.png)

Para más información sobre cómo solucionar problemas de las aplicaciones lógicas, consulte [Diagnóstico de errores de aplicaciones lógicas](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/crm/). 

## <a name="next-steps"></a>pasos siguientes
Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).

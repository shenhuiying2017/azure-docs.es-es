---
title: "Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps | Microsoft Docs"
description: "Compruebe si hay cambios de configuración en las máquinas virtuales (VM) mediante el uso de Azure Event Grid y Logic Apps"
keywords: "aplicaciones lógicas, cuadrículas de eventos, máquina virtual, VM"
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 08/16/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 4d4c16860dbec10162797a13c8f9f57106abd17f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps

Puede iniciar automáticamente el [flujo de trabajo de una aplicación lógica](../logic-apps/logic-apps-what-are-logic-apps.md) cuando se producen eventos específicos en recursos de Azure o de otros fabricantes. Estos recursos pueden publicar esos eventos en una [cuadrícula de eventos Azure](../event-grid/overview.md). A su vez, la cuadrícula de eventos envía esos eventos a los suscriptores que tienen colas, webhooks o [centros de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) como puntos de conexión. Como suscriptor, su aplicación lógica puede esperar esos eventos desde la cuadrícula de eventos antes de ejecutar flujos de trabajo automatizados para llevar a cabo ciertas tareas, sin escribir código.

Por ejemplo, aquí incluimos algunos eventos que los publicadores pueden enviar a los suscriptores a través del servicio Azure Event Grid:

* Crear, leer, actualizar o eliminar un recurso. Por ejemplo, puede supervisar los cambios que podrían incurrir en gastos en su suscripción de Azure y afectar a la facturación. 
* Agregar o quitar a una persona de una suscripción de Azure.
* La aplicación realiza una acción concreta.
* Aparece un nuevo mensaje en una cola.

En este tutorial se crea una aplicación lógica que supervisa los cambios realizados en una máquina virtual y envía mensajes de correo electrónico sobre dichos cambios. Cuando crea una aplicación lógica con una suscripción a eventos de un recurso de Azure, los eventos fluyen desde ese recurso a través de una cuadrícula de eventos a la aplicación lógica. El tutorial le guía en la creación de esta aplicación lógica:

![Introducción: Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación lógica que supervisa eventos procedentes de una cuadrícula de eventos.
> * Incorporar una condición que comprueba específicamente cambios en la máquina virtual.
> * Enviar un mensaje de correo electrónico cuando la máquina virtual cambia.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de correo electrónico de [cualquier proveedor de correo electrónico que sea compatible con Azure Logic Apps](../connectors/apis-list.md), como Office 365 Outlook, Outlook.com o Gmail, para el envío de notificaciones. Este tutorial usa Office 365 Outlook.

* Una [máquina virtual](https://azure.microsoft.com/services/virtual-machines). Si aún no lo ha hecho, cree una máquina virtual siguiendo un [tutorial para crear una máquina virtual](https://docs.microsoft.com/azure/virtual-machines/). Para hacer que la máquina virtual publique eventos, [no es necesario hacer nada más](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Creación de una aplicación lógica que supervisa eventos procedentes de una cuadrícula de eventos

En primer lugar, cree una aplicación lógica y agregue un desencadenador de la cuadrícula de eventos que supervise el grupo de recursos de la máquina virtual. 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 

2. En la esquina superior izquierda del menú principal de Azure, elija **Nuevo** > **Integración empresarial** > **Aplicación lógica**.

   ![Creación de la aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Crear la aplicación lógica con los valores especificados en la tabla siguiente:

   ![Proporcione los detalles de la aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Name** | *{nombre-de-la-aplicación-lógica}* | Proporcione un nombre único de aplicación lógica. | 
   | **Suscripción** | *{su-suscripción-de-Azure}* | Seleccione la misma suscripción de Azure para todos los servicios de este tutorial. | 
   | **Grupos de recursos** | *{su-grupo-de-recursos-de-Azure}* | Seleccione el mismo grupo de recursos de Azure para todos los servicios de este tutorial. | 
   | **Ubicación** | *{su-región-de-Azure}* | Seleccione la misma región para todos los servicios de este tutorial. | 
   | | | 

4. Cuando esté listo, elija **Anclar al panel** y elija **Crear**.

   Ahora ha creado un recurso de Azure para la aplicación lógica. 
   Después de que Azure implemente la aplicación lógica, el Diseñador de aplicaciones lógicas muestra plantillas de patrones comunes de modo que pueda empezar con mayor rapidez.

   > [!NOTE] 
   > Cuando selecciona **Anclar al panel**, la aplicación lógica se abre automáticamente en el Diseñador de aplicaciones lógicas. En caso contrario, puede buscarla y abrirla manualmente.

5. Ahora elija una plantilla de aplicación lógica. En **Plantillas**, elija **Aplicación lógica en blanco** para que pueda crear la aplicación lógica desde el principio.

   ![Selección de la plantilla de aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   El Diseñador de aplicaciones lógicas ahora muestra los [ *conectores* ](../connectors/apis-list.md) y [ *desencadenadores* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) que puede usar para iniciar la aplicación lógica, además de las acciones que puede agregar después de un desencadenador para realizar tareas. Un desencadenador es un evento que crea una instancia de aplicación lógica e inicia el flujo de trabajo de la aplicación lógica. 
   La aplicación lógica necesita un desencadenador como primer elemento.

6. En el cuadro de búsqueda, escriba "cuadrícula de eventos" como filtro. Seleccione este desencadenador: **Azure Event Grid - On a resource event**

   ![Seleccione este desencadenador: "Azure Event Grid - On a resource event"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Cuando se le solicite, inicie sesión en Azure Event Grid con sus credenciales de Azure.

   ![Inicie sesión con sus credenciales de Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Si inició sesión con una cuenta personal de Microsoft, como @outlook.com o @hotmail.com, el desencadenador de Event Grid podría no aparecer correctamente. Como alternativa, elija [Conectar con entidad de servicio](/azure-resource-manager/resource-group-create-service-principal-portal.md) o autenticarse como un miembro de Azure Active Directory que esté asociado a la suscripción de Azure, por ejemplo, *nombre-de-usuario*@emailoutlook.onmicrosoft.com.

8. Ahora suscriba la aplicación lógica a los eventos de publicador. Proporcione los detalles de la suscripción a eventos como se especifica en la tabla siguiente:

   ![Proporcione detalles de la suscripción de eventos](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Suscripción** | *{suscripción-de-Azure-de-la-máquina-virtual}* | Seleccione la suscripción de Azure del publicador de eventos. En este tutorial, seleccione la suscripción de Azure de la máquina virtual. | 
   | **Tipo de recurso** | Microsoft.Resources.resourceGroups | Seleccione el tipo de recurso del publicador de eventos. Para este tutorial, seleccione el valor especificado para que la aplicación lógica supervise solo los grupos de recursos. | 
   | **Nombre de recurso** | *{nombre-del-grupo-de-recursos-de-la-máquina-virtual}* | Seleccione el nombre de recurso del publicador. En este tutorial, seleccione el nombre del grupo de recursos para la máquina virtual. | 
   | Para configuraciones opcionales, elija **Mostrar opciones avanzadas**. | *{ver descripciones}* | * **Filtro de prefijo**: en este tutorial, deje esta opción vacía. El comportamiento predeterminado coincide con todos los valores. Sin embargo, puede especificar una cadena de prefijo como un filtro, por ejemplo, una ruta de acceso y un parámetro para un recurso concreto. <p>* **Filtro de sufijo**: en este tutorial, deje esta opción vacía. El comportamiento predeterminado coincide con todos los valores. Sin embargo, puede especificar una cadena de sufijo como un filtro, por ejemplo, una extensión de nombre de archivo, si desea solo determinados tipos de archivos.<p>* **Nombre de la suscripción**: proporcione un nombre único para la suscripción a eventos. |
   | | | 

   Cuando haya terminado, el desencadenador de Event Grid podría parecerse a este ejemplo:
   
   ![Detalles del desencadenador de cuadrícula de eventos de ejemplo](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Guarde la aplicación lógica. En la barra de herramientas del diseñador, haga clic en **Guardar**. Para contraer y ocultar los detalles de una acción en la aplicación lógica, elija la barra de título de la acción.

   ![Guardado de la aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Cuando se guarda la aplicación lógica con un desencadenador de Event Grid, Azure crea automáticamente una suscripción a eventos para la aplicación lógica en el recurso seleccionado. Así, cuando el recurso publica un evento en la cuadrícula de eventos, esa cuadrícula de eventos inserta automáticamente el evento en la aplicación lógica. Este evento desencadena la aplicación lógica, que crea y ejecuta una instancia del flujo de trabajo que se define en los pasos siguientes.

La aplicación lógica ahora está activa y escucha los eventos de Event Grid, pero no hace nada hasta que agregue acciones al flujo de trabajo. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Incorporación de una condición que comprueba los cambios de la máquina virtual

Para ejecutar la aplicación lógica solo cuando se produzca un evento específico, agregue una condición que compruebe si hay operaciones de escritura en la máquina virtual. Cuando esta condición se cumpla, la aplicación lógica le enviará un mensaje de correo electrónico con detalles acerca de la máquina virtual actualizada.

1. En el Diseñador de aplicaciones lógicas, bajo el desencadenador de la cuadrícula de eventos, elija **Nuevo paso** > **Agregar una condición**.

   ![Incorporación de una condición a la aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   El Diseñador de aplicaciones lógicas agrega una condición vacía al flujo de trabajo, incluidas las rutas de acceso de la acción que se deben seguir en función de si la condición se cumple o no.

   ![Condición vacía](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. En el cuadro **Condición**, elija **Editar en el modo avanzado**.
Escriba esta expresión:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   Ahora, la condición es similar a este ejemplo:

   ![Condición vacía](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Esta expresión comprueba el evento `body` para un objeto `data` cuya propiedad `operationName` es la operación `Microsoft.Compute/virtualMachines/write`. 
   Más información sobre el [Esquema de eventos de la cuadrícula de eventos](../event-grid/event-schema.md).

3. Para proporcionar una descripción para la condición, elija el botón de **puntos suspensivos** (**...** ) situado en la forma de la condición y luego elija **Cambiar nombre**.

   > [!NOTE] 
   > Los ejemplos siguientes de este tutorial también proporcionan descripciones para los pasos del flujo de trabajo de la aplicación lógica.

4. Ahora elija **Editar en modo básico** para que la expresión se resuelva automáticamente como se muestra:

   ![Condición de aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Guarde la aplicación lógica.

## <a name="send-email-when-your-virtual-machine-changes"></a>Envío de un mensaje de correo electrónico cuando la máquina virtual cambia

Ahora, agregue una [ *acción* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) para recibir un mensaje de correo electrónico cuando se cumpla la condición especificada.

1. En el cuadro **If true** (Si es true) de la condición, elija **Agregar una acción**.

   ![Agregar una acción para cuando la condición se cumpla](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. En el cuadro de búsqueda, escriba "email" como filtro. En función de su proveedor de correo electrónico, busque y seleccione el conector correspondiente. A continuación, seleccione la acción "enviar correo electrónico" para el conector. Por ejemplo: 

   * Para una cuenta profesional o educativa de Azure, seleccione el conector de Office 365 Outlook. 
   * Para las cuentas de Microsoft personales, seleccione el conector de Outlook.com. 
   * Para las cuentas de Gmail, seleccione el conector de Gmail. 

   Vamos a continuar con el conector de Office 365 Outlook. 
   Si utiliza un proveedor diferente, los pasos siguen siendo los mismos, pero la interfaz de usuario podría ser diferente. 

   ![Seleccione la acción "send email"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Si ya no tiene una conexión para el proveedor de correo electrónico, inicie sesión en su cuenta de correo electrónico cuando se le pida que se autentique.

4. Proporcione detalles para el correo electrónico como se especifica en la tabla siguiente:

   ![Acción de correo electrónico vacía](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Para seleccionar desde los campos disponibles en el flujo de trabajo, haga clic en un cuadro de edición para que se abra la lista **Contenido dinámico** o elija **Agregar contenido dinámico**. Para más campos, elija **Ver más** para cada sección de la lista. Para cerrar la lista **Contenido dinámico** elija **Agregar contenido dinámico**.

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **To** | *{dirección-de-correo electrónico-del-destinatario}* |Escriba la dirección de correo electrónico del destinatario. Para realizar pruebas, puede usar su propia dirección de correo electrónico. | 
   | **Asunto** | Recurso actualizado: **Asunto**| Escriba el contenido del asunto del correo electrónico. Para este tutorial, escriba el texto sugerido y seleccione el campo **Asunto** del evento. En este caso, el asunto del correo electrónico incluye el nombre del recurso actualizado (máquina virtual). | 
   | **Cuerpo** | Grupo de recursos: **Tema** <p>Tipo de evento: **Tipo de evento**<p>Identificador del evento: **ID**<p>Hora: **Hora del evento** | Escriba el contenido del cuerpo del correo electrónico. Para este tutorial, escriba el texto sugerido y seleccione los campos **Tema**, **Tipo de evento**, **ID** y **Hora del evento** del evento para que el correo electrónico incluya el nombre del grupo de recursos, tipo de evento, marca de tiempo del evento e identificador del evento de la actualización. <p>Para agregar líneas en blanco en el contenido, presione MAYÚS + ENTRAR. | 
   | | | 

   > [!NOTE] 
   > Si selecciona un campo que representa una matriz, el diseñador agrega automáticamente un bucle **For each** alrededor de la acción que haga referencia a la matriz. De este modo, la aplicación lógica realiza la acción en cada elemento de la matriz.

   Ahora, la acción de correo electrónico podría parecerse a este ejemplo:

   ![Selección de los datos que se incluirán en el correo electrónico](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Y la aplicación lógica terminada podría parecerse a este ejemplo:

   ![Aplicación lógica terminada](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Guarde la aplicación lógica. Para contraer y ocultar los detalles de cada acción en la aplicación lógica, elija la barra de título de la acción.

   ![Guardado de la aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   La aplicación lógica ahora está activa, pero espera a que haya cambios en la máquina virtual antes de hacer algo. 
   Para probar la aplicación lógica ahora, pase a la sección siguiente.

## <a name="test-your-logic-app-workflow"></a>Comprobación del flujo de trabajo de la aplicación lógica

1. Para comprobar que la aplicación lógica está recibiendo los eventos especificados, actualice la máquina virtual. 

   Por ejemplo, puede cambiar el tamaño de la máquina virtual en Azure Portal o [cambiar el tamaño de la máquina virtual con Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Transcurridos unos instantes, debería recibir un correo electrónico. Por ejemplo:

   ![Correo electrónico acerca de la actualización de una máquina virtual](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Para revisar el historial de ejecución y desencadenamiento de la aplicación lógica, elija **Overview** (Información general). Para ver más detalles acerca de una ejecución, elija la fila correspondiente a esa ejecución.

   ![Historial de ejecuciones de la aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Para ver las entradas y salidas de cada paso, expanda el paso que desea revisar. Esta información puede ayudarle a diagnosticar y depurar los problemas de la aplicación lógica.
 
   ![Detalles del historial de ejecución de la aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Enhorabuena, ha creado y ejecutado una aplicación lógica que supervisa los eventos del recurso mediante una cuadrícula de eventos y le envía por correo electrónico un mensaje cuando se producen esos eventos. También ha aprendido cómo puede crear fácilmente flujos de trabajo que automatizan los procesos y a integrar sistemas y servicios en la nube.

## <a name="clean-up-resources"></a>Limpieza de recursos

Este tutorial utiliza recursos y realiza acciones que generan gastos en su suscripción de Azure. Cuando haya terminado con el tutorial y las pruebas, asegúrese de deshabilitar o eliminar todos los recursos si no desea que generen gastos.

Puede detener la ejecución de la aplicación lógica y el envío de correo electrónico sin eliminar la aplicación. En el menú de la aplicación lógica, elija **Overview** (Información general). En la barra de herramientas, elija **Deshabilitar**.

![Desactive la aplicación lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>P+F

**P**: ¿Qué otras tareas de supervisión de máquinas virtuales puedo realizar con cuadrículas de eventos y aplicaciones lógicas? </br>
**R**: Puede supervisar otros cambios de configuración, por ejemplo:

* Una máquina virtual obtiene derechos de control de acceso basado en roles (RBAC).
* Se realizan cambios en un grupo de seguridad de red (NSG) en una interfaz de red (NIC).
* Los discos para una máquina virtual se agregan o se quitan.
* Se asigna una dirección IP pública a una máquina virtual de NIC.

## <a name="next-steps"></a>Pasos siguientes

* [Información general sobre la cuadrícula de eventos](../event-grid/overview.md)
* [Conceptos sobre la cuadrícula de eventos](../event-grid/concepts.md)
* [Inicio rápido: Creación y enrutamiento de eventos personalizados con Azure Event Grid](../event-grid/custom-event-quickstart.md)
* [Esquema de eventos de Event Grid](../event-grid/event-schema.md)
* [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)
* [Creación de flujos de trabajo de aplicación lógica con plantillas predefinidas](../logic-apps/logic-apps-use-logic-app-templates.md)
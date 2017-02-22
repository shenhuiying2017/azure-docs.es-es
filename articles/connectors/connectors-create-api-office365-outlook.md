---
title: "Adición del conector de Office 365 Outlook a Logic Apps | Microsoft Docs"
description: "Cree aplicaciones lógicas con el conector de Office 365 para habilitar la interacción con Office 365. Por ejemplo: crear, editar y actualizar contactos y elementos de calendario."
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 92c0892128655141f29380890c31451e62ca8853


---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Introducción al conector de Office 365 Outlook
El conector de Office 365 Outlook permite la interacción con Outlook en Office 365. Utilice este conector para crear, editar y actualizar elementos de calendario y contactos, y también recibir, enviar y responder al correo electrónico.

Con Office 365 Outlook:

* Creará el flujo de trabajo con las características de correo electrónico y calendario de Office 365. 
* Usará desencadenadores para iniciar el flujo de trabajo cuando reciba un correo electrónico nuevo, se actualice un elemento de calendario, etc.
* Usará acciones para enviar correos electrónicos, crear eventos de calendario y mucho más. Por ejemplo, al recibir un nuevo objeto de Salesforce (un desencadenador), enviar de un correo electrónico a Office 365 Outlook (acción). 

En este tema se muestra cómo usar el conector de Office 365 Outlook en una aplicación lógica, y se enumeran los desencadenadores y las acciones.

> [!NOTE]
> Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas.
> 
> 

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-what-are-logic-apps.md) y [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-office-365"></a>Conectarse a Office 365
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a Office 365 Outlook, primero necesita una *conexión* de Office 365. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que desea conectarse. Por lo tanto, para crear la conexión con Office 365 Outlook, escriba las credenciales de la cuenta de Office 365.

## <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Uso de un desencadenador
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. Los desencadenadores "sondean" el servicio en el intervalo y la frecuencia que desee. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. En la aplicación lógica, escriba "office 365" para obtener una lista de los desencadenadores:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Seleccione **Office 365 Outlook - Cuando un evento próximo va a comenzar pronto**. Si ya existe una conexión, seleccione un calendario de la lista desplegable.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Si se le solicita que inicie sesión, escriba los datos de inicio de sesión para crear la conexión. En la sección [Creación de la conexión](connectors-create-api-office365-outlook.md#create-the-connection) de este tema se enumeran los pasos. 
   
   > [!NOTE]
   > En este ejemplo, la aplicación lógica se ejecuta cuando se actualiza un evento de calendario. Para ver los resultados de este desencadenador, agregue otra acción que le envíe un mensaje de texto. Por ejemplo, agregue la acción de Twilio *Enviar mensaje* que envía un mensaje de texto cuando quedan 15 minutos para que empiece el evento de calendario. 
   > 
   > 
3. Seleccione el botón **Editar** y defina los valores de **Frecuencia** e **Intervalo**. Por ejemplo, si desea que el desencadenador sondee cada 15 minutos, establezca el valor de **Frecuencia** en **Minuto** y el de **Intervalo** en **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Elija **Add an action**(Agregar una acción).
3. En el cuadro de texto, escriba "office 365" para obtener una lista de todas las acciones disponibles.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. En nuestro ejemplo, elija **Office 365 Outlook - Crear contacto**. Si ya existe una conexión, elija las propiedades **Folder ID** (Identificador de carpeta), **Given Name** (Nombre), etc.:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Si se le solicita la información de conexión, escriba los detalles para crear la conexión. Estas propiedades se describen en la sección [Creación de la conexión](connectors-create-api-office365-outlook.md#create-the-connection) de este tema. 
   
   > [!NOTE]
   > En este ejemplo, creamos un nuevo contacto en Office 365 Outlook. Para crear el contacto puede utilizar la salida de otro desencadenador. Por ejemplo, agregue el desencadenador de SalesForce *Cuando se crea un objeto*. Después, agregue la acción *Crear contacto* de Office 365 Outlook, que usa los campos de SalesForce para crear el contacto nuevo en Office 365. 
   > 
   > 
5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="technical-details"></a>Detalles técnicos
Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## <a name="office-365-triggers"></a>Desencadenadores de Office 365
| Desencadenador | Description |
| --- | --- |
| [Cuando un evento próximo va a comenzar pronto](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon) |Con esta operación se desencadena un flujo cuando se va a iniciar un evento próximamente. |
| [Cuando llega un nuevo correo electrónico](connectors-create-api-office365-outlook.md#when-a-new-email-arrives) |Con esta operación se desencadena un flujo cuando llega un nuevo correo electrónico. |
| [Cuando se crea un evento nuevo](connectors-create-api-office365-outlook.md#when-a-new-event-is-created) |Con esta operación se desencadena un flujo cuando se crea un evento nuevo en un calendario. |
| [Cuando se modifica un evento](connectors-create-api-office365-outlook.md#when-an-event-is-modified) |Con esta operación se desencadena un flujo cuando se modifica un evento en un calendario. |

## <a name="office-365-actions"></a>Acciones de Office 365
| Acción | Description |
| --- | --- |
| [Obtener correos electrónicos](connectors-create-api-office365-outlook.md#get-emails) |Con esta operación se obtienen mensajes de correo electrónico de una carpeta. |
| [Enviar un correo electrónico](connectors-create-api-office365-outlook.md#send-an-email) |Con esta operación se envía un mensaje de correo electrónico. |
| [Eliminar correo electrónico](connectors-create-api-office365-outlook.md#delete-email) |Con esta operación se eliminan correos electrónicos por identificador. |
| [Marcar como leído](connectors-create-api-office365-outlook.md#mark-as-read) |Con esta operación se marca el correo electrónico como leído. |
| [Responder al correo](connectors-create-api-office365-outlook.md#reply-to-email) |Con esta operación se responde a un correo electrónico. |
| [Obtener datos adjuntos](connectors-create-api-office365-outlook.md#get-attachment) |Con esta operación se obtienen datos adjuntos de correo electrónico por identificador. |
| [Enviar correo electrónico con opciones](connectors-create-api-office365-outlook.md#send-email-with-options) |Con esta operación se envía un correo electrónico con varias opciones y se espera una respuesta del destinatario con una de las opciones. |
| [Enviar correo electrónico de aprobación](connectors-create-api-office365-outlook.md#send-approval-email) |Con esta operación se envía un correo electrónico de aprobación y se espera una respuesta del destinatario. |
| [Obtener calendarios](connectors-create-api-office365-outlook.md#get-calendars) |Con esta operación se enumeran los calendarios disponibles. |
| [Obtener eventos](connectors-create-api-office365-outlook.md#get-events) |Con esta operación se obtienen los eventos de calendario. |
| [Crear evento](connectors-create-api-office365-outlook.md#create-event) |Con esta operación se crea un nuevo evento de calendario. |
| [Obtener evento](connectors-create-api-office365-outlook.md#get-event) |Con esta operación se obtienen eventos específicos de un calendario. |
| [Eliminar evento](connectors-create-api-office365-outlook.md#delete-event) |Con esta operación se eliminan eventos de calendario. |
| [Actualizar evento](connectors-create-api-office365-outlook.md#update-event) |Con esta operación se actualizan eventos de calendario. |
| [Obtener carpetas de contactos](connectors-create-api-office365-outlook.md#get-contact-folders) |Con esta operación se enumeran las carpetas de contactos disponibles. |
| [Obtener contactos](connectors-create-api-office365-outlook.md#get-contacts) |Con esta operación se recuperan contactos de una carpeta. |
| [Crear contacto](connectors-create-api-office365-outlook.md#create-contact) |Con esta operación se crean contactos en una carpeta. |
| [Obtener contacto](connectors-create-api-office365-outlook.md#get-contact) |Con esta operación se obtienen contactos específicos de una carpeta. |
| [Eliminar contacto](connectors-create-api-office365-outlook.md#delete-contact) |Con esta operación se eliminan contactos de una carpeta. |
| [Actualizar contacto](connectors-create-api-office365-outlook.md#update-contact) |Con esta operación se actualizan contactos en una carpeta. |

### <a name="trigger-and-action-details"></a>Detalles de los desencadenadores y las acciones
En esta sección podrá consultar los detalles específicos acerca de los desencadenadores y las acciones, como las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### <a name="when-an-upcoming-event-is-starting-soon"></a>When an upcoming event is starting soon
Con esta operación se desencadena un flujo cuando se va a iniciar un evento próximamente. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Calendar id (Identificador de calendario) |Identificador único del calendario |
| lookAheadTimeInMinutes |Look ahead time (Tiempo para inicio) |Tiempo (en minutos) por delante para buscar próximos eventos |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
CalendarItemsList: Lista de elementos de calendario

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| value |array |Lista de elementos de calendario |

#### <a name="get-emails"></a>Obtener correos electrónicos
Con esta operación se obtienen mensajes de correo electrónico de una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderPath |Ruta de acceso a la carpeta |Ruta de acceso de la carpeta para recuperar mensajes (predeterminada: 'Bandeja de entrada') |
| top |Top (Máximo) |Número de correos electrónicos para recuperar (valor predeterminado: 10) |
| fetchOnlyUnread |Fetch Only Unread Messages (Recuperar solo mensajes no leídos) |¿Recuperar solo correos electrónicos no leídos? |
| includeAttachments |Include Attachments (Incluir datos adjuntos) |Si se establece en true, los datos adjuntos también se recuperarán junto con el correo electrónico |
| searchQuery |Search Query (Consulta de búsqueda) |Consulta de búsqueda para filtrar correos electrónicos |
| skip |Skip |Número de correos electrónicos para omitir (valor predeterminado: 0) |
| skipToken |Skip Token (Token de omisión) |Omitir token para obtener la página nueva |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ReceiveMessage: Recibir mensaje de correo electrónico

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| De |cadena |De |
| Para |cadena |Para |
| Asunto |cadena |Asunto |
| Cuerpo |cadena |Cuerpo |
| Importancia |cadena |Importancia |
| HasAttachment |boolean |Tiene datos adjuntos |
| Id |cadena |Id. de mensaje |
| IsRead |boolean |Es de lectura |
| DateTimeReceived |cadena |Fecha y hora de recibo |
| Datos adjuntos |array |Datos adjuntos |
| Cc |cadena |Las direcciones de correo electrónico, como someone@contoso.com, se separan con punto y coma. |
| CCO |cadena |Las direcciones de correo electrónico, como someone@contoso.com, se separan con punto y coma. |
| IsHtml |boolean |Es HTML |

#### <a name="send-an-email"></a>un correo electrónico
Con esta operación se envía un mensaje de correo electrónico. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| emailMessage* |Email |Email |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="delete-email"></a>Eliminar correo electrónico
Con esta operación se eliminan correos electrónicos por identificador. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| messageId* |Id. de mensaje |Identificador del correo electrónico que se va a eliminar |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="mark-as-read"></a>Marcar como leído
Con esta operación se marca el correo electrónico como leído. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| messageId* |Id. de mensaje |Identificador del correo electrónico que se va a marcar como leído |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="reply-to-email"></a>Reply to email
Con esta operación se responde a un correo electrónico. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| messageId* |Id. de mensaje |Identificador del correo electrónico al que se va a responder |
| comment* |Comentario |Comentario de respuesta |
| replyAll |Reply All (Responder a todos) |Responder a todos los destinatarios |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="get-attachment"></a>Obtener datos adjuntos
Con esta operación se obtienen datos adjuntos de correo electrónico por identificador. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| messageId* |Id. de mensaje |Identificador del correo electrónico |
| attachmentId* |Attachment Id (Identificador de datos adjuntos) |Identificador de los datos adjuntos para descargar |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="when-a-new-email-arrives"></a>When a new email arrives
Con esta operación se desencadena un flujo cuando llega un nuevo correo electrónico.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderPath |Ruta de acceso a la carpeta |Carpeta de correo electrónico para recuperar (predeterminada: Bandeja de entrada) |
| to |Para |Direcciones de correo electrónico de destinatarios |
| from |De |Dirección De |
| importance |Importancia |Importancia del correo electrónico (alta, normal o baja) (predeterminada: Normal) |
| fetchOnlyWithAttachment |Has Attachments (Con datos adjuntos) |Recuperar solo correos electrónicos con datos adjuntos |
| includeAttachments |Include Attachments (Incluir datos adjuntos) |Incluir datos adjuntos |
| subjectFilter |Subject Filter (Filtro de asunto) |Cadena que se va a buscar en el asunto |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
TriggerBatchResponse[ReceiveMessage]

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="send-email-with-options"></a>Enviar correo electrónico con opciones
Con esta operación se envía un correo electrónico con varias opciones y se espera una respuesta del destinatario con una de las opciones. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| optionsEmailSubscription* |Solicitud de suscripción para correo electrónico con opciones |Solicitud de suscripción para correo electrónico con opciones |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
SubscriptionResponse: Modelo de suscripción de correo electrónico de aprobación

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| id |cadena |Identificador de la suscripción |
| resource |cadena |Recurso de la solicitud de suscripción |
| notificationType |cadena |Tipo de notificación |
| notificationUrl |cadena |URL de la notificación |

#### <a name="send-approval-email"></a>Enviar correo electrónico de aprobación
Con esta operación se envía un correo electrónico de aprobación y se espera una respuesta del destinatario. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| approvalEmailSubscription* |Solicitud de suscripción para correo electrónico de aprobación |Solicitud de suscripción para correo electrónico de aprobación |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
SubscriptionResponse: Modelo de suscripción de correo electrónico de aprobación

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| id |cadena |Identificador de la suscripción |
| resource |cadena |Recurso de la solicitud de suscripción |
| notificationType |cadena |Tipo de notificación |
| notificationUrl |cadena |URL de la notificación |

#### <a name="get-calendars"></a>Obtener calendarios
Con esta operación se enumeran los calendarios disponibles. 

No hay parámetros para esta llamada.

##### <a name="output-details"></a>Detalles de salida
TablesList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="get-events"></a>Obtención de eventos
Con esta operación se obtienen los eventos de calendario. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Calendar id (Identificador de calendario) |Selección de calendario |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para recuperar (valor predeterminado = 256) |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
CalendarEventList: Lista de elementos de calendario

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| value |array |Lista de elementos de calendario |

#### <a name="create-event"></a>Crear evento
Con esta operación se crea un nuevo evento de calendario. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Calendar id (Identificador de calendario) |Selección de calendario |
| item* |Elemento |Evento para crear |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
CalendarEvent: Tipo de modelo de evento de calendario específico del conector

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| Id |cadena |Identificador único del evento. |
| Attendees |array |Lista de asistentes al evento. |
| Cuerpo |not defined |Cuerpo del mensaje asociado al evento. |
| BodyPreview |cadena |Vista previa del cuerpo del mensaje asociado al evento. |
| Categorías |array |Categorías asociadas al evento. |
| ChangeKey |cadena |Identifica la versión del objeto del evento. Cada vez que el evento cambia, también lo hace ChangeKey. |
| DateTimeCreated |cadena |Fecha y la hora de creación del evento. |
| DateTimeLastModified |cadena |Fecha y la hora de la última modificación del evento. |
| End |cadena |hora de finalización del evento. |
| EndTimeZone |cadena |Especifica la zona horaria para la hora de finalización de la reunión. Este valor debe ser como se define en Windows (ejemplo: "Hora estándar del Pacífico"). |
| HasAttachments |boolean |Se establece en true si el evento tiene datos adjuntos. |
| Importancia |cadena |Importancia del evento: baja, normal o alta. |
| IsAllDay |boolean |Se establece en true si el evento dura todo el día. |
| IsCancelled |boolean |Se establece en true si el evento se ha cancelado. |
| IsOrganizer |boolean |Se establece en true si el remitente del mensaje es también el organizador. |
| Ubicación |not defined |Ubicación del evento. |
| Organizer |not defined |Organizador del evento. |
| Periodicidad |not defined |Patrón de periodicidad del evento. |
| Reminder |integer |Tiempo de recordatorio de inicio del evento en minutos. |
| ResponseRequested |boolean |Se establece en true si el remitente quiere recibir una respuesta cuando se acepte o se rechace el evento. |
| ResponseStatus |not defined |Indica el tipo de respuesta que se envía al recibir un mensaje de evento. |
| SeriesMasterId |cadena |Identificador único para el tipo de evento principal de la serie. |
| ShowAs |cadena |Se muestra como libre u ocupado. |
| Iniciar |cadena |Hora de inicio del evento. |
| StartTimeZone |cadena |Especifica la zona horaria para la hora de inicio de la reunión. Este valor debe ser como se define en Windows (ejemplo: "Hora estándar del Pacífico"). |
| Asunto |cadena |Asunto del evento. |
| Tipo |cadena |Tipo de evento: Instancia única, Repetición, Excepción o Series Master (Principal de serie). |
| WebLink |cadena |Vista previa del cuerpo del mensaje asociado al evento. |

#### <a name="get-event"></a>Obtener evento
Con esta operación se obtienen eventos específicos de un calendario. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Calendar id (Identificador de calendario) |Selección de calendario |
| id* |Item id (Identificador de elemento) |Selección de evento |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
CalendarEvent: Tipo de modelo de evento de calendario específico del conector

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| Id |cadena |Identificador único del evento. |
| Attendees |array |Lista de asistentes al evento. |
| Cuerpo |not defined |Cuerpo del mensaje asociado al evento. |
| BodyPreview |cadena |Vista previa del cuerpo del mensaje asociado al evento. |
| Categorías |array |Categorías asociadas al evento. |
| ChangeKey |cadena |Identifica la versión del objeto del evento. Cada vez que el evento cambia, también lo hace ChangeKey. |
| DateTimeCreated |cadena |Fecha y la hora de creación del evento. |
| DateTimeLastModified |cadena |Fecha y la hora de la última modificación del evento. |
| End |cadena |hora de finalización del evento. |
| EndTimeZone |cadena |Especifica la zona horaria para la hora de finalización de la reunión. Este valor debe ser como se define en Windows (ejemplo: "Hora estándar del Pacífico"). |
| HasAttachments |boolean |Se establece en true si el evento tiene datos adjuntos. |
| Importancia |cadena |Importancia del evento: baja, normal o alta. |
| IsAllDay |boolean |Se establece en true si el evento dura todo el día. |
| IsCancelled |boolean |Se establece en true si el evento se ha cancelado. |
| IsOrganizer |boolean |Se establece en true si el remitente del mensaje es también el organizador. |
| Ubicación |not defined |Ubicación del evento. |
| Organizer |not defined |Organizador del evento. |
| Periodicidad |not defined |Patrón de periodicidad del evento. |
| Reminder |integer |Tiempo de recordatorio de inicio del evento en minutos. |
| ResponseRequested |boolean |Se establece en true si el remitente quiere recibir una respuesta cuando se acepte o se rechace el evento. |
| ResponseStatus |not defined |Indica el tipo de respuesta que se envía al recibir un mensaje de evento. |
| SeriesMasterId |cadena |Identificador único para el tipo de evento principal de la serie. |
| ShowAs |cadena |Se muestra como libre u ocupado. |
| Iniciar |cadena |Hora de inicio del evento. |
| StartTimeZone |cadena |Especifica la zona horaria para la hora de inicio de la reunión. Este valor debe ser como se define en Windows (ejemplo: "Hora estándar del Pacífico"). |
| Asunto |cadena |Asunto del evento. |
| Tipo |cadena |Tipo de evento: Instancia única, Repetición, Excepción o Series Master (Principal de serie). |
| WebLink |cadena |Vista previa del cuerpo del mensaje asociado al evento. |

#### <a name="delete-event"></a>Eliminar evento
Con esta operación se eliminan eventos de calendario. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Calendar id (Identificador de calendario) |Selección de calendario |
| id* |Id |Selección de evento |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="update-event"></a>Actualizar evento
Con esta operación se actualizan eventos de calendario. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Calendar id (Identificador de calendario) |Selección de calendario |
| id* |Id |Selección de evento |
| item* |Elemento |Evento para actualizar |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
CalendarEvent: Tipo de modelo de evento de calendario específico del conector

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| Id |cadena |Identificador único del evento. |
| Attendees |array |Lista de asistentes al evento. |
| Cuerpo |not defined |Cuerpo del mensaje asociado al evento. |
| BodyPreview |cadena |Vista previa del cuerpo del mensaje asociado al evento. |
| Categorías |array |Categorías asociadas al evento. |
| ChangeKey |cadena |Identifica la versión del objeto del evento. Cada vez que el evento cambia, también lo hace ChangeKey. |
| DateTimeCreated |cadena |Fecha y la hora de creación del evento. |
| DateTimeLastModified |cadena |Fecha y la hora de la última modificación del evento. |
| End |cadena |hora de finalización del evento. |
| EndTimeZone |cadena |Especifica la zona horaria para la hora de finalización de la reunión. Este valor debe ser como se define en Windows (ejemplo: "Hora estándar del Pacífico"). |
| HasAttachments |boolean |Se establece en true si el evento tiene datos adjuntos. |
| Importancia |cadena |Importancia del evento: baja, normal o alta. |
| IsAllDay |boolean |Se establece en true si el evento dura todo el día. |
| IsCancelled |boolean |Se establece en true si el evento se ha cancelado. |
| IsOrganizer |boolean |Se establece en true si el remitente del mensaje es también el organizador. |
| Ubicación |not defined |Ubicación del evento. |
| Organizer |not defined |Organizador del evento. |
| Periodicidad |not defined |Patrón de periodicidad del evento. |
| Reminder |integer |Tiempo de recordatorio de inicio del evento en minutos. |
| ResponseRequested |boolean |Se establece en true si el remitente quiere recibir una respuesta cuando se acepte o se rechace el evento. |
| ResponseStatus |not defined |Indica el tipo de respuesta que se envía al recibir un mensaje de evento. |
| SeriesMasterId |cadena |Identificador único para el tipo de evento principal de la serie. |
| ShowAs |cadena |Se muestra como libre u ocupado. |
| Iniciar |cadena |Hora de inicio del evento. |
| StartTimeZone |cadena |Especifica la zona horaria para la hora de inicio de la reunión. Este valor debe ser como se define en Windows (ejemplo: "Hora estándar del Pacífico"). |
| Asunto |cadena |Asunto del evento. |
| Tipo |cadena |Tipo de evento: Instancia única, Repetición, Excepción o Series Master (Principal de serie). |
| WebLink |cadena |Vista previa del cuerpo del mensaje asociado al evento. |

#### <a name="when-a-new-event-is-created"></a>When a new event is created
Con esta operación se desencadena un flujo cuando se crea un evento nuevo en un calendario. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Calendar id (Identificador de calendario) |Selección de calendario |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para recuperar (valor predeterminado = 256) |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
CalendarItemsList: Lista de elementos de calendario

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| value |array |Lista de elementos de calendario |

#### <a name="when-an-event-is-modified"></a>When an event is modified
Con esta operación se desencadena un flujo cuando se modifica un evento en un calendario. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Calendar id (Identificador de calendario) |Selección de calendario |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para recuperar (valor predeterminado = 256) |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
CalendarItemsList: Lista de elementos de calendario

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| value |array |Lista de elementos de calendario |

#### <a name="get-contact-folders"></a>Obtener carpetas de contactos
Con esta operación se enumeran las carpetas de contactos disponibles. 

No hay parámetros para esta llamada.

##### <a name="output-details"></a>Detalles de salida
TablesList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="get-contacts"></a>Obtener contactos
Con esta operación se recuperan contactos de una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Folder id (Identificador de carpeta) |Identificador único de la carpeta de contactos que se va a recuperar |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir la devolución de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para recuperar (valor predeterminado = 256) |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ContactList: Lista de contactos

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| value |array |Lista de contactos |

#### <a name="create-contact"></a>Crear contacto
Con esta operación se crean contactos en una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Folder id (Identificador de carpeta) |Selección de una carpeta de contactos |
| item* |Elemento |Contacto para crear |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Contact: Contacto

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| Id |cadena |Identificador único del contacto. |
| ParentFolderId |cadena |Identificador de la carpeta del contacto principal. |
| Birthday |cadena |Cumpleaños del contacto. |
| FileAs |cadena |Nombre de archivo del contacto. |
| DisplayName |cadena |Nombre para mostrar del contacto. |
| GivenName |cadena |Nombre de pila del contacto. |
| Initials |cadena |Iniciales del contacto. |
| MiddleName |cadena |Segundo nombre del contacto. |
| NickName |cadena |Apodo del contacto. |
| Surname |cadena |Apellidos del contacto. |
| Título |cadena |Título del contacto. |
| Generation |cadena |Generación del contacto. |
| EmailAddresses |array |Direcciones de correo electrónico del contacto. |
| ImAddresses |array |Direcciones de mensajería instantánea (MI) del contacto. |
| JobTitle |cadena |Puesto del contacto. |
| CompanyName |cadena |Nombre de la empresa del contacto. |
| Departamento |cadena |Departamento del contacto. |
| OfficeLocation |cadena |Ubicación de la oficina del contacto. |
| Profession |cadena |Profesión del contacto. |
| BusinessHomePage |cadena |Página principal del negocio del contacto. |
| AssistantName |cadena |Nombre del asistente del contacto. |
| Manager |cadena |Nombre del gerente del contacto. |
| HomePhones |array |Números de teléfono particulares del contacto. |
| BusinessPhones |array |Números de teléfono de trabajo del contacto. |
| MobilePhone1 |cadena |Número de teléfono móvil del contacto. |
| HomeAddress |not defined |Dirección del domicilio del contacto. |
| BusinessAddress |not defined |Dirección de la oficina del contacto. |
| OtherAddress |not defined |Otras direcciones del contacto. |
| YomiCompanyName |cadena |Nombre fonético del negocio japonés del contacto. |
| YomiGivenName |cadena |Nombre de pila fonético (nombre) japonés del contacto. |
| YomiSurname |cadena |Apellido fonético (apellido) japonés del contacto. |
| Categorías |array |Categorías asociadas al contacto. |
| ChangeKey |cadena |Identifica la versión del objeto del evento. |
| DateTimeCreated |cadena |Hora a la que se creó el contacto. |
| DateTimeLastModified |cadena |Hora a la que se modificó el contacto. |

#### <a name="get-contact"></a>Obtener contacto
Con esta operación se obtienen contactos específicos de una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Folder id (Identificador de carpeta) |Selección de una carpeta de contactos |
| id* |Item id (Identificador de elemento) |Identificador único de un contacto para recuperar |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Contact: Contacto

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| Id |cadena |Identificador único del contacto. |
| ParentFolderId |cadena |Identificador de la carpeta del contacto principal. |
| Birthday |cadena |Cumpleaños del contacto. |
| FileAs |cadena |Nombre de archivo del contacto. |
| DisplayName |cadena |Nombre para mostrar del contacto. |
| GivenName |cadena |Nombre de pila del contacto. |
| Initials |cadena |Iniciales del contacto. |
| MiddleName |cadena |Segundo nombre del contacto. |
| NickName |cadena |Apodo del contacto. |
| Surname |cadena |Apellidos del contacto. |
| Título |cadena |Título del contacto. |
| Generation |cadena |Generación del contacto. |
| EmailAddresses |array |Direcciones de correo electrónico del contacto. |
| ImAddresses |array |Direcciones de mensajería instantánea (MI) del contacto. |
| JobTitle |cadena |Puesto del contacto. |
| CompanyName |cadena |Nombre de la empresa del contacto. |
| Departamento |cadena |Departamento del contacto. |
| OfficeLocation |cadena |Ubicación de la oficina del contacto. |
| Profession |cadena |Profesión del contacto. |
| BusinessHomePage |cadena |Página principal del negocio del contacto. |
| AssistantName |cadena |Nombre del asistente del contacto. |
| Manager |cadena |Nombre del gerente del contacto. |
| HomePhones |array |Números de teléfono particulares del contacto. |
| BusinessPhones |array |Números de teléfono de trabajo del contacto. |
| MobilePhone1 |cadena |Número de teléfono móvil del contacto. |
| HomeAddress |not defined |Dirección del domicilio del contacto. |
| BusinessAddress |not defined |Dirección de la oficina del contacto. |
| OtherAddress |not defined |Otras direcciones del contacto. |
| YomiCompanyName |cadena |Nombre fonético del negocio japonés del contacto. |
| YomiGivenName |cadena |Nombre de pila fonético (nombre) japonés del contacto. |
| YomiSurname |cadena |Apellido fonético (apellido) japonés del contacto. |
| Categorías |array |Categorías asociadas al contacto. |
| ChangeKey |cadena |Identifica la versión del objeto del evento. |
| DateTimeCreated |cadena |Hora a la que se creó el contacto. |
| DateTimeLastModified |cadena |Hora a la que se modificó el contacto. |

#### <a name="delete-contact"></a>Eliminar contacto
Con esta operación se eliminan contactos de una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Folder id (Identificador de carpeta) |Selección de una carpeta de contactos |
| id* |Id |Identificador único de contacto para eliminar |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="update-contact"></a>Actualizar contacto
Con esta operación se actualizan contactos en una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Folder id (Identificador de carpeta) |Selección de una carpeta de contactos |
| id* |Id |Identificador único de contacto para actualizar |
| item* |Elemento |Elemento de contacto para actualizar |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Contact: Contacto

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| Id |cadena |Identificador único del contacto. |
| ParentFolderId |cadena |Identificador de la carpeta del contacto principal. |
| Birthday |cadena |Cumpleaños del contacto. |
| FileAs |cadena |Nombre de archivo del contacto. |
| DisplayName |cadena |Nombre para mostrar del contacto. |
| GivenName |cadena |Nombre de pila del contacto. |
| Initials |cadena |Iniciales del contacto. |
| MiddleName |cadena |Segundo nombre del contacto. |
| NickName |cadena |Apodo del contacto. |
| Surname |cadena |Apellidos del contacto. |
| Título |cadena |Título del contacto. |
| Generation |cadena |Generación del contacto. |
| EmailAddresses |array |Direcciones de correo electrónico del contacto. |
| ImAddresses |array |Direcciones de mensajería instantánea (MI) del contacto. |
| JobTitle |cadena |Puesto del contacto. |
| CompanyName |cadena |Nombre de la empresa del contacto. |
| Departamento |cadena |Departamento del contacto. |
| OfficeLocation |cadena |Ubicación de la oficina del contacto. |
| Profession |cadena |Profesión del contacto. |
| BusinessHomePage |cadena |Página principal del negocio del contacto. |
| AssistantName |cadena |Nombre del asistente del contacto. |
| Manager |cadena |Nombre del gerente del contacto. |
| HomePhones |array |Números de teléfono particulares del contacto. |
| BusinessPhones |array |Números de teléfono de trabajo del contacto. |
| MobilePhone1 |cadena |Número de teléfono móvil del contacto. |
| HomeAddress |not defined |Dirección del domicilio del contacto. |
| BusinessAddress |not defined |Dirección de la oficina del contacto. |
| OtherAddress |not defined |Otras direcciones del contacto. |
| YomiCompanyName |cadena |Nombre fonético del negocio japonés del contacto. |
| YomiGivenName |cadena |Nombre de pila fonético (nombre) japonés del contacto. |
| YomiSurname |cadena |Apellido fonético (apellido) japonés del contacto. |
| Categorías |array |Categorías asociadas al contacto. |
| ChangeKey |cadena |Identifica la versión del objeto del evento. |
| DateTimeCreated |cadena |Hora a la que se creó el contacto. |
| DateTimeLastModified |cadena |Hora a la que se modificó el contacto. |

## <a name="http-responses"></a>Respuestas HTTP
Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP: 

| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).




<!--HONumber=Jan17_HO3-->



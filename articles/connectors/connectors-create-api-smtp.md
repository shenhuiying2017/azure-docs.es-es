<properties
pageTitle="SMTP | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conexión a SMTP para envío de correo electrónico."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# Introducción al conector de SMTP

Conexión a SMTP para envío de correo electrónico.

Para poder usar [un conector](./apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión a SMTP

Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para poder conectarse a SMTP, primero debe crear una *conexión* a SMTP. Para ello, tendrá que especificar las credenciales que usa habitualmente para acceder al servicio al que desea conectarse. Por lo tanto, en el ejemplo de SMTP, necesitaría las credenciales de su nombre de conexión, la dirección del servidor SMTP y la información de inicio de sesión de usuario para poder crear una conexión a SMTP. [Más información acerca de las conexiones]()

### Creación de una conexión a SMTP

>[AZURE.INCLUDE [Pasos para crear una conexión a SMTP](../../includes/connectors-create-api-smtp.md)]

## Uso de un desencadenador de SMTP

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

En este ejemplo, como SMTP no tiene un desencadenador propio, usaremos el desencadenador **Salesforce - When an object is created** (Salesforce - Cuando se crea un objeto). Este desencadenador se activará al crear un objeto de Salesforce. En nuestro ejemplo, vamos a configurarlo para que cada vez que se crea un cliente potencial en Salesforce, se produzca una acción *Enviar correo electrónico* a través del conector de SMTP con una notificación sobre el cliente potencial que se está creando.

1. Escriba *salesforce* en el cuadro de búsqueda del Diseñador de aplicaciones lógicas y seleccione el desencadenador **Salesforce - When an object is created** (Salesforce - Cuando se crea un objeto).  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  

2. Se muestra el control **When an object is created** (Cuando se crea un objeto).  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  

3. Seleccione el **tipo de objeto** y luego *Lead* (Cliente potencial) en la lista de objetos. En este paso está indicando que va a crear un desencadenador que enviará una notificación a su aplicación lógica cada vez que se cree un cliente potencial en Salesforce.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  

4. Se ha creado el desencadenador.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## Uso de una acción de SMTP

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Ahora que se ha agregado el desencadenador, siga estos pasos para agregar una acción de SMTP que se produzca cuando se crea un cliente potencial en Salesforce.

1. Seleccione **+ Nuevo paso** para agregar la acción que desea que se ejecute cuando se cree un cliente potencial.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  

2. Seleccione **Add an action** (Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  

3. Escriba *smtp* para buscar todas las acciones relacionadas con SMTP.

4. Seleccione **SMTP - Send Email** (SMTP - Enviar correo electrónico) como la acción que se realizará cuando se crea el cliente potencial. Se abre el bloque de control de acción. Tendrá que establecer la conexión de SMTP en el bloque de diseñador si no lo ha hecho previamente.  
 ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)  

5. Indique la información de correo electrónico que desee en el bloque **SMTP - Send Email** (SMTP - Enviar correo electrónico).  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  

6. Guarde el trabajo para activar el flujo de trabajo.

## Detalles técnicos

Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## Desencadenadores de SMTP

SMTP no tiene ningún desencadenador.

## Acciones de SMTP

SMTP tiene la acción siguiente:


|Acción|Descripción|
|--- | ---|
|[Enviar correo electrónico](connectors-create-api-smtp.md#send-email)|Esta operación envía un correo electrónico a uno o más destinatarios.|

### Detalles de la acción

Estos son los detalles de la acción de este conector, junto con sus respuestas:


### Enviar correo electrónico
Esta operación envía un correo electrónico a uno o más destinatarios.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|Para|Para|Indicar las direcciones de correo electrónico separadas por punto y coma, como recipient1@domain.com;destinatario2@dominio.com|
|CC|cc|Indicar las direcciones de correo electrónico separadas por punto y coma, como recipient1@domain.com;destinatario2@dominio.com|
|Asunto|Asunto|Asunto del correo electrónico|
|Cuerpo|Cuerpo|Cuerpo del correo electrónico|
|De|De|Dirección de correo electrónico del remitente como sender@domain.com|
|IsHtml|Es HTML|Enviar el correo electrónico como HTML (true/false)|
|CCO|bcc|Indicar las direcciones de correo electrónico separadas por punto y coma, como recipient1@domain.com;destinatario2@dominio.com|
|Importancia|Importancia|Importancia del correo electrónico (alta, normal o baja)|
|ContentData|Datos de contenido de los datos adjuntos|Datos de contenido (codificación en base64 para transmisiones y tal cual para cadena)|
|ContentType|Tipo de contenido de los datos adjuntos|Tipo de contenido|
|ContentTransferEncoding|Codificación de transferencia de contenido de los datos adjuntos|Codificación de transferencia de contenido (base64 o ninguna)|
|FileName|Nombre de archivo de los datos adjuntos|Nombre de archivo|
|ContentId|Identificador de contenido de los datos adjuntos|Id. de contenido|

El símbolo * indica que la propiedad es obligatoria.


## Respuestas HTTP

Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP:

|Name|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido.|
|default|Error en la operación.|

## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->

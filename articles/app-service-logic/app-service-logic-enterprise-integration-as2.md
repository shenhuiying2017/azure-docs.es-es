<properties 
	pageTitle="Aprenda a crear un contrato AS2 para Enterprise Integration Pack" 
	description="Aprenda a crear un contrato AS2 para Enterprise Integration Pack| Servicio de aplicaciones de Microsoft Azure" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Integración de empresas con AS2

## Creación de un contrato AS2
Para poder utilizar las características de empresa en aplicaciones lógicas, primero debe crear contratos.

### Requisitos para poder comenzar
- Debe tener una [cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) definida en su suscripción de Azure
- Debe contar con, como mínimo, dos [partners](./app-service-logic-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración

>[AZURE.NOTE]Al crear un contrato, el contenido del archivo de contrato debe coincidir con el tipo de contrato.


Cuando haya [creado una cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) y [agregado los partners](./app-service-logic-enterprise-integration-partners.md), podrá generar un contrato siguiendo estos pasos:

### En la página principal del Portal de Azure

Después de iniciar sesión en el [Portal de Azure](http://portal.azure.com "Portal de Azure"):
1. En el menú de la izquierda, seleccione **Examinar**.

>[AZURE.TIP]Si no ve el vínculo **Examinar**, puede que primero tenga que expandir el menú. Para ello, haga clic en el vínculo **Mostrar menú** que se encuentra en la parte superior izquierda del menú contraído.

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Escriba *integration* en el cuadro de búsqueda del filtro; después, seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. En la hoja **Integration Accounts** (Cuentas de integración) que se abre, seleccione la cuenta de integración en la que va a crear el contrato. Si no ve ninguna lista con cuentas de integración, [cree una antes](./app-service-logic-enterprise-integration-accounts.md "Todo sobre las cuentas de integración"). ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Seleccione el icono **Agreements** (Contratos). Si no lo ve, agréguelo antes. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)
5. Seleccione el botón **Agregar** de la hoja Agreements (Contratos) que se abre. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
6. Escriba un **nombre** para el contrato; después, seleccione **Host Partner** (Partner anfitrión), **Host Identity** (Identidad anfitriona), **Guest Partner** (Partner invitado), **Guest Identity** (Identidad invitada) en la hoja Agreements (Contratos) que se abre. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)

A continuación, encontrará información que puede resultarle útil a la hora de configurar las opciones del contrato:
  
|Propiedad|Descripción|
|----|----|
|Host Partner (Partner anfitrión)|Un contrato tiene asociado un partner anfitrión e invitado. El partner anfitrión representa la organización que configura el contrato.|
|Host Identity (Identidad anfitriona)|Un identificador del partner anfitrión. |
|Guest Partner (Partner invitado)|Un contrato tiene asociado un partner anfitrión e invitado. El partner invitado representa la organización que está haciendo negocios con el partner anfitrión.|
|Guest Identity (Identidad invitada)|Un identificador de la identidad invitada.|
|Receive Settings (Configuración de recepción)|Estas propiedades se aplican a todos los mensajes que recibe un contrato|
|Send Settings (Configuración de envío)|Estas propiedades se aplican a todos los mensajes que envía un contrato|  
Prosigamos:
7. Seleccione **Receive Settings** (Configuración de recepción) para configurar cómo se administran los mensajes que se reciben través de este contrato.
 
 - Si lo desea, puede invalidar las propiedades del mensaje entrante. Para ello, active la casilla **Override message properties** (Invalidar propiedades del mensaje).
  - Active la casilla **Message should be signed** (Debe firmarse el mensaje) si quiere exigir que se firmen todos los mensajes entrantes. Si selecciona esta opción, también debe elegir el **certificado** que se usará para validar la firma de los mensajes.
  - Si lo desea, puede exigir que también se cifren los mensajes. Para ello, active la casilla **Message should be encrypted** (Debe cifrarse el mensaje). Después, tendrá que seleccionar el **certificado** que se usará para descodificar los mensajes entrantes.
  - También puede exigir que se compriman los mensajes. Para ello, active la casilla **Message should be compressed** (Debe comprimirse el mensaje). ![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)

Si quiere obtener más información sobre las opciones que habilita la configuración de recepción, consulte la tabla siguiente.

|Propiedad|Descripción|
|----|----|
|Override message properties (Invalidar propiedades del mensaje)|Seleccione esta opción para indicar que se pueden invalidar las propiedades de los mensajes recibidos |
|Message should be signed (Debe firmarse el mensaje)|Habilite esta opción para exigir que se firmen digitalmente los mensajes|
|Message should be encrypted (Debe cifrarse el mensaje)|Habilite esta opción para exigir que se cifren los mensajes. Los mensajes que no estén cifrados se rechazarán.|
|Message should be compressed (Debe comprimirse el mensaje)|Habilite esta opción para exigir que se compriman los mensajes. Los mensajes que no estén comprimidos se rechazarán.|
|MDN Text (Texto de MDN)|Se trata de una MDN predeterminada que se envía al remitente del mensaje|
|Send MDN (Enviar MDN)|Habilite esta opción para permitir que se envíen MDN.|
|Send signed MDN (Enviar MDN firmada)|Habilite esta opción para exigir que se firmen las MDN.|
|MIC Algorithm (Algoritmo de MIC)||
|Send asynchronous MDN (Enviar MDN asincrónica)|Habilite esta opción para exigir que los mensajes se envíen de forma asincrónica.|
|URL|Se trata de la dirección URL a la que se enviará los mensajes.|
Ahora, continuemos:
8. Seleccione **Send Settings** (Configuración de envío) para configurar cómo se controlarán los mensajes enviados a través de este contrato. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)

Si quiere obtener más información sobre las opciones que habilita la configuración de envío, consulte la tabla siguiente.

|Propiedad|Descripción|
|----|----|
|Enable message signing (Habilitar firma de mensajes)|Active esta casilla para habilitar que se firmen todos los mensajes enviados desde el contrato.|
|MIC Algorithm (Algoritmo de MIC)|Seleccione el algoritmo que se utilizará en la firma de los mensajes|
|Certificate|Seleccione el certificado que va a utilizar en la firma de los mensajes|
|Enable message encryption (Habilitar el cifrado de mensajes)|Active esta casilla para cifrar todos los mensajes enviados desde este contrato.|
|Algoritmo de cifrado|Seleccione el algoritmo de cifrado que se utilizará en el cifrado de los mensajes|
|Unfold HTTP headers (Expandir encabezados HTTP)|Active esta casilla para expandir el encabezado HTTP Content-Type en una sola línea.|
|Request MDN (Solicitar MDN)|Active esta casilla con el fin de solicitar una MDN para todos los mensajes enviados desde este contrato|
|Request signed MDN (Solicitar MDN firmada)|Habilite esta opción para solicitar que se firmen todas las MDN enviadas a este contrato|
|Request asynchronous MDN (Solicitar MDN asíncrona)|Habilite esta opción para solicitar que se envíen MDN asíncronas a este contrato|
|URL|La dirección URL a la que se enviarán las MDN|
|Enable NRR (Habilitar NRR)|Active esta casilla para habilitar que no se rechace la recepción de los mensajes|
Ya casi hemos acabado.
9. Seleccione el icono de **Agreements** (Contratos) de la hoja Integration Account (Cuenta de integración); verá en la lista el contrato que acaba de agregar. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)

<!---HONumber=AcomDC_0803_2016-->
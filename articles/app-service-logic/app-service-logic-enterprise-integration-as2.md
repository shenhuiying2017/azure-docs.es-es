---
title: Aprenda a crear un contrato AS2 para Enterprise Integration Pack
description: Aprenda a crear un contrato AS2 para Enterprise Integration Pack| Servicio de aplicaciones de Microsoft Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13b2757a10c6da7ca3dd14c3db9620fab0035561


---
# <a name="enterprise-integration-with-as2"></a>Integración de empresas con AS2
## <a name="create-an-as2-agreement"></a>Creación de un contrato AS2
Para poder utilizar las características de empresa en aplicaciones lógicas, primero debe crear contratos. 

### <a name="heres-what-you-need-before-you-get-started"></a>Requisitos para poder comenzar
* Una [cuenta de integración](app-service-logic-enterprise-integration-accounts.md) definida en su suscripción de Azure  
* Al menos dos [asociados](app-service-logic-enterprise-integration-partners.md) definidos en su cuenta de integración  

> [!NOTE]
> Al crear un contrato, el contenido del archivo de contrato debe coincidir con el tipo de contrato.    
> 
> 

Cuando haya [creado una cuenta de integración](app-service-logic-enterprise-integration-accounts.md) y [agregado los asociados](app-service-logic-enterprise-integration-partners.md), podrá generar un contrato siguiendo estos pasos:  

### <a name="from-the-azure-portal-home-page"></a>En la página principal del Portal de Azure
Después de iniciar sesión en el [Portal de Azure](http://portal.azure.com "Portal de Azure"):  

1. En el menú de la izquierda, seleccione **Examinar** .  

> [!TIP]
> Si no ve el vínculo **Examinar** , puede que primero tenga que expandir el menú. Para ello, haga clic en el vínculo **Mostrar menú** que se encuentra en la parte superior izquierda del menú contraído.  
> 
> 

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

1. Escriba *integration* en el cuadro de búsqueda del filtro; después, seleccione **Cuentas de integración** en la lista de resultados.       
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
2. En la hoja **Cuentas de integración** que se abre, seleccione la cuenta de integración en la que va a crear el acuerdo. Si no ve ninguna lista con cuentas de integración, [créela antes](app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
3. Seleccione el icono **Acuerdos** . Si no lo ve, agréguelo antes.   
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)   
4. Seleccione el botón **Agregar** en la hoja Acuerdos que se abre.  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
5. Escriba el **nombre** del contrato y seleccione los valores de **Asociado del host**, **Identidad del host**, **Asociado invitado** e **Identidad del invitado** en la hoja Contratos que se abre.  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)  

A continuación, encontrará información que puede resultarle útil a la hora de configurar las opciones del contrato: 

| Propiedad | Descripción |
| --- | --- |
| Host Partner (Partner anfitrión) |Un contrato tiene asociado un partner anfitrión e invitado. El partner anfitrión representa la organización que configura el contrato. |
| Host Identity (Identidad anfitriona) |Un identificador del partner anfitrión. |
| Guest Partner (Partner invitado) |Un contrato tiene asociado un partner anfitrión e invitado. El partner invitado representa la organización que está haciendo negocios con el partner anfitrión. |
| Guest Identity |Un identificador de la identidad invitada. |
| Receive Settings (Configuración de recepción) |Estas propiedades se aplican a todos los mensajes que recibe un contrato |
| Send Settings (Configuración de envío) |Estas propiedades se aplican a todos los mensajes que envía un contrato |

Prosigamos:  

1. Seleccione **Receive Settings** (Configuración de recepción) para configurar cómo se administran los mensajes que se reciben través de este contrato.  
   
   * Si lo desea, puede invalidar las propiedades del mensaje entrante. Para ello, active la casilla **Override message properties** (Invalidar propiedades del mensaje).
   * Active la casilla **Message should be signed** (Debe firmarse el mensaje) si quiere exigir que se firmen todos los mensajes entrantes. Si selecciona esta opción, también debe elegir el **certificado** que se usará para validar la firma de los mensajes.
   * Si lo desea, puede exigir que también se cifren los mensajes. Para ello, active la casilla **Message should be encrypted** (Debe cifrarse el mensaje). Después, tendrá que seleccionar el **certificado** que se usará para descodificar los mensajes entrantes.
   * También puede exigir que se compriman los mensajes. Para ello, active la casilla **Message should be compressed** (Debe comprimirse el mensaje).  
     ![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)  

Si quiere obtener más información sobre las opciones que habilita la configuración de recepción, consulte la tabla siguiente.  

| Propiedad | Descripción |
| --- | --- |
| Override message properties |Seleccione esta opción para indicar que se pueden invalidar las propiedades de los mensajes recibidos |
| Message should be signed |Habilite esta opción para exigir que se firmen digitalmente los mensajes |
| Message should be encrypted |Habilite esta opción para exigir que se cifren los mensajes. Los mensajes que no estén cifrados se rechazarán. |
| Message should be compressed |Habilite esta opción para exigir que se compriman los mensajes. Los mensajes que no estén comprimidos se rechazarán. |
| MDN Text (Texto de MDN) |Se trata de una MDN predeterminada que se envía al remitente del mensaje |
| Send MDN (Enviar MDN) |Habilite esta opción para permitir que se envíen MDN. |
| Send signed MDN (Enviar MDN firmada) |Habilite esta opción para exigir que se firmen las MDN. |
| MIC Algorithm (Algoritmo de MIC) | |
| Send asynchronous MDN (Enviar MDN asincrónica) |Habilite esta opción para exigir que los mensajes se envíen de forma asincrónica. |
| URL |Se trata de la dirección URL a la que se enviará los mensajes. |

Ahora, continuemos:  

1. Seleccione **Send Settings** (Configuración de envío) para configurar cómo se controlarán los mensajes enviados a través de este contrato.  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)  

Si quiere obtener más información sobre las opciones que habilita la configuración de envío, consulte la tabla siguiente.  

| Propiedad | Descripción |
| --- | --- |
| Enable message signing (Habilitar firma de mensajes) |Active esta casilla para habilitar que se firmen todos los mensajes enviados desde el contrato. |
| MIC Algorithm (Algoritmo de MIC) |Seleccione el algoritmo que se utilizará en la firma de los mensajes |
| certificado |Seleccione el certificado que va a utilizar en la firma de los mensajes |
| Enable message encryption (Habilitar el cifrado de mensajes) |Active esta casilla para cifrar todos los mensajes enviados desde este contrato. |
| Algoritmo de cifrado |Seleccione el algoritmo de cifrado que se utilizará en el cifrado de los mensajes |
| Unfold HTTP headers (Expandir encabezados HTTP) |Active esta casilla para expandir el encabezado HTTP Content-Type en una sola línea. |
| Request MDN (Solicitar MDN) |Active esta casilla con el fin de solicitar una MDN para todos los mensajes enviados desde este contrato |
| Request signed MDN (Solicitar MDN firmada) |Habilite esta opción para solicitar que se firmen todas las MDN enviadas a este contrato |
| Request asynchronous MDN (Solicitar MDN asíncrona) |Habilite esta opción para solicitar que se envíen MDN asíncronas a este contrato |
| URL |La dirección URL a la que se enviarán las MDN |
| Enable NRR (Habilitar NRR) |Active esta casilla para habilitar que no se rechace la recepción de los mensajes |

Ya casi hemos acabado.  

1. Seleccione el icono de **Agreements** (Contratos) de la hoja Integration Account (Cuenta de integración); verá en la lista el contrato que acaba de agregar.  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)




<!--HONumber=Nov16_HO3-->



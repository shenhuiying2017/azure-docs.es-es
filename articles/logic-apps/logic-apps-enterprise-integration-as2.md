---
title: "Información acerca de la creación de un contrato de AS2 para Enterprise Integration Pack| Microsoft Docs"
description: "Información acerca de la creación de un contrato de AS2 para Enterprise Integration Pack| Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 47a1c5653d84a55224c18222bc547e1c863ea442
ms.openlocfilehash: ea256557a38b3ce6cb457d195fa4b48cfd8879b8


---
# <a name="enterprise-integration-with-as2"></a>Integración de empresas con AS2
## <a name="create-an-as2-agreement"></a>Creación de un contrato AS2
Para poder utilizar las características empresariales de Logic Apps, primero deben crearse los contratos. 

### <a name="heres-what-you-need-before-you-get-started"></a>Requisitos para poder comenzar
* Una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md) definida en su suscripción de Azure  
* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) definidos en su cuenta de integración  

> [!NOTE]
> Al crear un contrato, el contenido del archivo de contrato debe coincidir con el tipo de contrato.    
> 
> 

Cuando haya [creado una cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md) y [agregado los asociados](logic-apps-enterprise-integration-partners.md), podrá generar un contrato siguiendo estos pasos:  

### <a name="from-the-azure-portal-home-page"></a>En la página principal del Portal de Azure
Una vez que haya iniciado sesión en [Azure Portal](http://portal.azure.com "Azure Portal"):  

1. Seleccione **Más servicios** y escriba **integración** en el cuadro de búsqueda de filtro. Seleccione **Cuentas de integración** en la lista de resultados.    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
2. Seleccione la cuenta integración en la que vaya a agregar el certificado. 
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
3. Seleccione el icono **Acuerdos** . Si no lo ve, agréguelo antes.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
4. Seleccione el botón **Agregar** en la hoja Acuerdos que se abre.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. En la hoja Acuerdos, escriba el **nombre** del contrato, seleccione **AS2** en **Tipo de contrato** y especifique los valores de **Asociado del host**, **Identidad del host**, **Asociado invitado** e **Identidad del invitado**.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

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
   
   * Si lo desea, puede invalidar las propiedades del mensaje entrante. Para ello, active la casilla **Reemplazar propiedades del mensaje**.
   * Active la casilla **Debe firmarse el mensaje** si quiere que, de forma obligatoria, todos los mensajes vayan firmados. Si la activa, tendrá que seleccionar *guest partner public certificate* (certificado público del asociado invitado) para poder validar la firma de los mensajes.
   * Active la casilla **Debe cifrarse el mensaje** si quiere que, de forma obligatoria, todos los mensajes entrantes vayan cifrados.  Si la activa, tendrá que seleccionar *host partner private certificate* (certificado privado del asociado del host) para poder descifrar los mensajes entrantes.
   * También puede exigir que se compriman los mensajes. Para ello, active la casilla **Debe comprimirse el mensaje**.    
   * Active la casilla **Enviar MDN** si desea enviar una MDN sincrónica para los mensajes recibidos.
   * Active la casilla **Enviar MDN firmado** si desea enviar una MDN firmada para los mensajes recibidos.
   * Active la casilla **Enviar MDN asincrónico** si desea enviar una MDN asincrónica para los mensajes recibidos.     
    ![](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

Si quiere obtener más información sobre las opciones que habilita la configuración de recepción, consulte la tabla siguiente.  

| Propiedad | Descripción |
| --- | --- |
| Override message properties |Seleccione esta opción para indicar que se pueden invalidar las propiedades de los mensajes recibidos |
| Message should be signed |Habilite esta opción si desea que, de forma obligatoria, los mensajes vayan firmados digitalmente.  Configure el certificado público del asociado invitado para la comprobación de firmas.  |
| Message should be encrypted |Habilite esta opción para exigir que se cifren los mensajes. Los mensajes que no estén cifrados se rechazarán. Configure el certificado privado del asociado del host para descifrar los mensajes.  |
| Message should be compressed |Habilite esta opción para exigir que se compriman los mensajes. Los mensajes que no estén comprimidos se rechazarán. |
| MDN Text (Texto de MDN) |Se trata de una MDN predeterminada que se envía al remitente del mensaje |
| Send MDN (Enviar MDN) |Habilite esta opción para permitir que se envíen MDN. |
| Send signed MDN (Enviar MDN firmada) |Habilite esta opción para exigir que se firmen las MDN. |
| MIC Algorithm (Algoritmo de MIC) | |
| Send asynchronous MDN (Enviar MDN asincrónica) |Habilite esta opción para exigir que los mensajes se envíen de forma asincrónica. |
| URL |Se trata de la dirección URL a la que se enviarán las MDN. |

Ahora, continuemos:  

1. Seleccione **Send Settings** (Configuración de envío) para configurar cómo se controlarán los mensajes enviados a través de este contrato.  

   * Active la casilla **Habilitar la firma de mensajes** si desea enviar mensajes firmados al asociado. Si la activa, tendrá que seleccionar *host partner private certificate MIC Algorithm* (Algoritmo MIC de certificado privado del asociado del host) y *host partner private certificate* (certificado privado del asociado del host) para poder firmar los mensajes.
   * Active la casilla **Habilitar el cifrado de mensajes** si desea enviar mensajes cifrados al asociado. Si la activa, tendrá que seleccionar *guest partner public certificate algorithm* (algoritmo de certificado público del asociado invitado) y *guest partner public certificate* (certificado público del asociado invitado) para poder cifrar los mensajes.
   * Active la casilla **Debe comprimirse el mensaje** para comprimir el mensaje. 
   * Active la casilla **Expandir encabezados HTTP** para expandir el encabezado HTTP Content-Type en una sola línea. 
   * Active la casilla **Solicitar MDN** si desea recibir una MDN sincrónica de los mensajes enviados.
   * Active la casilla **Solicitar MDN firmada** si desea recibir una MDN firmada de los mensajes enviados.
   * Active la casilla **Solicitar MDN asincrónica** si desea recibir una MDN asincrónica de los mensajes enviados. Si la activa, tendrá que especificar la dirección URL a la que se enviarán las MDN.  
   * Active la casilla **Habilitar NRR** para que no se rechace la recepción de mensajes.    
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

Si quiere obtener más información sobre las opciones que habilita la configuración de envío, consulte la tabla siguiente.  

| Propiedad | Descripción |
| --- | --- |
| Enable message signing (Habilitar firma de mensajes) |Active esta casilla para que se firmen todos los mensajes enviados desde el contrato. |
| MIC Algorithm (Algoritmo de MIC) |Seleccione el algoritmo que se utilizará para firmar los mensajes. Configure el algoritmo MIC del certificado privado del asociado del host que se va a utilizar para firmar los mensajes. |
| Certificate |Seleccione el certificado que se va a usar para firmar los mensajes. Configure el certificado privado del asociado del host que se va a utilizar para firmar los mensajes. |
| Enable message encryption (Habilitar el cifrado de mensajes) |Active esta casilla para cifrar todos los mensajes enviados desde este contrato. Configure el algoritmo del certificado público del asociado invitado que se va a utilizar para cifrar los mensajes. |
| Algoritmo de cifrado |Seleccione el algoritmo de cifrado que se va a utilizar para cifrar los mensajes. Configure el certificado público del asociado invitado que se va a utilizar para cifrar los mensajes. |
| Unfold HTTP headers (Expandir encabezados HTTP) |Active esta casilla para expandir el encabezado HTTP Content-Type en una sola línea. |
| Request MDN (Solicitar MDN) |Active esta casilla si desea solicitar una MDN para todos los mensajes enviados desde este contrato. |
| Request signed MDN (Solicitar MDN firmada) |Habilite esta opción para solicitar que se firmen todas las MDN enviadas a este contrato |
| Request asynchronous MDN (Solicitar MDN asíncrona) |Habilite esta opción para solicitar que se envíen MDN asíncronas a este contrato |
| URL |La dirección URL a la que se enviarán las MDN |
| Enable NRR (Habilitar NRR) |Active esta casilla para que no se rechace la recepción de mensajes. |

Seleccione el icono de **Agreements** (Contratos) de la hoja Integration Account (Cuenta de integración); verá en la lista el contrato que acaba de agregar.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  


<!--HONumber=Jan17_HO4-->



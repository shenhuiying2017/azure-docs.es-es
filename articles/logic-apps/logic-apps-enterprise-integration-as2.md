---
title: "Creación de un contrato de AS2 en Azure Logic Apps | Microsoft Docs"
description: "Creación de un contrato de AS2 para Enterprise Integration Pack | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 6a947ea997bbcfe1b6b28c7cbb49911836750e6a
ms.openlocfilehash: a490b89c5420aecdfb3f79289979faab9a4630e9


---
# <a name="enterprise-integration-with-as2"></a>Integración de empresas con AS2
Para poder utilizar las características empresariales de Logic Apps, primero deben crearse los contratos.

## <a name="prerequisites"></a>Requisitos previos
* Debe tener una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md) definida en su suscripción de Azure.  
* Debe tener al menos dos [asociados](logic-apps-enterprise-integration-partners.md) definidos en su cuenta de integración.  

> [!NOTE]
> Al crear un contrato, el contenido del archivo de contrato debe coincidir con el tipo de contrato.    

Cuando haya creado una cuenta de integración y agregado asociados, podrá generar un contrato usando los procedimientos de las secciones siguientes.  

## <a name="create-an-agreement"></a>Creación de un contrato

1. Inicie sesión en [Azure Portal](http://portal.azure.com "Azure Portal").  
2. Seleccione **Más servicios**, escriba **integración** en el cuadro de búsqueda de filtros y después **Cuentas de integración** en la lista de resultados.

 ![Seleccione "Cuentas de integración" en la lista de resultados.](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. Seleccione la cuenta integración en la que quiera agregar el certificado.

 ![Seleccione la cuenta de integración.](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Seleccione el icono **Acuerdos** . Si el icono no aparece, agréguelo.

 ![Seleccione el icono "Acuerdos".](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. En la hoja **Contratos**, seleccione **Agregar**.

 ![Seleccione "Agregar".](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
6. Escriba un nombre para el acuerdo, seleccione **AS2** en la lista **Tipo de contrato** y escriba la información pertinente en las listas **Asociado del host**, **Identidad del host**, **Asociado invitado** e **Identidad del invitado**.

 ![Escriba un nombre para el contrato](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

 En la tabla siguiente se describen las propiedades del cuadro de diálogo **Agregar**:

    | Propiedad | Descripción |
    | --- | --- |
    | Host Partner (Partner anfitrión) | Un contrato requiere un asociado anfitrión y uno invitado. El asociado del host representa la organización que configura el contrato. |
    | Host Identity (Identidad anfitriona) | Un identificador del partner anfitrión. |
    | Guest Partner (Partner invitado) | Un contrato requiere un asociado anfitrión y uno invitado. El partner invitado representa la organización que está haciendo negocios con el partner anfitrión. |
    | Guest Identity | Un identificador de la identidad invitada. |
    | Receive Settings (Configuración de recepción) | Las propiedades que se aplican a todos los mensajes recibidos por el contrato. |
    | Send Settings (Configuración de envío) | Las propiedades que se aplican a todos los mensajes que envía el contrato. |

7. Para configurar cómo se administran los mensajes que se reciben través de este contrato, siga estos pasos:

 a. Seleccione **Configuración de recepción**.

 b. Si lo desea, puede invalidar las propiedades de los mensajes entrantes activando la casilla **Reemplazar propiedades del mensaje**.

 c. Para exigir que se firmen todos los mensajes entrantes, active la casilla **Debe firmarse el mensaje**. Si selecciona esta opción, valide la firma en los mensajes seleccionando **guest partner public certificate** (certificado público del asociado invitado) en la lista **Certificado**.

 d. Para exigir que se cifren todos los mensajes entrantes, active la casilla **Debe cifrarse el mensaje**. Si la activa, descifre los mensajes entrantes seleccionando **host partner private certificate** (certificado privado del asociado del host) en la lista **Certificado**.

 e. Para exigir que los mensajes se compriman, active la casilla **Debe comprimirse el mensaje**.    

 f. Para enviar una notificación de disposición de mensaje (MDN) sincrónica para los mensajes recibidos, active la casilla **Enviar MDN**.

 g. Para enviar MDN firmadas, active la casilla **Enviar MDN firmado**.

 h. Para enviar MDN asincrónicas para mensajes recibidos, active la casilla **Enviar MDN asincrónico**.

 ![Establecimiento de las propiedades de "Configuración de recepción"](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

 En la tabla siguiente se describen las propiedades de **Configuración de recepción**:  

 | Propiedad | Descripción |
 | --- | --- |
 | Override message properties | Indica que se pueden invalidar las propiedades de los mensajes recibidos. |
 | Message should be signed | Exige que los mensajes se firmen digitalmente. Configure el certificado público del asociado invitado para la comprobación de firmas.  |
 | Message should be encrypted | Exige el cifrado de los mensajes. Los mensajes que no estén cifrados se rechazarán. Configure el certificado privado del asociado del host para el descifrado de los mensajes.  |
 | Message should be compressed | Requiere que los mensajes estén comprimidos. Los mensajes que no estén comprimidos se rechazarán. |
 | MDN Text (Texto de MDN) | La notificación de disposición del mensaje (MDN) predeterminada que se enviará al remitente del mensaje. |
 | Send MDN (Enviar MDN) | Exige el envío de MDN. |
 | Send signed MDN (Enviar MDN firmada) | Exige que las MDN estén firmadas. |
 | MIC Algorithm (Algoritmo de MIC) | |
 | Send asynchronous MDN (Enviar MDN asincrónica) | Exige que los mensajes se envíen de forma asincrónica. |
 | URL | La dirección URL a la que se enviarán los MDN. |

8. Para configurar cómo se administran los mensajes que se envían a través de este contrato, siga estos pasos:

 a. Haga clic en **Enviar configuración**.  

 b. Para enviar mensajes firmados al asociado, active la casilla **Habilitar la firma de mensajes**. Si selecciona esta opción, firme los mensajes seleccionando **host partner private certificate MIC Algorithm** (algoritmo MIC de certificado privado del asociado del host) en la lista **Algoritmo MIC** y **host partner private certificate** (certificado privado del asociado del host) en la lista **Certificado**.

 c. Para enviar mensajes cifrados al asociado, active la casilla **Habilitar el cifrado de mensajes**. Si selecciona esta opción, cifre los mensajes seleccionando **guest partner public certificate** (certificado público del asociado invitado) en la lista **Algoritmo de cifrado** y **guest partner public certificate** (certificado público del asociado invitado) en la lista **Certificado**.

 d. Para comprimir el mensaje, active la casilla **Habilitar la compresión de mensajes**.

 e. Para desplegar el encabezado HTTP Content-Type en una sola línea, active la casilla **Expandir encabezados HTTP**.

 f. Para recibir MDN sincrónicos para los mensajes enviados, active la casilla **Solicitar MDN**.

 g. Para recibir MDN firmadas para los mensajes enviados, active la casilla **Solicitar MDN firmada**.

 h. Para recibir MDN asincrónicas para los mensajes enviados, active la casilla **Solicitar MDN asincrónica**. Si selecciona esta opción, escriba la dirección URL a la que se enviarán las MDN.  

 i. Para exigir la recepción sin rechazo, active la casilla **Habilitar NRR**.

 j. Seleccione **Aceptar**.

 ![Establecimiento de las propiedades de "Configuración de envío"](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

 En la tabla siguiente se describen las propiedades de **Enviar configuración**:  

 | Propiedad | Descripción |
 | --- | --- |
 | Enable message signing (Habilitar firma de mensajes) | Exige que se firmen todos los mensajes enviados desde el contrato. |
 | MIC Algorithm (Algoritmo de MIC) | El algoritmo que se utiliza para firmar los mensajes. Configura el algoritmo MIC del certificado privado del asociado del host para firmar los mensajes. |
 | Certificate | El certificado que se utilizará para firmar los mensajes. Configura el certificado privado del asociado del host para firmar los mensajes. |
 | Enable message encryption (Habilitar el cifrado de mensajes) | Exige que se cifren todos los mensajes enviados desde el contrato. Configura el algoritmo del certificado público del asociado invitado para el cifrado de los mensajes. |
 | Algoritmo de cifrado | El algoritmo de cifrado que se va a utilizar para cifrar los mensajes. Configura el certificado público del asociado invitado para el cifrado de los mensajes. |
 | Certificate | El certificado que se utilizará para cifrar los mensajes. Configura el certificado privado del asociado invitado para el cifrado de los mensajes. |
 | Habilitar la compresión de mensajes | Exige que se compriman todos los mensajes enviados desde el contrato. |
 | Unfold HTTP headers (Expandir encabezados HTTP) | Coloca el encabezado HTTP Content-Type en una sola línea. |
 | Request MDN (Solicitar MDN) | Exige una MDN para todos los mensajes enviados desde el contrato. |
 | Request signed MDN (Solicitar MDN firmada) | Exige que se firmen todas las MDN enviadas al contrato. |
 | Request asynchronous MDN (Solicitar MDN asíncrona) | Exige que se envíen MDN asincrónicas a este contrato. |
 | URL | La dirección URL a la que se enviarán los MDN. |
 | Enable NRR (Habilitar NRR) | Exige la recepción sin rechazo (NRR), un atributo de comunicación que prueba que se recibieron los datos según la dirección indicada. |

## <a name="view-the-agreements-list"></a>Visualización de la lista de contratos
Para ver el contrato recién añadido, seleccione el icono de **Contratos** de la hoja **Cuenta de integración**.

![Visualización de la lista "Contratos"](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  



<!--HONumber=Feb17_HO1-->



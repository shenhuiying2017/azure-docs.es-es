---
title: "Mensajes AS2 para la integración empresarial B2B: Azure Logic Apps | Microsoft Docs"
description: "Intercambio de mensajes AS2 para la integración empresarial B2B con Azure Logic Apps"
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
ms.date: 06/08/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 6a283d8772e48aa6671d88288c2083d891a220d5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="exchange-as2-messages-for-enterprise-integration-with-logic-apps"></a>Intercambio de mensajes AS2 para la integración empresarial con las aplicaciones lógicas

Para poder intercambiar mensajes AS2 para Azure Logic Apps, debe crear un contrato AS2 y almacenarlo en la cuenta de integración. Estos son los pasos para crear un contrato AS2.

## <a name="before-you-start"></a>Antes de comenzar

Esto es lo que necesita:

* Una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md) que ya esté definida y asociada a su suscripción de Azure
* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración y configurados con el calificador AS2 en **Identidades de negocio**

> [!NOTE]
> Al crear un contrato, el contenido del archivo de contrato debe coincidir con el tipo de contrato.    

Cuando haya [creado una cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md) y [agregado los asociados](logic-apps-enterprise-integration-partners.md), siga estos pasos para generar un contrato AS2.

## <a name="create-an-as2-agreement"></a>Creación de un contrato AS2

1.  Inicie sesión en [Azure Portal](http://portal.azure.com "Azure Portal").  

2.  En el menú izquierdo, seleccione **Más servicios**. En el cuadro de búsqueda, escriba **integración** como filtro. En la lista de resultados, seleccione **Cuentas de integración**.

    > [!TIP]
    > Si no ve **Más servicios**, expanda el menú. En la parte superior del menú contraído, seleccione **Mostrar menú**.

    ![Más servicios, filtre por "integración", seleccione "Cuentas de integración"](./media/logic-apps-enterprise-integration-as2/overview-1.png)

3. En la hoja **Cuentas de integración** que se abre, seleccione la cuenta donde se vaya a crear el contrato.
Si no ve ninguna, [créela](../logic-apps/logic-apps-enterprise-integration-accounts.md "Información completa sobre las cuentas de integración").  

    ![Selección de la cuenta de integración donde desea crear el contrato](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Seleccione el icono de **contratos**. Si no tiene un icono Acuerdos, agréguelo.

    ![Elección del icono "Acuerdos"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. En la hoja Acuerdos que se abre, seleccione **Agregar**.

    ![Elección de "Agregar"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. En **Agregar**, escriba un valor en **Nombre** para el contrato. Para el **Tipo de contrato**, seleccione **AS2**. Seleccione valores en **Asociado del host**, **Identidad del host**, **Asociado invitado** e **Identidad del invitado** para el contrato.

    ![Especificación de los detalles del contrato](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Propiedad | DESCRIPCIÓN |
    | --- | --- |
    | NOMBRE |Nombre del contrato |
    | Tipo de contrato | Debe ser AS2 |
    | Host Partner (Partner anfitrión) |Un contrato tiene asociado un partner anfitrión e invitado. El asociado del host representa la organización que configura el contrato. |
    | Host Identity (Identidad anfitriona) |Un identificador del asociado del host. |
    | Guest Partner (Partner invitado) |Un contrato tiene asociado un partner anfitrión e invitado. El partner invitado representa la organización que está haciendo negocios con el partner anfitrión. |
    | Guest Identity |Un identificador del asociado invitado. |
    | Receive Settings (Configuración de recepción) |Estas propiedades se aplican a todos los mensajes que recibe un contrato. |
    | Send Settings (Configuración de envío) |Estas propiedades se aplican a todos los mensajes que envía un contrato. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configuración de la forma en que su contrato controla los mensajes recibidos

Ahora que ha establecido las propiedades del contrato, puede configurar cómo este identifica y controla los mensajes entrantes recibidos del asociado a través de este contrato.

1.  En **Agregar**, seleccione **Configuración de recepción**.
Configure estas propiedades en función del contrato con el asociado con el que intercambia mensajes. Para las descripciones de las propiedades, consulte la tabla de esta sección.

    ![Selección de "Configuración de recepción"](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Si lo desea, puede invalidar las propiedades de los mensajes entrantes al seleccionar **Reemplazar propiedades del mensaje**.

3. Para exigir que se firmen todos los mensajes entrantes, seleccione **Debe firmarse el mensaje**. En la lista **Certificado**, seleccione un [certificado público del asociado invitado](../logic-apps/logic-apps-enterprise-integration-certificates.md) existente para validar la firma de los mensajes. Como alternativa, si no tiene el certificado, créelo.

4.  Para exigir que se cifren todos los mensajes entrantes, seleccione **Debe cifrarse el mensaje**. Desde la lista **Certificado**, seleccione un [certificado privado del asociado del host](../logic-apps/logic-apps-enterprise-integration-certificates.md) existente para descifrar los mensajes entrantes. Como alternativa, si no tiene el certificado, créelo.

5. Para exigir la compresión de los mensajes, seleccione **Debe comprimirse el mensaje**.

6. Para enviar una notificación de disposición de mensaje (MDN) sincrónica para los mensajes recibidos, seleccione **Enviar MDN**.

7. Para enviar MDN firmadas, seleccione **Enviar MDN firmado**.

8. Para enviar MDN asincrónicas para los mensajes recibidos, seleccione **Enviar MDN asincrónico**.

9. Cuando haya terminado, asegúrese de guardar la configuración con **Aceptar**.

Ahora el contrato está preparado para controlar los mensajes entrantes que cumplan la configuración seleccionada.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Override message properties |Indica que se pueden invalidar las propiedades de los mensajes recibidos. |
| Message should be signed |Exige que los mensajes se firmen digitalmente. Configure el certificado público del asociado invitado para la comprobación de firmas.  |
| Message should be encrypted |Exige el cifrado de los mensajes. Los mensajes sin cifrado se rechazan. Configure el certificado privado del asociado del host para el descifrado de los mensajes.  |
| Message should be compressed |Requiere que los mensajes estén comprimidos. Los mensajes sin comprimir se rechazan. |
| MDN Text (Texto de MDN) |La notificación de disposición del mensaje (MDN) predeterminada que se enviará al remitente del mensaje. |
| Send MDN (Enviar MDN) |Exige el envío de MDN. |
| Send signed MDN (Enviar MDN firmada) |Exige que las MDN estén firmadas. |
| MIC Algorithm (Algoritmo de MIC) |Selecciona el algoritmo que se utilizará para firmar los mensajes. |
| Send asynchronous MDN (Enviar MDN asincrónica) | Exige que los mensajes se envíen de forma asincrónica. |
| URL | Especifica la dirección URL a la que enviar la MDN. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configuración de la forma en que su contrato envía mensajes

Puede configurar cómo este contrato identifica y administra los mensajes que se envían a los asociados a través de él.

1.  En **Agregar**, seleccione **Send Settings** (Configuración de envío).
Configure estas propiedades en función del contrato con el asociado con el que intercambia mensajes. Para las descripciones de las propiedades, consulte la tabla de esta sección.

    ![Establecimiento de las propiedades de "Configuración de envío"](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Seleccione **Habilitar la firma de mensajes** si desea enviar mensajes firmados al asociado. Para firmar los mensajes, en la lista **Algoritmo MIC**, seleccione el *algoritmo MIC del certificado privado del asociado del host*. En la lista **Certificado**, seleccione un [certificado privado del asociado del host](../logic-apps/logic-apps-enterprise-integration-certificates.md) existente.

3. Seleccione **Habilitar el cifrado de mensajes** para enviar mensajes cifrados al asociado. Para el cifrado de los mensajes, en la lista **Algoritmo de cifrado**, seleccione el *algoritmo del certificado público del asociado invitado*.
En la lista **Certificado**, seleccione un [certificado público del asociado invitado](../logic-apps/logic-apps-enterprise-integration-certificates.md) existente.

4. Para comprimir el mensaje, seleccione **Habilitar la compresión de mensajes**.

5. Para expandir el encabezado HTTP Content-Type en una sola línea, seleccione **Expandir encabezados HTTP**.

6. Para recibir MDN sincrónicas para los mensajes enviados, seleccione **Solicitar MDN**.

7. Para recibir MDN firmadas para los mensajes enviados, seleccione **Solicitar MDN firmada**.

8. Para recibir MDN asincrónicas para los mensajes enviados, seleccione **Solicitar MDN asincrónica**. Si selecciona esta opción, escriba la dirección URL donde se enviarán las MDN.

9. Para exigir la recepción sin rechazo, seleccione **Habilitar NRR**.  

10. Para especificar el formato de algoritmo que se usará en MIC o al iniciar sesión en los encabezados salientes del mensaje AS2 o MDN, seleccione **Formato del algoritmo SHA2**.  

11. Cuando haya terminado, asegúrese de guardar la configuración con **Aceptar**.

Ahora el contrato está preparado para controlar los mensajes salientes que cumplan la configuración seleccionada.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Enable message signing (Habilitar firma de mensajes) |Exige que se firmen todos los mensajes enviados desde el contrato. |
| MIC Algorithm (Algoritmo de MIC) |Algoritmo que se utilizará para firmar los mensajes. Configura el algoritmo MIC del certificado privado del asociado del host para firmar los mensajes. |
| Certificate |Seleccione el certificado que se va a usar para firmar los mensajes. Configura el certificado privado del asociado del host para firmar los mensajes. |
| Enable message encryption (Habilitar el cifrado de mensajes) |Exige que se cifren todos los mensajes enviados desde el contrato. Configura el algoritmo del certificado público del asociado invitado para el cifrado de los mensajes. |
| Algoritmo de cifrado |El algoritmo de cifrado que se va a utilizar para cifrar los mensajes. Configura el certificado público del asociado invitado para el cifrado de los mensajes. |
| Certificate |El certificado que se utilizará para cifrar los mensajes. Configura el certificado privado del asociado invitado para el cifrado de los mensajes. |
| Habilitar la compresión de mensajes |Exige que se compriman todos los mensajes enviados desde el contrato. |
| Unfold HTTP headers (Expandir encabezados HTTP) |Coloca el encabezado HTTP Content-Type en una sola línea. |
| Request MDN (Solicitar MDN) |Exige una MDN para todos los mensajes enviados desde el contrato. |
| Request signed MDN (Solicitar MDN firmada) |Exige que se firmen todas las MDN enviadas al contrato. |
| Request asynchronous MDN (Solicitar MDN asíncrona) |Exige que se envíen MDN asincrónicas a este contrato. |
| URL |Especifica la dirección URL a la que enviar la MDN. |
| Enable NRR (Habilitar NRR) |Exige la recepción sin rechazo (NRR), un atributo de comunicación que prueba que se recibieron los datos según la dirección indicada. |
| Formato del algoritmo SHA2 |Seleccione el formato de algoritmo que se usará en MIC o al iniciar sesión en los encabezados salientes del mensaje AS2 o MDN. |

## <a name="find-your-created-agreement"></a>Búsqueda del contrato creado

1.  Cuando termine de establecer las propiedades del contrato, en la hoja **Agregar**, elija **Aceptar** para terminar de crear el contrato y volver a la hoja de la cuenta de integración.

    Ahora el contrato recién agregado aparece en la lista **Acuerdos**.

2.  También puede ver los contratos en la información general de la cuenta de integración. En la hoja de la cuenta de integración, elija **Información general** y seleccione el icono **Acuerdos**. 

    ![Elección del icono "Acuerdos" para ver todos los contratos](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>Visualización de Swagger
Vea los [detalles de Swagger](/connectors/as2/). 

## <a name="next-steps"></a>pasos siguientes
* [Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  

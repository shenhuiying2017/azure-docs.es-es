---
title: "Conexión a un sistema SAP local en Azure Logic Apps | Microsoft Docs"
description: "Conexión a un sistema SAP local en el flujo de trabajo de aplicaciones lógicas a través de la puerta de enlace de datos local"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3fea93f558d5a4ef62550fd1f6486903cb812930
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>Conexión a un sistema SAP local en el flujo de trabajo de aplicaciones lógicas con el conector SAP 

La puerta de enlace de datos local le permite administrar los datos y acceder de manera segura a los recursos locales. En este tema se muestra cómo se pueden conectar las aplicaciones lógicas a un sistema SAP local. En este ejemplo, la aplicación lógica solicita un IDOC a través de HTTP y devuelve la respuesta.    

> [!NOTE]
> Limitaciones actuales: 
> - La aplicación lógica agota el tiempo si todos los pasos necesarios para la respuesta no finalizan dentro del [límite de tiempo de espera de la solicitud](./logic-apps-limits-and-config.md). En este escenario, las solicitudes pueden quedar bloqueadas. 
> - El selector de archivos no muestra todos los campos disponibles. En este escenario, puede agregar manualmente rutas de acceso.

## <a name="prerequisites"></a>Requisitos previos

- Instale y configure la versión 1.15.6150.1 u otra más reciente de la [puerta de enlace de datos local](https://www.microsoft.com/download/details.aspx?id=53127). En [Conexión a la puerta de enlace de datos local para las aplicaciones lógicas](http://aka.ms/logicapps-gateway) se enumeran los pasos. La puerta de enlace debe instalarse en una máquina local para poder continuar.

- Descargue e instale la última biblioteca de clientes de SAP en la misma máquina en la que instaló la puerta de enlace. Use cualquiera de las versiones de SAP siguientes: 
    - SAP Server
        - Cualquier SAP Server que admita .NET Connector (NCo) 3.0
 
    - SAP Client
        - SAP .NET Connector (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>Incorporación de desencadenadores y acciones para conectarse al sistema SAP

El conector SAP tiene acciones, pero no desencadenadores. Por lo tanto, tenemos que usar otro desencadenador al principio del flujo de trabajo. 

1. Agregue el desencadenador Request/Response (Solicitud/respuesta) y, a continuación, seleccione **Nuevo paso**.

2. Seleccione **Agregar una acción**y, a continuación, seleccione el conector SAP escribiendo `SAP` en el campo de búsqueda:    

     ![Seleccione el servidor de aplicaciones de SAP o el servidor de mensajes de SAP.](media/logic-apps-using-sap-connector/sap-action.png)

3. Seleccione el [**servidor de aplicaciones de SAP**](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) o el [**servidor de mensajes de SAP**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm), según la configuración de SAP. Si no tiene una conexión existente, se le pedirá que cree una.

   1. Seleccione **Connect via on-premises data gateway** (Conectarse mediante la puerta de enlace de datos local) y especifique los detalles del sistema SAP:   

       ![Agregue una cadena de conexión a SAP.](media/logic-apps-using-sap-connector/picture2.png)  

   2. En **Puerta de enlace**, seleccione una puerta de enlace existente, o instale una nueva; seleccione **Instalar puerta de enlace**.

        ![Instalación de una nueva puerta de enlace](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. Después de escribir todos los detalles, seleccione **Crear**. 
   Logic Apps configura y comprueba la conexión, asegurándose de que la conexión funciona correctamente.

4. Escriba un nombre para la conexión SAP.

5. Las distintas opciones de SAP ahora están disponibles. Para buscar la categoría IDOC, selecciónela en la lista. O escriba manualmente en la ruta de acceso y seleccione la respuesta HTTP en el campo **cuerpo**:

     ![Acción de SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Agregue la acción para crear una **respuesta HTTP**. El mensaje de respuesta debe proceder de la salida SAP.

7. Guarde la aplicación lógica. Pruébela enviando un IDOC a través de la dirección URL del desencadenador HTTP. Una vez enviado el IDOC, espere a recibir la respuesta de la aplicación lógica:   

     > [!TIP]
     > Consulte cómo [supervisar las aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Ahora que se ha agregado el conector SAP a la aplicación lógica, empiece a explorar otras funcionalidades:

- BAPI
- RFC

## <a name="get-help"></a>Obtener ayuda

Para formular preguntas, o responderlas, y saber lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Azure Logic Apps y los conectores, vote o envíe ideas en el [sitio de comentarios de usuario de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de cómo validar, transformar y realizar otras funciones similares a las de BizTalk en [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- [Conexión a datos locales](../logic-apps/logic-apps-gateway-connection.md) desde aplicaciones lógicas

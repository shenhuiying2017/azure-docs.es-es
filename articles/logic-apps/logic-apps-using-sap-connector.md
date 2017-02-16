---
title: Uso del conector de SAP con puertas de enlace de datos locales en Azure Logic Apps | Microsoft Docs
description: "Logic Apps le permite conectarse fácilmente a un sistema SAP local durante el flujo de trabajo."
services: logic-apps
documentationcenter: dev-center-name
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 12584e9aa9c1400aba040320bd3a5f3ba3017470
ms.openlocfilehash: a483f527d15372c94fe7fe813dc49c3d6423e1e3


---
# <a name="get-started-with-the-sap-connector"></a>Introducción al conector de SAP 

La conectividad de nube híbrida es la esencia de Logic Apps. La puerta de enlace de datos local le permite administrar los datos y acceder de manera segura a los recursos locales de Logic Apps. En este artículo, aprenderemos a establecer conexión con un sistema de SAP local en un escenario muy sencillo, que consiste en solicitar un IDOC a través de HTTP y enviar la respuesta de vuelta.    

 > [!NOTE]
 > Este conector de SAP admite los siguientes sistemas SAP. Actualmente, existe una limitación en el tiempo de espera de Logic Apps que bloquea las solicitudes que superan los 90 segundos. También hay una limitación en el número de campos que se muestran en el selector de archivos (las rutas deben agregarse manualmente).
 >
 >

## <a name="prerequisites"></a>Requisitos previos
- Instale y configure la [puerta de enlace de datos local](https://www.microsoft.com/en-us/download/details.aspx?id=53127) más reciente.  

    Instale la puerta de enlace de datos local más reciente, versión 1.15.6150.1 o superior, si no la tiene. Se puede encontrar instrucciones [en este artículo](http://aka.ms/logicapps-gateway). La puerta de enlace debe instalarse en una máquina local para poder continuar con el resto de los pasos.

- Descargue e instale la última biblioteca de clientes de SAP en la misma máquina en la que instaló la puerta de enlace universal.

## <a name="use-sap-connector"></a>Uso del conector de SAP

1. Vamos a crear un desencadenador de solicitudes HTTP. Después, escribiremos “SAP” en el campo de búsqueda para seleccionar la acción del conector de SAP.    
 ![Busque SAP](media/logic-apps-using-sap-connector/picture1.png)

2. Elija “SAP” (ApplicationHost o MessagingHost en función de la configuración de SAP) y cree una conexión utilizando la puerta de enlace universal.
 - Si no tiene una conexión existente, se le pedirá que cree una.
 - Si activa la casilla "Conectar mediante puerta de enlace de datos local", aparecerán campos adicionales.
 - Especifique la cadena del nombre de conexión.
 - Seleccione la puerta de enlace que instaló en el paso anterior o haga clic en “Instalar puerta de enlace” para instalar una nueva.   
 ![Agregue una cadena de conexión a SAP.](media/logic-apps-using-sap-connector/picture2.png)   
  
  > [!NOTE]
  > Existen dos conexiones de SAP diferentes: una para el [host de aplicación](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) y otra para el [host de mensajería](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).
  >
  >

3. Una vez proporcionados todos los detalles, haga clic en "Crear". Logic Apps configurará y probará la conexión para asegurarse de que funciona correctamente. Una vez realizadas todas las comprobaciones, verá las opciones de la tarjeta que seleccionó anteriormente. Utilice el selector de archivos para encontrar la categoría de IDOC adecuada o escriba manualmente la ruta y seleccione la respuesta HTTP en el campo del cuerpo.    
 ![ACCIÓN DE SAP](media/logic-apps-using-sap-connector/picture3.png)

4. Cree una respuesta HTTP agregando una nueva acción. El mensaje de respuesta debe salir de SAP.

5. Guarde la aplicación lógica y pruébela enviando un IDOC a través de la dirección URL del desencadenador HTTP.

6. Una vez enviado el IDOC, espere a recibir la respuesta de la aplicación lógica.   

  > [!TIP]
  > Consulte cómo [supervisar las aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md).
  >
  >

7. Una vez hecho esto, tendrá una aplicación lógica en funcionamiento que utilizará el conector de SAP. Puede empezar a explorar las otras funcionalidades que ofrece:
  - BAPI
  - RFC

## <a name="next-steps"></a>Pasos siguientes
- Información sobre [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Cree una [conexión local](../logic-apps/logic-apps-gateway-connection.md) a Logic Apps.


<!--HONumber=Jan17_HO3-->



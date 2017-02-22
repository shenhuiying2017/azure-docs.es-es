---
title: "Conexión a un sistema SAP local en Azure Logic Apps | Microsoft Docs"
description: "Uso de la puerta de enlace de datos local para conectarse a un sistema SAP local en el flujo de trabajo de aplicaciones lógicas"
services: logic-apps
documentationcenter: 
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 72ac4936c656847fa07f1c1a37d6ddbf4ec1acf4
ms.openlocfilehash: 62b30acf324a5ed6a1b817157c86575d83b4104e


---
# <a name="use-the-sap-connector-in-your-logic-app"></a>Uso del conector SAP en la aplicación lógica 

La puerta de enlace de datos local le permite administrar los datos y acceder de manera segura a los recursos locales. En este tema se describe el procedimiento para conectarse a un sistema SAP local para solicitar un IDOC a través de HTTP y enviar la respuesta de vuelta.    

 > [!NOTE]
> Limitaciones actuales:
 > - El tiempo de espera de Logic Apps se agota si hay una solicitud que supera los 90 segundos. En este escenario, se pueden bloquear las solicitudes. 
 > - El selector de archivos no muestra todos los campos disponibles. En este escenario, puede agregar manualmente rutas de acceso.

## <a name="prerequisites"></a>Requisitos previos
- Instale y configure la versión 1.15.6150.1 u otra más reciente de la [puerta de enlace de datos local](https://www.microsoft.com/download/details.aspx?id=53127). En [Conexión a la puerta de enlace de datos local para las aplicaciones lógicas](http://aka.ms/logicapps-gateway) se enumeran los pasos. La puerta de enlace debe instalarse en una máquina local para poder continuar.

- Descargue e instale la última biblioteca de clientes de SAP en la misma máquina en la que instaló la puerta de enlace. Use cualquiera de las versiones de SAP siguientes: 
    - SAP Server
        - SAP ECC 6.0 Unicode
        - SAP ECC 6.0 Unicode con EHP 4.0
        - SAP ECC 6.0 con EHP 7.0 y todas las versiones anteriores de EHP
 
    - SAP Client
        - SAP RFC SDK 7.20 UNICODE
        - SAP .NET Connector (NCo) 3.0

## <a name="add-the-sap-connector"></a>Incorporación del conector de SAP

El conector de SAP incluye acciones. No tiene ningún desencadenador. Como resultado, use otro desencadenador al principio del flujo de trabajo. 

1. Agregue el desencadenador Request/Response (Solicitud/respuesta) y, a continuación, seleccione **Nuevo paso**.
2. Seleccione **Agregar una acción**y, a continuación, seleccione el conector SAP escribiendo `SAP` en el campo de búsqueda:    
 ![Seleccionar el servidor de aplicaciones de SAP o el servidor de mensajes de SAP](media/logic-apps-using-sap-connector/picture1.png)

3. Seleccione [servidor de aplicaciones](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) o [servidor de mensajes](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) de **SAP**, según la configuración de SAP. Si no tiene una conexión existente, se le pedirá que cree una: 
    1. Seleccione **Connect via on-premises data gateway** (Conectarse mediante la puerta de enlace de datos local) y especifique los detalles del sistema SAP:   
 ![Agregar una cadena de conexión a SAP](media/logic-apps-using-sap-connector/picture2.png)  
    2. Seleccione una **puerta de enlace** existente. O bien, seleccione **Instalar puerta de enlace** para instalar una nueva puerta de enlace:    
 ![Instalar una nueva puerta de enlace](media/logic-apps-using-sap-connector/install-gateway.png)
  
    3. Después de escribir todos los detalles, seleccione **Crear**. Logic Apps configura y prueba la conexión para asegurarse de que funciona correctamente.

4. Escriba un nombre para la conexión SAP.

5. Las distintas opciones de SAP ahora están disponibles. Use el selector de archivos para buscar la categoría de IDOC. O escriba manualmente en la ruta de acceso y seleccione la respuesta HTTP en el campo **cuerpo**:     
 ![ACCIÓN DE SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Cree una respuesta HTTP mediante la adición de una nueva acción. El mensaje de respuesta debe proceder de la salida SAP.

7. Guarde la aplicación lógica. Pruébela enviando un IDOC a través de la dirección URL del desencadenador HTTP. Una vez enviado el IDOC, espere a recibir la respuesta de la aplicación lógica:   

  > [!TIP]
  > Consulte cómo [supervisar las aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Ahora que se ha agregado el conector SAP a la aplicación lógica, empiece a explorar otras funcionalidades:

  - BAPI
  - RFC

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información acerca de cómo validar, transformar y realizar otras funciones similares a las de BizTalk en [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Cree una [conexión local](../logic-apps/logic-apps-gateway-connection.md) a Logic Apps.



<!--HONumber=Feb17_HO1-->



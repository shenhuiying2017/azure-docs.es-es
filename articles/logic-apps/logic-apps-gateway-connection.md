---
title: 'Acceso a datos locales: Azure Logic Apps | Microsoft Docs'
description: "Cómo las aplicaciones lógicas pueden acceder a datos locales mediante la conexión a una puerta de enlace de datos local."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: ef7df25d8080cae41235dffb287906508d4a652d
ms.lasthandoff: 03/30/2017


---
# <a name="connect-to-on-premises-data-from-logic-apps"></a>Conexión a datos locales desde aplicaciones lógicas

Para acceder a datos locales, puede configurar una conexión a una puerta de enlace de datos local para conectores de Azure Logic Apps compatibles. Los siguientes pasos lo guían por el proceso de instalación y configuración de la puerta de enlace de datos local para que funcione con sus aplicaciones lógicas.
La puerta de enlace de datos local admite estas conexiones de origen de datos:

*   BizTalk Server
*    DB2  
*   Sistema de archivos
*   Informix
*   MQ
*    Base de datos de Oracle 
*   Servidor de aplicaciones de SAP 
*   Servidor de mensajes de SAP
*    SQL Server

Para más información acerca de estas conexiones, consulte [Conectores para Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="requirements"></a>Requisitos

* Debe tener una dirección de correo electrónico profesional o educativa en Azure para asociar la puerta de enlace de datos local a su cuenta (cuenta basada en Azure Active Directory).

* Si usa una cuenta de Microsoft (por ejemplo, @outlook.com), puede usar su cuenta de Azure para [crear una dirección de correo electrónico profesional o educativa](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

* Debe tener la [puerta de enlace de datos local ya instalada en una máquina local](logic-apps-gateway-install.md).

* Puede asociar la instalación a solo un recurso de puerta de enlace. No puede haber otra puerta de enlace de datos local de Azure que reclame su puerta de enlace. La reclamación se produce en el momento de la ([creación durante el paso 2 en este tema](#2-create-an-azure-on-premises-data-gateway-resource)).

## <a name="install-and-configure-the-connection"></a>Instalación y configuración de la conexión

### <a name="1-install-the-on-premises-data-gateway"></a>1. Instalación de la puerta de enlace de datos local

Si aún no lo ha hecho, siga estos pasos para [instalar la puerta de enlace de datos local](logic-apps-gateway-install.md). Para poder continuar con los demás pasos, asegúrese de haber instalado la puerta de enlace de datos en una máquina local.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Creación de un recurso de puerta de enlace de datos local de Azure

Después de instalar la puerta de enlace, debe asociar su suscripción de Azure con ella.

> [!IMPORTANT] 
> Asegúrese de que el recurso de puerta de enlace se haya creado en la misma región de Azure que la aplicación lógica. Si no lo implementa en la misma región, no podrá acceder a él en la aplicación lógica. 
> 

1. Inicie sesión en Azure con la misma dirección de correo electrónico profesional o académica utilizada durante la instalación de la puerta de enlace.
2. Elija **Nuevo**.
3. Busque y seleccione **Puerta de enlace de datos local**.
4. Para asociar la puerta de enlace a su cuenta, complete la información, lo que incluye seleccionar el valor adecuado en **Nombre de instalación**.
   
    ![Conexión a una puerta de enlace de datos local][1]

5. Para crear el recurso, elija **Crear**.

### <a name="3-create-a-logic-app-connection-in-logic-app-designer"></a>3. Creación de una conexión de aplicación lógica en el Diseñador de aplicación lógica

Ahora que su suscripción de Azure está asociada a una instancia de la puerta de enlace de datos local, puede crear una conexión con la puerta de enlace desde su aplicación lógica.

1. Abra una aplicación lógica y elija un conector que admita la conectividad local, como SQL Server.
2. Seleccione **Connect via on-premises data gateway** (Conectarse a través de la puerta de enlace de datos local).
   
    ![Creación de una puerta de enlace del diseñador de Aplicaciones lógicas][2]

3. Seleccione la **puerta de enlace** a la que desea conectarse y complete cualquier otra información obligatoria sobre la conexión.
4. Para crear la conexión, haga clic en **Crear**.

La conexión está ahora configurada para que la aplicación lógica la use.

## <a name="data-gateway-connection-modifications"></a>Modificaciones en la conexión de la puerta de enlace de datos
Una vez agregada la conexión de la puerta de enlace de datos en su aplicación lógica, es posible que tenga que modificarla a fin de ajustar los valores de configuración específicos para dicha conexión. Puede encontrar la conexión en cualquiera de estos dos lugares:
* En la hoja principal de la aplicación lógica, debería ver un panel para las conexiones de API en la sección Herramientas de desarrollo. Seleccione que se muestren todas las conexiones de API asociadas a la aplicación lógica. Una de ellas será la conexión de la puerta de enlace de datos. Si la selecciona, podrá ver y modificar la configuración vinculada a la conexión.
* Al seleccionar la hoja principal de Conexiones de API, aparecerán todas las conexiones de API de la suscripción. En esta lista, figurará la conexión de la puerta de enlace de datos. Si la selecciona, podrá ver y modificar la configuración vinculada a la conexión.

## <a name="next-steps"></a>Pasos siguientes

* [Ejemplos y escenarios habituales de las aplicaciones lógicas](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Características de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png


---
title: "Conexión a la puerta de enlace de datos local de Logic Apps | Microsoft Docs"
description: "Información sobre cómo crear una conexión a la puerta de enlace de datos local desde una aplicación lógica."
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
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 0a16f22b6e3bb60091409c64b631afcba3d6db10


---
# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Conexión a la puerta de enlace de datos local para las Aplicaciones lógicas
Los conectores de las aplicaciones lógicas compatibles permiten configurar la conexión para acceder a los datos locales a través de la puerta de enlace de datos local.  Los siguientes pasos le guiarán por el proceso de instalación y configuración de la puerta de enlace de datos local para trabajar con una aplicación lógica.

## <a name="prerequisites"></a>Requisitos previos
  * Debe utilizar una dirección de correo electrónico profesional o educativa en Azure para asociar la puerta de enlace de datos local a su cuenta (cuenta basada en Azure Active Directory).
  * Si usa una cuenta Microsoft (por ejemplo, @outlook.com, o @live.com)), podrá usar su cuenta de Azure para crear una dirección de correo electrónico profesional o educativa [siguiendo estos pasos](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal) 
  * Debe tener la puerta de enlace de datos local [instalada en una máquina local](logic-apps-gateway-install.md).
  * Ninguna otra puerta de enlace de datos local de Azure debe haber reclamado la puerta de enlace ([esto se produce en el proceso de creación del paso 2 que se indica a continuación](#2-create-an-azure-on-premises-data-gateway-resource)): una instalación solo puede estar asociada a un recurso de puerta de enlace.

## <a name="installing-and-configuring-the-connection"></a>Instalación y configuración de la conexión
### <a name="1-install-the-on-premises-data-gateway"></a>1. Instalación de la puerta de enlace de datos local
Puede encontrar información sobre cómo instalar la puerta de enlace de datos local [en este artículo](logic-apps-gateway-install.md).  La puerta de enlace debe instalarse en una máquina local para poder continuar con el resto de los pasos.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Creación de un recurso de puerta de enlace de datos local de Azure
Una vez instalada, debe asociar su suscripción de Azure a la puerta de enlace de datos local.

1. Inicie sesión en Azure con la misma dirección de correo electrónico profesional o académica utilizada durante la instalación de la puerta de enlace.
2. Haga clic en el botón de recurso **Nuevo** .
3. Busque y seleccione **Puerta de enlace de datos local**
4. Complete la información para asociar la puerta de enlace a su cuenta (lo que incluye seleccionar el **nombre de la instalación**
   
    ![Conexión a una puerta de enlace de datos local][1]
5. Haga clic en el botón **Crear** para crear el recurso.

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. Creación de una conexión con la aplicación lógica del diseñador
Ahora que su suscripción de Azure está asociada a una instancia de la puerta de enlace de datos local, puede crear una conexión con ella desde dentro de una Aplicación lógica.

1. Abra una aplicación lógica y elija un conector que admita la conectividad local (en la fecha en la que se redactó este artículo, SQL Server).
2. Seleccione la casilla **Connect via on-premises data gateway**
   
    ![Creación de una puerta de enlace del diseñador de Aplicaciones lógicas][2]
3. Seleccione la **puerta de enlace** a la que desea conectarse y cumplimente cualquier otra información obligatoria sobre la conexión.
4. Haga clic en **Crear** para crear la conexión.

Llegado a este punto, la conexión ya debería estar configurada correctamente para que pueda utilizarla en la aplicación lógica.  

## <a name="next-steps"></a>Pasos siguientes
* [Ejemplos y escenarios habituales de las aplicaciones lógicas](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Características de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png



<!--HONumber=Jan17_HO3-->



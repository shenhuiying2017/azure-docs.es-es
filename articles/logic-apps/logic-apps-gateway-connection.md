---
title: "Obtener acceso a orígenes de datos locales para Azure Logic Apps | Documentos de Microsoft"
description: "Configure la puerta de enlace de datos local para poder obtener acceso a orígenes de datos locales desde aplicaciones lógicas."
keywords: "obtener acceso a datos, local, transferencia de datos, cifrado, orígenes de datos"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: f385d832deed2eaf8ea21eb75d62944cbbf3d13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>Conexión a orígenes de datos locales desde aplicaciones lógicas con una puerta de enlace de datos local

Para obtener acceso a orígenes de datos locales desde las aplicaciones lógicas, configure una puerta de enlace de datos local que puedan usar las aplicaciones lógicas con conectores compatibles. La puerta de enlace actúa como un puente que permite la transferencia rápida de datos y el cifrado entre orígenes de datos locales y las aplicaciones lógicas. La puerta de enlace retransmite datos desde orígenes locales en canales cifrados hasta Azure Service Bus. Todo el tráfico se origina como tráfico de salida seguro desde el agente de puerta de enlace. Más información sobre [cómo funciona la puerta de enlace de datos](logic-apps-gateway-install.md#gateway-cloud-service). 

La puerta de enlace admite conexiones a estos orígenes de datos locales:

*   BizTalk Server 2016
*   DB2  
*   Sistema de archivos
*   Informix
*   MQ
*   MySQL
*   Base de datos de Oracle
*   PostgreSQL
*   Servidor de aplicaciones de SAP 
*   Servidor de mensajes de SAP
*   SharePoint
*   SQL Server
*   Teradata

En estos pasos se indica cómo configurar la puerta de enlace de datos local para trabajar con las aplicaciones lógicas. Para obtener más información sobre las conexiones admitidas, consulte [Conectores para Azure Logic Apps](../connectors/apis-list.md). 

Para información sobre cómo usar la puerta de enlace con otros servicios, consulte estos artículos:

*   [Puerta de enlace de datos local de Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Puerta de enlace de datos local de Azure Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Puerta de enlace de datos local de Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Administración de una puerta de enlace de datos local en Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>Requisitos

* Debe tener [instalada la puerta de enlace de datos local en un equipo local](logic-apps-gateway-install.md).

* Al iniciar sesión en Azure Portal, tiene que usar la misma cuenta profesional o educativa que se usó para [instalar la puerta de enlace de datos local](logic-apps-gateway-install.md#requirements). Además, su cuenta de inicio de sesión debe tener una suscripción de Azure que se usará cuando cree un recurso de puerta de enlace en Azure Portal para la instalación de la puerta de enlace.

* No puede haber ningún recurso de puerta de enlace de Azure que ya haya solicitado la instalación de la puerta de enlace. Solo puede asociar la instalación de la puerta de enlace a un recurso de puerta de enlace de Azure. La notificación se produce al crear el recurso de puerta de enlace, de modo que la instalación no está disponible para otros recursos.

* La puerta de enlace de datos local se ejecuta como un servicio de Windows y está configurada para usar `NT SERVICE\PBIEgwService` para las credenciales de inicio de sesión del servicio de Windows. Para crear y mantener el recurso de la puerta de enlace en Azure Portal, la [cuenta del servicio de Windows](../logic-apps/logic-apps-gateway-install.md) debe tener al menos permisos de **Colaborador**. 

  > [!NOTE]
  > Esta cuenta de servicio de Windows no es la misma que se usa para conectarse a orígenes de datos locales, ni la cuenta profesional o educativa de Azure que se usa para iniciar sesión en servicios en la nube.

## <a name="set-up-the-data-gateway-connection"></a>Configuración de una conexión de puerta de enlace de datos

### <a name="1-install-the-on-premises-data-gateway"></a>1. Instalación de la puerta de enlace de datos local

Si aún no lo ha hecho, siga los [pasos para instalar la puerta de enlace de datos local](logic-apps-gateway-install.md). Antes de continuar con los demás pasos, asegúrese de haber instalado la puerta de enlace de datos en un equipo local.

<a name="create-gateway-resource"></a>

### <a name="2-create-an-azure-resource-for-the-on-premises-data-gateway"></a>2. Creación de un recurso de Azure para la puerta de enlace de datos local

Después de instalar la puerta de enlace en un equipo local, debe crear la puerta de enlace de datos como recurso en Azure. En este paso también se asocia el recurso de puerta de enlace a su suscripción de Azure.

1. Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal"). Asegúrese de usar la misma dirección de correo electrónico profesional o educativa de Azure que se usó para instalar la puerta de enlace.

2. En el menú de la izquierda de Azure, elija **Nuevo** > **Enterprise Integration** > **On-premises data gateway**, como se muestra aquí:

   ![Busque "Puerta de enlace de datos local".](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. En la página **Crear puerta de enlace de conexión**, especifique estos datos para crear el recurso de puerta de enlace de datos:

    * **Nombre**: escriba un nombre para el recurso de puerta de enlace. 

    * **Suscripción**: seleccione la suscripción de Azure que se asociará al recurso de puerta de enlace. 
    Esta suscripción debe ser la misma que la aplicación lógica.
   
      La suscripción predeterminada se basa en la cuenta de Azure que usó para iniciar sesión.

    * **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente para implementar el recurso de puerta de enlace. 
    Los grupos de recursos le ayudan a administrar recursos de Azure relacionados como una colección.

    * **Ubicación**: Azure restringe esta ubicación a la misma región que se seleccionó para el servicio en la nube de puerta de enlace durante la [instalación de la puerta de enlace](logic-apps-gateway-install.md). 

      > [!NOTE]
      > Asegúrese de que la ubicación del recurso de puerta de enlace coincide con la ubicación del servicio en la nube de puerta de enlace. De lo contrario, es posible que la instalación de la puerta de enlace no aparezca en la lista de puertas de enlace instaladas para que la pueda seleccionar en el paso siguiente.
      > 
      > Puede usar regiones diferentes para el recurso de puerta de enlace y la aplicación lógica.

    * **Nombre de la instalación**: si la instalación de la puerta de enlace no está seleccionada, seleccione la puerta de enlace que ha instalado anteriormente. 

    Para agregar el recurso de puerta de enlace al panel de Azure, elija **Anclar al panel**. 
    Cuando termine, seleccione **Crear**.

    Por ejemplo:

    ![Proporcionar detalles para crear la puerta de enlace de datos local](./media/logic-apps-gateway-connection/createblade.png)

    Para buscar o ver la puerta de enlace de datos en cualquier momento, en el menú principal de Azure de la izquierda, vaya a **Más servicios** > **Enterprise Integration** > **Puertas de enlace de datos locales**.

    ![Vaya a "Más servicios", "Enterprise Integration", "Puertas de enlace de datos locales"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

### <a name="3-connect-your-logic-app-to-the-on-premises-data-gateway"></a>3. Conexión de la aplicación lógica a la puerta de enlace de datos local

Ahora que ha creado el recurso de puerta de enlace de datos y ha asociado su suscripción de Azure a ese recurso, cree una conexión entre la aplicación lógica y la puerta de enlace de datos.

> [!NOTE]
> La ubicación de la conexión de puerta de enlace debe existir en la misma región que la aplicación lógica, pero puede usar una puerta de enlace de datos que exista en una región distinta.

1. En Azure Portal, cree o abra la aplicación lógica en el Diseñador de aplicaciones lógicas.

2. Agregue un conector que admita conexiones locales, como SQL Server.

3. Siguiendo con el orden que se muestra, seleccione **Connect via on-premises data gateway** (Conectarse a través de la puerta de enlace de datos local), proporcione un nombre único de conexión y la información necesaria, y seleccione el recurso de puerta de enlace de datos que quiere usar. Cuando termine, seleccione **Crear**.

   > [!TIP]
   > Un nombre de conexión único le ayuda a identificar fácilmente la conexión más adelante, especialmente al crear varias conexiones. Si procede, incluya también el dominio completo para el nombre de usuario. 

   ![Creación de conexiones entre la aplicación lógica y la puerta de enlace de datos](./media/logic-apps-gateway-connection/blankconnection.png)

Enhorabuena, la conexión de la puerta de enlace ya está lista para que la use la aplicación lógica.

## <a name="edit-your-gateway-connection-settings"></a>Editar la configuración de conexión de la puerta de enlace

Después de crear una conexión de puerta de enlace para la aplicación lógica, es posible que quiera actualizar más tarde la configuración de esa conexión concreta.

1. Para buscar la conexión de puerta de enlace:

   * En el menú de la aplicación lógica, en **Herramientas de desarrollo**, seleccione **Conexiones de API**. 
   
     En el panel **Conexiones de API** se muestran todas las conexiones de API asociadas a la aplicación lógica, incluidas las conexiones de la puerta de enlace.

     ![Vaya a la aplicación lógica y seleccione "Conexiones de API".](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * O bien, en el menú principal de Azure, vaya a **Más servicios** > **Web y móvil** > **Conexiones de API** para ver todas las conexiones de API, incluidas las conexiones de puerta de enlace, que estén asociadas con su suscripción a Azure. 

   * O bien, en el menú principal de Azure, vaya a **Todos los recursos** para ver todas las conexiones de API, incluidas las conexiones de la puerta de enlace, que estén asociadas con su suscripción de Azure.

2. Seleccione la conexión de puerta de enlace que quiere ver o editar y elija **Editar conexión de API**.

   > [!TIP]
   > Si las actualizaciones no surten efecto, intente [detener y reiniciar el servicio de Windows de puerta de enlace](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>
## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>Cambiar o eliminar el recurso de puerta de enlace de datos local

Para crear un recurso de puerta de enlace diferente, asociar la puerta de enlace a un recurso diferente o quitar el recurso de puerta de enlace, puede eliminar el recurso de puerta de enlace sin que afecte a la instalación de puerta de enlace. 

1. En el menú principal de Azure, vaya a **Todos los recursos**. 
2. Busque y seleccione el recurso de puerta de enlace de datos.
3. Elija **Puerta de enlace de datos local** y, en la barra de herramientas de recursos, elija **Eliminar**.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Protección de las aplicaciones lógicas](./logic-apps-securing-a-logic-app.md)
* [Ejemplos y escenarios habituales de las aplicaciones lógicas](./logic-apps-examples-and-scenarios.md)

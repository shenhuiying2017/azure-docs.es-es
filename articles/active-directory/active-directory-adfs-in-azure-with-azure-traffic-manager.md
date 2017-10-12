---
title: "Implementación entre regiones geográficas de AD FS de alta disponibilidad en Azure con Azure Traffic Manager | Microsoft Docs"
description: "En este documento aprenderá cómo implementar AD FS en Azure para alta disponibilidad."
keywords: "Ad fs con Azure Traffic Manager, adfs con Azure Traffic Manager, geográfico, centro de datos múltiples, centros de datos geográficos, centros de datos geográficos múltiples, implementar AD FS en azure, implementar azure adfs, azure adfs, azure ad fs, implementar adfs, implementar ad fs, adfs en azure, implementar adfs en azure, implementar AD FS en azure, adfs de azure, introducción a AD FS, Azure, ADFS en Azure, iaas, ADFS, mover ADFS a azure"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: a14bc870-9fad-45ed-acd5-a90ccd432e54
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: anandy;billmath
ms.openlocfilehash: 077710049894d2690299ce0fcb0ead9911aa4bb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Implementación entre regiones geográficas de AD FS de alta disponibilidad en Azure con Azure Traffic Manager
[Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md) proporciona instrucciones paso a paso sobre cómo implementar una infraestructura de AD FS sencilla para su organización en Azure. En este artículo se proporcionan los pasos para crear una implementación entre regiones geográficas de AD FS en Azure mediante [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Azure Traffic Manager ayuda a crear una infraestructura de AD FS de alta disponibilidad y rendimiento, extendida geográficamente, para su organización mediante una serie de métodos de enrutamiento disponibles que satisfacen las distintas necesidades de la infraestructura.

Una infraestructura entre regiones geográficas de AD FS de alta disponibilidad permite:

* **Eliminación de puntos únicos de error:** con funcionalidades de conmutación por error de Azure Traffic Manager, puede lograr una infraestructura de AD FS de alta disponibilidad incluso cuando uno de los centros de datos deja de funcionar en algún lugar del mundo.
* **Rendimiento mejorado:** puede usar la implementación recomendada en este artículo para proporcionar una infraestructura de AD FS de alto rendimiento que pueda ayudar a los usuarios a autenticarse con mayor rapidez. 

## <a name="design-principles"></a>Principios de diseño
![Diseño general](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Los principios de diseño básicos serán los mismos que se muestran en los principios de diseño del artículo sobre la implementación de AD FS en Azure. El diagrama anterior muestra una extensión sencilla de la implementación básica en otra región geográfica. A continuación se muestran algunos puntos que hay que tener en cuenta al ampliar la implementación a una nueva región geográfica.

* **Red Virtual:** debe crear una nueva red virtual en la región geográfica en la que desea implementar una infraestructura de AD FS adicional. En el diagrama anterior, ve la red virtual Geo1 y la red virtual Geo2 como las dos redes virtuales en cada región geográfica.
* **Controladores de dominio y servidores de AD FS en una nueva red virtual geográfica:** se recomienda implementar controladores de dominio en una nueva región geográfica para que los servidores de AD FS de la nueva región no tengan que ponerse en contacto con un controlador de dominio de otra red lejana para realizar una autenticación y mejorar así el rendimiento.
* **Cuentas de almacenamiento:** las cuentas de almacenamiento están asociadas a una región. Como va a implementar máquinas en una nueva región geográfica, tendrá que crear nuevas cuentas de almacenamiento para usarlas en la región.  
* **Grupos de seguridad de red:** al igual que las cuentas de almacenamiento, los grupos de seguridad de red creados en una región no se pueden usar en otra región geográfica. Por lo tanto, necesitará crear nuevos grupos de seguridad de red similares a los de la primera región geográfica para subredes INT y DMZ en la nueva región geográfica.
* **Etiquetas DNS para direcciones IP públicas:** Azure Traffic Manager puede hacer referencia a los puntos de conexión SOLO a través de etiquetas DNS. Por lo tanto, es necesario crear etiquetas DNS para las direcciones IP públicas de los equilibradores de carga externos.
* **Azure Traffic Manager** : Microsoft Azure Traffic Manager permite controlar la distribución del tráfico de los usuarios a los puntos de conexión de servicio que se ejecutan en distintos centros de datos de todo el mundo. Azure Traffic Manager funciona en el nivel de DNS. Usa las respuestas DNS para dirigir el tráfico de usuario final a los puntos de conexión distribuidos globalmente. A continuación, los clientes se conectan a esos puntos de conexión directamente. Con las distintas opciones de enrutamiento de Rendimiento, Ponderado y Prioridad, puede elegir la opción de enrutamiento que mejor se adapte a las necesidades de su organización. 
* **Conectividad de red virtual a red virtual entre dos regiones:** no es necesario tener conectividad entre las redes virtuales en sí. Como cada red virtual tiene acceso a los controladores de dominio y tiene un servidor WAP y de AD FS propio, puede funcionar sin ninguna conectividad entre las redes virtuales de regiones distintas. 

## <a name="steps-to-integrate-azure-traffic-manager"></a>Pasos para integrar Azure Traffic Manager
### <a name="deploy-ad-fs-in-the-new-geographical-region"></a>Implementación de AD FS en la nueva región geográfica
Siga los pasos y las directrices de [Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md) para implementar la misma topología en la nueva región geográfica.

### <a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Etiquetas DNS para direcciones IP públicas de los equilibradores de carga (públicos) accesibles desde Internet
Como se mencionó anteriormente, Azure Traffic Manager solo puede hacer referencia a etiquetas DNS como puntos de conexión y, por lo tanto, es importante crear etiquetas DNS para direcciones IP públicas de los equilibradores de carga externos. La captura de pantalla muestra cómo puede configurar la etiqueta DNS para la dirección IP pública. 

![Etiqueta DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

### <a name="deploying-azure-traffic-manager"></a>Implementación de Azure Traffic Manager
Siga estos pasos para crear un perfil del administrador de tráfico. Para más información, también puede hacer referencia a [Administración de un perfil de Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Creación de un perfil de Traffic Manager:** asigne un nombre único a su perfil del administrador de tráfico. Este nombre del perfil forma parte del nombre DNS y actúa como prefijo para la etiqueta de nombre de dominio de Traffic Manager. El nombre/prefijo se agrega a trafficmanager.net para crear una etiqueta DNS para el administrador de tráfico. La captura de pantalla siguiente muestra el prefijo DNS del administrador de tráfico que se establece como mysts y la etiqueta DNS resultante será mysts.trafficmanager.net. 
   
    ![Creación del perfil de Traffic Manager](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
2. **Método de enrutamiento de tráfico:** existen tres opciones de enrutamiento disponibles en el administrador de tráfico:
   
   * Prioridad 
   * Rendimiento
   * Ponderado
     
     **Rendimiento** es la opción recomendada para lograr una infraestructura de AD FS con una alta capacidad de respuesta. Sin embargo, puede elegir el método de enrutamiento que mejor se adapte a sus necesidades de implementación. La funcionalidad de AD FS no se ve afectada por la opción de enrutamiento seleccionada. Consulte [Métodos de enrutamiento de tráfico de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md) para más información. En la captura de pantalla de ejemplo anterior, puede ver el método **Rendimiento** seleccionado.
3. **Configuración de puntos de conexión:** en la página del administrador de tráfico, haga clic en los puntos de conexión y seleccione Agregar. Se abrirá una página Agregar ounto de conexión similar a la siguiente captura de pantalla
   
   ![Configuración de extremos](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
   
   Para las distintas entradas, siga este criterio:
   
   **Tipo:** seleccione el punto de conexión de Azure ya que se va a apuntar a una dirección IP pública de Azure.
   
   **Nombre:** cree un nombre que desea asociar con el punto de conexión. Este no es el nombre DNS y no influye en los registros DNS.
   
   **Tipo de recurso de destino:** seleccione la dirección IP pública como valor de esta propiedad. 
   
   **Recurso de destino:** le proporcionará una opción para elegir entre las diferentes etiquetas DNS que tiene disponibles con su suscripción. Elija la etiqueta DNS correspondiente al punto de conexión que está configurando.
   
   Agregue un punto de conexión en cada región geográfica en la que desee que Azure Traffic Manager enrute tráfico hacia ella.
   Para más información y ver pasos detallados acerca de cómo agregar y configurar puntos de conexión en el administrador de tráfico, consulte [Adición, deshabilitación, habilitación o eliminación de puntos de conexión](../traffic-manager/traffic-manager-endpoints.md)
4. **Configurar sondeo:** en la página del administrador de tráfico, haga clic en Configuración. En la página de configuración, debe cambiar la configuración del monitor para realizar el sondeo en el puerto HTTP 80 y la ruta de acceso relativa /adfs/probe
   
    ![Configuración del sondeo](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 
   
   > [!NOTE]
   > **Asegúrese de que el estado de los puntos de conexión sea EN LÍNEA una vez completada la configuración**. Si todos los puntos de conexión están en estado "Degradado"', Azure Traffic Manager intentará enrutar el tráfico lo mejor que pueda suponiendo que el diagnóstico sea correcto y que todos los puntos de conexión estén accesibles.
   > 
   > 
5. **Modificación de registros DNS para Azure Traffic Manager:** el servicio de federación debe ser un registro CNAME para el nombre DNS de Azure Traffic Manager. Cree un registro CNAME en los registros DNS públicos para que quien intente ponerse en contacto con el servicio de federación llegue realmente a Azure Traffic Manager.
   
    Por ejemplo, para que el servicio de federación fs.fabidentity.com apunte a Traffic Manager, necesitará actualizar el registro de recursos DNS para que sea el siguiente:
   
    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

## <a name="test-the-routing-and-ad-fs-sign-in"></a>Prueba de enrutamiento y de inicio de sesión de AD FS
### <a name="routing-test"></a>Prueba de enrutamiento
Una prueba muy básica para el enrutamiento sería intentar hacer ping en el nombre DNS del servicio de federación desde una máquina de cada región geográfica. Según el método de enrutamiento seleccionado, el punto de conexión al que se hace ping se reflejará en realidad en la pantalla de ping. Por ejemplo, si ha seleccionado el enrutamiento de rendimiento, se alcanzará el punto de conexión más cercano a la región del cliente. A continuación se muestra la instantánea de dos pings desde dos máquinas de cliente de regiones distintas, una en la región de Asia oriental y la otra en el oeste de EE. UU. 

![Prueba de enrutamiento](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

### <a name="ad-fs-sign-in-test"></a>Prueba de inicio de sesión de AD FS
La forma más sencilla de probar AD FS es mediante la página IdpInitiatedSignon.aspx. Para poder hacerlo, es necesario habilitar IdpInitiatedSignOn en las propiedades de AD FS. Siga estos pasos para comprobar la configuración de AD FS.

1. Ejecute el siguiente cmdlet en el servidor AD FS, con PowerShell, para habilitarlo. 
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
2. Desde cualquier acceso de equipo externo https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Debería ver la página de AD FS como aparece a continuación:
   
    ![Prueba de ADFS - desafío de autenticación](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)
   
    y cuando se inicia sesión correctamente, aparecerá un mensaje de confirmación similar al siguiente:
   
    ![Prueba de ADFS - autenticación correcta](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)

## <a name="related-links"></a>Vínculos relacionados
* [Implementación básica de AD FS en Azure](active-directory-aadconnect-azure-adfs.md)
* [Microsoft Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [Métodos de enrutamiento del tráfico del Administrador de tráfico](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="next-steps"></a>Pasos siguientes
* [Administrar un perfil del Administrador de tráfico de Azure](../traffic-manager/traffic-manager-manage-profiles.md)
* [Adición, deshabilitación, habilitación o eliminación de extremos](../traffic-manager/traffic-manager-endpoints.md) 


---
title: Azure Traffic Manager - P+F | Microsoft Docs
description: "Este artículo ofrece respuestas a las preguntas más frecuentes sobre Traffic Manager."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: cc095b419eae7e85590cdd323a5cf3809c45452e
ms.lasthandoff: 03/22/2017

---

# <a name="traffic-manager-frequently-asked-questions-faq"></a>Preguntas más frecuentes (P+F) sobre Traffic Manager

## <a name="traffic-manager-basics"></a>Introducción a Traffic Manager

### <a name="what-ip-address-does-traffic-manager-use"></a>¿Qué dirección IP usa el Administrador de tráfico?

Tal y como se explica en la sección sobre el [funcionamiento de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), este servicio funciona en el nivel de DNS. Envía respuestas DNS para dirigir a los clientes al punto de conexión de servicio adecuado. Después, los clientes se conectan directamente al punto de conexión de servicio, y no a través del Administrador de tráfico.

Por lo tanto, este servicio no proporciona un punto de conexión o una dirección IP para que los clientes puedan conectarse. Por tanto, si desea una dirección IP estática para el servicio, esta debe configurarse en el servicio y no en Traffic Manager.

### <a name="does-traffic-manager-support-sticky-sessions"></a>¿Admite Traffic Manager sesiones temporales?

Tal y como se explica en la sección sobre el [funcionamiento de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), este servicio funciona en el nivel de DNS. para dirigir a los clientes al punto de conexión de servicio adecuado. Los clientes se conectan directamente al punto de conexión de servicio, y no a través de Traffic Manager. Por tanto, Traffic Manager no ve el tráfico HTTP que circula entre el cliente y el servidor.

Además, la dirección IP de origen de la consulta de DNS que recibe Traffic Manager pertenece al servicio DNS recursivo, no al cliente. Por lo tanto, Traffic Manager no tiene forma de realizar un seguimiento de los clientes individuales y no puede implementar sesiones temporales. Esta limitación es común en todos los sistemas de administración de tráfico basado en DNS; no se trata de una limitación específica de Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>¿Por qué obtengo un error HTTP al utilizar Traffic Manager?

Tal y como se explica en la sección sobre el [funcionamiento de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), este servicio funciona en el nivel de DNS. para dirigir a los clientes al punto de conexión de servicio adecuado. Después, los clientes se conectan directamente al punto de conexión de servicio, y no a través del Administrador de tráfico. Traffic Manager no ve el tráfico HTTP que circula entre cliente y servidor. Por tanto, todos los errores HTTP que obtenga deben provenir de la aplicación. Para que el cliente pueda conectarse a la aplicación, todos los pasos de la resolución DNS deben estar completos. Esto incluye cualquier interacción que tenga Traffic Manager en el flujo de tráfico de la aplicación.

Por tanto, debe llevar a cabo una investigación más extensa centrándose en la aplicación.

El encabezado host HTTP enviado desde el explorador del cliente es la fuente más habitual de problemas. Asegúrese de que la aplicación está configurada para aceptar el encabezado host correcto para el nombre de dominio que use. Para los puntos de conexión que utilizan Azure App Service, consulte [Configuración de un nombre de dominio personalizado para una aplicación web en Azure App Service mediante Traffic Manager](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>¿Cómo afecta al rendimiento el uso del Administrador de tráfico?

Tal y como se explica en la sección sobre el [funcionamiento de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), este servicio funciona en el nivel de DNS. Puesto que los clientes se conectan directamente a los puntos de conexión de servicio, el rendimiento no se verá afectado cuando se utiliza el Administrador de tráfico una vez establecida la conexión.

Como Traffic Manager se integra con las aplicaciones en el nivel de DNS, hay que insertar una búsqueda DNS adicional en la cadena de resolución DNS. El impacto del Administrador de tráfico en el tiempo de resolución DNS es mínimo. Traffic Manager usa una red global de servidores de nombres y redes de difusión por proximidad [anycast](https://en.wikipedia.org/wiki/Anycast) para asegurarse de que siempre se enruten las consultas de DNS en el servidor de nombres disponible más cercano. Además, como las respuestas DNS se almacenan en caché, la latencia DNS adicional que se genera por utilizar Administrador de tráfico se aplica solo a una fracción de las sesiones.

El método Rendimiento enruta el tráfico al punto de conexión más cercano disponible. Como resultado, el impacto en el rendimiento general asociado a este método debería ser mínimo. Cualquier aumento en la latencia de DNS se debería reemplazar por una menor latencia de red al punto de conexión.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>¿Qué protocolos de aplicación puedo usar con el Administrador de tráfico?

Tal y como se explica en la sección sobre el [funcionamiento de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), este servicio funciona en el nivel de DNS. Una vez finalizada la búsqueda DNS, los clientes se conectan directamente al punto de conexión de la aplicación, y no a través del Administrador de tráfico. Por tanto, la conexión puede emplear cualquier protocolo de aplicación. Sin embargo, para realizar las comprobaciones de estado de Traffic Manager se requiere un punto de conexión HTTP o HTTPS. El punto de conexión de una comprobación de estado puede ser diferente al punto de conexión de la aplicación al que se conectan los clientes.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>¿Puedo usar Traffic Manager con un nombre de dominio desnudo?

No. Los estándares DNS no permiten que los registros CNAME coexistan con otros registros DNS del mismo nombre. El vértice (o raíz) de una zona DNS siempre contiene dos registros DNS preexistentes; los registros SOA y NS autoritativos. Esto significa que no se puede crear un registro CNAME en el vértice de la zona sin infringir los estándares DNS.

Traffic Manager requiere un registro CNAME de DNS para asignar el nombre DNS del dominio personal. Por ejemplo, asigne www.contoso.com al nombre DNS del perfil de Traffic Manager contoso.trafficmanager.net. Además, este perfil devuelve un segundo registro CNAME de DNS para indicar a qué punto de conexión debe conectarse el cliente.

Para solucionar este problema, se recomienda utilizar una redirección HTTP para dirigir el tráfico desde el nombre de dominio desnudo a una dirección URL diferente que, a continuación, puede utilizar Traffic Manager. Por ejemplo, el dominio desnudo "contoso.com" puede redirigir a los usuarios al registro CNAME "www.contoso.com" que apunta al nombre DNS de Traffic Manager.

En nuestra cola de trabajos pendientes de características realizamos un seguimiento de los dominios vacíos que son totalmente compatibles con el Administrador de tráfico. Puede dar su apoyo a esta solicitud de característica [votando por ella en nuestro sitio de comentarios de la comunidad](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).


## <a name="traffic-manager-geographic-traffic-routing-method"></a>Método de enrutamiento del tráfico geográfico de Traffic Manager

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>¿En qué casos de uso el enrutamiento geográfico resulta útil? 
El enrutamiento geográfico tipo se puede usar en cualquier escenario en el que el cliente de Azure necesite distinguir a sus usuarios en función de las regiones geográficas. Un ejemplo de esto es proporcionar a los usuarios de regiones específicas una experiencia de usuario diferente a los usuarios de otras regiones. Otro ejemplo es cumplir los mandatos de soberanía de datos locales que requieren que los usuarios de una región específica sean atendidos solo por puntos de conexión de dicha región.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>¿Cuáles son las regiones que son compatibles con Traffic Manager para el enrutamiento geográfica? 
Puede encontrar la jerarquía de países o regiones utilizada por Traffic Manager [aquí](traffic-manager-geographic-regions.md). Aunque esta página se mantendrá actualizada con cualquier cambio que se realice, puede recuperar mediante programación la misma información mediante la [API de REST de Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>¿Cómo determina Traffic Manager desde dónde consulta un usuario? 
Traffic Manager busca la dirección IP de origen de la consulta (probablemente se trata de un solucionador DNS local que realiza la consulta en nombre del usuario) y usa una dirección IP interna a la asignación de la región para determinar la ubicación. Esta asignación se actualiza de forma continuada para tener en cuenta los cambios de Internet. 

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>¿El punto de conexión debe encontrarse físicamente en la misma región que la configurada para el enrutamiento geográfico? 
No, la ubicación del punto de conexión no impone restricción alguna sobre qué las regiones pueden asignarse a él. Por ejemplo, un punto de conexión en la región de Azure del centro de EE. UU. puede hacer que todos los usuarios de la India se redirijan a él.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>¿Se pueden asignar regiones geográficas a puntos de conexión en un perfil que no está configurado para el enrutamiento geográfico? 
Sí, si el método de enrutamiento de un perfil no es geográfico, puede usar la [API de REST de Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) para asignar las regiones geográficas a puntos de conexión de dicho perfil. En el caso de perfiles de tipo de enrutamiento no geográficos, se omitirá esta configuración. Si cambia después este perfil al tipo de enrutamiento geográfico, Traffic Manager usará esas asignaciones.


### <a name="when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic-i-am-getting-an-error"></a>Cuando intento cambiar el método de enrutamiento de un perfil existente a geográfico, obtengo un error.
Todos los puntos de conexión en un perfil con necesidades de enrutamiento geográfico deben tener al menos una región asignada a ellos. Para convertir un perfil existente al tipo de enrutamiento geográfico, primero debe asociar las regiones geográficas a todos sus puntos de conexión mediante la [API de REST de Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) antes de cambiar el tipo de enrutamiento a geográfico. Si usa el portal, tendrá que eliminar primero los puntos de conexión, cambiar el método de enrutamiento del perfil a geográfico y, después, agregar los puntos de conexión junto con su asignación de región geográfica. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>¿Por qué se recomienda encarecidamente que los clientes creen perfiles anidados en lugar de puntos de conexión en un perfil con el enrutamiento geográfico habilitado? 
Una región puede asignarse a un único punto de conexión dentro de un perfil si está utilizando el tipo de enrutamiento geográfico. Si ese punto de conexión no es un tipo anidado con un perfil secundario asociado a él, en caso de que ese punto de conexión esté incorrecto, Traffic Manager le seguirá enviando tráfico ya que la alternativa de no enviar ningún tráfico es peor. Traffic Manager no realiza la conmutación por error a otro punto de conexión, incluso cuando la región asignada es la "principal" de la región asignada al punto de conexión incorrecto (por ejemplo, si un punto de conexión con la región España es incorrecto, no se realizará la conmutación por error a otro punto de conexión que tenga la región Europa asignada a él). Esto se hace para asegurarse de que Traffic Manager respeta los límites geográficos que un cliente ha configurado en su perfil. Para obtener el beneficio de la conmutación por error a otro punto de conexión cuando un punto de conexión es incorrecto, se recomienda que las regiones geográficas se asignen a perfiles anidados con varios puntos de conexión dentro de ellos en lugar de a puntos de conexión individuales. De este modo, si se produce un error en un punto de conexión del perfil secundario anidado, el tráfico puede conmutar por error a otro punto de conexión en el mismo perfil secundario anidado.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>¿Existen restricciones en la versión de API que admite este tipo de enrutamiento?

Sí, solo la API versión 2017-03-01 y más reciente admiten el tipo de enrutamiento geográfico. Las versiones anteriores de la API no se pueden usar para crear perfiles de tipo de enrutamiento geográfico ni para asignar regiones geográficas a puntos de conexión. Si se usa una versión anterior de la API para recuperar perfiles desde una suscripción de Azure, no se devolverá ningún perfil de tipo de enrutamiento geográfico. Además, al usar las versiones anteriores de la API, los perfiles devueltos que tengan puntos de conexión con una asignación de región geográfica no mostrarán su asignación de región geográfica.



## <a name="traffic-manager-endpoints"></a>Puntos de conexión del Administrador de tráfico

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>¿Puedo usar el Administrador de tráfico con puntos de conexión de varias suscripciones?

No se pueden usar puntos de conexión de varias suscripciones con Azure Web Apps. Azure Web Apps requiere que cualquier nombre de dominio personalizado usado con Web Apps se use únicamente en una suscripción. No es posible usar Web Apps desde varias suscripciones con el mismo nombre de dominio.

Para otros tipos de punto de conexión, es posible usar el Administrador de tráfico con puntos de conexión de más de una suscripción. La forma en que configure Traffic Manager depende de si usa el modelo de implementación clásica o el de Resource Manager.

* En Resource Manager, pueden agregarse puntos de conexión de cualquier suscripción al Administrador de tráfico, siempre y cuando la persona que configura el perfil de este servicio tenga acceso de lectura al punto de conexión. Estos permisos pueden concederse mediante la funcionalidad de [control de acceso basado en rol (RBAC) de Azure Resource Manager](../active-directory/role-based-access-control-configure.md).
* En la interfaz del modelo de implementación clásica, Traffic Manager requiere que Cloud Services o Web Apps, cuando estén configurados como puntos de conexión de Azure, se encuentren en la misma suscripción que el perfil de Traffic Manager. Los puntos de conexión de servicio en la nube en otras suscripciones se pueden agregar a Traffic Manager como puntos de conexión "externos". Estos puntos de conexión externos se facturan como puntos de conexión de Azure, en lugar de a la tarifa Puntos de conexión externos.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>¿Puedo usar Traffic Manager con espacios de ensayo de servicio en la nube?

Sí. Los espacios de ensayo de servicio en la nube se pueden configurar en Traffic Manager como puntos de conexión externos. Las comprobaciones de estado se siguen cobrando a la tarifa Puntos de conexión de Azure. Asimismo, dado que el tipo de punto de conexión externo está en uso, no se recopilan automáticamente los cambios en el servicio subyacente. Con puntos de conexión externos, Traffic Manager no detecta cuándo se detiene o elimina el servicio en la nube. Por lo tanto, Traffic Manager sigue facturando las comprobaciones de estado hasta que se deshabilita o elimina el punto de conexión.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>¿Admite el Administrador de tráfico puntos de conexión IPv6?

Traffic Manager no proporciona actualmente servidores de nombres que admitan direcciones IPv6. Sin embargo, aún se puede usar Traffic Manager con clientes IPv6 que se conecten a puntos de conexión IPv6. Un cliente no envía solicitudes de DNS directamente a Traffic Manager. En su lugar, el cliente usa un servicio DNS recursivo. Un cliente solo IPv6 envía solicitudes al servicio DNS recursivo por medio de IPv6. Después, el servicio recursivo debería poder entrar en contacto con los servidores de nombres de Traffic Manager mediante IPv4.

Traffic Manager responde con el nombre DNS del punto de conexión. Para admitir un punto de conexión IPv6, debe existir un registro AAAA de DNS que apunte el nombre DNS del punto de conexión a la dirección IPv6. Las comprobaciones de estado de Traffic Manager solo admiten direcciones IPv4. El servicio debe exponer un punto de conexión IPv4 en el mismo nombre DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>¿Puedo usar el Administrador de tráfico con más de una aplicación web en la misma región?

Normalmente, el Administrador de tráfico se utiliza para dirigir el tráfico a las aplicaciones implementadas en diferentes regiones. Sin embargo, también se puede utilizar en los casos en que una aplicación tenga más de una implementación en la misma región. Los puntos de conexión de Azure de Traffic Manager no permiten que se agregue más de un punto de conexión de aplicación web de la misma región de Azure al mismo perfil de Traffic Manager.

En los pasos siguientes se ofrece una solución alternativa a esta restricción:

1. Compruebe que los puntos de conexión estén en diferentes "unidades de escalado" de aplicación web. Un nombre de dominio debe estar asignado a un único sitio en una unidad de escalado concreta. Por lo tanto, dos aplicaciones web en la misma unidad de escalado no pueden compartir un perfil de Traffic Manager.
2. Agregue el nombre de dominio personal como nombre de host personalizado a cada aplicación web. Cada aplicación web debe estar en una unidad de escalado distinta. Todas las aplicaciones web deben pertenecer a la misma suscripción.
3. Agregue un único punto de conexión de aplicación web al perfil de Traffic Manager como punto de conexión de Azure.
4. Agregue cada punto de conexión de aplicación web adicional al perfil del Administrador de tráfico como punto de conexión externo. Los puntos de conexión externos solo se pueden agregar mediante el modelo de implementación de Resource Manager.
5. Cree un registro CNAME de DNS en su dominio personal que apunte al nombre DNS del perfil de Traffic Manager (<…>.trafficmanager.net).
6. Acceda al sitio mediante el nombre de dominio personal, no el nombre DNS del perfil del Administrador de tráfico.

##  <a name="traffic-manager-endpoint-monitoring"></a>Supervisión de puntos de conexión de Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>¿Traffic Manager es resistente a errores de región de Azure?

Traffic Manager es un componente clave de la entrega de aplicaciones de alta disponibilidad en Azure.
Para ofrecer alta disponibilidad, Traffic Manager debe tener un grado de disponibilidad excepcionalmente alto y ser resistente a errores regionales.

Por diseño, los componentes de Traffic Manager son resistentes a un error completo de cualquier región de Azure. Esta resistencia se aplica a todos los componentes del Administrador de tráfico: los servidores de nombres DNS, la API, la capa de almacenamiento y el servicio de supervisión del punto de conexión.

En el improbable caso de una interrupción de toda una región de Azure, se espera que Traffic Manager continúe funcionando con normalidad. Las aplicaciones implementadas en varias regiones de Azure pueden confiar en Traffic Manager para dirigir el tráfico a una instancia disponible de su aplicación.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>¿Cómo afecta al Administrador de tráfico la elección de la ubicación del grupo de recursos?

El Administrador de tráfico es servicio global individual. No es regional. La elección de la ubicación del grupo de recursos no supone ninguna diferencia con respecto a los perfiles del Administrador de tráfico implementados en dicho grupo de recursos.

Azure Resource Manager requiere que todos los grupos de recursos especifiquen una ubicación, que determina la ubicación predeterminada de los recursos implementados en dicho grupo de recursos. Cuando se crea un perfil de Traffic Manager, se crea en un grupo de recursos. Todos los perfiles de Traffic Manager usan **global** como ubicación, lo que omite la configuración predeterminada del grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>¿Cómo se determina el estado actual de cada punto de conexión?

El estado de supervisión actual de cada punto de conexión, junto con el del perfil global, se muestra en el Portal de Azure. Esta información también está disponible a través de la [API de REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), los [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) y la [CLI de Azure multiplataforma](../cli-install-nodejs.md) del Monitor de tráfico.

Azure no proporciona información histórica sobre el estado del punto de conexión o la capacidad de generar alertas sobre los cambios de estado del punto de conexión.

### <a name="can-i-monitor-https-endpoints"></a>¿Puedo supervisar puntos de conexión HTTPS?

Sí. El Administrador de tráfico admite el sondeo a través de HTTPS. Configure **HTTPS** como protocolo de la configuración de supervisión.

Traffic Manager no puede proporcionar ninguna validación de certificado, incluidos:

* No se validan certificados del servidor
* No se admiten certificados del servidor de SNI
* No se admiten certificados de cliente

### <a name="what-host-header-do-endpoint-health-checks-use"></a>¿Qué encabezado host se utiliza en las comprobaciones de estado de punto de conexión?

Traffic Manager usa encabezados de host en las comprobaciones de estado HTTP y HTTPS. El encabezado de host que usa Traffic Manager es el nombre del destino del punto de conexión configurado en el perfil. El valor utilizado en el encabezado host no se puede especificar por separado de la propiedad 'target'.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>¿Cuáles son las direcciones IP desde las que proceden las comprobaciones de estado?

La lista siguiente contiene las direcciones IP desde las que pueden originarse las comprobaciones de estado de Traffic Manager. Puede usar esta lista para asegurarse de que se permiten conexiones entrantes de estas direcciones IP en los puntos de conexión para comprobar su estado de mantenimiento.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

## <a name="traffic-manager-nested-profiles"></a>Perfiles anidados de Traffic Manager

### <a name="how-do-i-configure-nested-profiles"></a>¿Cómo se configuran los perfiles anidados?

Los perfiles anidados de Traffic Manager se pueden configurar mediante Azure Resource Manager y las API de REST del portal clásico, cmdlets de Azure PowerShell y comandos de la CLI de Azure multiplataforma. También son compatibles con el nuevo Azure Portal. No son compatibles con el portal clásico.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>¿Cuántas capas de anidamiento admite el Administrador de tráfico?

Puede anidar perfiles con hasta 10 niveles de profundidad. No se permiten "bucles".

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>¿Puedo combinar otros tipos de puntos de conexión con perfiles secundarios anidados, en el mismo perfil del Administrador de tráfico?

Sí. No hay ninguna restricción en el modo de combinar puntos de conexión de diferentes tipos dentro de un perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>¿Cómo se aplica el modelo de facturación para perfiles anidados?

El uso de perfiles anidados no afecta de forma negativa a los precios.

La facturación de Traffic Manager tiene dos componentes: comprobaciones de estado del punto de conexión y millones de consultas DNS.

* Comprobaciones de estado del punto de conexión: cuando un perfil secundario se configura como punto de conexión de un perfil primario no se aplica ningún cargo. La supervisión de los puntos de conexión del perfil secundario se facturan de la manera habitual.
* Consultas de DNS: cada consulta se cuenta una sola vez. Las consultas a un perfil primario que devuelven un punto de conexión de un perfil secundario solo se cuentan con respecto al perfil primario.

Para obtener todos los detalles, consulte la [página de precios de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>¿Se ve afectado el rendimiento por el uso de perfiles anidados?

No. No hay ningún efecto sobre el rendimiento derivado del uso de perfiles anidados.

Los servidores de nombres de Traffic Manager atravesarán la jerarquía de perfil internamente cuando se procese cada consulta de DNS. Una consulta de DNS realizada a un perfil primario puede recibir una respuesta de DNS con un punto de conexión de un perfil secundario. Se usará un único registro CNAME tanto si usa un solo perfil como si usa perfiles anidados. No hay necesidad de crear un registro CNAME para cada perfil de la jerarquía.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>¿Cómo calcula Traffic Manager el estado de un punto de conexión anidado en un perfil primario?

El perfil primario no realiza comprobaciones de estado en el perfil secundario directamente. En su lugar, el estado de los puntos de conexión del perfil secundario se usa para calcular el estado general del perfil secundario. Esta información se propaga hacia arriba en la jerarquía del perfil anidado para determinar el estado del punto de conexión anidado. El perfil primario usa este estado agregado para determinar si se puede dirigir el tráfico al perfil secundario.

En la tabla siguiente se describe el comportamiento de las comprobaciones de estado de Traffic Manager para un punto de conexión anidado.

| Estado de supervisión de perfiles secundarios | Estado de supervisión de extremos principales | Notas |
| --- | --- | --- |
| Deshabilitado. Se ha deshabilitado el perfil secundario. |Stopped |El estado del extremo primario es “Detenido”, no “Deshabilitado”. El estado “Deshabilitado” se reserva para indicar que el usuario ha deshabilitado el extremo en el perfil principal. |
| Degradado. Al menos un punto de conexión de perfil secundario se encuentra en estado Degradado. |En línea: el número de puntos de conexión en línea en el perfil secundario es, como mínimo, el valor de MinChildEndpoints.<BR>CheckingEndpoint: el número de puntos de conexión en línea más CheckingEndpoint en el perfil secundario es, como mínimo, el valor de MinChildEndpoints.<BR>Degradado: en caso contrario. |El tráfico se enruta a un punto de conexión con estado CheckingEndpoint. Si MinChildEndpoints se establece demasiado alto, el punto de conexión siempre se degradará. |
| En línea. Al menos un punto de conexión de perfil secundario se encuentra en estado En línea. Ningún punto de conexión está en estado Degradado. |Consulte más arriba. | |
| CheckingEndpoints. Al menos un punto de conexión de perfil secundario es "CheckingEndpoint". Ningún punto de conexión está "En línea" o "Degradado". |Igual que el anterior. | |
| Inactivo. Todos los puntos de conexión de perfil secundario están en estado "Deshabilitado" o "Detenido", o bien se trata de un perfil sin ningún punto de conexión. |Stopped | |

## <a name="next-steps"></a>Pasos siguientes:
- Obtenga más información sobre la [supervisión del punto de conexión y la conmutación por error automática](../traffic-manager/traffic-manager-monitoring.md)del Administrador de tráfico.
- Obtenga más información sobre los [métodos de enrutamiento del tráfico](../traffic-manager/traffic-manager-routing-methods.md)del Administrador de tráfico.

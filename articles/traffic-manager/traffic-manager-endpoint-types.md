---
title: Tipos de punto de conexión del Administrador de tráfico | Microsoft Docs
description: En este artículo, se explican los diferentes tipos de puntos de conexión que pueden utilizarse con el Administrador de tráfico de Azure.
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2016
ms.author: sewhee

---
# Puntos de conexión del Administrador de tráfico
El Administrador de tráfico de Microsoft Azure permite controlar cómo se distribuye el tráfico de usuarios a las implementaciones de aplicaciones que se ejecutan en distintos centros de datos o en otras ubicaciones del mundo.

Cada implementación de aplicaciones debe configurarse como un punto de conexión en el Administrador de tráfico. Cuando el Administrador de tráfico recibe una solicitud de DNS, selecciona uno de estos puntos de conexión para que se devuelva en la respuesta de DNS en función de la disponibilidad del punto de conexión actual y el método de enrutamiento de tráfico elegido. Para obtener más información, consulte [Cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).

En esta página se describe cómo el Administrador de tráfico admite diferentes tipos de puntos de conexión.

## Tipos de puntos de conexión del Administrador de tráfico
El Administrador de tráfico admite tres tipos de puntos de conexión:

* Los **puntos de conexión de Azure** se utilizan para los servicios hospedados en Azure.
* Los **puntos de conexión externos** se emplean para los servicios hospedados fuera de Azure; es decir, de forma local o con otro proveedor de hospedaje.
* Los **puntos de conexión anidados** se utilizan para combinar los perfiles del Administrador de tráfico con el objetivo de crear esquemas de enrutamiento de tráfico más flexibles y, de este modo, satisfacer los requisitos de implementaciones más complejas y de mayor envergadura.

No hay restricciones en cuanto a cómo se combinan los diferentes tipos de puntos de conexión en un único perfil del Administrador de tráfico. Cada perfil puede contener cualquier combinación de tipos de punto de conexión.

En las siguientes secciones se describe con más detalle cada tipo de punto de conexión.

### Puntos de conexión de Azure
Los puntos de conexión de Azure se utilizan para configurar servicios basados en Azure en el Administrador de tráfico. En estos momentos, admiten los siguientes tipos de recursos de Azure:

* Servicios en la nube PaaS y máquinas virtuales IaaS clásicos
* Aplicaciones web
* Recursos de PublicIPAddress (que pueden conectarse a las máquinas virtuales directamente o a través de una instancia de Azure Load Balancer). Tenga en cuenta que publicIpAddress debe tener un nombre DNS asignado para usarse en el Administrador de tráfico.

Los recursos de PublicIPAddress son exclusivos de Azure Resource Manager y no están disponibles en las API de Administración del servicio de Azure. Por lo tanto, solo se pueden utilizar en este servicio del Administrador de tráfico. Los otros tipos de punto de conexión pueden emplearse a través de Administración del servicio y Azure Resource Manager en el Administrador de Tráfico.

Al utilizar los puntos de conexión de Azure, el Administrador de tráfico detectará cuándo se ha iniciado y detenido un servicio en la nube PaaS o una máquina virtual IaaS clásica, o bien una aplicación web. Estas acciones se reflejan en el estado del punto de conexión (consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md#endpoint-and-profile-status) para obtener más información). Cuando se detiene el servicio subyacente, el Administrador de tráfico dejará de facturar las comprobaciones de estado del punto de conexión y no dirigirá el tráfico a este. Cuando se vuelve a iniciar el servicio, se reanuda la facturación y el punto de conexión puede recibir tráfico. Esto no sucede con los puntos de conexión de PublicIpAddress.

### Puntos de conexión externos
Los puntos de conexión externos se utilizan para configurar el Administrador de tráfico con el objetivo de dirigir el tráfico a los servicios fuera de Azure; por ejemplo, un servicio hospedado de manera local o con otro proveedor.

Los puntos de conexión externos pueden utilizarse de forma individual o combinados con los de Azure en el mismo perfil del Administrador de tráfico. La combinación de los puntos de conexión de Azure con los externos posibilita distintos escenarios:

* Utilizar Azure para proporcionar una mayor redundancia a una aplicación local existente en un modelo de conmutación por error de activo-activo o de activo-pasivo
* Utilizar Azure para extender una aplicación local existente a más ubicaciones geográficas, junto con el método de [enrutamiento de tráfico de rendimiento del Administrador de tráfico](traffic-manager-routing-methods.md#performance-traffic-routing-method), con el objetivo de reducir la latencia de la aplicación y conseguir que los usuarios finales obtengan un mejor rendimiento
* Utilizar Azure para ofrecer más capacidad a una aplicación local existente, bien de forma continua o bien empleando el modelo de migración a la nube para controlar un pico de demanda

En algunos casos, puede ser útil utilizar los puntos de conexión externos para hacer referencia a servicios de Azure (consulte las [preguntas más frecuentes](#faq) para ver ejemplos). En este caso, las comprobaciones de estado se facturarán con la tarifa de los puntos de conexión de Azure, y no los externos. Sin embargo, a diferencia de los puntos de conexión de Azure, si detiene o elimina el servicio subyacente, se le seguirá cobrando por las comprobaciones de estado correspondientes hasta que deshabilite o elimine expresamente el punto de conexión en el Administrador de tráfico.

### Puntos de conexión anidados
Los puntos de conexión anidados se utilizan para combinar los perfiles del Administrador de tráfico con el objetivo de crear esquemas de enrutamiento de tráfico más flexibles y, de este modo, satisfacer los requisitos de implementaciones más complejas y de mayor envergadura.

Gracias a ellos, se agrega como punto de conexión un perfil secundario a uno primario para crear una jerarquía. Los perfiles primarios y secundarios pueden contener otros puntos de conexión de cualquier tipo, incluidos otros perfiles anidados.

Para obtener más información, consulte [Perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md).

## Aplicaciones web como puntos de conexión
A la hora de configurar aplicaciones web como puntos de conexión en el Administrador de tráfico, hay que tener en cuenta otras consideraciones:

1. Solo las aplicaciones web del nivel de SKU Estándar o uno superior pueden utilizarse con el Administrador de tráfico. Las llamadas al Administrador de tráfico para agregar aplicaciones web de SKU de niveles inferiores no se realizarán correctamente. Si se degrada la SKU de una aplicación web existente, el Administrador de tráfico no podrá volver a enviar tráfico a dicha aplicación web y es posible que el punto de conexión se elimine del Administrador de tráfico.
2. Cuando se recibe una solicitud HTTP mediante el servicio de aplicaciones web, se utiliza el encabezado host de la solicitud para determinar qué aplicación web debe atenderla. El encabezado host contiene el nombre DNS utilizado para iniciar la solicitud, por ejemplo, contosoapp.azurewebsites.net. Para usar otro nombre DNS con la aplicación web, este debe estar registrado como dominio personalizado de la aplicación. Al agregar un punto de conexión de aplicación web a un perfil del Administrador de tráfico como punto de conexión de Azure, el nombre DNS del perfil del Administrador de tráfico se registra automáticamente como dominio personalizado de la aplicación. Este registro se elimina automáticamente cuando se elimina el punto de conexión.
3. Normalmente, una aplicación web se configurará como punto de conexión de Azure. Sin embargo, existen algunas circunstancias en las que resulta útil configurar una aplicación web con un punto de conexión externo (consulte, por ejemplo, la siguiente sección). Solo se pueden configurar aplicaciones web como puntos de conexión externo en el Administrador de tráfico cuando se emplea este servicio de Azure Resource Manager, y no Administración del servicio.
4. Cada perfil del Administrador de tráfico puede tener, como máximo, un punto de conexión de aplicación web en cada región de Azure. En estas [preguntas más frecuentes](#faq) se ofrece una solución alternativa a esta restricción.

## Habilitación y deshabilitación de puntos de conexión
Deshabilitar un punto de conexión en el Administrador de tráfico es un proceso muy útil para eliminar temporalmente el tráfico de un punto de conexión que se encuentre en modo de mantenimiento o que se vaya a volver a implementar. Cuando el punto de conexión esté de nuevo en funcionamiento, se puede volver a habilitar.

Los puntos de conexión se pueden habilitar y deshabilitar a través del portal del Administrador de tráfico, PowerShell, CLI o la API de REST, que son compatibles con Administración del servicio y Azure Resource Manager.

> [!NOTE]
> Deshabilitar un punto de conexión de Azure no afectará a su estado de implementación en Azure. Un servicio de Azure (por ejemplo, una máquina virtual o una aplicación web) seguirá ejecutándose y podrá recibir tráfico, aunque esté deshabilitado en el Administrador de tráfico, siempre que el tráfico se dirija directamente a ese servicio en lugar de a través del nombre DNS del perfil del Administrador de tráfico. Para obtener más información, consulte [Cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).
> 
> 

Cuando se deshabilita un punto de conexión, ya no se devolverá en las respuestas de DNS y, por tanto, no se dirigirá tráfico al punto de conexión. Además, se detendrán las comprobaciones de estado en el punto de conexión y no se le cobrará. La diferencia entre deshabilitar y eliminar puntos de conexión está en que, con la primera, es más fácil volver a habilitarlos.

La idoneidad actual de cada punto de conexión para recibir tráfico depende del estado del perfil (habilitado o deshabilitado), del estado del punto de conexión (habilitado o deshabilitado) y de los resultados de las comprobaciones de estado de dicho punto de conexión. Consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md#endpoint-and-profile-status) para obtener más información.

> [!NOTE]
> Debido a que el Administrador de tráfico funciona en el nivel de DNS, no es posible influir en las conexiones existentes con ningún punto de conexión. Al dirigir el tráfico entre los puntos de conexión (ya sea cambiando la configuración del perfil o durante la conmutación por error o la conmutación por recuperación), el Administrador de tráfico dirigirá las conexiones nuevas a los puntos de conexión disponibles. Sin embargo, otros puntos de conexión pueden continuar recibiendo tráfico a través de las conexiones existentes hasta que finalizan las sesiones. Para habilitar el tráfico que se depura de las conexiones existentes, las aplicaciones deben limitar la duración de la sesión que se utiliza con cada punto de conexión.
> 
> 

Si se deshabilitan todos los puntos de conexión de un perfil, o si se deshabilita el propio perfil, las consultas de DNS recibirán respuestas NXDOMAIN. Lo mismo sucede si se elimina el perfil.

## P+F
### ¿Puedo usar el Administrador de tráfico con puntos de conexión de varias suscripciones?
Para las aplicaciones web de Azure, esto no es posible. Esto se debe a que las Aplicaciones web requieren que cualquier nombre de dominio personalizado usado con ellas se use solo en una sola suscripción. No es posible usar Aplicaciones web de varias suscripciones con el mismo nombre de dominio y, por tanto, no pueden utilizarse con el Administrador de tráfico.

Para otros tipos de punto de conexión, es posible usar el Administrador de tráfico con puntos de conexión de más de una suscripción. Aunque la forma de hacerlo depende de si se utilizan las API de Administración del servicio o las de Azure Resource Manager del Administrador de tráfico. El [Portal de Azure](https://portal.azure.com) usa Resource Manager y el [portal 'clásico'](https://manage.windowsazure.com), administración de servicios.

En Resource Manager, pueden agregarse puntos de conexión de cualquier suscripción al Administrador de tráfico, siempre y cuando la persona que configura el perfil de este servicio tenga acceso de lectura al punto de conexión. Estos permisos pueden concederse mediante la funcionalidad de [control de acceso basado en rol (RBAC) de Azure Resource Manager](../active-directory/role-based-access-control-configure.md).

En Service Management, el Administrador de tráfico precisa que todos los servicios en la nube o aplicaciones web configurados como puntos de conexión de Azure residan en la misma suscripción que el perfil del Administrador de tráfico. Pueden agregarse puntos de conexión de servicio en la nube de otras suscripciones al Administrador de tráfico como puntos de conexión externos (se seguirán facturando con la tarifa de los puntos de conexión internos).

### ¿Puedo usar el Administrador de tráfico con ranuras de ensayo de servicio en la nube?
Sí. Las ranuras de ensayo de servicio en la nube se pueden configurar en el Administrador de tráfico como puntos de conexión externos.

Como el servicio al que hace referencia el punto de conexión reside en Azure, las comprobaciones de estado se seguirán facturando con la tarifa de los puntos de conexión de Azure.

Asimismo, dado que el tipo de punto de conexión externo está en uso, no se recopilan automáticamente los cambios en el servicio subyacente. Por lo tanto, si el servicio en la nube se detiene o elimina, el punto de conexión del Administrador de tráfico seguirá facturando comprobaciones de estado hasta que se deshabilite o elimine dicho punto de conexión del Administrador de tráfico.

### ¿Admite el Administrador de tráfico puntos de conexión IPv6?
Sí. Aunque el Administrador de tráfico no proporciona en estos momentos servidores de nombres direccionables IPv6, los clientes IPv6 que se conectan a los puntos de conexión de este tipo pueden seguir utilizando este servicio.

Un cliente solo IPv6 puede seguir utilizando el Administrador de tráfico, dado que no realiza las solicitudes de DNS directamente al Administrador de tráfico, sino que utiliza un servicio DNS recursivo. Puede efectuar solicitudes a este servicio a través de IPv6 y el servicio recursivo debería poder comunicarse por IPv4 con los servidores de nombres del Administrador de tráfico.

Una vez que recibe una consulta de DNS, Administrador de tráfico responderá con el nombre DNS del punto de conexión al que debe conectarse el cliente. Se puede utilizar un punto de conexión IPv6 configurando simplemente un registro AAAA DNS para que el nombre DNS del punto de conexión apunte a la dirección IPv6.

Tenga en cuenta que, para que las comprobaciones de estado del Administrador de tráfico funcionen correctamente, el servicio también tendrá que exponer un punto de conexión IPv4. Esto se debe asignar desde el mismo nombre DNS del punto de conexión con un registro A DNS.

### ¿Puedo usar el Administrador de tráfico con más de una aplicación web en la misma región?
Normalmente, el Administrador de tráfico se utiliza para dirigir el tráfico a las aplicaciones implementadas en diferentes regiones. Sin embargo, también se puede utilizar en los casos en que una aplicación tenga más de una implementación en la misma región.

En el caso de las aplicaciones web, los puntos de conexión de Azure del Administrador de tráfico no permiten que se agregue más de un punto de conexión de aplicación web de la misma región de Azure al mismo perfil del Administrador de tráfico. En los pasos siguientes se ofrece una solución alternativa a esta restricción:

1. Compruebe que las aplicaciones web de la misma región se encuentren en diferentes unidades de escalado de aplicación web; es decir, en distintas instancias del servicio de aplicaciones web. Para ello, compruebe la ruta DNS de la entrada DNS <...>.azurewebsites.net; la unidad de escalado deberá ser similar a esta: waws-prod-xyz-123.vip.azurewebsites.net. Debe asignar un nombre de dominio determinado a un único sitio de una unidad de escalado específica y, por este motivo, dos aplicaciones web de la misma unidad de escalado no pueden compartir un perfil del Administrador de tráfico.
2. Suponiendo que cada aplicación web está en una unidad de escalado diferente, agregue el nombre de dominio personal como nombre de host personalizado a cada una de las aplicaciones web. Para ello, todas las Aplicaciones web deben pertenecer a la misma suscripción.
3. Agregue un solo punto de conexión de aplicación web de la misma forma que lo haría con el perfil del Administrador de tráfico; es decir, como punto de conexión de Azure.
4. Agregue cada punto de conexión de aplicación web adicional al perfil del Administrador de tráfico como punto de conexión externo. Para ello, hay que utilizar el servicio Azure Resource Manager del Administrador de tráfico en lugar de Administración del servicio.
5. Cree un registro CNAME de DNS para asignar el dominio personal (tal y como se utilizó en el paso 2 anterior) al nombre DNS del perfil del Administrador de tráfico (<…>.trafficmanager.net).
6. Acceda al sitio mediante el nombre de dominio personal, no el nombre DNS del perfil del Administrador de tráfico.

## Pasos siguientes
* Consulte [Cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).
* Obtenga información sobre la [supervisión del punto de conexión y la conmutación por error automática](traffic-manager-monitoring.md) del Administrador de tráfico.
* Conozca los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) del Administrador de tráfico.

<!---HONumber=AcomDC_0824_2016-->
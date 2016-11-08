---
title: Supervisión de puntos de conexión del Administrador de tráfico y conmutación por error | Microsoft Docs
description: Este artículo le puede ayudar a comprender la forma en que el Administrador de tráfico utiliza la supervisión de puntos de conexión y la conmutación por error automática de los puntos de conexión para permitir que los clientes de Azure implementen aplicaciones de alta disponibilidad
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: ''

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee

---
# <a name="traffic-manager-endpoint-monitoring-and-failover"></a>Supervisión de puntos de conexión del Administrador de tráfico y conmutación por error
El Administrador de tráfico de Azure incluye la supervisión de puntos de conexión integrados y la conmutación por error automática de los puntos de conexión. Esta característica le ayuda a ofrecer aplicaciones de alta disponibilidad que son resistentes a los errores de punto de conexión, como los errores de las regiones de Azure.

## <a name="configure-endpoint-monitoring"></a>Configuración de la supervisión de los puntos de conexión
Para configurar la supervisión de los puntos de conexión, debe especificar la siguiente configuración en el perfil del Administrador de tráfico:

* **Protocolo**. Elija HTTP o HTTPS. Es importante que tenga en cuenta que la supervisión HTTPS no comprueba si el certificado SSL es válido, solo comprueba que está presente.
* **Puerto**. Elija el puerto que se usará para la solicitud.
* **Ruta de acceso**. Especifique la ruta de acceso relativa y el nombre de la página web o el archivo a los que accederá la supervisión. Una barra diagonal (/) es una entrada válida para la ruta de acceso relativa. Este valor implica que el archivo se encuentra en el directorio raíz (valor predeterminado).

Para comprobar el estado de cada punto de conexión, el Administrador de tráfico realiza una solicitud GET al punto de conexión mediante el protocolo, el puerto y la ruta de acceso relativa especificados.

Una práctica común consiste en implementar una página personalizada dentro de la aplicación, por ejemplo, /health.aspx. Con esta ruta de acceso para la supervisión, puede realizar comprobaciones específicas de la aplicación, como la comprobación de los contadores de rendimiento o la comprobación de disponibilidad de la base de datos. Según estas comprobaciones personalizadas, la página devuelve un código de estado HTTP adecuado.

Todos los puntos de conexión de un perfil de Traffic Manager comparten la configuración de supervisión. Si necesita usar diferentes valores de supervisión para los distintos puntos de conexión, puede crear [perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Estado de los puntos de conexión y de los perfiles
Puede habilitar y deshabilitar los perfiles y los puntos de conexión del Administrador de tráfico. Sin embargo, como resultado de la configuración y los procesos automatizados del Administrador de tráfico, también podría producirse un cambio en el estado del punto de conexión.

### <a name="endpoint-status"></a>Estado del extremo
Puede habilitar o deshabilitar un punto de conexión específico. El servicio subyacente, que puede seguir siendo correcto, no se ve afectado. Al cambiar el estado del punto de conexión, se controla la disponibilidad del punto de conexión en el perfil de Traffic Manager. Cuando el estado de un punto de conexión es Deshabilitado, Traffic Manager no comprueba su estado y el punto de conexión no se incluye en la respuesta DNS.

### <a name="profile-status"></a>Estado del perfil
Con la configuración de estado de perfil, puede habilitar o deshabilitar un perfil específico. Aunque el estado del punto de conexión afecta a un solo punto de conexión, el estado del perfil afecta a todo el perfil, lo que incluye todos los puntos de conexión. Cuando se deshabilita un perfil, no se comprueba si son correctos los puntos de conexión y no se incluyen en una respuesta DNS. Se devuelve un código de respuesta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) para la consulta de DNS.

### <a name="endpoint-monitor-status"></a>Estado de supervisión de punto de conexión
El estado de supervisión de un punto de conexión es un valor generado por Traffic Manager que muestra el estado del punto de conexión. No se puede cambiar esta configuración manualmente. El estado de la supervisión del punto de conexión es una combinación de los resultados de la supervisión del punto de conexión y el estado del punto de conexión configurado. Los valores posibles del estado de supervisión del punto de conexión se muestran en la tabla siguiente:

| Estado del perfil | Estado del extremo | Estado de supervisión de punto de conexión | Notas |
| --- | --- | --- | --- |
| Disabled |Enabled |Inactivo |El perfil se ha deshabilitado. Aunque el estado del punto de conexión es Habilitado, el estado del perfil (Deshabilitado) tiene preferencia. Los puntos de conexión en los perfiles Disabled no se supervisan. Se devuelve un código de respuesta NXDOMAIN para la consulta de DNS. |
| &lt;cualquiera&gt; |Disabled |Disabled |Se ha deshabilitado el punto de conexión. Los puntos de conexión dehabilitados no se supervisan. El punto de conexión no se incluye en las respuestas DNS y, por tanto, no recibe tráfico. |
| Enabled |Enabled |En línea |El punto de conexión se supervisa y su estado es correcto. Se incluye en las respuestas DNS y, por tanto, puede recibir tráfico. |
| Enabled |Enabled |Degradado |Error en las comprobaciones de estado de supervisión de punto de conexión. El punto de conexión no se incluye en las respuestas DNS y no recibe tráfico. |
| Enabled |Enabled |CheckingEndpoint |El punto de conexión se supervisa, pero los resultados del primer sondeo no se han recibido todavía. CheckingEndpoint es un estado temporal que por lo general se produce inmediatamente después de agregar o habilitar un punto de conexión en el perfil. Un punto de conexión en este estado se incluye en las respuestas DNS y puede recibir tráfico. |
| Enabled |Enabled |Stopped |El servicio en la nube o la aplicación web a los que apunta el punto de conexión no se están ejecutando. Compruebe la configuración de la aplicación web o del servicio en la nube. Un punto de conexión en estado Detenido no se supervisa. No se incluye en las respuestas DNS y, por tanto, no recibe tráfico. |

Para obtener más información sobre cómo se calcula el valor de estado de supervisión del punto de conexión en el caso de puntos de conexión anidados, consulte [Perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md).

### <a name="profile-monitor-status"></a>Estado de supervisión de perfiles
El estado de supervisión de perfiles es una combinación del estado del perfil configurado y los valores del estado de supervisión del punto de conexión para todos los puntos de conexión. Los valores posibles se describen en la tabla siguiente:

| Estado del perfil (según la configuración) | Estado de supervisión de punto de conexión | Estado de supervisión de perfiles | Notas |
| --- | --- | --- | --- |
| Disabled |&lt;cualquiera&gt; o un perfil sin puntos de conexión definidos. |Disabled |El perfil se ha deshabilitado. |
| Enabled |El estado de al menos un punto de conexión es Degradado. |Degradado |Revise los valores del estado de los puntos de conexión individuales para determinar cuáles de ellos requieren mayor atención. |
| Enabled |El estado de al menos un punto de conexión es En línea. Ningún punto de conexión tiene un estado Degradado. |En línea |El servicio acepta el tráfico. No se requiere ninguna acción adicional. |
| Enabled |El estado de al menos un punto de conexión es CheckingEndpoint. Ningún punto de conexión está en estado En línea o Degradado. |CheckingEndpoints |Este estado de transición se produce cuando se crea o habilita un perfil. El estado del punto de conexión se comprueba por primera vez. |
| Enabled |El estado de todos los puntos de conexión definidos en el perfil es Deshabilitado o Detenido, o el perfil no tiene ningún punto de conexión definido. |Inactivo |No hay puntos de conexión activos, pero el perfil está todavía habilitado. |

## <a name="endpoint-failover-and-recovery"></a>Conmutación por error y recuperación de un punto de conexión
Traffic Manager comprueba periódicamente el estado de cada punto de conexión, incluidos aquellos incorrectos. Traffic Manager detecta cuándo es correcto un punto de conexión y lo pone de nuevo en rotación.

> [!NOTE]
> El Administrador de tráfico solo considera que un punto de conexión está en línea si el mensaje devuelto es 200 - Correcto. Un punto de conexión es incorrecto cuando tienen lugar alguna de estas condiciones:
> 
> * Se recibe una respuesta distinta de 200 (que incluya un código que no sea 2xx o un redireccionamiento 301/302)
> * Solicitud de autenticación de cliente
> * Tiempo de espera (el umbral de tiempo de espera es de 10 segundos)
> * No es posible establecer la conexión
> 
> Para más información sobre la solución de problemas de comprobaciones erróneas, consulte [Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md).
> 
> 

La escala de tiempo siguiente es una descripción detallada del proceso de supervisión.

![Secuencia de conmutación por error y conmutación por error de puntos de conexión del Administrador de tráfico](./media/traffic-manager-monitoring/timeline.png)

1. **GET**. Para cada punto de conexión, el sistema de supervisión de Traffic Manager realiza una solicitud GET en la ruta de acceso y en el archivo especificados en la configuración de supervisión.
2. **200 - CORRECTO**. El sistema de supervisión espera que se devuelva un mensaje HTTP 200 OK al cabo de 10 segundos. Cuando recibe esta respuesta, reconoce que el servicio está disponible.
3. **30 segundos entre comprobaciones**. La comprobación de estado del punto de conexión se repite cada 30 segundos.
4. **Servicio no disponible**. El servicio en la nube deja de estar disponible. El Administrador de tráfico no lo sabrá hasta la siguiente comprobación de mantenimiento.
5. **Intentos para acceder al archivo de supervisión (cuatro intentos)**. El sistema de supervisión realiza una solicitud GET, pero no recibe una respuesta dentro del período de tiempo de espera de 10 segundos (como alternativa, se puede recibir una respuesta distinta de 200). Después lo intenta tres veces en intervalos de 30 segundos. Si uno de los intentos anteriores es correcto, se restablece el número de intentos.
6. **Estado establecido en Degradado**. Después del cuarto error consecutivo, el sistema de supervisión marca el estado del punto de conexión no disponible como Degradado.
7. **El tráfico se desvía a otros puntos de conexión**. Se actualizan los servidores de nombres DNS del Administrador de tráfico y el Administrador de tráfico ya no devuelve el punto de conexión en respuesta a consultas DNS. Las nuevas conexiones se dirigen a otros puntos de conexión disponibles. En cambio, es posible que las respuestas DNS anteriores que incluyen este punto de conexión aún se puedan almacenar en caché por parte de servidores y clientes DNS recursivos. Los clientes siguen usando el punto de conexión hasta que expira la caché DNS. Conforme la caché DNS expira, los clientes realizan nuevas solicitudes DNS, que se dirigen a otros puntos de conexión. La duración de la caché la controla la opción de TTL del perfil del Administrador de tráfico, por ejemplo 30 segundos.
8. **Continúan las comprobaciones de estado**. El Administrador de tráfico sigue comprobando el estado del punto de conexión mientras tenga un estado Degradado. Traffic Manager detecta cuándo vuelve a ser correcto el punto de conexión.
9. **El servicio vuelve a estar en línea**. El servicio vuelve a estar disponible. El punto de conexión permanece en estado Degradado en el Administrador de tráfico hasta que el sistema de supervisión realiza su siguiente comprobación de estado.
10. **Se reanuda el tráfico al servicio**. El Administrador de tráfico envía una solicitud GET y recibe una respuesta de estado 200 - Correcto. El servicio ha vuelto a un estado correcto. Los servidores de nombres de Traffic Manager se actualizan y comienzan a distribuir el nombre DNS del servicio en las respuestas DNS. El tráfico vuelve al punto de conexión a medida que caducan las respuestas DNS en caché que devuelven otros puntos de conexión, y conforme se terminan las conexiones existentes a otros puntos de conexión.

> [!NOTE]
> Como el Administrador de tráfico funciona en el nivel de DNS, no influye en las conexiones existentes a algún punto de conexión. Cuando dirige el tráfico entre los puntos de conexión (bien por la configuración del perfil modificada o durante la conmutación por error o la conmutación por recuperación), el Administrador de tráfico dirige las nuevas conexiones a los puntos de conexión disponibles. Sin embargo, otros puntos de conexión pueden continuar recibiendo tráfico a través de las conexiones existentes hasta que finalizan esas sesiones. Para habilitar el tráfico que se depura de las conexiones existentes, las aplicaciones deben limitar la duración de la sesión que se utiliza con cada punto de conexión.
> 
> 

## <a name="traffic-routing-methods"></a>Métodos de enrutamiento del tráfico
Cuando un punto de conexión tiene un estado Degradado, ya no se devuelve en respuesta a las consultas de DNS. En su lugar, se elige y se devuelve un punto de conexión alternativo. El método de enrutamiento de tráfico configurado en el perfil determina cómo se elige el punto de conexión alternativo.

* **Prioridad**. Los puntos de conexión forman una lista clasificada en orden de prioridad. Siempre se devuelve el primer punto de conexión disponible de la lista. Si un punto de conexión pasa al estado Degradado, se devuelve el siguiente punto de conexión disponible.
* **Ponderado**. Se elige un punto de conexión disponible al azar según sus ponderaciones asignadas y las ponderaciones de los demás puntos de conexión disponibles.
* **Rendimiento**. Se devuelve el punto de conexión más cercano al usuario final. Si dicho punto de conexión no está disponible, el punto de conexión se elige de forma aleatoria entre todos los disponibles. Al elegir un punto de conexión aleatorio, se evita un error en cascada que se puede producir cuando se sobrecarga el punto de conexión más cercano. Puede configurar planes de conmutación por error alternativos para el enrutamiento del tráfico de rendimiento mediante los [perfiles anidados de Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).

Para más información, consulte [Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md).

> [!NOTE]
> Una excepción a un comportamiento normal del enrutamiento de tráfico se produce cuando todos los puntos de conexión tienen un estado Degradado. Traffic Manager hace todo lo posible por *responder como si todos los puntos de conexión en estado Degradado estuvieran en realidad en estado en línea*. Este comportamiento es preferible a la otra alternativa, que sería no devolver ningún punto de conexión en la respuesta DNS. No se supervisan los puntos de conexión con el estado Detenido o Deshabilitado, por lo tanto, no se consideran aptos para el tráfico.
> 
> Normalmente, esta condición es consecuencia de una configuración incorrecta del servicio, como por ejemplo:
> 
> * Una lista de control de acceso [ACL] que bloquea las comprobaciones de estado de Traffic Manager
> * Una configuración incorrecta de la ruta de acceso de supervisión en el perfil de Traffic Manager
> 
> La consecuencia de este comportamiento es que si las comprobaciones de estado de Traffic Manager no están configuradas correctamente, podría parecer por el enrutamiento del tráfico que Traffic Manager *funciona* correctamente. En cambio, en este caso, la conmutación por error del punto de conexión no se puede producir, lo que afecta a la disponibilidad general de la aplicación. Es importante comprobar que el perfil muestra un estado En línea y no un estado Degradado. Un estado En línea indica que las comprobaciones de estado de Traffic Manager funcionan según lo esperado.
> 
> 

Para obtener más información sobre la solución de problemas de comprobaciones se estado erróneas, consulte [Solución de problemas de estado degradado en Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faq"></a>P+F
### <a name="is-traffic-manager-resilient-to-azure-region-failures?"></a>¿Traffic Manager es resistente a errores de región de Azure?
Traffic Manager es un componente clave de la entrega de aplicaciones de alta disponibilidad en Azure.
Para ofrecer alta disponibilidad, Traffic Manager debe tener un grado de disponibilidad excepcionalmente alto y ser resistente a errores regionales.

Por diseño, los componentes de Traffic Manager son resistentes a un error completo de cualquier región de Azure. Esta resistencia se aplica a todos los componentes del Administrador de tráfico: los servidores de nombres DNS, la API, la capa de almacenamiento y el servicio de supervisión del punto de conexión.

En el improbable caso de una interrupción de toda una región de Azure, se espera que Traffic Manager continúe funcionando con normalidad. Las aplicaciones implementadas en varias regiones de Azure pueden confiar en Traffic Manager para dirigir el tráfico a una instancia disponible de su aplicación.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager?"></a>¿Cómo afecta al Administrador de tráfico la elección de la ubicación del grupo de recursos?
El Administrador de tráfico es servicio global individual. No es regional. La elección de la ubicación del grupo de recursos no supone ninguna diferencia con respecto a los perfiles del Administrador de tráfico implementados en dicho grupo de recursos.

Azure Resource Manager requiere que todos los grupos de recursos especifiquen una ubicación, que determina la ubicación predeterminada de los recursos implementados en dicho grupo de recursos. Cuando se crea un perfil de Traffic Manager, se crea en un grupo de recursos. Todos los perfiles de Traffic Manager usan **global** como ubicación, lo que omite la configuración predeterminada del grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint?"></a>¿Cómo se determina el estado actual de cada punto de conexión?
El estado de supervisión actual de cada punto de conexión, junto con el del perfil global, se muestra en el Portal de Azure. Esta información también está disponible a través de la [API de REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), los [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) y la [CLI de Azure multiplataforma](../xplat-cli-install.md) del Monitor de tráfico.

Azure no proporciona información histórica sobre el estado del punto de conexión o la capacidad de generar alertas sobre los cambios de estado del punto de conexión.

### <a name="can-i-monitor-https-endpoints?"></a>¿Puedo supervisar puntos de conexión HTTPS?
Sí. El Administrador de tráfico admite el sondeo a través de HTTPS. Configure **HTTPS** como protocolo de la configuración de supervisión.

Traffic Manager no puede proporcionar ninguna validación de certificado, incluidos:

* No se validan certificados del servidor
* No se admiten certificados del servidor de SNI
* No se admiten certificados de cliente

### <a name="what-host-header-do-endpoint-health-checks-use?"></a>¿Qué encabezado host se utiliza en las comprobaciones de estado de punto de conexión?
Traffic Manager usa encabezados de host en las comprobaciones de estado HTTP y HTTPS. El encabezado de host que usa Traffic Manager es el nombre del destino del punto de conexión configurado en el perfil. El valor utilizado en el encabezado host no se puede especificar por separado de la propiedad 'target'.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate?"></a>¿Cuáles son las direcciones IP desde las que proceden las comprobaciones de estado?
La lista siguiente contiene las direcciones IP desde las que pueden originarse las comprobaciones de estado de Traffic Manager. Puede usar esta lista para asegurarse de que se permiten conexiones entrantes de estas direcciones IP en los puntos de conexión para comprobar su estado de mantenimiento.

* 13.75.153.124
* 13.75.152.253
* 191.232.214.62
* 191.232.208.52
* 52.172.155.168
* 52.172.158.37
* 13.75.124.254
* 13.75.127.63
* 137.135.82.249
* 137.135.80.149
* 104.41.190.203
* 104.41.187.209
* 65.52.217.19
* 23.96.236.252
* 40.87.147.10
* 40.87.151.34
* 104.215.91.84
* 13.84.222.37
* 40.68.30.66
* 40.68.31.178
* 137.135.47.215
* 137.135.46.163

## <a name="next-steps"></a>Pasos siguientes
Aprenda [cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md)

Aprenda más sobre los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) que admite el Administrador de tráfico.

Aprenda a [crear un perfil del Administrador de tráfico](traffic-manager-manage-profiles.md)

[Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md)

<!--HONumber=Oct16_HO2-->



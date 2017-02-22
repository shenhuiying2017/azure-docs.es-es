---
title: Perfiles anidados de Traffic Manager | Microsoft Docs
description: "En este artículo se explica la característica &quot;Perfiles anidados&quot; del Administrador de tráfico de Azure."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 3e48a28aa1ecda6792e79646a33875c8f01a878f
ms.openlocfilehash: fdf22a3f8d0ba6f1838af4f5e6924c8c0a18ef64

---

# <a name="nested-traffic-manager-profiles"></a>Perfiles anidados del Administrador de tráfico

Traffic Manager incluye una serie de métodos de enrutamiento del tráfico que permiten controlar el modo en que se elige el punto de conexión que debe recibir tráfico de cada usuario final. Para más información, consulte [Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md).

Cada perfil del Administrador de tráfico especifica un único método de enrutamiento del tráfico. No obstante, hay escenarios en que se requiere un enrutamiento del tráfico más sofisticado que el proporcionado mediante un único perfil de Traffic Manager. Puede anidar perfiles de Traffic Manager para combinar las ventajas de más de un método de enrutamiento del tráfico. Los perfiles anidados permiten invalidar el comportamiento predeterminado de Traffic Manager para admitir implementaciones de aplicaciones más grandes y complejas.

Los ejemplos siguientes muestran cómo usar perfiles anidados de Traffic Manager en distintos escenarios.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Ejemplo 1: Enrutamiento de tráfico combinado: de "rendimiento" y "ponderado"

Supongamos que ha implementado una aplicación en las siguientes regiones de Azure: oeste de EE. UU., Europa Occidental y Asia Oriental. Usará el método de enrutamiento de tráfico de "rendimiento" de Traffic Manager para distribuir el tráfico a la región más cercana al usuario.

![Perfil único del Administrador de tráfico][1]

Ahora, suponga que desea probar una actualización de su servicio antes de implementarla más ampliamente. Desea usar el método de enrutamiento de tráfico "ponderado", que puede dirigir un porcentaje pequeño del tráfico a la implementación de prueba. Configure la implementación de prueba junto con la implementación de producción existente en Europa occidental.

No puede combinar ambos métodos de enrutamiento en un solo perfil. Para este escenario, debe crear un perfil de Traffic Manager mediante los dos puntos de conexión de Europa Occidental y el método de enrutamiento del tráfico "ponderado". A continuación, agregue este perfil "secundario" como punto de conexión al perfil "primario". El perfil primario aún utiliza el método de enrutamiento de tráfico de rendimiento y contiene las otras implementaciones globales como puntos de conexión.

En el siguiente diagrama, se ilustra este concepto:

![Perfiles anidados del Administrador de tráfico][2]

En esta configuración, el tráfico dirigido mediante el perfil primario distribuye el tráfico entre las regiones normalmente. En Europa Occidental, el perfil anidado distribuye el tráfico a los puntos de conexión de prueba y producción según las ponderaciones asignadas.

Cuando el perfil primario utiliza el método de enrutamiento de tráfico de "rendimiento", se debe asignar una ubicación a cada punto de conexión. La ubicación se asigna al configurar el punto de conexión. Elija la región de Azure más cercana a su implementación. Las regiones de Azure son los valores de ubicación compatibles con la tabla de latencia de Internet. Para más información, consulte [Método de enrutamiento de tráfico de rendimiento de Traffic Manager](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Ejemplo 2: Supervisión de puntos de conexión en perfiles anidados

El Administrador de tráfico supervisa activamente el estado de cada punto de conexión de servicio. Si un punto de conexión es incorrecto, Traffic Manager dirige a los usuarios a puntos de conexión alternativos para mantener la disponibilidad del servicio. Este comportamiento de conmutación por error y supervisión de los puntos de conexión se aplica a todos los métodos de enrutamiento de tráfico. Para más información, consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md). La supervisión de puntos de conexión funciona de manera diferente para perfiles anidados. Con perfiles anidados, el perfil primario no realiza comprobaciones de estado en el perfil secundario directamente. En su lugar, el estado de los puntos de conexión del perfil secundario se usa para calcular el estado general del perfil secundario. Esta información de estado se propaga hacia arriba en la jerarquía de perfil anidado. El perfil primario usa este estado agregado para determinar si se debe dirigir el tráfico al perfil secundario. Consulte la sección de [preguntas más frecuentes](#faq) de este artículo para obtener todos los detalles sobre la supervisión del estado de los perfiles anidados.

Volviendo al ejemplo anterior, supongamos que la implementación de producción en Europa Occidental no funciona. De forma predeterminada, el perfil "secundario" dirigirá todo el tráfico a la implementación de prueba. Si la implementación de prueba tampoco funciona, el perfil primario determinará que el perfil secundario no debe recibir tráfico ya que todos los puntos de conexión secundarios tienen un estado incorrecto. A continuación, el perfil primario distribuirá el tráfico a las demás regiones.

![Conmutación por error de perfil anidado (comportamiento predeterminado)][3]

Puede que esta solución le satisfaga. O puede que le preocupe que todo el tráfico de Europa Occidental va ahora a la implementación de prueba en lugar de un tráfico de subconjunto limitado. Independientemente del estado de la implementación de prueba, puede que desee conmutar por error a las demás regiones cuando se produzca un error en la implementación de producción de Europa Occidental. Para habilitar esta conmutación por error, puede especificar el parámetro 'MinChildEndpoints' al configurar el perfil secundario como un punto de conexión del perfil primario. El parámetro determina el número mínimo de puntos de conexión disponibles en el perfil secundario. El valor predeterminado es&1;. En este escenario, debe establecer el valor de MinChildEndpoints en 2. Por debajo de este umbral, el perfil primario considerará que todo el perfil secundario no está disponible y dirigirá el tráfico a los otros puntos de conexión.

En la siguiente ilustración, se muestra esta configuración:

![Conmutación por error de perfil anidado con "MinChildEndpoints" = 2][4]

> [!NOTE]
> El método de enrutamiento de tráfico por 'prioridad' distribuye todo el tráfico a un solo punto de conexión. Por lo tanto, en este caso, no tiene mucho sentido una configuración de MinChildEndpoints distinta de "1" para un perfil secundario.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Ejemplo 3: Regiones de conmutación por error en orden de prioridad en el enrutamiento de tráfico de "rendimiento"

El comportamiento predeterminado del método de enrutamiento de tráfico de "rendimiento" está diseñado para evitar sobrecargar el siguiente punto de conexión más cercano y provocar una serie de errores en cascada. Cuando se produce un error en un punto de conexión, todo el tráfico que se habría dirigido a ese punto de conexión se distribuye uniformemente a los demás puntos de conexión de todas las regiones.

![Enrutamiento de tráfico de "rendimiento" con conmutación por error predeterminada][5]

Sin embargo, suponga que prefiere que el tráfico de Europa Occidental conmute por error a la región oeste de EE. UU. y únicamente dirigirlo a otras regiones si ambos puntos de conexión no están disponibles. Puede crear esta solución mediante un perfil secundario con el método de enrutamiento de tráfico por "prioridad".

![Enrutamiento de tráfico de "rendimiento" con conmutación por error preferencial][6]

Dado que el punto de conexión de Europa Occidental tiene una mayor prioridad que el punto de conexión del oeste de EE. UU., todo el tráfico se enviará al primero cuando ambos puntos de conexión estén en línea. Si Europa Occidental no funciona, su tráfico se dirige a Oeste de EE. UU. Con el perfil anidado, el tráfico se dirigirá a Asia Oriental solo si Europa Occidental y oeste de EE. UU. dan error.

Puede repetir este patrón con todas las regiones. Reemplace los tres puntos de conexión del perfil primario por tres perfiles secundarios, cada uno proporcionando una secuencia de conmutación por error ordenada por orden de prioridad.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Ejemplo 4: Control del enrutamiento de tráfico de "rendimiento" entre varios puntos de conexión de la misma región

Suponga que el método de enrutamiento de tráfico de "rendimiento" se usa en un perfil que tiene más de un punto de conexión en una determinada región. De forma predeterminada, el tráfico dirigido a esa región se distribuirá uniformemente entre todos los puntos de conexión disponibles en esa región.

![Enrutamiento de tráfico de "rendimiento" con distribución del tráfico en la región (comportamiento predeterminado)][7]

En lugar de agregar varios puntos de conexión en Europa Occidental, esos puntos de conexión se incluyen en un perfil secundario independiente. El perfil secundario se agrega al elemento primario como el único punto de conexión en Europa Occidental. La configuración en el perfil secundario puede controlar la distribución del tráfico en Europa Occidental y permitir en esa región el enrutamiento de tráfico ponderado o basado en la prioridad.

![Enrutamiento de tráfico de "rendimiento" con distribución personalizada del tráfico en la región][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Ejemplo 5: Configuración de la supervisión por punto de conexión

Imagine que utiliza Traffic Manager para migrar limpiamente desde un sitio web local heredado a una nueva versión basada en la nube hospedada en Azure. Para el sitio heredado, deseará el URI de la página de inicio para supervisar el estado del sitio. Pero para la nueva versión basada en la nube, implementará una página de supervisión personalizada que incluye comprobaciones adicionales (ruta de acceso ‘/monitor.aspx’).

![Supervisión de puntos de conexión del Administrador de tráfico (comportamiento predeterminado)][9]

La configuración de supervisión en un perfil de Traffic Manager se aplica a todos los puntos de conexión dentro de un solo perfil. Con los perfiles anidados puede usar un perfil secundario diferente por sitio para definir distintas configuraciones de supervisión.

![Supervisión de puntos de conexión del Administrador de tráfico con configuración por punto de conexión][10]

## <a name="faq"></a>P+F

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

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md)

Aprenda a [crear un perfil de Administrador de tráfico](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png



<!--HONumber=Jan17_HO1-->



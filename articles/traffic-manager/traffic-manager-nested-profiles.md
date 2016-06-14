<properties 
   pageTitle="Perfiles anidados del Administrador de tráfico | Microsoft Azure"
   description="En este artículo se explica la característica ";Perfiles anidados"; del Administrador de tráfico de Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jtuliani" />

# Perfiles anidados del Administrador de tráfico

El Administrador de tráfico incluye una serie de métodos de enrutamiento del tráfico que permiten controlar el modo en que se elige el punto de conexión que debe recibir tráfico de cada usuario final. Estos métodos se describen en [Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md) y permiten al Administrador de tráfico satisfacer los requisitos más comunes de enrutamiento del tráfico.

Cada perfil del Administrador de tráfico especifica un único método de enrutamiento del tráfico. No obstante, hay ocasiones en que aplicaciones más complejas requieren un enrutamiento del tráfico más sofisticado que se puede proporcionar mediante un único perfil del Administrador de tráfico.

Para estas aplicaciones más complejas, el Administrador de tráfico permite la combinación o el *anidamiento* de perfiles, lo que ofrece las ventajas de que una sola aplicación pueda usar más de un método de enrutamiento de tráfico. Los perfiles anidados le permiten crear esquemas de enrutamiento de tráfico más flexibles y eficaces para satisfacer las necesidades de implementaciones más grandes y complejas.

Además, los perfiles anidados permiten invalidar el comportamiento predeterminado del Administrador de tráfico en determinados casos, por ejemplo, el enrutamiento del tráfico dentro de una región o durante la conmutación por error cuando se emplea el enrutamiento de tráfico de rendimiento.

En el resto de esta página se explica, mediante una secuencia de ejemplos, cómo se pueden usar los perfiles anidados del Administrador de tráfico en diversos escenarios. Concluimos con algunas preguntas frecuentes sobre los perfiles anidados

## Ejemplo 1: Enrutamiento de tráfico combinado: de "rendimiento" y "ponderado"

Supongamos que la aplicación se implementa en varias regiones de Azure: oeste de EE. UU., Europa Occidental y Asia Oriental. Usará el método de enrutamiento de tráfico de "rendimiento" del Administrador de tráfico para distribuir el tráfico a la región más cercana al usuario.

![Perfil único del Administrador de tráfico][1]

Ahora, suponga que desea probar una actualización de su servicio con un número pequeño de usuarios antes de implementarla más ampliamente. Para ello, le gustaría usar el método de enrutamiento de tráfico "ponderado", que puede dirigir un porcentaje pequeño del tráfico a la implementación de prueba. Con un solo perfil, no puede combinar ambos métodos de enrutamiento. Pero con perfiles anidados, puede usar ambos.

Le mostramos cómo: suponga que quiere probar la nueva implementación en Europa Occidental. Configurará la implementación de prueba junto con la implementación de producción existente, y creará un perfil de Administrador de tráfico usando solo estos dos puntos de conexión y el método de enrutamiento del tráfico "ponderado". Luego, agregará este perfil "secundario" como punto de conexión al perfil "primario", que aún utiliza el método de enrutamiento de tráfico de rendimiento y que también contiene las otras implementaciones globales como puntos de conexión.

En el siguiente diagrama, se ilustra este concepto:

![Perfiles anidados del Administrador de tráfico][2]

Con esta disposición, el tráfico dirigido a través del perfil primario se distribuye entre regiones como normal. En Europa occidental, el tráfico se dirigirá entre las implementaciones de producción y prueba según los pesos asignados.

Tenga en cuenta que cuando el perfil primario utiliza el método de enrutamiento de tráfico de "rendimiento", es necesario conocer la ubicación de cada punto de conexión. Tanto para los puntos de conexión anidados como para los externos, esta ubicación se debe especificar como parte de la configuración de los puntos de conexión. Elija la región de Azure más cercana a su implementación; las opciones disponibles son las regiones de Azure, dado que esas son las ubicaciones que se admiten en la tabla de latencia de Internet. Para más información, consulte [Método de enrutamiento de tráfico de rendimiento del Administrador de tráfico de Azure](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## Ejemplo 2: Supervisión de puntos de conexión en perfiles anidados

El Administrador de tráfico supervisa activamente el estado de cada punto de conexión de servicio. Si se determina que un punto de conexión es incorrecto, el Administrador de tráfico dirigirá a los usuarios a puntos de conexión alternativos, de forma que se preserva la disponibilidad global de su servicio. Este comportamiento de conmutación por error y supervisión de los puntos de conexión se aplica a todos los métodos de enrutamiento de tráfico. Consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md) para más información.

Para los perfiles anidados, se aplican algunas reglas especiales de supervisión de puntos de conexión. Cuando un perfil primario está configurado con un perfil secundario como punto de conexión anidado, el primario no realiza directamente comprobaciones de estado en el secundario. En su lugar, el estado de los puntos de conexión del perfil secundario se usa para calcular el estado global del perfil secundario, y esta información se propaga por la jerarquía de perfiles anidados para determinar el estado del punto de conexión anidado dentro del perfil primario. De esta forma se determina si el perfil primario dirigirá el tráfico al secundario. Más [abajo](#faq) se proporcionan detalles completos de cómo se calcula el estado del punto de conexión anidado en el perfil primario a partir del estado del perfil secundario.

Volviendo al ejemplo 1, supongamos que la implementación de producción en Europa Occidental no funciona. De forma predeterminada, el perfil "secundario" dirigirá todo el tráfico a la implementación de prueba. Si tampoco funciona, el perfil primario determinará que como los puntos de conexión secundarios tienen un estado incorrecto, el perfil secundario no debe recibir el tráfico y todo el tráfico de Europa Occidental se conmutará por error a las demás regiones.

![Conmutación por error de perfil anidado (comportamiento predeterminado)][3]

Puede que esté satisfecho con este arreglo, o que le preocupe que la implementación de prueba se use para la conmutación por error de todo el tráfico de Europa Occidental, y que prefiera entonces realizar la conmutación por error a las otras regiones si la implementación de producción en Europa Occidental no funciona, *sin importar* el estado de la implementación de prueba. Este caso también es posible: al configurar el perfil secundario como punto de conexión en el perfil primario, puede especificar el parámetro 'MinChildEndpoints', que determina el número mínimo de puntos de conexión que deben estar disponibles en el perfil secundario. Por debajo del umbral (cuyo valor predeterminado es 1), el perfil primario considerará todo el perfil secundario como no disponible y dirigirá el tráfico a los otros puntos de conexión del perfil primario.

En el ejemplo siguiente se ilustra el parámetro 'MinChildEndpoints' establecido en 2. Si cualquiera de las implementaciones de Europa Occidental no funciona, el perfil primario determinará que el perfil secundario no debe recibir tráfico y se dirigirá a los usuarios a las otras regiones.

![Conmutación por error de perfil anidado con "MinChildEndpoints" = 2][4]

Tenga en cuenta que cuando el perfil secundario usa el método de enrutamiento de tráfico de "prioridad", un único punto de conexión recibe todo el tráfico a ese perfil secundario. Por lo tanto, en este caso, no tiene mucho valor una configuración de MinChildEndpoints distinta de "1".

## Ejemplo 3: Regiones de conmutación por error en orden de prioridad en el enrutamiento de tráfico de "rendimiento"

Cuando un solo perfil utiliza el enutamiento de tráfico de "rendimiento", si un punto de conexión (por ejemplo, Europa Occidental) no funciona, todo el tráfico que se hubiera dirigido a ese punto de conexión se distribuye entre los demás puntos de conexión, en todas las regiones. Este es el comportamiento predeterminado del método de enrutamiento de tráfico de "rendimiento", diseñado para evitar sobrecargar el siguiente punto de conexión más cercano y provocar una serie de errores en cascada.

![Enrutamiento de tráfico de "rendimiento" con conmutación por error predeterminada][5]

Sin embargo, suponga que prefiere que el tráfico de Europa Occidental conmute por error a la región oeste de EE.UU. y únicamente dirigirlo a otra parte si esos puntos de conexión no están disponibles. Puede hacerlo si crea un perfil secundario que use el método de enrutamiento de tráfico de "prioridad", como se ilustra:

![Enrutamiento de tráfico de "rendimiento" con conmutación por error preferencial][6]

Dado que el punto de conexión de Europa Occidental tiene una mayor prioridad que el punto de conexión Oeste de EE.UU., todo el tráfico se enviará al primero cuando ambos estén en línea. Si Europa Occidental no funciona, su tráfico se dirige a Oeste de EE. UU. Solo si Oeste de EE. UU. tampoco funciona, el tráfico de Europa Occidental se dirigirá a Asia Oriental.

Puede repetir este patrón con todas las regiones y sustituir los tres puntos de conexión del perfil primario por tres perfiles secundarios, cada uno de los cuales proporciona una secuencia de conmutación por error con prioridad.

## Ejemplo 4: Control del enrutamiento de tráfico de "rendimiento" entre varios puntos de conexión de la misma región

Suponga que el método de enrutamiento de tráfico de "rendimiento" se usa en un perfil que tiene más de un punto de conexión en una determinada región, por ejemplo, Oeste de EE UU. De forma predeterminada, el tráfico dirigido a esa región se distribuirá uniformemente entre todos los puntos de conexión disponibles en dicha región.

![Enrutamiento de tráfico de "rendimiento" con distribución del tráfico en la región (comportamiento predeterminado)][7]

Este valor predeterminado se puede cambiar mediante los perfiles anidados del Administrador de tráfico. En lugar de agregar varios puntos de conexión en el oeste de Estados Unidos, esos puntos de conexión se pueden incluir en un perfil secundario independiente y se puede agregar dicho perfil al primario solo como punto de conexión en dicha región. La configuración en el perfil secundario se puede usar entonces para controlar la distribución del tráfico en el oeste de EE. UU. y permitir (por ejemplo) en esa región el enrutamiento de tráfico ponderado o basado en la prioridad.

![Enrutamiento de tráfico de "rendimiento" con distribución personalizada del tráfico en la región][8]

## Ejemplo 5: Configuración de la supervisión por punto de conexión

Imagine que utiliza el Administrador de tráfico para migrar limpiamente entre un sitio web local heredado y una nueva versión basada en la nube hospedada en Azure. Con el sitio heredado, querrá usar la página principal (ruta de acceso ‘/’) para supervisar el estado del sitio, pero para la nueva versión basada en la nube, implementará una página de supervisión personalizada que incluye comprobaciones adicionales (ruta de acceso ‘/monitor.aspx’).

![Supervisión de puntos de conexión del Administrador de tráfico (comportamiento predeterminado)][9]

La configuración de supervisión en un perfil de Administrador de tráfico se aplica a todos los puntos de conexión de un perfil, lo que significa que anteriormente ha tenido que usar la misma ruta de acceso en ambos sitios. Con los perfiles anidados de Administrador de tráfico, ahora puede usar un perfil secundario por sitio para definir distintas configuraciones de supervisión por sitio:

![Supervisión de puntos de conexión del Administrador de tráfico con configuración por punto de conexión][10]

## P+F

### ¿Cómo se configuran los perfiles anidados?

Los perfiles anidados del Administrador de tráfico se pueden configurar con Azure Resource Manager (ARM) y las API de REST de administración de servicios de Azure (ASM), cmdlets de PowerShell y comandos de la CLI de Azure multiplataforma. También se admiten a través del Portal de Azure, sin embargo, no se admiten en el portal "clásico".

### ¿Cuántas capas de anidamiento admite el Administrador de tráfico?
Puede anidar perfiles con hasta 10 niveles de profundidad. No se permiten "bucles".

### ¿Puedo combinar otros tipos de puntos de conexión con perfiles secundarios anidados, en el mismo perfil del Administrador de tráfico?

Sí. No hay ninguna restricción en el modo de combinar puntos de conexión de diferentes tipos dentro de un perfil.

### ¿Cómo se aplica el modelo de facturación para perfiles anidados?

El uso de perfiles anidados no afecta de forma negativa a los precios.

La facturación del Administrador de tráfico tiene dos componentes: comprobaciones de estado del punto de conexión y millones de consultas DNS (para ver los detalles completos, consulte nuestra [página de precios](https://azure.microsoft.com/pricing/details/traffic-manager/)). Le mostramos cómo se aplica a los perfiles anidados:

- Comprobaciones de estado del punto de conexión: cuando un perfil secundario se configura como punto de conexión de un perfil primario no se aplica ningún cargo. Los puntos de conexión del perfil secundario que supervisan los servicios subyacentes se facturan de la forma habitual.

- Consultas de DNS: cada consulta se cuenta una sola vez. Las consultas a un perfil primario que devuelven un punto de conexión de un perfil secundario solo se facturan con respecto al perfil primario.

### ¿Se ve afectado el rendimiento por el uso de perfiles anidados?

No, no hay ningún efecto sobre el rendimiento derivado del uso de perfiles anidados.

Los servidores de nombres del Administrador de tráfico recorren la jerarquía de perfiles internamente al procesar las consultas de DNS, así que una consulta de DNS a un perfil primario puede recibir una respuesta DNS con un punto de conexión del perfil secundario.

Así que solo se usa un registro CNAME, igual que cuando se usa un único perfil de Administrador de tráfico. **No** es necesaria una cadena de registros CNAME, uno por cada perfil de la jerarquía y, por lo tanto, no se producen problemas de rendimiento.

### ¿Cómo calcula el Administrador de tráfico el estado de un punto de conexión anidado en un perfil primario? ¿Según el estado del perfil secundario?

Cuando un perfil primario está configurado con un perfil secundario como punto de conexión anidado, el primario no realiza directamente comprobaciones de estado en el secundario. En su lugar, el estado de los puntos de conexión del perfil secundario se usa para calcular el estado global del perfil secundario, y esta información se propaga por la jerarquía de perfiles anidados para determinar el estado del punto de conexión anidado dentro del perfil primario. De esta forma se determina si el perfil primario dirigirá el tráfico al secundario.

En la tabla siguiente se describe el comportamiento de las comprobaciones de estado del Administrador de tráfico para un punto de conexión anidado en un perfil primario que apunta a un perfil secundario.

|Estado de supervisión de perfiles secundarios|Estado de supervisión de extremos principales|Notas|
|---|---|---|
|Deshabilitado. El usuario ha deshabilitado el perfil secundario.|Detenido|El estado del extremo primario es “Detenido”, no “Deshabilitado”. El estado “Deshabilitado” se reserva para indicar que el usuario ha deshabilitado el extremo en el perfil principal.|
|Degradado. Al menos un punto de conexión de perfil secundario se encuentra en estado Degradado.|En línea: el número de puntos de conexión en línea en el perfil secundario es, como mínimo, el valor de MinChildEndpoints. CheckingEndpoint: el número de puntos de conexión en línea más CheckingEndpoint en el perfil secundario es, como mínimo, el valor de MinChildEndpoints. Degradado: en caso contrario.|El tráfico se enruta a un punto de conexión con estado CheckingEndpoint. Si MinChildEndpoints se establece demasiado alto, el punto de conexión siempre se degradará.|
|En línea. Como mínimo, hay un punto de conexión de perfil secundario en estado En línea y ninguno en estado Degradado.|Consulte más arriba.||
|CheckingEndpoints. Como mínimo, hay un punto de conexión de perfil secundario en estado "CheckingEndpoint" y ninguno en estado "En línea" o "Degradado"|Igual que el anterior.||
|Inactivo. Todos los puntos de conexión de perfil secundario están en estado "Deshabilitado" o "Detenido", o bien se trata de un perfil sin ningún punto de conexión|Detenido||


## Pasos siguientes

Más información sobre [cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md)

Aprenda a [crear un perfil de Administrador de tráfico](traffic-manager-manage-profiles.md).

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

<!---HONumber=AcomDC_0601_2016-->
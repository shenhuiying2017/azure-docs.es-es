<properties
   pageTitle="Supervisión de puntos de conexión del Administrador de tráfico y conmutación por error | Microsoft Azure"
   description="Este artículo le puede ayudar a comprender la forma en que el Administrador de tráfico utiliza la supervisión de puntos de conexión y la conmutación por error automática de los puntos de conexión para permitir que los clientes de Azure implementen aplicaciones de alta disponibilidad"
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
   ms.date="07/01/2016"
   ms.author="jtuliani" />

# Supervisión de puntos de conexión del Administrador de tráfico y conmutación por error

El Administrador de tráfico de Azure incluye la supervisión de puntos de conexión integrados y la conmutación por error automática de los puntos de conexión. Esta característica le ayuda a ofrecer aplicaciones de alta disponibilidad que son resistentes a los errores de punto de conexión, como los errores de las regiones de Azure.

El Administrador de tráfico funciona de esta manera: realiza solicitudes normales a cada puno de conexión y luego comprueba la respuesta. Si uno de los puntos de conexión no proporciona una respuesta válida, el Administrador de tráfico muestra su estado como Degradado. Ya no se incluye en las respuestas DNS, las cuales, devuelven en su lugar un punto de conexión disponible alternativo. De esta manera, el tráfico de usuario se aleja de los puntos de conexión erróneos y se dirige a los que están disponibles.

Las comprobaciones de estado de los puntos de conexión continúan hasta que se vuelven a poner en rotación, una vez que se recuperan y vuelven a tener un estado correcto.

## Configuración de la supervisión de los puntos de conexión

Para configurar la supervisión de los puntos de conexión, debe especificar la siguiente configuración en el perfil del Administrador de tráfico:

- **Protocolo**. Elija HTTP o HTTPS. Es importante que tenga en cuenta que la supervisión HTTPS no comprueba si el certificado SSL es válido, solo comprueba que está presente.
- **Puerto**. Elija el puerto que se usará para la solicitud. Los puertos HTTP estándar y HTTPS se encuentran entre las opciones.
- **Ruta de acceso**. Especifique la ruta de acceso relativa y el nombre de la página web o el archivo a los que intentará acceder la comprobación de estado de supervisión. Tenga en cuenta que una barra diagonal (/) es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado).

Para comprobar el estado de cada punto de conexión, el Administrador de tráfico realiza una solicitud GET al punto de conexión mediante el protocolo, el puerto y la ruta de acceso relativa especificados.

Una práctica común consiste en implementar una página personalizada dentro de la aplicación, por ejemplo, /health.aspx. Configure esta página para convertirla en la ruta de acceso de supervisión del punto de conexión del Administrador de tráfico. En dicha página, puede realizar todas las comprobaciones específicas de la aplicación necesarias, como la comprobación de la disponibilidad de una base de datos, antes de devolver HTTP 200 (si el servicio es correcto) o un código de estado diferente, si no lo es.

Los valores de la supervisión de los puntos de conexión se configuran en el nivel de perfil del Administrador de tráfico, no en cada uno de los puntos de conexión. Por consiguiente, se usan los mismos valores para comprobar el estado de todos los puntos de conexión. Si necesita utilizar diferentes valores de supervisión para los distintos puntos de conexión, puede hacerlo mediante los [perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## Estado de los puntos de conexión y de los perfiles

Puede habilitar y deshabilitar los perfiles y los puntos de conexión del Administrador de tráfico. Sin embargo, como resultado de la configuración y los procesos automatizados del Administrador de tráfico, también podría producirse un cambio en el estado del punto de conexión. Los parámetros siguientes describen cómo funciona esto.

### Estado del extremo

Estado del extremo es una configuración controlada por el usuario, por la que puede habilitar o deshabilitar un punto de conexión concreto. Esto no afecta el estado del servicio subyacente (que podría estar aún ejecutándose). Al contrario, controla la disponibilidad de un punto de conexión específico desde la perspectiva del Administrador de tráfico. Cuando el estado de un punto de conexión es Deshabilitado, el Administrador de tráfico no comprueba su estado y el punto de conexión no se incluye en la respuesta DNS.

### Estado del perfil

Estado del perfil es una configuración controlada por el usuario. Puede utilizarla para habilitar o deshabilitar un perfil fácilmente. Mientras que el estado del punto de conexión afecta a un solo punto de conexión, el estado del perfil afecta a todo el perfil, lo que incluye todos los puntos de conexión. No se comprueba el estado de los puntos de conexión de un perfil con estado Deshabilitado y no se incluye ningún punto de conexión en la respuesta DNS (se devuelve una respuesta NXDOMAIN).

### Estado de supervisión de punto de conexión

El estado de supervisión de un punto de conexión es una configuración generada por el Administrador de tráfico que muestra el estado actual del punto de conexión. No se puede cambiar esta configuración manualmente. Refleja la supervisión continuada del punto de conexión, además de otra información, como su estado. Los valores posibles del estado de supervisión del punto de conexión se muestran en la tabla siguiente. (Para más información sobre cómo se calcula el valor de estado de supervisión del punto de conexión en el caso de puntos de conexión anidados, consulte [Perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md)).

|Estado del perfil|Estado del extremo|Estado de supervisión de punto de conexión (API y Portal)|Notas|
|---|---|---|---|
|Disabled|Enabled|Inactivo|El usuario ha deshabilitado el perfil. Aunque el estado del punto de conexión puede seguir siendo Habilitado, el estado del perfil (Deshabilitado) tiene preferencia. Los puntos de conexión en estado deshabilitado no se supervisan y no se incluye ningún punto de conexión en las respuestas DNS (se devuelve una respuesta NXDOMAIN).|
|&lt;cualquiera&gt;|Disabled|Disabled|El usuario ha deshabilitado el punto de conexión. Un punto de conexión en estado Deshabilitado no se supervisa. No está disponible para incluirse en las respuestas DNS y, por lo tanto, no recibe tráfico.|
|Enabled|Enabled|En línea|El punto de conexión se supervisa y su estado es correcto. Está disponible para incluirse en las respuestas DNS y, por tanto, puede recibir tráfico.|
|Enabled|Enabled|Degradado|Error en las comprobaciones de estado de supervisión de punto de conexión. El punto de conexión no está disponible para incluirse en las respuestas DNS y, por tanto, no recibe tráfico.|
|Enabled|Enabled|CheckingEndpoint|El punto de conexión se supervisa, pero los resultados del primer sondeo no se han recibido todavía. Este estado es temporal y se produce normalmente cuando se acaba de agregar un nuevo punto de conexión al perfil o cuando se acaba de habilitar un punto de conexión o un perfil. Un punto de conexión en este estado está disponible para incluirse en las respuestas DNS y, por lo tanto, puede recibir tráfico.|
|Enabled|Enabled|Stopped|El servicio en la nube o la aplicación web a los que apunta el punto de conexión no se están ejecutando. Compruebe la configuración de la aplicación web o del servicio en la nube. Un punto de conexión en estado Detenido no se supervisa. No está disponible para incluirse en las respuestas DNS y, por lo tanto, no recibe tráfico.|

### Estado de supervisión de perfiles

El estado de supervisión de perfiles es una combinación del estado de supervisión de punto de conexión de todos los puntos de conexión del perfil y del estado del perfil configurado. Los valores posibles se describen en la tabla siguiente:

|Estado del perfil (según la configuración)|Estado de supervisión de punto de conexión|Estado de supervisión de perfil (API y Portal)|Notas|
|---|---|---|---|
|Disabled|&lt;cualquiera&gt; o un perfil sin extremos definidos.|Disabled|El usuario ha deshabilitado el perfil.|
|Enabled|El estado de al menos un punto de conexión es Degradado.|Degradado|Esta es una marca que indica que se requiere acción del cliente. Revise los valores del estado de los puntos de conexión individuales para determinar cuáles de ellos requieren mayor atención.|
|Enabled|El estado de al menos un punto de conexión es En línea. Ningún punto de conexión tiene un estado Degradado.|En línea|El servicio acepta tráfico y no es necesario que el cliente haga nada.|
|Enabled|El estado de al menos un punto de conexión es CheckingEndpoint. Ningún punto de conexión está en estado En línea o Degradado.|CheckingEndpoints|Estado de transición. Esto suele ocurrir cuando se acaba de crear o habilitar un perfil y es la primera vez que se comprueba el estado del punto de conexión.|
|Enabled|El estado de todos los puntos de conexión definidos en el perfil es Deshabilitado o Detenido, o el perfil no tiene ningún punto de conexión definido.|Inactivo|No hay puntos de conexión activos, pero el perfil está todavía habilitado.|

## Conmutación por error y conmutación por recuperación de un punto de conexión

Considere un escenario en el que un punto de conexión antes correcto produce un error. El Administrador de tráfico detecta el error y el punto de conexión se saca de la rotación. Más adelante, se recupera. El Administrador de tráfico detecta esta recuperación y el punto de conexión se vuelve a incorporar a la rotación.

>[AZURE.NOTE] El Administrador de tráfico solo considera que un punto de conexión está en línea si el mensaje devuelto es 200 - Correcto. Si se produce alguna de las acciones siguientes, el Administrador de tráfico la considera una comprobación de estado del punto de conexión erróneo:

>- Se recibe una respuesta distinta de 200 (que incluya un código que no sea 2xx o un redireccionamiento 301/302)
>- Solicitud de autenticación de cliente
>- Tiempo de espera (el umbral de tiempo de espera es de 10 segundos)
>- No es posible establecer la conexión

>Para más información sobre la solución de problemas de comprobaciones erróneas, consulte [Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md).

La escala de tiempo siguiente describe con todo detalle la secuencia de pasos que se produce.

![Secuencia de conmutación por error y conmutación por error de puntos de conexión del Administrador de tráfico](./media/traffic-manager-monitoring/timeline.png)

1. **GET**. El sistema de supervisión del Administrador de tráfico realiza una solicitud GET en la ruta de acceso y en el archivo especificados en la configuración de supervisión. Esto se repite en todos los puntos de conexión.
2. **200 - CORRECTO**. El sistema de supervisión espera que se devuelva un mensaje HTTP 200 OK al cabo de 10 segundos. Cuando recibe esta respuesta, reconoce que el servicio está disponible.
3. **30 segundos entre comprobaciones**. La comprobación de estado del punto de conexión se repetirá cada 30 segundos.
4. **Servicio no disponible**. El servicio en la nube deja de estar disponible. El Administrador de tráfico no lo sabrá hasta la siguiente comprobación de mantenimiento.
5. **Intentos para acceder al archivo de supervisión (cuatro intentos)**. El sistema de supervisión realiza una solicitud GET, pero no recibe una respuesta dentro del período de tiempo de espera de 10 segundos (como alternativa, se puede recibir una respuesta distinta de 200). A continuación, lo intenta tres veces en intervalos de 30 segundos. Si uno de los intentos anteriores es correcto, se restablece el número de intentos.
6. **Estado establecido en Degradado**. Después del cuarto error consecutivo, el sistema de supervisión marca el estado del punto de conexión no disponible como Degradado.
7. **El tráfico se desvía a otros puntos de conexión**. Se actualizan los servidores de nombres DNS del Administrador de tráfico y el Administrador de tráfico ya no devuelve el punto de conexión en respuesta a consultas DNS. Las nuevas conexiones se dirigen a otros puntos de conexión disponibles. Sin embargo, es posible que las respuestas DNS anteriores que incluyen este punto de conexión se almacenen en caché por parte de servidores y clientes DNS recursivos. Esto provocaría que algunos clientes intentaran conectarse a este punto de conexión. Conforme estas cachés caducan, los clientes realizan nuevas solicitudes DNS, las cuales se dirigen a otros puntos de conexión. La duración de la caché la controla la opción de TTL del perfil del Administrador de tráfico, por ejemplo 30 segundos.
8. **Continúan las comprobaciones de estado**. El Administrador de tráfico sigue comprobando el estado del punto de conexión mientras tenga un estado Degradado. Esto es para que pueda detectar cuando el punto de conexión vuelve a su estado.
9. **El servicio vuelve a estar en línea**. El servicio vuelve a estar disponible. El punto de conexión permanece en estado Degradado en el Administrador de tráfico hasta que el sistema de supervisión realiza su siguiente comprobación de estado.
10. **Se reanuda el tráfico al servicio**. El Administrador de tráfico envía una solicitud GET y recibe una respuesta de estado 200 - Correcto. Esto indica que el servicio ha vuelto a un estado correcto. Los servidores de nombres del Administrador de tráfico se actualizan y comienzan a distribuir el nombre DNS del servicio en las respuestas DNS. El tráfico vuelve al punto de conexión a medida que caducan las respuestas DNS en caché que devuelven otros puntos de conexión, y conforme se terminan las conexiones existentes a otros puntos de conexión.

>[AZURE.NOTE] Como el Administrador de tráfico funciona en el nivel de DNS, no influye en las conexiones existentes a algún punto de conexión. Cuando dirige el tráfico entre los puntos de conexión (bien por la configuración del perfil modificada o durante la conmutación por error o la conmutación por recuperación), el Administrador de tráfico dirige las nuevas conexiones a los puntos de conexión disponibles. Sin embargo, otros puntos de conexión pueden continuar recibiendo tráfico a través de las conexiones existentes hasta que finalizan esas sesiones. Para habilitar el tráfico que se depura de las conexiones existentes, las aplicaciones deben limitar la duración de la sesión que se utiliza con cada punto de conexión.

## Puntos de conexión degradados

Cuando un punto de conexión tiene el estado Degradado, ya no se devuelve en respuesta a las consultas DNS (para ver una excepción a esta regla, consulte la nota al final de esta sección). En su lugar, se elige y se devuelve un punto de conexión alternativo. La elección de un punto de conexión alternativo depende del método de enrutamiento de tráfico configurado:

- **Prioridad**. Los puntos de conexión forman una lista clasificada en orden de prioridad. Siempre se devuelve el primer punto de conexión disponible de la lista. Si un punto de conexión pasa al estado Degradado, se devuelve el siguiente punto de conexión disponible.
- **Ponderado**. Se puede devolver cualquiera de los puntos de conexión disponibles, elegido al azar según sus ponderaciones asignadas y las ponderaciones de los demás puntos de conexión disponibles. Si un punto de conexión tiene el estado Degradado, se elige otro del grupo de restantes puntos de conexión disponibles.
- **Rendimiento**. Se devuelve el punto de conexión más próximo al usuario final (excluyendo los puntos de conexión con estado Deshabilitado o Detenido). Si dicho punto de conexión no está disponible, el punto de conexión que se devuelve se elige aleatoriamente entre todos los disponibles. (Esto ayuda a evitar un error en cascada que podría producirse si se sobrecarga el punto de conexión más cercano. Puede configurar planes de conmutación por error alternativos para el enrutamiento del tráfico de rendimiento mediante los [perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)).

Para más información, consulte [Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md).

>[AZURE.NOTE] ¿Qué ocurre si todos los puntos de conexión del Administrador de tráfico (excepto los puntos de conexión con estado Deshabilitado o Detenido) no superan sus comprobaciones de estado y muestran el estado Degradado?

>La causa de esto suele ser normalmente un error en la configuración del servicio (por ejemplo, una lista de control de acceso (ACL) que bloquea las comprobaciones de estado del Administrador de tráfico) o un error en la configuración del perfil del Administrador de tráfico (por ejemplo, una ruta de acceso de supervisión incorrecta).

>En este caso, el Administrador de tráfico hace todo lo posible por *responder como si todos los puntos de conexión en estado Degradado estuvieran en realidad en estado en línea*. Esto es preferible a la otra alternativa, que sería no devolver ningún punto de conexión en la respuesta DNS.

>Una consecuencia de este comportamiento es que si las comprobaciones de estado del Administrador de tráfico no están configuradas correctamente, podría parecer por el enrutamiento del tráfico que el Administrador de tráfico *funciona* correctamente. Sin embargo, en este caso, la conmutación por error del punto de conexión no se produce si el punto de conexión presenta errores. Esto afecta a la disponibilidad general de la aplicación. Para tener la seguridad de que esto no ocurra, es importante comprobar que el perfil muestra un estado En línea y no un estado Degradado. Un estado En línea muestra que las comprobaciones de estado del Administrador de tráfico funcionan según lo esperado.

Para más información sobre cómo solucionar los problemas de las comprobaciones de estado incorrectas, consulte [Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md).

## P+F

### ¿Es el propio Administrador de tráfico resistente a errores de región de Azure?

Al proporcionar comprobaciones de estado integradas y conmutación por error automática entre regiones, el Administrador de tráfico es un componente clave para la entrega de aplicaciones de alta disponibilidad en Azure, incluidas las aplicaciones resistentes a una interrupción de Azure en toda la región.

Como tal, el propio Administrador de tráfico debe ofrecer un nivel de disponibilidad excepcionalmente alto, lo que incluye la resistencia a errores regionales.

Aunque algunas partes del Administrador de tráfico se ejecutan en Azure, estas se distribuyen entre regiones y se han diseñado para que sean resistentes a un error completo de cualquiera de las regiones de Azure. Esta resistencia se aplica a todos los componentes del Administrador de tráfico: los servidores de nombres DNS, la API, la capa de almacenamiento y el servicio de supervisión del punto de conexión.

Por lo tanto, incluso en el improbable caso de una interrupción total de toda una región de Azure, el Administrador de tráfico debe seguir funcionando con normalidad y los clientes con aplicaciones implementadas en varias regiones de Azure pueden confiar en el Administrador de tráfico para dirigir el tráfico a una instancia disponible de su aplicación.

### ¿Cómo afecta al Administrador de tráfico la elección de la ubicación del grupo de recursos?

El Administrador de tráfico es servicio global individual. No es regional. La elección de la ubicación del grupo de recursos no supone ninguna diferencia con respecto a los perfiles del Administrador de tráfico implementados en dicho grupo de recursos.

Azure Resource Manager requiere que todos los grupos de recursos especifiquen una ubicación, que determina la ubicación predeterminada de los recursos implementados en dicho grupo de recursos. Si crea un nuevo grupo de recursos al crear un perfil del Administrador de tráfico a través del Portal de Azure, se le pedirá esta ubicación.

Todos los perfiles del Administrador de tráfico usan **global** como ubicación. Dado que este es el único valor aceptado, esta configuración no es accesible en el Portal de Azure, PowerShell o la CLI de Azure. Esto reemplaza el valor predeterminado del grupo de recursos.

### ¿Cómo se determina el estado actual de cada punto de conexión?

El estado de supervisión actual de cada punto de conexión, junto con el del perfil global, se muestra en el Portal de Azure. Esta información también está disponible a través de la [API de REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), los [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) y la [CLI de Azure multiplataforma](../xplat-cli-install.md) del Monitor de tráfico.

No es posible ver información histórica sobre el estado anterior del punto de conexión ni configurar alertas de los cambios de estado del punto de conexión.

### ¿Puedo supervisar puntos de conexión HTTPS?

Sí. El Administrador de tráfico admite el sondeo a través de HTTPS. Simplemente configure **HTTPS** como protocolo de la configuración de supervisión. Observe lo siguiente:

- No se valida el certificado del servidor
- No se admiten certificados del servidor de SNI
- No se admiten certificados de cliente

### ¿Qué encabezado host se utiliza en las comprobaciones de estado de punto de conexión?

El encabezado de host que se utiliza en las comprobaciones de estado de HTTPS es el nombre DNS asociado con cada punto de conexión. Este se expone como el destino del punto de conexión en el Portal de Azure, los [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), la [CLI de Azure multiplataforma](../xplat-cli-install.md) y la [API de REST](https://msdn.microsoft.com/library/azure/mt163667.aspx).

Este valor forma parte de la configuración del punto de conexión. El valor utilizado en el encabezado host no se puede especificar por separado de la propiedad 'target'.


## Pasos siguientes

Aprenda [cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).

Aprenda más sobre los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) que admite el Administrador de tráfico.

Aprenda a [crear un perfil del Administrador de tráfico](traffic-manager-manage-profiles.md).

[Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md)

<!---HONumber=AcomDC_0706_2016-->
<properties 
   pageTitle="Supervisión de puntos de conexión del Administrador de tráfico y conmutación por error | Microsoft Azure"
   description="Este artículo le ayudará a comprender la forma en que el Administrador de tráfico utiliza la supervisión de puntos de conexión y la conmutación por error automática de los puntos de conexión para permitir que los clientes de Azure implementen aplicaciones de alta disponibilidad"
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
   ms.date="06/04/2016"
   ms.author="jtuliani" />

# Supervisión de puntos de conexión del Administrador de tráfico y conmutación por error

El Administrador de tráfico de Azure incluye la supervisión de puntos de conexión integrados y la conmutación por error automática de los puntos de conexión. Esto le permite ofrecer aplicaciones de alta disponibilidad que son resistentes a errores de los puntos de conexión, entre lie que se incluyen los errores de la región de Azure.

Para ello, realiza solicitudes periódicas a cada punto de conexión y compruebe la respuesta. Si un punto de conexión no proporciona una respuesta válida, se marca como "Degradado" y no se incluye en las respuestas DNS, que en su lugar devuelven un punto de conexión alternativo disponible. Por consiguiente, el tráfico del usuario final se aleja de los puntos de conexión con errores y se dirige a los que están disponibles.

Las comprobaciones de mantenimiento siguen realizándose en los puntos de conexión marcados como ‘Degradados’, con el fin de que puedan volver automáticamente a la rotación una vez que vuelvan a su estado.

## Configuración de la supervisión de los puntos de conexión

Para configurar la supervisión de los puntos de conexión, debe especificar la siguiente configuración en el perfil del Administrador de tráfico:

- **Protocolo**: elija HTTP o HTTPS. Es importante que tenga en cuenta que la supervisión HTTPS no comprueba si el certificado SSL es válido, solo comprueba que está presente.
- **Puerto**: elija el puerto que se usará para la solicitud. Los puertos HTTP estándar y HTTPS se encuentran entre las opciones.
- **Ruta de acceso**: especifique la ruta de acceso relativa y el nombre de la página web o archivo al que intentará acceder la comprobación de estado de supervisión. Tenga en cuenta que una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado).

Para comprobar el estado de cada punto de conexión, el Administrador de tráfico realizará una solicitud GET al punto de conexión mediante el protocolo, el puerto y la ruta de acceso relativa especificados.

Una práctica común consiste en implementar una página personalizada en la aplicación, por ejemplo, '/health.aspx' y configurarla como ruta de acceso de supervisión del punto de conexión del Administrador de tráfico. En dicha página, puede realizar todas las comprobaciones específicas de la aplicación como sean necesarias, como la comprobación de la disponibilidad de una base de datos back-end, antes de devolver HTTP 200 (si el servicio es correcto) u otro código de estado.

Los valores de la supervisión de los puntos de conexión se configuran en el nivel de perfil del Administrador de tráfico, no en cada uno de los puntos de conexión. Por consiguiente, se usan los mismos valores para comprobar el estado de todos los puntos de conexión. Si necesita utilizar diferentes valores de supervisión para los distintos puntos de conexión, puede hacerlo mediante los [perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## Estado de los puntos de conexión y de los perfiles

El usuario puede habilitar y deshabilitar los puntos de conexión y los perfiles del Administrador de tráfico. También se pueden producir cambios en el estado como consecuencia de comprobaciones de mantenimiento de los puntos de conexión. Los parámetros siguientes describen el comportamiento de forma detallada.

### Estado del punto de conexión

Estado de punto de conexión es un valor controlado por el usuario que permite habilitar o deshabilitar fácilmente puntos de conexión individuales. Esto no afecta al estado del servicio subyacente (que puede seguir en ejecución), más bien controla la disponibilidad de este punto de conexión desde la perspectiva del Administrador de tráfico. Cuando un punto de conexión está deshabilitado, no se comprueba el estado y no se devolverá en una respuesta DNS.

### Estado del perfil

Estado de perfil es un valor controlado por el usuario que permite habilitar o deshabilitar fácilmente el perfil. Mientras que Estado de punto de conexión afecta a un solo punto de conexión, Estado de perfil afecta a todo el perfil, incluyendo todos los puntos de conexión. Si está deshabilitado, no se comprueba el estado de los puntos de conexión y no se devuelven puntos de conexión en la respuesta DNS (las consultas de DNS reciben respuestas 'NXDOMAIN').

### Estado de supervisión de punto de conexión

Estado de supervisión de punto de conexión es un valor que genera el Administrador de tráfico; el usuario no lo puede establecer. Muestra el estado actual del punto de conexión, que refleja la supervisión del punto de conexión en curso y otra información como el estado de punto de conexión. Los valores posibles de Estado de supervisión de punto de conexión se muestran en la tabla siguiente. (Para obtener detalles sobre cómo se calcula el valor de Estado de supervisión de punto de conexión en los puntos de conexión anidados, consulte [perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md)).

|Estado del perfil|Estado del extremo|Estado de supervisión de extremos (API y Portal)|Notas|
|---|---|---|---|
|Disabled|Enabled|Inactivo|El usuario ha deshabilitado el perfil. Aunque el valor de Estado de punto de conexión pueda ser 'Enabled', el estado del perfil tiene prioridad. Los puntos de conexión en los perfiles Disabled no se supervisan. No se devuelve ningún punto de conexión en las respuestas DNS (en su lugar, se proporciona una respuesta 'NXDOMAIN').|
|&lt;cualquiera&gt;|Disabled|Disabled|El usuario ha deshabilitado el punto de conexión. Los puntos de conexión dehabilitados no se supervisan. No están disponibles para su inclusión en las respuestas DNS y, por consiguiente, no reciben tráfico.|
|Enabled|Enabled|En línea|El punto de conexión se supervisa y su estado es correcto. Está disponibles para su inclusión en las respuestas DNS y, por consiguiente, puede recibir tráfico.|
|Enabled|Enabled|Degradado|Error en las comprobaciones de estado de supervisión de punto de conexión. El punto de conexión no está disponible para su inclusión en las respuestas DNS y, por consiguiente, no recibe tráfico.|
|Enabled|Enabled|CheckingEndpoint|El extremo se supervisa pero los resultados del primer sondeo no se han recibido todavía. Este estado es temporal cuando se acaba de agregar un nuevo punto de conexión al perfil o cuando se acaba de habilitar un punto de conexión o un perfil. Los puntos de conexión están disponibles para su inclusión en las respuestas DNS y, por consiguiente, pueden recibir tráfico.|
|Enabled|Enabled|Detenido|El servicio en la nube o la aplicación web al que apunta el punto de conexión no se están ejecutando. Compruebe la configuración de la aplicación web o del servicio en la nube. Los puntos de conexión detenidos no se supervisan. No están disponibles para su inclusión en las respuestas DNS y, por consiguiente, no reciben tráfico.|

### Estado de supervisión de perfiles

Estado de supervisión de perfiles es una combinación del estado de supervisión de punto de conexión de todos los puntos de conexión del perfil y el estado del perfil configurado. Los valores posibles se describen en la tabla siguiente:

|Estado del perfil (según la configuración)|Estado de supervisión de extremos|Estado de supervisión de perfil (API y Portal)|Notas|
|---|---|---|---|
|Disabled|&lt;cualquiera&gt; o un perfil sin extremos definidos.|Disabled|El usuario ha deshabilitado el perfil.|
|Enabled|El estado de al menos un extremo es "Degradado".|Degradado|Esta es una marca que indica que se requiere acción del cliente. Revise los valores del estado de los puntos de conexión individuales para determinar cuáles de ellos requieren mayor atención.|
|Enabled|El estado de al menos un extremo es "En línea". No hay ningún extremo con estado "Degradado".|En línea|El servicio acepta tráfico y no es necesario que el cliente haga nada.|
|Enabled|El estado de al menos un extremo es "CheckingEndpoint". Ningún extremo está "En línea" o "Degradado".|CheckingEndpoints|Estado de transición. Esto suele ocurrir cuando se acaba de crear o habilitar un perfil y es la primera vez que se comprueba el estado del punto de conexión.|
|Enabled|El estado de todos los extremos definidos en el perfil es “Deshabilitado” o “Detenido”, o el perfil no tiene ningún extremo definido.|Inactivo|No hay extremos activos, pero el perfil está todavía habilitado.|

## Conmutación por error y conmutación por recuperación de un punto de conexión

Considere un escenario en el que se produce un error en un punto de conexión del Administrador de tráfico que antes estaba en buen estado, el Administrador de tráfico detecta dicho error y el punto de conexión se saca de la rotación. Posteriormente, el punto de conexión se recupera, el Administrador de tráfico también lo detecta y el punto de conexión vuelve a la rotación.

>[AZURE.NOTE] \: el Administrador de tráfico solo considera que un extremo está En línea si el mensaje devuelto es 200 OK. Si se produce alguna de las acciones siguientes, contará como un error de comprobación:

>- Se recibe una respuesta distinta de 200 (que incluya un código que no sea 2xx o un redireccionamiento 301/302)
>- Solicitud de autenticación de cliente
>- Tiempo de espera (el umbral de tiempo de espera es de 10 segundos)
>- No es posible establecer la conexión

>Para obtener más detalles acerca de por qué ha fallado la comprobación de la solución de problemas, consulte [Estado degradado de la solución de problemas en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md).

La escala de tiempo siguiente describe con todo detalle la secuencia de pasos que se produce.

![Secuencia de conmutación por error y conmutación por error de puntos de conexión del Administrador de tráfico](./media/traffic-manager-monitoring/timeline.png)

1. **GET**: el sistema de supervisión del Administrador de tráfico realiza una operación GET en la ruta de acceso y en el archivo especificados en la configuración de supervisión. Esto se repite en todos los puntos de conexión.
2. **200 OK**: el sistema de supervisión espera que se devuelva un mensaje HTTP 200 OK en un máximo de 10 segundos. Cuando recibe esta respuesta, entiende que el servicio está disponible.
3. **30 segundos entre comprobaciones**: la comprobación de mantenimiento del punto de conexión se repetirá cada 30 segundos.
4. **Servicio en la nube no disponible**: el servicio en la nube deja de estar disponible. El Administrador de tráfico no lo sabrá hasta la siguiente comprobación de mantenimiento.
5. **Intenta acceder al archivo de supervisión (4 intentos)**: el sistema de supervisión realiza una solicitud GET, pero no recibe una respuesta dentro del período de tiempo de espera de 10 segundos (como alternativa, se puede recibir una respuesta distinta de 200). A continuación realiza tres intentos más a intervalos de 30 segundos. Si uno de los intentos anteriores es correcto, se restablece el número de intentos.
6. **Marcado como degradado**: después del cuarto error seguido, el sistema de supervisión marcará el punto de conexión no disponible como Degradado. 
7. **El tráfico se desvía a otros puntos de conexión**: se actualizan los servidores de nombres DNS del Administrador de tráfico y este no devolverá el punto de conexión como respuesta a las consultas DNS. Por lo tanto, las nuevas conexiones se dirigirá a los restantes puntos de conexión disponibles. Sin embargo, las respuestas DNS anteriores que incluyen este punto de conexión pueden almacenarse en caché por parte de servidores DNS y clientes DNS recursivos, lo que causa que algunos clientes intenten conectarse a este punto de conexión. A medida que estas memorias caché expiran, los clientes realizarán nuevas consultas DNS, que se dirigirán a otros puntos de conexión. La duración de la memoria caché la controla la opción de TTL del perfil del Administrador de tráfico, por ejemplo 30 segundos. 
8. **Las comprobaciones de mantenimiento continúan**: el Administrador de tráfico sigue comprobando el estado del punto de conexión mientras se encuentra en estado Degradado. Esto es para que pueda detectar cuando el punto de conexión vuelve a su estado.
9. **El servicio vuelve a estar en línea**: el servicio está disponible. El punto de conexión permanece en estado Degradado en el Administrador de tráfico hasta que el sistema de supervisión realiza la siguiente comprobación de mantenimiento.
10. **Se reanuda el tráfico al servicio**: el Administrador de tráfico envía una solicitud GET y recibe un mensaje 200 OK, lo que indica que el servicio ha vuelto a un estado correcto. Los servidores de nombres del Administrador de tráfico se actualizan una vez más y comienzan a distribuir el nombre DNS del servicio en las respuestas DNS. El tráfico se devolverá al punto de conexión nuevamente una vez que las respuestas de DNS en caché que devuelven otros puntos de conexión expiren y cuando se terminen las conexiones existentes a otros puntos de conexión.

>[AZURE.NOTE] Debido a que el Administrador de tráfico funciona en el nivel de DNS, no es posible influir en las conexiones existentes con ningún punto de conexión. Al dirigir el tráfico entre los puntos de conexión (ya sea cambiando la configuración del perfil o durante la conmutación por error o la conmutación por recuperación), el Administrador de tráfico dirigirá las conexiones nuevas a los puntos de conexión disponibles. Sin embargo, otros puntos de conexión pueden continuar recibiendo tráfico a través de las conexiones existentes hasta que finalizan las sesiones. Para habilitar el tráfico que se depura de las conexiones existentes, las aplicaciones deben limitar la duración de la sesión que se utiliza con cada punto de conexión.

## Puntos de conexión degradados

Cuando un punto de conexión se degrada, no se devuelve en respuesta a las consultas DNS (para ver una excepción a esta regla, consulte la nota siguiente). En su lugar, se elige y devuelve un punto de conexión alternativo. La elección de un punto de conexión alternativo depende del método de enrutamiento de tráfico configurado:

- **Prioridad**: los puntos de conexión forman una lista de prioridades. Siempre se devuelve el primer punto de conexión disponible de la lista. Si pasa al estado Degradado, se devuelve el siguiente punto de conexión disponible.
- **Ponderado**: se puede devolver cualquiera de los punto de conexión disponibles, elegido al azar según sus ponderaciones asignadas y las ponderaciones de los demás puntos de conexión disponibles. Si un punto de conexión pasa a ser degradado, se elige otro del grupo de restantes puntos de conexión disponibles.
- **Rendimiento**: se devuelve el punto de conexión "más próximo" al usuario final (excluyendo los puntos de conexión deshabilitados o detenidos). Si dicho punto de conexión no está disponible, el punto de conexión que se devuelve se elige aleatoriamente entre todos los disponibles. (Esto ayuda a evitar un error en cascada que podría producirse si se sobrecarga el punto de conexión más cercano. Los planes de conmutación por error alternativos para el enrutamiento de tráfico de rendimiento pueden configurarse mediante los [perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)).

Para más información, consulte [Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md).

>[AZURE.NOTE] ¿Qué ocurre si todos los puntos de conexión del Administrador de tráfico (excepto los puntos de conexión "Deshabilitados" o "Detenidos") no superan sus comprobaciones de mantenimiento y se marcan como "Degradados"?

>Esto suele ocurrir debido a un error en la configuración del servicio (como por ejemplo, una ACL que bloquea las comprobaciones de mantenimiento del Administrador de tráfico) o a un error en la configuración del perfil del Administrador de tráfico (por ejemplo, una ruta de acceso de supervisión incorrecta).

>En ese caso, el Administrador de tráfico realiza un "esfuerzo" y *responde como si todos los puntos de conexión degradados están realmente "En línea"*. Esto es preferible a la alternativa, que sería no devolver un punto de conexión en la respuesta DNS.

>Una consecuencia de este comportamiento es que cuando las comprobaciones de mantenimiento del Administrador de tráfico no están configuradas correctamente, puede parecer desde el enrutamiento de tráfico que el Administrador de tráfico funciona correctamente. Sin embargo, la conmutación por error del punto de conexión no sucederá si se produce algún error en un punto de conexión, lo que afecta a la disponibilidad general de la aplicación. Para asegurarse de que esto no ocurre, es importante asegurarse de que el perfil muestra el estado 'En línea', en lugar de "Degradado". Un estado 'En línea' muestra que las comprobaciones de mantenimiento del Administrador de tráfico funcionan del modo esperado.

Para más detalles acerca de como solucionar los problemas de las comprobaciones de mantenimiento con errores, consulte [Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md).
 
## P+F

### ¿Es el propio Administrador de tráfico resistente a errores de región de Azure?

Al proporcionar comprobaciones de mantenimiento integradas y conmutación por error automática entre regiones, el Administrador de tráfico de Azure es un componente clave para la entrega de aplicaciones de alta disponibilidad en Azure, incluidas las aplicaciones resistentes ante una interrupción de Azure en toda la región.

Como tal, reconocemos que el propio Administrador de tráfico debe ofrecer un nivel de disponibilidad, incluida la resistencia a un error regional, excepcionalmente alto.

Aunque algunas partes del Administrador de tráfico se ejecutan en Azure, estas se distribuyen entre regiones y se han diseñado para que sean resistentes ante un error completo de cualquiera de las regiones de Azure. Esta resistencia se aplica a todos los componentes del Administrador de tráfico: los servidores de nombres DNS, la API, la capa de almacenamiento y el servicio de supervisión del punto de conexión.

Por lo tanto, incluso en el improbable caso de una interrupción total de toda una región de Azure, el Administrador de tráfico debe seguir funcionando con normalidad y los clientes con aplicaciones implementadas en varias regiones de Azure pueden basarse en el Administrador de tráfico para dirigir el tráfico a una instancia disponible de su aplicación.

### ¿Cómo afecta la elección de la ubicación del grupo de recursos al Administrador de tráfico?

El Administrador de tráfico es servicio global individual. No es regional. La elección de la ubicación del grupo de recursos no supone ninguna diferencia con respecto a los perfiles del Administrador de tráfico implementados en dicho grupo de recursos.

Azure Resource Manager requiere que todos los grupos de recursos especifiquen una 'ubicación', que determina la ubicación predeterminada para los recursos implementados en dicho grupo de recursos. Si crea un nuevo grupo de recursos al crear un perfil del Administrador de tráfico a través del Portal de Azure, se le pedirá esta ubicación.

Todos los perfiles del Administrador de tráfico usan "global" como ubicación. Dado que es el único valor aceptado, no se expone en las experiencias del Portal de Azure, PowerShell o CLI. Esto reemplaza el valor predeterminado del grupo de recursos.

### ¿Cómo se determina el estado actual de cada punto de conexión?

El estado de actual de cada punto de conexión, y del perfil global, se muestra en el Portal de administración de Azure. Esta información también está disponible a través de la [API de REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), los [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), y la [CLI de Azure multiplataforma](../xplat-cli-install.md) del Administrador de tráfico.

En la actualidad, no es posible ver información histórica sobre el estado anterior del punto de conexión ni configurar alertas de los cambios de estado del punto de conexión.

### ¿Puedo supervisar puntos de conexión HTTPS?

Sí. El Administrador de tráfico admite el sondeo a través de HTTPS. Simplemente configure 'HTTPS' como protocolo de la configuración de supervisión. Observe lo siguiente:

- No se valida el certificado del servidor
- No se admiten certificados del servidor de SNI
- No se admiten certificados de cliente

### ¿Qué encabezado 'host' se utiliza para las comprobaciones de mantenimiento del punto de conexión?

El encabezado 'host' que se utiliza en las comprobaciones de mantenimiento de HTTPS es el nombre DNS asociado con cada punto de conexión. Este se expone como 'target' del punto de conexión en el Portal de Azure, [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), [CLI de Azure multiplataforma](../xplat-cli-install.md) y [API de REST](https://msdn.microsoft.com/library/azure/mt163667.aspx).

Este valor forma parte de la configuración del punto de conexión. El valor utilizado en el encabezado de host no se puede especificar por separado de la propiedad 'target'.


## Pasos siguientes

Consulte [Cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).

Más información acerca de los [métodos de enrutamiento del tráfico](traffic-manager-routing-methods.md) que admite el Administrador de tráfico.

Consulte [Administrar un perfil del Administrador de tráfico de Azure](traffic-manager-manage-profiles.md).

[Solución de problemas de estado degradado en el Administrador de tráfico de Azure](traffic-manager-troubleshooting-degraded.md).
 

<!---HONumber=AcomDC_0608_2016-->
<properties title="Manage your Search service on Microsoft Azure" pageTitle="Manage your Search service on Microsoft Azure" description="Manage your Search service on Microsoft Azure" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Administración del servicio de búsqueda en Microsoft Azure

[WACOM.INCLUDE [Este artículo usa el portal de Azure en vista previa](../includes/preview-portal-note.md)]

Búsqueda de Azure es un servicio basado en la nube y una API basada en HTTP que puede usarse en aplicaciones de búsqueda personalizadas. Nuestro servicio de búsqueda proporciona el motor para análisis de texto de búsqueda de texto completo, características de búsqueda avanzadas, almacenamiento y una sintaxis de comando de consulta.

En este artículo se explica cómo administrar un servicio de búsqueda en Microsoft Azure.

Como se ha indicado, el nuevo portal de vista previa es necesario para las tareas administrativas. Búsqueda de Azure no está disponible en versiones anteriores del portal. Una API de administración estará disponible pronto para permitir la secuencia de comandos de tareas de administración comunes.

<!--TOC-->

-   [Adición del servicio de búsqueda a su suscripción][Adición del servicio de búsqueda a su suscripción]
-   [Tareas administrativas][Tareas administrativas]
-   [URL de servicio][URL de servicio]
-   [Administración de las claves de API][Administración de las claves de API]
-   [Supervisar el uso de recursos][Supervisar el uso de recursos]
-   [Escalado o reducción vertical][Escalado o reducción vertical]
-   [Inicio o interrupción del servicio][Inicio o interrupción del servicio]

## Adición del servicio de búsqueda a su suscripción

Como administrador, puede agregar Búsqueda a su suscripción de Azure existente usando el nuevo [portal de Azure en vista previa][portal de Azure en vista previa]. Solo los administradores pueden agregar características a una suscripción. Al configurar su servicio, hay dos niveles de precio entre los que se puede elegir.

Sin cargo alguno para los suscriptores existentes, puede usar un servicio compartido, recomendado con fines didácticos, evaluación de prueba de concepto y pequeños proyectos de desarrollo. El servicio compartido incluye un espacio de almacenamiento de 50 MB, tres índices y recuento de documentos (un límite máximo de 10.000 documentos, incluso si el consumo de almacenamiento es inferior a los 50 MB totales permitidos). No hay garantías de rendimiento con el servicio compartido, de modo que si crea una aplicación de búsqueda de producción, considere la búsqueda estándar en su lugar.

La búsqueda estándar se puede facturar al registrarse para obtener recursos dedicados e infraestructura usada solo por su suscripción. La búsqueda estándar se asigna en agrupaciones de particiones (almacenamiento) definidas por el usuario y réplicas (cargas de trabajo de servicio) y los precios se establecen por unidad de búsqueda. Puede escalar en particiones o réplicas de forma independiente, agregando más de cualquiera que sea el recurso requerido.

Para planificar la capacidad y entender el impacto de facturación, recomendamos estos vínculos:

-   [Límites y restricciones][Límites y restricciones]
-   [Detalles de precios][Detalles de precios]

Cuando esté listo para registrarse, consulte [Configuración de Búsqueda en el portal de vista previa de Azure][Configuración de Búsqueda en el portal de vista previa de Azure].

## Tareas administrativas

Aunque algunos servicios pueden tener coadministradores, un servicio de búsqueda de Azure tiene un administrador por suscripción. Debe ser administrador para realizar las tareas descritas en esta sección.
Además de agregar Búsqueda a la suscripción, un administrador es responsable de estas tareas adicionales:

-   Distribución de la URL de servicio (definida durante el aprovisionamiento del servicio).
-   Administración y distribución de las claves de API.
-   Supervisar el uso de recursos
-   Escalado o reducción vertical (se aplica solo a la búsqueda estándar)
-   Inicio o interrupción del servicio

## URL de servicio

La URL de servicio se define como una propiedad fija cuando crea el servicio; no se puede cambiar más adelante.

Los desarrolladores que crean aplicaciones de búsqueda deberán conocer la URL de servicio para las solicitudes de HTTP. Puede buscar con rapidez la URL de servicio mediante el panel de servicios.

Para obtener la URL de servicio del panel de servicios:

1.  Inicie sesión en el [portal de vista previa de Azure][portal de Azure en vista previa].
2.  Haga clic en **Examinar** | **Todo** | **Servicios de búsqueda**.
3.  Haga clic en el nombre de su servicio de búsqueda para abrir el panel.
4.  Haga clic en **PROPIEDADES** para deslizar una página de propiedades al abrirla. La URL de servicio se encuentra en la parte superior de la página. Puede anclar esta página para obtener acceso rápidamente más adelante.

    ![][0]

Asimismo, es posible que los desarrolladores le pregunten sobre la versión de la API. Un requisito de codificación de la API de Búsqueda de Azure siempre es especificar la versión de la API en la solicitud. El fin de este requisito es que los desarrolladores puedan seguir usando una versión anterior y, a continuación, cambien a una versión posterior en el momento adecuado.

La versión de la API no se muestra en las páginas del portal, por lo que no se trata de información que puedas proporcionar. Para obtener información sobre versiones de la API actuales y anteriores, consulte la [API de REST de Búsqueda de Azure][API de REST de Búsqueda de Azure].

<!---->

## Administración de las claves de API

Los desarrolladores que crean aplicaciones de búsqueda deberán tener una clave de API para obtener acceso a Búsqueda. Cada una de las solicitudes de HTTP a su servicio de búsqueda necesitarán una clave de API generada de forma específica para su servicio. La clave de API es el único mecanismo para autenticarse en la URL de su servicio de búsqueda.

Se usan dos tipos de claves para obtener acceso a su servicio de búqueda:

-   Administración (válida para cualquier operación)
-   Consulta (válida solo para solicitudes de consulta)

Una clave de API de administración se crea con el servicio. Existe una clave principal y otra secundaria. Puede usarlas indistintamente; ninguna refleja un nivel de acceso superior o inferior, lo que resulta útil si quiere sustituir claves. Puede volver a generar cualquiera de las claves de administración, pero no puede agregarla al recuento total de claves de administración. Puede haber un máximo de dos claves de administración por servicio de búsqueda.

Las claves de consulta están diseñadas para su uso en aplicaciones cliente que llaman a Búsqueda directamente. Puede crear hasta 50 claves de consulta.

Para obtener o volver a generar claves de API, abra el panel de servicios. Haga clic en **CLAVES** para deslizar la página de administración de claves al abrirla. Los comandos para volver a generar o crear claves están en la parte superior de la página.

![][1]

<!---->

## Supervisar el uso de recursos

En esta vista previa pública, la supervisión de recursos se limita a la información que se muestra en el panel de servicios y algunas métricas que puede obtener al consultar el servicio.

En el panel de servicios, en la sección Uso, podrá determinar rápidamente si los niveles de recursos de partición son adecuados para su aplicación.

Al usar la API del servicio de búsqueda, podrá obtener una recuento de los documentos e índices. Existen límites máximos asociados a estos recuentos basados en el nivel de precio. Consulte [Límites y restricciones][Límites y restricciones] para obtener detalles.

-   [Obtención de estadísticas de índice][Obtención de estadísticas de índice]
-   [Documentos de recuento][Documentos de recuento]

> [WACOM.NOTE] Los comportamientos de Almacenamiento en caché pueden sobrevalorar un límite temporalmente. Por ejemplo, al usarse el servicio compartido, es posible que vea un recuento de documentos sobre el límite máximo de 10.000 documentos. La sobrevaloración es temporal y se detectará en la próxima comprobación de aplicación de límite.


<!---->
## Escalado o reducción vertical

Cada uno de los servicios de búsqueda se inicia con una cantidad mínima de una réplica y una partición. Si se registró para obtener recursos dedicados usando el nivel de precio estándar, puede hacer clic en el icono de **escalación** del panel de servicios para reajustar el número de particiones y réplicas usadas por su servicio.

Al agregar cualquiera de los recursos, el servicio los usa automáticamente. No es necesario que haga nada más, pero habrá un ligero retraso antes de materializarse el impacto del nuevo recurso. Puede tardar 15 minutos o más en aprovisionar los recursos adicionales.

![][2]

### Adición de réplicas

La adición de réplicas permiten un aumento de las consultas por segundo (QPS) o la consecución de una alta disponibilidad. Cada réplica tiene una copia de un índice, de modo que la adición de una réplica más se traduce en un índice más que puede usarse para ofrecer solicitudes de consulta. En la actualidad, la regla general es que son necesarias al menos tres réplicas para obtener una alta disponibilidad.

Un servicio de búsqueda con más réplicas puede equilibrar la carga de solicitudes de consulta sobre un número de índices mayor. Una vez especificado un nivel del volumen de consultas, el rendimiento de las consultas va a ser más rápido cuando haya más copias del índice disponible para ofrecer la solicitud. En caso de experimentar una latencia de consulta, puede esperar que se produzca un impacto positivo en el rendimiento una vez que las réplicas adicionales estén en línea.

Aunque el rendimiento de las consultas aumente a medida que agrega réplicas, esto no lo dobla ni triplica precisamente a medida que agrega réplicas a su servicio. Todas las aplicaciones de búsqueda están sujetas a factores externos que pueden afectar al rendimiento de las consultas. Las consultas complejas y la latencia de red son dos factores que contribuyen a la existencia de variaciones en los tiempos de respuesta de las consultas.

### Adición de particiones

La mayoría de las aplicaciones de servicio tienen una necesidad integrada de más réplicas en lugar de particiones, ya que la mayor parte de las aplicaciones que usan la búsqueda pueden ajustarse fácilmente a una sola partición que puede admitir hasta 15 millones de documentos.

En los casos donde se requiere un mayor recuento de documentos, puede agregar particiones. Tenga en cuenta que las particiones se agregan en múltiplos de 12 (concretamente 1, 2, 3, 4, 6 o 12). Se trata de un artefacto de particionamiento; un índice se crea en 12 particiones que pueden almacenarse en su totalidad en una partición o dividirse de igual forma en 2, 3, 4, 6 o 12 particiones (una partición por partición).

### Eliminación de réplicas

Tras períodos de elevados volúmenes de consultas, es muy probable que elimine réplicas una vez que se hayan normalizado las cargas de consultas de búsqueda (por ejemplo, tras finalizar las ventas navideñas).

Para ello, basta con que mueva de nuevo el control deslizante de la réplica a un número inferior. No es necesario que haga nada más. Al reducir el recuento de réplicas, las máquinas virtuales se abandonan en el centro de datos. Ahora, sus operaciones de ingesta de consultas y datos se ejecutarán en menos VM que antes. El límite mínimo es una réplica.

### Eliminación de particiones

A diferencia de la eliminación de réplicas, que no requiere que haga nada más, es posible que tenga que hacer algo si usa más almacenamiento del que se puede reducir. Por ejemplo, si su solución usa tres particiones, intentar reducirla a una o dos particiones generará un error si usa más espacio de almacenamiento del que se puede almacenar en el reducido número de particiones. En este caso, las opciones con las que cuenta son eliminar índices o documentos en un índice asociado para liberar espacio o mantener la configuración actual.

No existe un método de detección que indique qué particiones de índice se almacenan en particiones concretas. Cada partición proporciona un espacio de almacenamiento de aproximadamente 25 MB, de modo que será necesario reducirlo a un tamaño al que pueda ajustarse su número de particiones. Si quiere volver a una partición, las 12 particiones deberán ajustarse.

Para ayudar en una futura planificación, es posible que quiera comprobar el espacio de almacenamiento (usando [Obtención de estadísticas de índice][Obtención de estadísticas de índice]) para ver cuánto usó en realidad.


<!---->
## Inicio o interrupción del servicio

Puede iniciar, interrumpir o incluso eliminar el servicio usando comandos en el panel de servicios.

![][3]

Con la interrupción o inicio del servicio no se desactiva la facturación. Debe eliminar el servicio para evitar que se le cobren todas las cargas. Cualquier dato asociado a su servicio se eliminará cuando su servicio no esté disponible.


<!--Anchors-->  
  [Adición del servicio de búsqueda a su suscripción]: #sub-1
  [Tareas administrativas]: #sub-2
  [URL de servicio]: #sub-3
  [Administración de las claves de API]: #sub-4
  [Supervisar el uso de recursos]: #sub-5
  [Escalado o reducción vertical]: #sub-6
  [Inicio o interrupción del servicio]: #sub-7
  [portal de Azure en vista previa]: https://portal.azure.com
  [Límites y restricciones]: http://msdn.microsoft.com/es-es/library/dn798934.aspx
  [Detalles de precios]: http://go.microsoft.com/fwlink/p/?LinkdID=509792
  [Configuración de Búsqueda en el portal de vista previa de Azure]: ../search-configure/
<!--Image references-->
  [0]: ./media/search-manage/Azure-Search-Manage-1-URL.png
  [API de REST de Búsqueda de Azure]: http://go.microsoft.com/fwlink/p/?LinkdID=509922
  [1]: ./media/search-manage/Azure-Search-Manage-2-Keys.png


<!--Link references-->
  [Obtención de estadísticas de índice]: http://msdn.microsoft.com/es-es/library/dn798942.aspx
  [Documentos de recuento]: http://msdn.microsoft.com/es-es/library/dn798924.aspx
  [2]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
  [3]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png

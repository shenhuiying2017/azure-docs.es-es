<properties
   pageTitle="Información general sobre Administrador de tráfico"
   description="Este artículo contiene la información general técnica de Administrador de tráfico".
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/23/2015"
   ms.author="joaoma" />

# Información general sobre Administrador de tráfico

El Administrador de tráfico de Microsoft Azure permite controlar la distribución del tráfico de usuarios en los extremos especificados, que pueden incluir servicios en la nube, sitios web y otros extremos de Azure. El Administrador de tráfico aplica un motor de directivas inteligente a las consultas del Sistema de nombres de dominio (DNS) para los nombres de dominio de los recursos de Internet. Los servicios en la nube o los sitios web de Azure pueden ejecutarse en centros de datos diferentes de todo el mundo.

Administrador de tráfico puede ayudarle a lo siguiente:

-**Mejorar la disponibilidad de las aplicaciones críticas:** el Administrador de tráfico permite mejorar la disponibilidad de las aplicaciones críticas supervisando los extremos de Azure y ofreciendo funcionalidad de conmutación por error automática cuando un servicio en la nube de Azure, un sitio web de Azure u otra ubicación deja de funcionar.

-**Mejorar la capacidad de respuesta para las aplicaciones de alto rendimiento:** Azure permite ejecutar servicios en la nube en los centros de datos ubicados en todo el mundo. El Administrador de tráfico puede mejorar la capacidad de respuesta de las aplicaciones y los tiempos de entrega de contenido dirigiendo a los usuarios finales al extremo con la latencia de red más baja del cliente.

-**Actualizar y realizar el mantenimiento del servicio sin tiempo de inactividad:** el Administrador de tráfico admite escenarios extendidos para implementaciones en la nube híbrida y locales, entre los que se incluyen los escenarios "irrumpir en la nube," "migrar a la nube" y "conmutación por error en la nube". Para el mantenimiento planeado, debe deshabilitar el extremo en el Administrador de tráfico y esperar a que el extremo finalice el establecimiento de las conexiones existentes. Cuando ya no hay más tráfico al extremo, debe actualizar el servicio en ese extremo y probarlo, y volverlo a habilitar de nuevo en el Administrador de tráfico. Esto ayuda a mantener y actualizar los servicios sin tiempo de inactividad para los clientes.

-**Distribución de tráfico para implementaciones grandes y complejas:** con los perfiles anidados del Administrador de tráfico, en que un perfil del Administrador de tráfico puede tener otro perfil del Administrador de tráfico como extremo, puede crear configuraciones para optimizar el rendimiento y la distribución para implementaciones más grandes y complejas. Para obtener más información, consulte [Perfiles anidados](#Nested).

## Funcionamiento del Administrador de tráfico

Al configurar un perfil del Administrador de tráfico, los valores que especifica proporcionan al Administrador de tráfico la información necesaria para determinar qué extremo debe atender la solicitud basada en una consulta DNS. No hay tráfico de extremo real que se enrute a través del Administrador de tráfico.

La *Figura 1* muestra la manera en que el Administrador de tráfico dirige a los usuarios a uno de un conjunto de extremos. Los números de la Figura 1 corresponden a las descripciones numeradas siguientes:

![How Traffic Manager works](./media/traffic-manager/IC740854.jpg)

Figura 1

1. **Tráfico de usuario hacia el nombre de dominio de la compañía:** el cliente solicita información mediante el nombre de dominio de la compañía. El objetivo es resolver un nombre DNS en una dirección IP. Es preciso reservar los dominios de la compañía a través de registros de nombre de dominio de Internet normales, que se mantienen fuera del Administrador de tráfico. En la Figura 1, el ejemplo de dominio de compañía es www.contoso.com.
1. **Nombre del dominio de la compañía hacia el nombre de dominio del Administrador de tráfico:** el registro de recursos de DNS para el nombre de la compañía señala a un nombre de dominio del Administrador de tráfico mantenido en el Administrador de tráfico de Azure. Esto se logra usando un registro de recursos CNAME que asigna el nombre de dominio de la compañía al nombre de dominio del Administrador de tráfico. En el ejemplo, el nombre de dominio del Administrador de tráfico es contoso.trafficmanager.net.
1. **Nombre del dominio y perfil del Administrador de tráfico:** el nombre de dominio del Administrador de tráfico forma parte del perfil del Administrador de tráfico. El servidor DNS del usuario envía una nueva consulta DNS para el nombre de dominio del Administrador de tráfico (en el ejemplo, contoso.trafficmanager.net), que reciben los servidores de nombres DNS del Administrador de tráfico.
1. **Reglas procesadas del perfil del Administrador de tráfico:** el Administrador de tráfico usa el método de equilibrio de carga y el estado de supervisión especificados para determinar qué extremo de Azure u otro debe atender la solicitud.
1. **Nombre de dominio de extremo enviado al usuario:** el Administrador de tráfico devuelve un registro CNAME que asigna el nombre de dominio del Administrador de tráfico al nombre de dominio del extremo. El servidor DNS del usuario resuelve el nombre de dominio del extremo en su dirección IP y lo envía al usuario.
1. **El usuario llama al extremo:** el usuario llama al extremo devuelto directamente usando su dirección IP.

Dado que el dominio de la compañía y la dirección IP resuelta se almacenan en la memoria caché del equipo cliente, el usuario continuará interactuando con el extremo elegido hasta que expire la entrada de la memoria caché de DNS local. Es importante tener en cuenta que el cliente DNS almacena en memoria caché las entradas del host DNS durante su período de vida (TTL). Al recuperar entradas del host de la caché de cliente DNS se omite el perfil del Administrador de tráfico y se pueden producir retrasos en la conexión si el extremo pasa a no estar disponible antes de que expire el período de vida (TTL). Si expira el TTL de una entrada del host DNS en la caché y el equipo cliente necesita volver a resolver el nombre de dominio de la compañía, se envía una nueva consulta DNS. El equipo cliente podrá recibir la dirección IP de otro extremo en función del método de equilibrio de carga que se aplique y del estado de los extremos en el momento de la solicitud.

## Implementación del Administrador de tráfico


La *Figura 2* muestra por orden los pasos necesarios para implementar el Administrador de tráfico. Estos pasos se pueden realizar en un orden ligeramente diferente una vez que entienda perfectamente la configuración y los procedimientos recomendados del Administrador de tráfico. Los números de la Figura 2 corresponden a las descripciones numeradas siguientes:

![How to configure Traffic Manager](./media/traffic-manager/IC740855.jpg)

Figura 2

1. **Implemente los servicios en la nube de Azure, los sitios web de Azure u otros extremos en el entorno de producción.** Al crear un perfil del Administrador de tráfico, se debe asociar a una suscripción. A continuación, debe agregar extremos para los servicios en la nube y los sitios web de nivel Estándar en producción que forman parte de la misma suscripción. Si un extremo está en almacenamiento provisional y no en un entorno de producción de Azure o no está en la misma suscripción, se puede agregar como extremo externo. Para obtener más información acerca de los servicios en la nube, consulte [Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obtener más información sobre los sitios web, consulte [Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
1. **Decida un nombre para el dominio del Administrador de tráfico.** Es recomendable que el nombre tenga un prefijo único. La parte final del dominio, trafficmanager.net, es fija. Para obtener más información, consulte [Procedimientos recomendados](#bkmk_TrafficManagerBestPracticesProfile).
1. **Determine qué configuración de monitorización desea utilizar.** El Administrador de tráfico supervisa los extremos para asegurarse de que estén en línea, independientemente de cuál sea el método de equilibrio de carga. Después de configurar los parámetros de supervisión, el Administrador de tráfico no dirigirá tráfico a los extremos que estén sin conexión según el sistema de supervisión, a menos que detecte que todos los extremos están sin conexión o que no pueda detectar el estado de alguno de los extremos contenidos en el perfil. Para obtener más información acerca de la monitorización, consulte [Acerca de la supervisión del Administrador de tráfico](https://msdn.microsoft.com/library/azure/dn339013.aspx).
1. **Decida qué método de equilibrio de carga desea utilizar.** Hay disponibles tres métodos de equilibrio de carga. Deje tiempo para entender qué método satisface mejor sus requisitos. Si necesita cambiar el método más adelante, puede hacerlo en cualquier momento. Observe también que cada método requiere pasos de configuración ligeramente diferentes. Para más información sobre los métodos de equilibrio de carga, consulte [Acerca de los métodos de equilibrio de carga del Administrador de tráfico](https://msdn.microsoft.com/library/azure/dn339010.aspx).
1. **Cree el perfil y configure las opciones de ajuste.** Puede usar las API de REST, Windows PowerShell o el Portal de administración para crear el perfil del Administrador de tráfico y configurar sus valores. Para obtener más información, consulte [Configuración de los valores del Administrador de tráfico](#Configure). n los pasos siguientes se da por supuesto que utilizará Creación rápida en el Portal de administración.
	- 	**Cree el perfil del Administrador de tráfico:** para crear un perfil con Creación rápida en el Portal de administración, consulte [Creación de un perfil del Administrador de tráfico mediante Creación rápida](https://msdn.microsoft.com/library/azure/dn339012.aspx).
	- 	**Configure los valores del método de equilibrio de carga:** mientras esté en Creación rápida, debe seleccionar el método de equilibrio de carga para el perfil. Este valor se puede cambiar en cualquier momento después de completar los pasos de Creación rápida. Para los pasos de configuración, vea el tema correspondiente al método de equilibrio de carga: [Configuracion del equilibrio de carga de rendimiento](https://msdn.microsoft.com/library/azure/hh744835.aspx)
	
	[AZURE.NOTE]**El método round robin de equilibrio de carga actualmente admite una distribución ponderada del tráfico de red. Sin embargo, en este momento debe usar API de REST o Windows PowerShell para configurar la ponderación. Para obtener más información y una configuración de ejemplo, consulte [Extremos externos del Administrador de tráfico de Azure y equilibrio de carga ponderado round robin a través de PowerShell](https://msdn.microsoft.com/library/azure/hh744829.aspx) en el blog de Azure.**
	- 	**Configure los extremos:** los extremos no se configuran durante Creación rápida. Después de crear el perfil y especificar el método de equilibrio de carga, debe dar a conocer los extremos al Administrador de tráfico.
	- 	**Configure los valores de supervisión:** os valores de supervisión no se configuran durante Creación rápida. Después de crear el perfil y especificar el método de equilibrio de carga, debe indicar al Administrador de tráfico lo que tiene que supervisar. Consulte los pasos para configurar la monitorización en [Configuración de la supervisión del Administrador de tráfico](configure-traffic-manager-monitoring.md).
1. **Pruebe el perfil del Administrador de tráfico:**  Compruebe que el perfil y el dominio funcionan de la manera esperada. Para obtener información acerca de cómo hacerlo, consulte [Comprobación de la configuración del Administrador de tráfico](testing-traffic-manager-settings.md)..
1. **Apunte el recurso DNS del nombre de dominio de la compañía hacia el perfil para activarlo.** Para obtener más información, consulte [Selección de un dominio de la compañía en Internet para un dominio del Administrador de tráfico](point-a-company-internet-domain-to-a-traffic-manager-domain.md).

Usando el ejemplo de la Figura 1, cambiaría el registro de recursos DNS en los servidores por lo siguiente para señalar el nombre de dominio de la compañía hacia el nombre de dominio del Administrador de tráfico:

    www.contoso.com IN CNAME contoso.trafficmanager.net


## Configuración de los valores del Administrador de tráfico


Puede configurar las opciones del Administrador de tráfico mediante el Portal de administración, las API de REST y los cmdlets de Windows PowerShell.

Aunque no todos los elementos de las API de REST son visibles en el Portal de administración, muchas opciones están disponibles mediante cualquier método. Para obtener más información sobre el uso de las API de REST, consulte [Operaciones en el Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).


Para obtener más información acerca de los cmdlets de Windows PowerShell para el Administrador de tráfico, consulte [Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).


[AZURE.NOTE] **Actualmente no hay compatibilidad para la configuración de extremos externos (tipo = 'Any'), ponderaciones para el método de equilibrio de carga round robin y perfiles anidados con el Portal de administración. Debe usar REST (consulte Creación de definición) o Windows PowerShell (consulte Add-AzureTrafficManagerEndpoint).**

### Configuración de opciones en el Portal de administración


En el Portal de administración, puede crear el perfil del Administrador de tráfico con Creación rápida. Creación rápida permite crear un perfil básico. Una vez creado el perfil, puede configurar otras opciones adicionales o editar las opciones que configuró anteriormente. Para obtener más información acerca de cómo crear el perfil del Administrador de tráfico mediante Creación rápida, consulte Creación de un perfil del Administrador de tráfico mediante Creación rápida.

[Creación de un perfil del Administrador de tráfico mediante Creación rápida](https://msdn.microsoft.com/library/azure/dn339012.aspx)

En el Portal de administración se pueden configurar los valores siguientes:

-**Prefijo de DNS:** un prefijo único que usted crea. En el Portal de administración, los perfiles se muestran por prefijo.

-**TTL de DNS: ** el valor del período de vida (TTL) de DNS controla la frecuencia con la que el servidor de nombres de almacenamiento en caché local del cliente consultará en el sistema DNS del Administrador de tráfico de Azure las entradas de DNS actualizadas.

-**Suscripción:** seleccione la suscripción con la que se corresponderá su perfil. Tenga en cuenta que esta opción solo aparece si tiene varias suscripciones.

-**Método de equilibrio de carga:** la forma en que desea que el Administrador de tráfico administre el equilibrio de carga.

-**Orden de conmutación por error:** cuando se emplea el método de equilibrio de carga de conmutación por error, el orden de los extremos.

-**Supervisión:** las opciones de supervisión incluyen el protocolo (HTTP o HTTPS), el puerto, la ruta de acceso relativa y el nombre de archivo.

### Configuración de opciones mediante las API de REST


Es posible crear y configurar el perfil del Administrador de tráfico mediante las API de REST. Para obtener más información, consulte [Operaciones en el Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

-**Perfil:** un perfil contiene el prefijo del nombre de dominio que haya creado el usuario. Cada perfil se corresponde con la suscripción del usuario. Es posible crear varios perfiles por suscripción. El nombre del perfil es visible en el Portal de administración. El nombre creado por el usuario, contenido en el perfil, se denomina Dominio del Administrador de tráfico.

-**Definición:** una definición contiene los parámetros de ajuste de la directiva y del monitor. Cada definición se corresponde con un perfil. Es posible tener una sola definición por perfil. La propia definición no es visible en el Portal de administración, aunque muchos de los parámetros de ajuste contenidos en la definición sí son visibles y pueden configurarse en el Portal.

-**Opciones de DNS:** en cada definición hay opciones de DNS. Aquí es donde se configura el TTL de DNS.

-**Supervisiones:** dentro de cada definición hay configuraciones de supervisión. Aquí es donde se configuran el protocolo, el puerto, la ruta de acceso relativa y el nombre de archivo. La configuración de supervisión es visible y se puede configurar en el Portal de administración. Para obtener más información, consulte [Acerca de la supervisión del Administrador de tráfico](https://msdn.microsoft.com/library/azure/dn339013.aspx).

-**Directiva:** dentro de cada definición hay parámetros de ajuste de directiva. La directiva es donde se especifican los métodos y extremos del equilibrio de la carga. La propia directiva no es visible en el Portal de administración, aunque algunos de los parámetros de ajuste de la misma son visibles y pueden configurarse en el Portal. Para obtener más información, consulte [Acerca de los métodos de equilibrio de carga del Administrador de tráfico](about-traffic-manager-balancing-methods.md)..

## Configuración de opciones mediante Windows PowerShell

Puede crear y configurar el perfil de Administrador de tráfico mediante Windows PowerShell. Para obtener más información, consulte [Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## <a name=bkmk_TrafficManagerBestPracticesProfile>Best practices</a>

- **Haga que los prefijos sean únicos y fáciles de entender: ** el nombre de DNS del perfil del Administrador de tráfico debe ser único. Solo puede controlar la primera parte del nombre DNS. El nombre de dominio del Administrador de tráfico se usa únicamente con fines de identificación y para dirigir solicitudes de cliente. Los equipos cliente nunca mostrarán estos nombres al usuario final. Sin embargo, los perfiles se identifican por este nombre de dominio, por lo cual resulta importante que pueda identificarlo entre otros nombres de dominio enumerados en el Portal de administración.
- **Utilice puntos para que los nombres de dominio sean únicos o fáciles de leer: ** también puede usar puntos para separar las partes del prefijo del nombre de dominio.  Si tiene previsto crear varias directivas en el Administrador de tráfico, utilice una jerarquía coherente para distinguir un servicio de otro. Por ejemplo, Contoso tiene servicios globales para la administración de la web, la facturación y las utilidades. Las tres directivas serían web.contoso.trafficmanager.net, bill.contoso.trafficmanager.net y util.contoso.trafficmanager.net. Al configurar servicios en la nube o sitios web, use nombres que incluyan la ubicación. Por ejemplo, web-us-contoso.cloudapp.net y web-asia-contoso.cloudapp.net. Las limitaciones son las que impone el DNS. Suponga que un nombre de dominio es una secuencia de etiquetas separadas por puntos (etiqueta.etiqueta.etiqueta.etiqueta.etc.). Cuando se redactó este documento, los límites para los nombres de dominio en el Administrador de tráfico eran como sigue:
	- Cada etiqueta puede tener un máximo de 63 caracteres.
	- No puede tener más de 40 etiquetas en total. Puesto que dos de las etiquetas las toma trafficmanager.net, quedan 38 para el prefijo.
	- El nombre de dominio completo puede tener un máximo de 253 caracteres. Tenga presente que trafficmanager.net tomará 19 de los caracteres.
- **TTL de DNS:-**  el valor del TTL de DNS controla la frecuencia con la que el servidor de nombres de almacenamiento en caché local del cliente consultará en el sistema DNS del Administrador de tráfico de Azure las entradas de DNS actualizadas. Los cambios que se producen dentro del Administrador de tráfico, como cambios de perfil o cambios en la disponibilidad del extremo, tardarán este tiempo en actualizarse en todo el sistema global de servidores DNS. Se recomienda dejar el valor predeterminado de 300 segundos (5 minutos). Un número mayor aumenta el tiempo en que las resoluciones y los clientes DNS almacenan en memoria caché las respuestas DNS del Administrador de tráfico, lo que reduce la latencia de consulta DNS total. Sin embargo, si se necesita una conmutación por error muy rápida, puede ser preferible configurar un valor menor.
- **Los extremos deben estar en una sola suscripción:-** todos los extremos deben encontrarse en la misma suscripción donde se vaya a crear el perfil. Puede agregar extremos de diferentes suscripciones a un perfil como extremos externos, pero Azure no los quitará automáticamente si deshabilita o elimina el servicio asociado. En consecuencia, los extremos externos permanecen en el perfil del Administrador de tráfico y continuarán cobrándosele a menos que los quite manualmente.

-**Solo servicios de producción:-** solamente están disponibles los extremos en un entorno de producción. No puede dirigir hacia los extremos que se ejecutan en un entorno de ensayo. Tenga en cuenta que si realiza un intercambio de IP virtual (VIP) mientras un perfil está dirigiendo tráfico, el tráfico usará el extremo que acaba de incorporarse al entorno de producción.

-**Asigne un nombre a los extremos de manera que se puedan identificar con facilidad:-** considere el prefijo de DNS que desea usar. Se usa el nombre de DNS porque está garantizado que es único en una suscripción, en tanto que es posible que el del servicio en la nube o sitio web no lo sea. Para evitar confusiones, asigne al servicio en la nube o sitio web un nombre y un prefijo de DNS que sean iguales o similares. Si tiene más de 20 servicios en la nube y sitios web, una denominación incorrecta podría dificultar en gran medida la búsqueda del extremo correcto. Además, los nombres inadecuados de los extremos dificultan el mantenimiento de los perfiles.

-**Todos los extremos de un perfil deben prestar servicio a las mismas operaciones y puertos:-**  si mezcla los extremos, habrá más posibilidades de que un cliente llame a un extremo que no pueda atender su solicitud.

-**Todos los servicios en la nube de un perfil deben usar la misma configuración de supervisión:-**  solo puede elegir una única ruta de acceso y un único archivo para supervisar todos los extremos de una definición determinada. Puede escribir "/" en el cuadro de texto Nombre de archivo y ruta de acceso relativa, de forma que la supervisión intentará tener acceso a la ruta de acceso y nombre de archivo predeterminados.

-**Para cambios temporales, deshabilite los extremos en lugar de cambiar la configuración:**  en muchos casos, es posible que desee desconectar un extremo. En lugar de quitar el extremo del perfil, deshabilite simplemente en el perfil el extremo individual. Esta acción deja el extremo como parte del perfil, pero el perfil actúa como si el extremo no estuviera incluido en él. Esto es muy útil para quitar temporalmente un extremo que está en modo de mantenimiento o que se va a volver a implementar. Una vez que el extremo vuelve a estar en funcionamiento, puede habilitarlo. Para obtener más información, consulte [Deshabilitación o habilitación de un extremo](disable-or-enable-an-endpoint.md).

-**Almacenamiento: -**  la forma de diseñar la ubicación y la distribución del almacenamiento es un aspecto importante cuando se usa el Administrador de tráfico. Piense en la transacción íntegramente y en cómo los datos se van a trasmitir cuando diseñe e implemente las aplicaciones para el Administrador de tráfico.

-**SQL Azure -**  De manera similar a lo que sucede con el diseño del almacenamiento, analice el estado de la aplicación y los requisitos de datos cuando amplíe los extremos a varias regiones geográficas.

## <a name="Nested">Perfiles anidados</a>

Puede especificar el nombre de otro perfil del Administrador de trafico como un extremo, una práctica que se conoce como perfiles anidados. El nombre del perfil del Administrador de tráfico es su nombre DNS, como por ejemplo, contoso-europe.trafficmanager.net.

De este modo puede configurar el Administrador de tráfico para que las consultas de nombres DNS entrantes se analicen en un conjunto de niveles a fin de garantizar que el cliente que realiza la solicitud es dirigido al conjunto de extremos correcto. En la Figura 3 se muestra un ejemplo.

![Example of nested Traffic Manager profiles](./media/traffic-manager/IC751072.png)

**Figura  3**

Puede anidar hasta 10 niveles y cada perfil se puede configurar con un método de equilibrio de carga distinto.

Por ejemplo, puede crear una configuración para lo siguiente:

-En el nivel superior (el perfil del Administrador de tráfico que se asigna a su nombre DNS externo), puede configurar el perfil con el método de equilibrio de carga de rendimiento.

-En el nivel medio, un conjunto de perfiles del Administrador de tráfico representa distintos centros de datos y usa el método de equilibrio de carga round robin.

-En el nivel inferior, un conjunto de extremos de servicio en la nube de cada centro de datos atiende las solicitudes de tráfico del usuario.

Como resultado, los usuarios son dirigidos a un base de centros adecuado a nivel regional en función del rendimiento y a un servicio en al nube dentro de ese centro de datos en función de una distribución de carga igual o ponderada. Por ejemplo, se puede usar la ponderación para distribuir un pequeño porcentaje de tráfico a una implementación nueva o de evaluación para pruebas o comentarios del cliente.

En la Figura 4 se muestra la configuración.

![Example of multi-tiered Traffic Manager profiles](./media/traffic-manager/IC751073.png)

**Figura  4**

En la Figura 4, el perfil del Administrador de tráfico del nivel superior es un perfil principal y los perfiles del Administrador de tráfico del nivel medio son perfiles secundarios.

Si el Administrador de tráfico dirige a los usuarios a un perfil secundario que tiene un número pequeño de extremos en buen estado, puede que se sobrecarguen esos extremos y se generen problemas de rendimiento. Para evitar esta situación, puede configurar el perfil de Administrador de tráfico principal con un umbral de extremos en buen estado que determine si cualquier extremo dentro de los perfiles secundarios del perfil principal puede recibir tráfico. Por ejemplo, si quiere asegurarse de que haya al menos tres extremos en buen estado dentro de los perfiles secundarios, debe establecer el valor del umbral en 3. En el ejemplo de la Figura 4, debe configurar el perfil del Administrador de tráfico del nivel superior para ese umbral.

Para agregar un perfil del Administrador de tráfico como extremo y configurar el número mínimo de extremos en buen estado, debe usar REST (consulte [Creación de definición ](http://go.microsoft.com/fwlink/p/?LinkId=400772)) o Windows PowerShell (consulte [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). No puede usar el Portal de administración.

## Ilustraciones del Administrador de tráfico


Si desea tener las ilustraciones de este tema en formato de diapositivas de PowerPoint para su propia presentación del Administrador de tráfico o para modificarlas, consulte [Ilustraciones del Administrador de tráfico en la documentación de MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Otras referencias

[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!--HONumber=49-->
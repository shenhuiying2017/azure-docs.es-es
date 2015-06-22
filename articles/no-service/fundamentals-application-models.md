<properties umbracoNaviHide="0" pageTitle="Modelo de aplicación" metaKeywords="Azure, Azure, modelo de aplicación, modelo de aplicación de Azure, modelo de desarrollo, modelo de desarrollo de Azure, servicio hospedado, servicio hospedado de Azure, rol web, rol de trabajo" description="Conozca el modelo de aplicación de un servicio hospedado en Azure. Comprenda los conceptos principales, las consideraciones de diseño, la definición y configuración de su aplicación y el escalado." urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Azure Fundamentals - Application Models" authors="timlt" manager="timlt" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/1/2014" ms.author="timlt" />



#Modelos de ejecución de Azure

Azure proporciona distintos modelos para la ejecución de aplicaciones. Cada uno de estos modelos proporciona un conjunto de servicios distinto, por lo tanto, el que elija dependerá exactamente de lo que intente hacer. En este artículo se analizan tres, se describe cada una de las tecnologías y se dan ejemplos de cuándo se podrían utilizar.

##Tabla de contenido

- [Máquinas virtuales](#VMachine)
- [Sitios web](#WebSites)
- [Servicios en la nube](#CloudServices)
- [¿Qué debo utilizar? Toma de una decisión](#WhatShouldIUse)

<h2><a id="VMachine"></a>Máquinas virtuales</h2>
Máquinas virtuales de Azure permite que desarrolladores, personal de operaciones de TI y otros creen y utilicen máquinas virtuales en la nube. Al proporcionar lo que se conoce como *Infrastructura como servicio (IaaS)*, esta tecnología se puede usar de varias formas. [En la Ilustración 1](#Fig1) se muestran sus componentes básicos.

<a name="Fig1"></a>![01_CreatingVMs][01_CreatingVMs]

**Ilustración 1: Máquinas virtuales de Azure proporciona Infraestructura como servicio.**

Tal como muestra la ilustración, puede crear máquinas virtuales con el Portal de administración de Azure o con la API de administración de servicios de Azure. Para tener acceso al Portal de administración, puede utilizar cualquier explorador de los que se utilizan habitualmente, como Internet Explorer, Mozilla y Chrome. En el caso de la API de REST, Microsoft proporciona herramientas de scripting de cliente para sistemas Windows, Linux y Macintosh. Otros programas de software también pueden utilizar esta interfaz.

Independientemente de la manera en que tenga acceso a la plataforma, la creación de una máquina virtual requiere elegir un disco duro virtual (VHD) para la imagen de la máquina virtual. Estos discos duros virtuales se almacenan en blobs de Azure. 

Para comenzar, tiene dos opciones 

1. Cargar su propio disco duro virtual 
2. Utilizar discos duros virtuales proporcionados por Microsoft y sus asociados en la galería de Máquinas virtuales de Azure o en el sitio web [VMDepot](http://vmdepot.msopentech.com/) de código abierto de Microsoft 

Los discos duros virtuales de la galería y en VMDepot incluyen imágenes limpias de sistemas operativos Microsoft y Linux, así como imágenes que incluyen productos de Microsoft y de terceros instalados.  Las opciones crecen constantemente. Los ejemplos incluyen distintas versiones, ediciones y configuraciones de:
 
-	Windows Server 
-	Servidores Linux como Suse, Ubuntu y CentOS
-	SQL Server
-	BizTalk Server 
-	SharePoint Server



Además de un disco duro virtual, debe especificar el tamaño de la máquina virtual nueva.  Puede encontrar las estadísticas completas para cada tamaño [en la biblioteca de Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx).  

-	**Extra pequeño**, con un núcleo compartido y 768 MB de memoria.
-	**Pequeño**, con 1 núcleo y 1,75 GB de memoria.
-	**Mediano**, con 2 núcleos y 3,5 GB de memoria.
-	**Grande**, con 4 núcleos y 7 GB de memoria.
-	**Extra grande**, con 8 núcleos y 14 GB de memoria.
-	**A6**, con 4 núcleos y 28 GB de memoria.
-	**A7**, con 8 núcleos y 56 GB de memoria.


Finalmente, elija el centro de datos de Azure en que se ejecutará la nueva máquina virtual, ya sea en Estados Unidos, Europa o Asia. 

Una vez que comienza a ejecutarse una máquina virtual, se paga por cada hora de ejecución y se deja de pagar cuando se quita esa máquina virtual. El importe que se paga no depende de cuánto se utiliza la máquina virtual; se basa únicamente en el tiempo de reloj. El coste es el mismo para una máquina virtual inactiva durante una hora que para una máquina virtual muy cargada. 

Cada VM en ejecución tiene un *disco de SO* asociado, guardado en un blob. Cuando crea una máquina virtual mediante el uso de un disco duro virtual de la galería, ese disco duro virtual se copia al disco de sistema operativo de la máquina virtual. Aquí se almacena todo cambio que haga en el sistema operativo de la máquina virtual en ejecución. Por ejemplo, si instala una aplicación que modifica el Registro de Windows, ese cambio se almacenará en el disco de sistema operativo de la máquina virtual. La próxima vez que cree una máquina virtual desde ese disco de sistema operativo, la máquina virtual seguirá ejecutándose en el mismo estado en que la dejó. En el caso de discos duros virtuales almacenados en la galería, Microsoft aplica actualizaciones, como revisiones del sistema operativo, cada vez que sea necesario. Sin embargo, para los discos duros virtuales en sus propios discos de sistema operativo, es su responsabilidad aplicar estas actualizaciones (aunque Windows Update está activado de manera predeterminada).

Las máquinas virtuales en ejecución también se pueden modificar y luego capturar con la herramienta sysprep. Sysprep quita detalles como el nombre de la máquina, para así poder utilizar una imagen de disco duro virtual para crear máquinas virtuales adicionales con la misma configuración general. Estas imágenes se almacenan en el Portal de administración junto con las imágenes cargadas para poder utilizarlas como punto de partida para máquinas virtuales adicionales. 

Las máquinas virtuales también supervisan el hardware en que se hospeda cada máquina virtual que crea. Si un servidor físico que ejecuta una máquina virtual presenta errores, la plataforma lo detecta e inicia la misma máquina virtual en otra máquina. Y, suponiendo que cuenta con las licencias correctas, puede copiar un disco duro virtual modificado fuera del disco de sistema operativo y ejecutarlo en otro lugar, como en su propio centro de datos local o en otro proveedor de nube. 

Además de su disco de sistema operativo, la máquina virtual tiene uno o varios discos de datos. A pesar de que cada uno de estos parece una unidad de disco montada en su máquina virtual, sus contenidos están almacenados en un blob. Cada escritura realizada en un disco de datos se almacena continuamente en el blob subyacente. Al igual que ocurre con todos los blobs, Azure replica estos dentro de un centro de datos único y entre centros de datos para permitir la protección contra errores.

Las máquinas virtuales en ejecución se pueden administrar con el Portal de administración, PowerShell y otras herramientas de scripting, o bien, directamente a través de la API de REST (de hecho, todo lo que pueda hacer a través del Portal de administración se puede hacer de manera programática con esta API). Asociados de Microsoft, como RightScale y ScaleXtreme, también proporcionan servicios de administración que se basan en la API de REST.

Máquinas virtuales de Azure se puede utilizar de diversas maneras. Los principales escenarios a los que apunta Microsoft incluyen:

- **Máquinas virtuales para desarrollo y pruebas.** Los grupos de desarrollo habitualmente necesitan máquinas virtuales con configuraciones específicas para crear aplicaciones. Las máquinas virtuales de Azure ofrecen una manera sencilla y económica de crear estas VM, usarlas y quitarlas cuando ya no sean necesarias.
- **Ejecución de aplicaciones en la nube.** Para algunas aplicaciones, la ejecución en la nube pública tiene sentido económico. Por ejemplo, piense en una aplicación con grandes puntas en la demanda. Siempre es posible comprar suficientes máquinas para que su propio centro de datos ejecute esta aplicación, pero la mayoría de los equipos probablemente permanezcan sin usarse la mayor parte del tiempo. La ejecución de esta aplicación en Azure le permite pagar VM adicionales solo cuando las necesite y apagarlas cuando haya finalizado una punta de la demanda. O bien, supongamos que es una compañía nueva que necesita recursos informáticos a petición rápidamente y sin compromiso. Una vez más, Azure puede ser la opción más adecuada.
- **Ampliación de su propio centro de datos en la nube pública.** Con la red virtual de Azure, su organización puede crear una red virtual (VNET) que hace que un grupo de VM de Azure parezca formar parte de su propia red local. Esto permite ejecutar aplicaciones como SharePoint y otras en Azure, un enfoque que podría ser más fácil de implementar y menos caro que ejecutarlas en su propio centro de datos.
- **Recuperación ante desastres.** En lugar de pagar continuamente por un centro de datos de copia de seguridad que se utiliza en contadas ocasiones, la recuperación de desastres basada en IaaS le permite pagar por los recursos informáticos que necesita solo cuando realmente los necesita. Por ejemplo, si el centro de datos principal deja de funcionar, puede crear VM que se ejecutan en Azure para ejecutar aplicaciones esenciales y apagarlas cuando ya no sean necesarias.

Estas no son las únicas posibilidades, pero son buenos ejemplos de cómo podría utilizar Máquinas virtuales de Azure.  


###Agrupación de máquinas virtuales: Servicios en la nube 

Cuando cree una nueva VM con las máquinas virtuales de Azure, puede elegir ejecutarla de manera independiente o hacer que forme parte de un grupo de VM que se ejecutan de manera conjunta en un *servicio en la nube*. (a pesar de que tienen nombres similares, no confunda este concepto con Servicios en la nube, el nombre de la tecnología PaaS; no son lo mismo).  Cada máquina virtual independiente tiene asignada su propia dirección IP pública, aunque es posible tener acceso a todas las máquinas virtuales en el mismo servicio en la nube a través de una única dirección IP pública. [En la Ilustración 2](#Fig2) se muestra qué aspecto tiene esto.
 
<a name="Fig2"></a>![02_CloudServices][02_CloudServices]

**Ilustración 2: Cada máquina virtual tiene su propia dirección IP pública, aunque las máquinas virtuales agrupadas en un servicio en la nube están expuestas a través de una única dirección IP pública.**

Por ejemplo, si estuviese creando una máquina virtual para crear y probar una aplicación simple, probablemente utilizaría una máquina virtual independiente. Sin embargo, si está creando una aplicación de niveles múltiples, con un front-end web, una base de datos e incluso un nivel intermedio, lo más probable es que conecte varias máquinas virtuales con un servicio en la nube, tal como sugiere la ilustración 2.

Agrupar máquinas virtuales en un servicio en la nube le permite utilizar también otras opciones. Azure proporciona equilibro de carga para las máquinas virtuales en el mismo servicio en la nube, para así distribuir las solicitudes de usuario entre ellas. Las máquinas virtuales conectadas de este modo también se pueden comunicar directamente con otra sobre la red local dentro de un centro de datos de Azure. 

Las VM del mismo servicio en la nube también se pueden agrupar en uno o más *conjunto de disponibilidad*. Para comprender la importancia de esto, piense en una aplicación web que ejecuta varias máquinas virtuales de front-end. Si todas estas máquinas virtuales se implementan en la misma máquina física, o incluso en el mismo bastidor de máquinas, un simple error de hardware puede hacer que todas queden inaccesibles. Sin embargo, si estas máquinas virtuales se agrupan en un conjunto de disponibilidad, Azure las implementará en todo centro de datos, para que ninguna comparta un único punto posible de errores.

###Situación: Ejecución de una aplicación con SQL Server

Para comprender mejor cómo funciona Máquinas virtuales de Azure, resulta útil observar un par de escenarios con más detalle. Por ejemplo, imagine que desea crear una aplicación web confiable y escalable que se ejecuta en Azure. Una forma de hacerlo es ejecutar la lógica de la aplicación en una o varias máquinas virtuales de Azure y luego usar SQL Server para la administración de datos. [En la Ilustración 3](#Fig3) se muestra qué aspecto tiene esto.

<a name="Fig3"></a>![03_AppUsingSQLServer][03_AppUsingSQLServer]

**Ilustración 3: Una aplicación que se ejecuta en Máquinas virtuales de Azure puede usar SQL Server para almacenamiento.**

En este ejemplo, ambos tipos de máquinas virtuales se crean a partir de discos duros virtuales estándar en la galería. La lógica de la aplicación se ejecuta en Windows Server 2008 R2, de tal manera que el desarrollador crea tres máquinas virtuales a partir de este disco duro virtual y luego instala su aplicación en cada una de ellas. Como todas estas máquinas virtuales se encuentran en el mismo servicio en la nube, puede configurar el equilibro de carga de hardware para distribuir solicitudes entre ellas. El desarrollador también crea dos VM desde el VHD de la galería con SQL Server 2012. Cuando se están ejecutando, configura SQL Server en cada instancia para usar la creación de reflejo de bases de datos con conmutación por error automática. Esto no es necesario; la aplicación podría utilizar solo una instancia de SQL Server; sin embargo, al hacerlo de este modo se mejora la confiabilidad. 

###Situación: Ejecución de una granja de SharePoint 

Imagine que una organización desea crear una granja de SharePoint pero no desea ejecutarla en su propio centro de datos. Probablemente al centro de datos local le queden pocos recursos, o quizás la unidad de negocio que crea la granja no desea tener que tratar con este grupo de TI interno. En casos así, puede tener sentido ejecutar SharePoint en Máquinas virtuales de Azure. [En la Ilustración 4](#Fig4) se muestra qué aspecto tiene esto.

<a name="Fig4"></a>![04_SharePointFarm][04_SharePointFarm]
 
**Ilustración 4: Máquinas virtuales de Azure permite ejecutar una granja de SharePoint en la nube.**

Una granja de SharePoint tiene varios componentes, cada uno de los cuales se ejecuta en una máquina virtual de Azure creada a partir de un disco duro virtual distinto. Se necesita lo siguiente:

- Microsoft SharePoint. Existen imágenes de prueba en la galería, o bien la organización proporciona su propio disco duro virtual.
- Microsoft SQL Server. SharePoint depende de SQL Server, por lo que la organización crea máquinas virtuales que ejecutan SQL Server 2012 a partir de un disco duro virtual de galería estándar.
- Windows Server Active Directory. SharePoint también requiere Active Directory, por lo que la organización crea controladores de dominio en la nube con una imagen de Windows Server obtenida de la galería. Esto no resulta estrictamente necesario, porque también es posible utilizar controladores de dominio locales, pero como SharePoint interactúa frecuentemente con Active Directory, este método tendrá un mejor rendimiento.

A pesar de que no aparece en la ilustración, esta granja de SharePoint probablemente esté conectada a una red local mediante el uso de Red virtual de Azure. Esto permite que quienes utilizan esa red vean las máquinas virtuales y las aplicaciones que contienen como locales.

Tal como muestran estos ejemplos, puede utilizar Máquinas virtuales de Azure para crear y ejecutar aplicaciones nuevas en la nube, para ejecutar aplicaciones existentes o de otras maneras. Cualquiera que sea la opción que elija, el objetivo de la tecnología es el mismo: proporcionar una base general para la informática en la nube pública.



<h2><a id="WebSites"></a>Sitios web</h2>

Las personas utilizan las tecnologías web de muchas maneras distintas. Una corporación podría requerir la migración o configuración de un sitio web de presencia que pueda manejar millones de visitas a la semana y que se pueda implementar en varios centros de datos en todo el mundo. Una agencia de diseño web podría trabajar en conjunto con un equipo de desarrolladores para crear una aplicación web personalizada capaz de manejar miles de usuarios. Un desarrollador corporativo podría tener que configurar una aplicación para realizar seguimiento de los informes de gastos en la nube para usuarios autenticados desde su Active Directory corporativo. Un consultor de TI podría utilizar una aplicación popular de código abierto para configurar un sistema de administración de contenido para una pequeña empresa.
Todo esto se podría lograr con Máquinas virtuales de Azure. Pero crear y administrar máquinas virtuales sin procesar requiere ciertas aptitudes y esfuerzo. Si tiene que implementar un sitio web o una aplicación web, hay una solución más sencilla (y más barata): el enfoque comúnmente conocido como Plataforma como servicio (PaaS). Tal como muestra la ilustración 5, Azure proporciona este enfoque con Sitios web.


<a name="Fig5"></a>![05_Websites][05_Websites]
 
**Ilustración 5: Sitios web Azure es compatible con sitios web estáticos, aplicaciones web populares y aplicaciones web personalizadas creadas con diversas tecnologías.** 

Sitios web Azure se basa en Servicios en la nube de Azure para crear una solución de Plataforma como servicio optimizada para ejecutar aplicaciones web. Tal como muestra la ilustración, Sitios web se ejecuta en un conjunto de máquinas virtuales que puede contener varios sitios web creados por diversos usuarios, así como también máquinas virtuales estándar que pertenecen a un usuario individual. Las VM forman parte de un grupo de recursos administrados por los sitios web de Azure y, por tanto, permiten una alta confiabilidad y tolerancia a errores.
Comenzar es fácil. Con Sitios web Azure, los usuarios pueden seleccionar entre una variedad de aplicaciones, marcos y plantillas y crear un sitio web en cuestión de segundos. Luego pueden utilizar sus herramientas de desarrollo preferidas (WebMatrix, Visual Studio o cualquier otro editor) y las opciones de control de código fuente para configurar la integración continua y desarrollarse como equipo. Las aplicaciones basadas en una base de datos de MySQL pueden consumir un servicio de MySQL proporcionado para Azure por ClearDB, un asociado de Microsoft.
Los desarrolladores pueden crear grandes aplicaciones web escalables con sitios web. La tecnología es compatible con la creación de aplicaciones con ASP.NET, PHP, Node.js y Python. Las aplicaciones pueden utilizar sesiones persistentes, por ejemplo, y las aplicaciones web existentes se pueden mover sin cambios a esta plataforma de nube. Las aplicaciones creadas en Sitios web pueden utilizar otros aspectos de Azure, como Bus de servicio, Base de datos SQL y Almacenamiento de blobs. También puede ejecutar varias copias de una aplicación en distintas máquinas virtuales, con Sitios web equilibrando automáticamente la carga de solicitudes entre ellas. Y dado que se crean nuevas instancias de sitios web en máquinas virtuales que ya existen, el inicio de una nueva instancia de la aplicación ocurre muy rápidamente, es significativamente más rápido que esperar que se cree una nueva VM.
Como muestra la [Figura 5],(#Fig5) puede publicar código y otro contenido web en sitios web de varias maneras. Puede utilizar la tecnología WebDeploy de Microsoft, FTPS o FTP. Sitios web también es compatible con la publicación de código a partir de sistemas de control de código fuente, incluido Git, GitHub, CodePlex, BitBucket, Dropbox, Mercurial, Team Foundation Server y el Team Foundation Service basado en la nube.


<h2><a id="CloudServices"></a>Servicios en la nube</h2>

Máquinas virtuales de Azure proporciona IaaS, mientras que Sitios web Azure proporciona servicio de hospedaje de sitios web. La tercera opción de proceso, Servicios en la nube, ofrece *Plataforma como servicio (PaaS)*. Esta tecnología está diseñada para ser compatible con aplicaciones escalables, confiables y económicas de operar. También tiene como objetivo que los desarrolladores dejen de preocuparse de administrar la plataforma que utilizan, permitiéndoles así centrarse completamente en sus aplicaciones. [En la Ilustración 6](#Fig6) se muestra la idea.

<a name="Fig6"></a>![06_CloudServices2][06_CloudServices2] 

**Ilustración 6: Servicios en la nube de Azure proporciona Plataforma como servicio.**

Al igual que las otras opciones de proceso de Azure, Servicios en la nube se basa en las máquinas virtuales. La tecnología proporciona dos opciones de VM ligeramente diferentes: las instancias de *roles web* ejecutan una variante de Windows Server con IIS mientras que las instancias de *roles de trabajado* ejecutan la misma variante de Windows Server sin IIS. Una aplicación de Servicios en la nube se basa en alguna combinación de estas dos opciones. 

Por ejemplo, una aplicación simple podría utilizar solo un rol web, mientras que una aplicación más compleja podría utilizar un rol web para manejar solicitudes entrantes provenientes de los usuarios y luego transmitir el trabajo que crean esas solicitudes a un rol de trabajo para su procesamiento (esta comunicación podría utilizar bus de servicio o colas de Azure).

Como sugiere la ilustración, todas las máquinas virtuales de una sola aplicación se ejecutan en el mismo servicio en la nube, tal como se describió anteriormente para Máquinas virtuales de Azure. Debido a esto, los usuarios tienen acceso a la aplicación a través de una dirección IP pública única y se realiza automáticamente el equilibrio de carga de las solicitudes en todas las máquinas virtuales de la aplicación. Y al igual que ocurre con los servicios en la nube que se crean con Máquinas virtuales de Azure, la plataforma implementará las máquinas virtuales en una aplicación para así evitar un único punto de errores de hardware.

A pesar de que las aplicaciones se ejecutan en máquinas virtuales, resulta importante comprender que Servicios en la nube proporciona PaaS y no IaaS. Piénselo así: Con IaaS, tal como en Máquinas virtuales de Azure, primero crea y configura el entorno en que se ejecutará la aplicación, para luego implementar la aplicación en este entorno. Es responsabilidad suya administrar gran parte de este mundo, como por ejemplo, implementar nuevas versiones revisadas del sistema operativo en cada máquina virtual. Por el contrario, en PaaS ocurre como si el entorno ya existiese. Todo lo que tiene que hacer es implementar la aplicación. Usted maneja la administración de la plataforma en que se ejecuta, incluida la implementación de versiones nuevas del sistema operativo.

Con Servicios en la nube no se crean máquinas virtuales. En lugar de eso, se proporciona un archivo de configuración que le indica a Azure qué cantidad de cada una le gustaría tener, como por ejemplo, tres instancias de rol web y dos instancias de rol de trabajo, y la plataforma las crea por usted.  Sigue siendo necesario que elija el tamaño que deben tener esas máquinas virtuales (las opciones son las mismas que con Máquinas virtuales de Azure), pero no tiene que crearlas por sí mismo explícitamente. Si la aplicación necesita manejar una carga mayor, puede pedir más máquinas virtuales y Azure creará esas instancias. Si la carga disminuye, puede apagar esas instancias y así dejar de pagar por ellas.

Una aplicación de Servicios en la nube normalmente está a disposición de los usuarios a través de un proceso de dos pasos. Un desarrollador primero carga la aplicación en el área de ensayo de la plataforma. Cuando el desarrollador está preparado activar definitivamente la aplicación, utiliza el Portal de administración de Azure para solicitar ponerla en producción. Este intercambio entre el área de ensayo y la producción se puede llevar a cabo sin tiempo de inactividad, lo que permite actualizar una aplicación en ejecución a una versión nueva sin interrumpir ni molestar a sus usuarios. 

Servicios en la nube también brinda supervisión. Al igual que con Máquinas virtuales de Azure, detectará un servidor físico con errores y reiniciará las máquinas virtuales que se ejecutaban en ese servidor en una máquina nueva. Pero Servicios en la nube también detecta máquinas virtuales y aplicaciones con errores, no solo errores de hardware. A diferencia de Máquinas virtuales, Servicios en la nube tiene un agente dentro de cada rol web o rol de trabajo, por lo que puede iniciar nuevas instancias de aplicaciones y máquinas virtuales cuando se produce un error.

La naturaleza de PaaS de Servicios en la nube tiene también otras implicaciones. Una de las más importantes es que las aplicaciones creadas sobre la base de esta tecnología deben estar escritas para ejecutarse correctamente en caso de cualquier error en instancia de rol web o de trabajo. Para lograrlo, una aplicación de Servicios en la nube no debiera mantener estado en el sistema de archivos de sus propias máquinas virtuales. A diferencia de las máquinas virtuales creadas con Máquinas virtuales de Azure, las escrituras que se realizan en las máquinas virtuales de Servicios en la nube no son permanentes; no hay nada parecido a un disco de datos de Máquinas virtuales. En lugar de eso, una aplicación de Servicios en la nube debe escribir explícitamente todo estado en la base de datos de SQL, blogs, tablas u algún otro tipo de almacenamiento externo. Cuando se crean aplicaciones de esta manera, estas resultan más fáciles de escalar y son más resistentes ante los errores, ambos objetivos importantes de Servicios en la nube.


<h2><a id="WhatShouldIUse"></a>¿Qué debo utilizar? Toma de una decisión</h2>

Los tres modelos de ejecución de Azure le permiten crear aplicaciones escalables y confiables en la nube. Dada esta similitud esencial, ¿cuál de estos modelos debería utilizar? La respuesta depende de qué es lo que intenta hacer.

Máquinas virtuales proporciona la solución más general. Si desea el mayor control posible, o si necesita máquinas virtuales genéricas, como para desarrollo y prueba, por ejemplo, esta es la mejor opción. Máquinas virtuales también es la mejor opción para ejecutar aplicaciones locales listas para la venta en la nube, tal como se ilustró en el ejemplo de SharePoint descrito anteriormente. Y como las máquinas virtuales que crea que con esta tecnología pueden verse tal como sus máquinas virtuales locales, probablemente esta también sea la mejor opción para la recuperación ante desastres. El asunto es que, por supuesto, con un gran poder viene una gran responsabilidad: IaaS requiere que asuma cierto trabajo administrativo.  

Sitios web es la opción adecuada cuando desea crea un sitio web simple. Esto es especialmente así si el sitio se basará en una aplicación existente, como Joomla, WordPress o Drupal. Sitios web también es una buena opción para crear una aplicación web que requiera poca administración, incluso una que deba ser muy escalable, o para mover una aplicación web IIS existente a la nube pública. También proporciona una implementación rápida. Una instancia nueva de la aplicación puede comenzar a ejecutarse casi de inmediato, mientras que implementar una máquina virtual nueva con Máquinas virtuales o Servicios en la nube puede demorar varios minutos. 

Servicios en la nube, que fue el modelo de ejecución inicial proporcionado por Azure, es un enfoque de PaaS explícito. A pesar de que la línea entre PaaS y el servicio de hospedaje de sitios web es poco clara, Servicios en la nube tiene importantes diferencias con Sitios web, incluidas las siguientes:

- A diferencia de Sitios web, Servicios en la nube le brinda acceso administrativo a las máquinas virtuales de su aplicación. Esto le permite instalar software arbitrario que necesite su aplicación, una acción que no es posible con Sitios web.
- Como Servicios en la nube ofrece roles web y roles de trabajo, resulta una mejor opción que Sitios web para aplicaciones de niveles múltiples que necesiten máquinas virtuales independientes para su lógica de negocios.
- Servicios en la nube proporciona entornos de ensayo y producción independientes, lo que hace que las actualizaciones de aplicaciones sean más fluidas que con Sitios web. 
- A diferencia de Sitios web, puede utilizar tecnologías de red como Red virtual de Azure y Azure Connect para enlazar equipos locales con aplicaciones de Servicios en la nube. 
- Servicios en la nube le permite utilizar Escritorio remoto para conectarse directamente a las máquinas virtuales de una aplicación, algo que no es posible con Sitios web.

Como es PaaS, Servicios en la nube también ofrece algunas ventajas sobre Máquinas virtuales de Azure. Por ejemplo, se realizan más tareas de administración en su lugar, como la implementación de actualizaciones de sistema operativo, para que así sus costos de operaciones tiendan a ser más bajos que con el enfoque de IaaS de Máquinas virtuales de Azure.

Los tres modelos de ejecución de Azure tienen ventajas y desventajas. Para tomar la mejor decisión, debe comprender estos puntos, debe saber lo que intenta hacer y luego debe elegir la opción que mejor le acomode.

A veces, el modelo de ejecución correcto no es solo uno. En situaciones así, es perfectamente posible combinar opciones. Por ejemplo, imagine que construye una aplicación en la que desearía contar con los beneficios de administración de los roles web de Servicios en la nube, pero también querría utilizar un SQL Server estándar por motivos de compatibilidad o rendimiento. En este caso, la mejor opción es combinar modelos de ejecución, como muestra la [Figura 7](#Fig7) .

<a name="Fig7"></a>![07_CombineTechnologies][07_CombineTechnologies] 
 
**Ilustración 7: Una aplicación única puede utilizar varios modelos de ejecución.**

Tal como muestra la ilustración, las máquinas web de Servicios en la nube se ejecutan en un servicio en la nube independiente de las máquinas virtuales de Máquinas virtuales. Pero de todos modos, ambos se pueden comunicar de manera muy eficiente, de tal modo que crear una aplicación de este modo es, a menudo, la mejor opción.

Azure proporciona distintos modelos de ejecución porque las plataformas de nube necesitan permitir muchos escenarios distintos. Toda persona que desee utilizar esta plataforma de manera eficaz (si ha leído hasta aquí, probablemente sea una de ellas) necesita comprender cada uno de ellos.

[01_CreatingVMs]: ./media/fundamentals-application-models/ExecModels_01_CreatingVMs.png
[02_CloudServices]: ./media/fundamentals-application-models/ExecModels_02_CloudServices.png
[03_AppUsingSQLServer]: ./media/fundamentals-application-models/ExecModels_03_AppUsingSQLServer.png
[04_SharePointFarm]: ./media/fundamentals-application-models/ExecModels_04_SharePointFarm.png
[05_Websites]: ./media/fundamentals-application-models/ExecModels_05_Websites.png
[06_CloudServices2]: ./media/fundamentals-application-models/ExecModels_06_CloudServices2.png
[07_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png



<!--HONumber=35.2-->
 
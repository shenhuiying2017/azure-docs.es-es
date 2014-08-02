
# Introducción a Azure

Azure es la plataforma de aplicaciones de Microsoft para la nube pública. Esta plataforma puede utilizarse de muchas maneras diferentes. Por ejemplo, se puede utilizar Azure para crear una aplicación web que ejecute y almacene sus datos en centros de datos de Azure. O bien, se puede utilizar simplemente para almacenar datos, y las aplicaciones que usan esos datos ejecutarse en local (es decir, fuera de la nube pública). Se puede utilizar Azure para crear máquinas virtuales para las tareas de desarrollo y prueba o para ejecutar SharePoint y otras aplicaciones. Asimismo, se puede emplear para crear aplicaciones escalables de forma masiva con muchos usuarios. Dado que la plataforma ofrece una amplia variedad de servicios, se puede hacer todo esto, y mucho más.

Sin embargo, para realizar cualquiera de ellas es necesario conocer sus aspectos básicos. Incluso si no conoce nada de la informática en nube, este artículo le llevará por los fundamentos de Azure. El objetivo es proporcionarle una base que le permita comprender y utilizar esta plataforma de nube.

## Tabla de contenido

* [Los componentes de Azure](#components)
* [Modelos de ejecución/proceso](#models)
* [Administración de datos](#data)
* [Redes](#networking)
* [Análisis de negocios](#analytics)
* [Mensajería](#messaging)
* [Almacenamiento en caché](#caching)
* [Identidad](#identity)
* [Informática de alto rendimiento (HPC)](#HPC)
* [Multimedia](#media)
* [Comercio](#commerce)
* [SDK](#sdk)
* [Tareas iniciales](#start)

<h2><a id="components" ></a>Los componentes de Azure</h2>


Para comprender lo que ofrece Azure, resulta útil agrupar sus servicios en diferentes categorías. La ilustración 1 muestra una manera de hacer esto.

![Componentes de Azure](./media/intro-to-azure/IntroAzure1.png)   
 **Ilustración 1: Azure proporciona servicios de aplicaciones accesibles desde Internet que se ejecutan en centros de datos de Azure.**

Para comenzar con Azure, es necesario conocer al menos los aspectos básicos de cada uno de sus componentes. También puede consultar la página [Azure Poster (en inglés)][1] para obtener un resumen visual rápido. Los colores de los cuadros de la ilustración 1 corresponden a su agrupación en el póster.

El resto de este artículo trata de las tecnologías que se muestran en la ilustración, con una descripción de lo que ofrece cada una y en qué casos podría utilizarla.

<h2><a id="models" ></a>Modelos de ejecución/proceso</h2>


Una de las funciones más básicas de una plataforma de nube es la ejecución de aplicaciones. Para tal fin, Azure proporciona cuatro opciones: Máquinas virtuales, Servicios en la nube, Sitios web y Servicios móviles. Cada uno de los modelos de ejecución de Azure desempeña su propia función.

Máquinas virtuales de Azure proporciona un entorno informático a petición de uso general. Servicios en la nube es una buena opción para crear aplicaciones escalables y confiables con bajos costes administrativos. Sitios web Azure ofrece una variedad de aplicaciones, marcos de trabajo y plantillas para la creación rápida de aplicaciones web escalables de gran tamaño y sitios web de presencia y la posterior administración de desarrollos, pruebas y operaciones de manera eficiente. De igual manera, Servicios móviles de Azure acelera el desarrollo de aplicaciones para dispositivos móviles, y proporciona una forma llave en mano para almacenar los datos en la nube, autenticar a los usuarios y enviar notificaciones de inserción.

Estas tecnologías se pueden usar por separado o combinarlas como sea necesario para crear la base perfecta para su aplicación. El enfoque que elija depende del problema que intente resolver.

### Máquinas virtuales

La posibilidad de crear una máquina virtual a petición, ya sea a partir de una imagen estándar o de una que suministre el usuario, puede resultar muy útil. Y si a esto se suma la posibilidad de pagar por esta VM por minuto y solo cuando se está ejecutando, su utilidad es incluso mayor. Este enfoque, conocido comúnmente como Infraestructura como servicio (IaaS), es lo que proporciona Máquinas virtuales de Azure.

Para crear una VM, es necesario especificar qué VHD se va a utilizar y el tamaño de la VM. Luego, se paga por el tiempo que se ejecuta la VM. Máquinas virtuales de Azure ofrece una galería de discos duros virtuales (VHD) en existencias. Estos incluyen opciones proporcionadas por Microsoft, como Windows Server 2008 R2, Windows Server 2012 y Windows Server 2008 R2 con SQL Server, junto con imágenes de Linux que proporcionan socios de Microsoft. También tiene la libertad de crear VM a partir de sus propios VHD y agregarlas a la galería.

Con independencia del lugar de donde provenga la imagen, puede almacenar repetidamente los cambios realizados mientras la VM esté en ejecución. La siguiente vez que cree una VM a partir de ese VHD, las cosas estarán donde las dejó. También es posible copiar el VHD modificado fuera de Azure y luego ejecutarlo localmente.

Las VM de Azure se pueden utilizar de muchas maneras diferentes. Así, se pueden utilizar para crear una plataforma económica de desarrollo y prueba que puede apagar cuando termine de usarla. O también, podría crear y ejecutar aplicaciones que utilicen los lenguajes y las bibliotecas que prefiera. Esas aplicaciones pueden utilizar cualquiera de las opciones de administración de datos que proporciona Azure, y también puede elegir usar SQL Server u otro DBMS que se ejecute en una o varias máquinas virtuales. Otra opción es utilizar VM de Azure como extensión de su centro de datos local, y ejecutar SharePoint u otras aplicaciones. Para permitir esto, es posible crear dominios de Windows en la nube ejecutando Active Directory en las VM de Azure. Este enfoque bastante general de la informática en nube se puede utilizar para solucionar muchos problemas diferentes. De usted depende la decisión final.

### Sitios web

Una de las cosas más importantes que la gente hace en la nube es ejecutar sitios y aplicaciones web. Máquinas virtuales de Azure le permite esto, pero le deja con la responsabilidad de administrar una o varias VM. &iquest;Y si lo que desea es un sitio web donde otra persona se encargue del trabajo administrativo por usted?

Esto es exactamente lo que proporciona Sitios web Azure. Este modelo de ejecución ofrece un entorno web administrado mediante el Portal de administración de Azure así como de API. Puede mover un sitio web existente a Sitios web Azure sin cambios, o puede crear uno directamente en la nube. Cuando un sitio web está en ejecución, puede agregar o quitar instancias de forma dinámica, y recurrir a Sitios web Azure para equilibrar las solicitudes de carga entre ellos. Sitios web Azure ofrece tanto una opción compartida, en la que el sitio web se ejecuta en una máquina virtual con otros sitios, como una opción estándar, que permite que un sitio se ejecute en su propia VM. La opción estándar también le permite aumentar el tamaño (potencia informática) de las instancias, si es necesario.

Sitios web Azure va a resultar útil para corporaciones, desarrolladores y agencias de diseño web. En el caso de las corporaciones, constituye una solución fácil de administrar, escalable, muy segura y de alta disponibilidad para la ejecución de sitios web de presencia. Para el desarrollo, es compatible con .NET, PHP, Node.js y Python, junto con Base de datos SQL y MySQL (de ClearDB, un socio de Microsoft) para el almacenamiento relacional. También ofrece compatibilidad integrada con varias aplicaciones conocidas, como WordPress, Joomla y Drupal. El objetivo es proporcionar una plataforma de bajo coste, escalable y enormemente útil para la creación de sitios y aplicaciones web en la nube pública.

### Servicios en la nube

Supongamos que desea crear una aplicación en la nube que pueda admitir varios usuarios de manera simultánea, que no requiera mucha administración y que siempre esté disponible. Podría ser, por ejemplo, un proveedor de software consolidado que ha decidido embarcarse en el Software como servicio (SaaS) mediante la creación de una de sus aplicaciones en la nube. O bien, podría ser un principiante que desea crear una aplicación de consumidor que espera que crezca rápido. Si fuera a crear sobre Azure, &iquest;qué modelo de ejecución debería usar?

Sitios web Azure le permite crear esta clase de aplicación web, pero existen algunas limitaciones. No dispone de acceso administrativo, por ejemplo. Esto significa que no puede instalar software arbitrario. Máquinas virtuales de Azure le proporciona mucha flexibilidad, incluso acceso administrativo, y puede usarlo desde luego para crear una aplicación muy escalable. Sin embargo, tendrá que gestionar muchos aspectos de confiabilidad y administración por sí mismo. Lo que desea es una opción que le otorgue el control que necesita, pero que además gestione la mayoría del trabajo relacionado con dichos aspectos.

Pues esto es exactamente lo que proporciona Servicios en la nube de Azure. Esta tecnología está diseñada expresamente para permitir aplicaciones escalables, confiables y de bajo coste administrativo, y es un ejemplo de lo que comúnmente se denomina plataforma como servicio (PaaS). Para utilizarla, debe crear una aplicación mediante la tecnología que elija, por ejemplo C#, Java, PHP, Python, Node.js o alguna otra. A continuación, el código se ejecuta en máquinas virtuales (conocidas como instancias) que ejecutan una versión de Windows Server.

Pero estas VM son distintas de aquellas que se crean con Máquinas virtuales de Azure. Y la diferencia es esta: Azure es quien las administra. Así, realiza tareas como instalar revisiones de sistemas operativos e implementar automáticamente las nuevas imágenes revisadas. Esto implica que su aplicación no debe mantener el estado en las instancias de rol web o de trabajo, sino que, por el contrario, se mantiene en una de las opciones de administración de datos de Azure que se describen en la siguiente sección. Azure también supervisa las VM y las reinicia en caso de error.

A la hora de crear una instancia, dispone de dos roles para elegir, ambos basados en Windows Server. La principal diferencia entre los dos es que una instancia de un rol web ejecuta IIS, mientras que la de un rol de trabajo no. No obstante, ambas se administran de la misma forma, y lo normal en una aplicación es usar las dos. Por ejemplo, una instancia de rol web podría aceptar solicitudes de los usuarios, y luego pasarlas a una instancia de rol de trabajo para procesarlas. Para escalar o reducir verticalmente la aplicación, puede solicitar que Azure cree más instancias de uno de los roles o apagar las instancias existentes. Y como sucede con Máquinas virtuales de Azure, solo paga por el tiempo que se ejecuta cada instancia de rol web o de trabajo.

### Servicios móviles

Si va a crear una aplicación para un dispositivo móvil, Servicios móviles de Azure acelera el desarrollo al proporcionar una forma llave en mano de almacenar los datos en la nube, autenticar a los usuarios y enviar notificaciones de inserción. 

Las bibliotecas de cliente nativas para Android, iOS, HTML/JavaScript, Windows Phone y la Tienda Windows facilitan la disponibilidad de aplicaciones en todas las principales plataformas móviles. Una API de REST abierta y flexible también le permite usar la funcionalidad de datos y autenticación de Servicios móviles con aplicaciones en casi cualquier plataforma. Un único servicio móvil puede respaldar varias aplicaciones cliente, de este modo puede proporcionar una experiencia de usuario coherente entre dispositivos.

Servicios móviles le permite aprovisionar y administrar de forma sencilla los datos almacenados en una Base de datos SQL, autenticar a los usuarios a través de proveedores de identidad conocidos, como cuentas de Facebook, Twitter, Microsoft o Google, y utilizar los servicios de notificación para distribuir notificaciones de inserción a la aplicación. Con el código de servidor, puede utilizar fácilmente opciones adicionales de almacenamiento de datos como almacenamiento de blobs o MongoDB, consumir complementos de la Tienda Azure como SendGrid o Pusher, utilizar otros servicios de Azure como Bus de servicio y roles de trabajo, o incluso conectar con sistemas locales. Un servicio se puede escalar conforme una aplicación se haga más popular, y se permiten la supervisión y el registro.

Si bien es cierto que puede crear el back-end de una aplicación móvil mediante Máquinas virtuales, Servicios en la nube o Sitios web, si opta por Servicios móviles, tardará mucho menos tiempo en programar los componentes de servicio subyacentes.

<h2><a id="data" ></a>Administración de datos</h2>


Las aplicaciones necesitan datos, y diferentes tipos de aplicaciones necesitan diferentes tipos de datos. Como consecuencia, Azure ofrece varias formas de almacenar y administrar los datos.

Una de ellas ya se ha mencionado: la posibilidad de ejecutar SQL Server u otro DBMS en una VM creada con Máquinas virtuales de Azure. (Es importante darse cuenta de que esta opción no se limita a los sistemas relacionales; también tiene la libertad de ejecutar tecnologías NoSQL como MongoDB y Cassandra). Ejecutar su propio sistema de base de datos es sencillo, es una réplica de lo que estamos acostumbrados a hacer en nuestros centros de datos, pero también exige tratar la administración de ese DBMS. Para facilitar las cosas, Azure proporciona tres opciones de administración de datos que se administran básicamente solas. La ilustración 2 muestra las opciones.

![Administración de datos de Azure](./media/intro-to-azure/IntroAzure3.png)   
 **Ilustración 2: Para la administración de los datos, Azure proporciona almacenamiento relacional, tablas NoSQL escalables y almacenamiento binario no estructurado.**

Cada una de estas tres opciones aborda una necesidad diferente: almacenamiento relacional, acceso rápido a posiblemente grandes cantidades de datos con tipo simple y almacenamiento binario no estructurado. En los tres casos, los datos se replican automáticamente en tres equipos diferentes en un centro de datos de Azure para proporcionar alta disponibilidad. También merece la pena señalar que es posible tener acceso a las tres opciones a través de aplicaciones de Azure o de aplicaciones que se ejecuten en cualquier otra plataforma, como el centro de datos local, un equipo portátil o el teléfono. Y, con independencia de cómo las aplique, paga por todos los servicios de administración de datos de Azure según el uso, lo que incluye un cargo de un gigabyte por mes por los datos almacenados.

### Base de datos SQL

Para el almacenamiento relacional, Azure proporciona Base de datos SQL. Antes llamada SQL Azure, Base de datos SQL ofrece todas estas características principales de un sistema de administración de base de datos relacional, lo que incluye transacciones atómicas, acceso simultáneo a los datos por parte de varios usuarios con integridad de datos, consultas SQL ANSI y un modelo de programación conocido. Al igual que SQL Server, se puede obtener acceso a Base de datos SQL mediante Entity Framework, ADO.NET, JDBC y otras conocidas tecnologías de acceso a datos. También es compatible con la mayoría del lenguaje T-SQL, junto con herramientas de SQL Server como SQL Server Management Studio. A todos aquellos que estén familiarizados con SQL Server (u otra base de datos relacional), el uso de Base de datos SQL les resultará muy sencillo.

Pero Base de datos SQL no es simplemente un DBMS en la nube, es un servicio PaaS. En este sentido, sigue teniendo el control sobre sus datos y sobre quién puede tener acceso a ellos, pero Base de datos SQL se ocupa del repetitivo trabajo administrativo, como administrar la infraestructura de hardware y mantener actualizado el software de base de datos y del sistema operativo. Base de datos SQL también proporciona una opción de federación que distribuye los datos entre varios servidores. Esto resulta de utilidad para aplicaciones que trabajan con grandes cantidades de datos o que necesitan expandir las solicitudes de acceso a datos entre varios servidores con el fin de obtener un mejor rendimiento.

Si va a crear una aplicación de Azure (usando uno de los tres modelos de ejecución) que necesita almacenamiento relacional, Base de datos SQL puede ser una buena opción. Las aplicaciones que se ejecutan fuera de la nube pueden utilizar también este servicio, aunque hay multitud de otros escenarios de uso. Por ejemplo, se puede tener acceso a los datos almacenados en Base de datos SQL desde diferentes sistemas cliente, como equipos de escritorio, equipos portátiles, tabletas y teléfonos. Y como proporciona alta disponibilidad integrada a través de la replicación, el uso de Base de datos SQL puede ayudar a reducir el tiempo de inactividad.

### Tablas

Supongamos que desea crear una aplicación de Azure que necesita acceso rápido a datos con tipo, quizás a una gran cantidad de ellos, pero no es necesario realizar consultas SQL complejas en estos datos. Por ejemplo, imagine que va a crear una aplicación de consumidor que necesita almacenar información de perfil de cliente para cada usuario. Su aplicación va a ser muy conocida, así que tiene que permitir muchos datos, pero prácticamente lo único que hará con esos datos es almacenarlos y recuperarlos de formas muy sencillas. Esta es exactamente la clase de escenario donde las tablas de Azure tienen sentido.

No se deje confundir por el nombre: esta tecnología no proporciona almacenamiento relacional. (De hecho, es un ejemplo de un enfoque NoSQL conocido como almacén de valores/claves). Por el contrario, las tablas de Azure permiten a una aplicación almacenar propiedades de diversos tipos, como cadenas, enteros y fechas. Luego, la aplicación puede recuperar un grupo de propiedades proporcionando una clave única para ese grupo. Aunque no se admiten operaciones complejas, como uniones, las tablas ofrecen acceso rápido a datos con tipo. También son muy escalables, una sola tabla puede contener hasta un terabyte de datos. Y dada su simplicidad, las tablas son habitualmente menos caras de utilizar que el almacenamiento relacional de Base de datos SQL.

### Blobs

La tercera opción para la administración de datos, los blobs de Azure, está concebida para almacenar datos binarios no estructurados. Al igual que las tablas, los blobs proporcionan almacenamiento económico, y un solo blob puede ser tan grande como un terabyte. Una aplicación que almacena vídeo, por ejemplo, o datos de copia de seguridad u otra información binaria, puede utilizar blobs como método de almacenamiento sencillo y barato. Las aplicaciones de Azure pueden utilizar también unidades de Azure, que permiten a los blobs proporcionar almacenamiento persistente para un sistema de archivos de Windows montado en una instancia de Azure. Aunque la aplicación ve archivos ordinarios de Windows, el contenido está realmente almacenado en un blob.

<h2><a id="networking" ></a>Redes</h2>


En la actualidad, Azure se ejecuta en varios centros de datos repartidos por Estados Unidos, Europa y Asia. Cuando ejecute una aplicación o almacene datos, puede seleccionar uno o varios de estos centros de datos. También puede conectar con ellos de varias formas:

* Puede utilizar Red virtual de Azure para conectar su propia red local con un conjunto definido de VM de Azure.

* Si su aplicación de Azure se va a ejecutar en varios centros de datos, puede utilizar el Administrador de tráfico de Azure para enrutar las solicitudes de los usuarios de forma inteligente entre instancias de la aplicación.

La ilustración 3 muestra estas opciones.

![Redes de Azure](./media/intro-to-azure/IntroAzure4.png)   
 **Ilustración 3: Azure permite crear una VPN en la nube y distribuir de forma inteligente las solicitudes de los usuarios entre diferentes centros de datos.**

### Red virtual

Una manera útil de usar la nube pública es considerarla una extensión de su propio centro de datos. Como puede crear VM a petición y luego eliminarlas (y dejar de pagar) cuando ya no las necesite, puede contar con el poder de la informática solo cuando lo desee. Y como Máquinas virtuales de Azure le permite crear VM que se ejecutan en SharePoint, Active Directory y otros programas de software local conocido, este enfoque puede funcionar con las aplicaciones que ya tiene.

Si bien, para que esto resulte realmente útil, sería aconsejable que sus usuarios trataran estas aplicaciones como si se ejecutaran en su propio centro de datos. Esto es exactamente lo que permite Red virtual de Azure. Mediante un dispositivo de puerta de enlace de VPN, un administrador puede configurar una red privada virtual (VPN) entre su red local y un grupo definido de VM que se ejecutan en Azure. Como asigna sus propias direcciones IP v4 a las VM en la nube, estas parecen estar en su propia red. De este modo, los usuarios de su organización pueden tener acceso a las aplicaciones que dichas VM contienen como si se estuvieran ejecutando localmente.

### Administrador de tráfico

Una aplicación de Azure con usuarios en una única parte del mundo podría ejecutarse en un solo centro de datos de Azure. Sin embargo, una aplicación con usuarios repartidos por todo el mundo es más probable que se ejecute en varios centros de datos, quizás incluso en todos ellos. En este segundo caso, se enfrenta a un problema: &iquest;Cómo dirige los usuarios a las instancias de aplicaciones de forma inteligente? La mayor parte del tiempo, deseará que cada usuario tenga acceso al centro de datos más próximo, dado que de este modo obtendrá el mejor tiempo de respuesta. &iquest;Pero qué sucede si esa instancia de la aplicación experimenta sobrecarga o no está disponible? En ese caso, sería mejor dirigir la solicitud automáticamente a otro centro de datos. Pues esto es exactamente lo que hace el Administrador de tráfico de Azure.

El propietario de la aplicación define las reglas que especifican cómo se deben dirigir las solicitudes de los usuarios a los centros de datos y, luego, confía en el Administrador de tráfico para hacer cumplir esas reglas. Por ejemplo, los usuarios se dirigirían normalmente al centro de datos de Azure más próximo, pero se les enviaría a otro cuando el tiempo de respuesta de su centro de datos predeterminado superase el tiempo de respuesta de otros centros de datos. En el caso de aplicaciones distribuidas globalmente con muchos usuarios, contar con un servicio integrado para solucionar problemas como estos resulta muy útil.

<h2><a id="analytics" ></a>Análisis de negocios</h2>


El análisis de los datos es una parte fundamental del modo en que las empresas utilizan la tecnología de la información. Una plataforma de nube proporciona un grupo de recursos a petición, de pago por uso, que constituye una buena base para este tipo de informática. En consonancia con esto, Azure proporciona dos opciones para el análisis de negocios. La ilustración 4 muestra las opciones.

![Análisis de Azure](./media/intro-to-azure/IntroAzure5.png)   
 **Ilustración 4: Para el análisis de negocios, Azure proporciona informes y compatibilidad con datos de gran tamaño.**

Los datos se pueden analizar de muchas maneras, de modo que estas dos opciones son bastante diferentes. Merece la pena que examinemos cada una de ellas por separado.

### Informes con Base de datos SQL

Una de las formas más comunes de utilizar los datos almacenados es crear informes basados en esos datos.

Ejecutar SQL Server Reporting Services (SSRS) en Máquinas virtuales de Azure le permite crear capacidades de generación de informes de fácil acceso en su aplicación de Azure. Puede crear informes con tablas, gráficos, mapas, medidores y mucho más en varios formatos, como HTML, XML, PDF y Excel.

También puede realizar análisis con datos de Base de datos SQL usando las herramientas locales de inteligencia empresarial, como SSRS. Para un cliente, Base de datos SQL se parece a SQL Server y, por lo tanto, las mismas tecnologías pueden funcionar con ambos.

### HDInsight (Hadoop)

Durante muchos años, el grueso de los análisis de datos se ha llevado a cabo en datos relacionales almacenados en un almacén de datos creado con un DBMS relacional. Este tipo de análisis de negocios es aún importante, y lo seguirá siendo durante mucho tiempo. Pero, &iquest;y si los datos que desea analizar fueran tan grandes que las bases de datos relacionales no pudieran gestionarlos? Y suponga que los datos no son relacionales. Podrían ser, por ejemplo, registros del servidor o datos de sucesos históricos de sensores o de alguna otra cosa. En este caso, tiene lo que se conoce como un problema de datos grandes. Y necesita otro enfoque.

La tecnología dominante en la actualidad para analizar los datos es Hadoop. Esta tecnología, que es un proyecto de código abierto de Apache, almacena los datos mediante el sistema de archivos distribuidos Hadoop (HDFS, Hadoop Distributed File System), y permite a los desarrolladores crear trabajos de MapReduce para analizar esos datos. HDFS distribuye los datos entre varios servidores y, a continuación, ejecuta fragmentos del trabajo de MapReduce en cada uno, de forma que los datos grandes se pueden procesar en paralelo.

HDInsight es el nombre del servicio de Azure basado en Hadoop de Apache. Como sugiere la ilustración 4, HDInsight permite a HDFS almacenar los datos en el clúster y distribuirlos entre varias VM. También extiende la lógica de un trabajo de MapReduce entre esas VM. Lo mismo que sucede con Hadoop local, los datos se procesan localmente (la lógica y los datos en los que se trabaja están en la misma VM) y en paralelo para obtener mejor rendimiento. HDInsight también puede almacenar los datos en Azure Storage Vault (ASV), que utiliza blobs. ASV le permite ahorrar dinero dado que puede eliminar su clúster de HDInsight cuando no lo utilice y seguir manteniendo los datos en la nube.

HDinsight también admite otros componentes del ecosistema Hadoop, como Hive y Pig. Microsoft ha creado también componentes que facilitan el trabajo con los datos producidos por HDInsight usando herramientas BI tradicionales, como el adaptador HiveODBC y el Explorador de datos que funcionan con Excel.

<h2><a id="messaging" ></a>Mensajería</h2>


Con independencia de lo que esté haciendo, con frecuencia el código necesita interactuar con otro código. En algunos casos, todo lo que hace falta es mensajería básica en cola. En otros, se requieren interacciones más complejas. Azure ofrece algunas formas diferentes de resolver estos problemas. La ilustración 5 muestra las opciones.

![Mensajería de Azure](./media/intro-to-azure/IntroAzure6.png)   
 **Ilustración 5: Para conectar aplicaciones, Azure proporciona colas, publicación o suscripción y conexiones sincrónicas a través de la nube.**

### Colas

Las colas son una idea sencilla: una aplicación coloca un mensaje en una cola y al final otra aplicación lee ese mensaje. Si su aplicación solo necesita este sencillo servicio, las colas de Azure podrían ser la mejor opción.

Actualmente, uno de los usos más comunes de las colas es dejar que una instancia de rol web se comunique con una instancia de rol de trabajo dentro de la misma aplicación de Servicios en la nube. Por ejemplo, supongamos que crea una aplicación de Azure para compartir vídeo. La aplicación consta de código PHP que se ejecuta en un rol web que permite a los usuarios cargar y ver vídeos, junto con un rol de trabajo implementado en C# que convierte el vídeo cargado a varios formatos. Cuando una instancia de rol web recibe un nuevo vídeo de un usuario, puede almacenarlo en un blob y enviar un mensaje a un rol de trabajo a través de una cola para indicar dónde encontrar este nuevo vídeo. A continuación, la instancia de rol de trabajo, no importa cuál, lee el mensaje de la cola y lleva a cabo las conversiones de vídeo necesarias en segundo plano. Estructurar una aplicación de esta forma permite el procesamiento asincrónico, y también que la aplicación sea más sencilla de escalar, dado que el número de instancias de rol web y de rol de trabajo se puede variar de forma independiente.

### Bus de servicio

Tanto si se ejecutan en la nube, en el centro de datos, en un dispositivo móvil o en cualquier otra parte, las aplicaciones necesitan interactuar. El objetivo del bus de servicio de Azure es permitir el intercambio de datos entre aplicaciones que se ejecutan prácticamente en cualquier parte.

Como muestra la ilustración 5, el bus de servicio proporciona un servicio de cola. Sin embargo, este servicio no es idéntico a las colas que acabamos de describir. Por ejemplo, a diferencia de las colas de Azure, el bus de servicio proporciona tanto colas (una a una) como mecanismos de publicación y suscripción. Con el mecanismo de publicación y suscripción, una aplicación puede enviar mensajes a un tema, mientras que otras aplicaciones pueden crear suscripciones a ese tema. Este permite la comunicación uno a varios entre un conjunto de aplicaciones, lo que hace posible que varios destinatarios lean el mismo mensaje. Y las colas no son la única opción: el bus de servicio también permite la comunicación directa por medio de su servicio de retransmisión, y proporciona una forma de interactuar a través de los firewalls. Las retransmisiones del bus de servicio permiten que las aplicaciones se comuniquen mediante el intercambio de mensajes a través de un punto final hospedado en la nube, en lugar de localmente.

Las aplicaciones que se comunican a través del bus de servicio pueden ser aplicaciones de Azure o software que se ejecute en alguna otra plataforma de nube. También pueden ser aplicaciones que se ejecutan fuera de la nube. Por ejemplo, piense en una línea aérea que implementa servicios de reservas en equipos dentro de su propio centro de datos. La línea aérea necesita exponer estos servicios a muchos clientes, como los terminales de facturación de los aeropuertos, los terminales del agente de reserva y, quizás, hasta los teléfonos de los clientes. Para ello, se podría utilizar un bus de servicio y crear interacciones sin conexión directa entre las diversas aplicaciones.

<h2><a id="caching" ></a>Almacenamiento en caché</h2>


Las aplicaciones tienden a obtener acceso a los mismos datos una y otra vez. Una manera de mejorar el rendimiento es mantener una copia de esos datos más cerca de la aplicación, y así reducir el tiempo necesario para recuperarlos. Azure proporciona dos servicios diferentes para este fin: almacenamiento en la caché de memoria interna de los datos que utilizan las aplicaciones de Azure y una red de entrega de contenido (CDN) que almacena en caché los datos del blob en discos que están cerca de sus usuarios. La ilustración 6 muestra ambos.

![Almacenamiento en caché de Azure](./media/intro-to-azure/IntroAzure7.png)   
 **Ilustración 6: Una aplicación de Azure puede almacenar en caché los datos en memoria, y las copias de un blob se pueden almacenar en caché en sitios de todo el mundo.**

### Almacenamiento en caché

Tener acceso a los datos almacenados en alguno de los servicios de administración de datos de Azure (Base de datos SQL, tablas o blobs) es bastante rápido. Pero tener acceso a los datos almacenados en memoria es incluso más rápido. Como consecuencia, mantener en memoria una copia de los datos a los que se tiene acceso con mayor frecuencia puede mejorar el rendimiento de la aplicación. Para tal fin, puede utilizar el almacenamiento en la caché de memoria interna de Azure.

Una aplicación de Servicios en la nube puede almacenar datos en esta caché y, luego, recuperarlos directamente sin necesidad de obtener acceso a almacenamiento persistente. Como muestra la ilustración 6, la caché se puede mantener dentro de las VM de su aplicación o la pueden proporcionar las VM dedicadas exclusivamente al almacenamiento en caché. En uno u otro caso, la caché se puede distribuir, y expandirse los datos que contiene entre varias VM de un centro de datos de Azure.

Por ejemplo, una aplicación que lee repetidamente un catálogo de productos podría beneficiarse del uso de este tipo de almacenamiento en caché, dado que los datos que necesita estarán disponibles más rápidamente. La tecnología también admite el bloqueo, de forma que se puede utilizar con datos de lectura/escritura y de solo lectura. Y las aplicaciones de ASP.NET pueden usar el servicio para almacenar datos de sesión con un simple cambio de la configuración.

### Servicio CDN

Supongamos que necesita almacenar datos de blob a los que van a tener acceso usuarios de todo el mundo. Puede ser un vídeo del último partido de la Copa del Mundo, por ejemplo, o actualizaciones de controladores o un conocido libro electrónico. Almacenar una copia de los datos en varios centros de datos de Azure puede ayudar, pero si hay muchos usuarios, es probable que no sea suficiente. Para conseguir un mejor rendimiento, puede utilizar el servicio CDN de Azure.

Este servicio CDN dispone de sitios en todo el mundo, cada uno de los cuales es capaz de almacenar copias de blobs de Azure. La primera vez que un usuario de cualquier parte del mundo tiene acceso a un blob en particular, la información que contiene se copia desde un centro de datos de Azure hasta el almacenamiento de CDN local de ese lugar geográfico. A partir de entonces, los accesos desde esa parte del mundo usarán la copia del blob almacenada en el CDN (no tendrán que recorrer todo el camino hasta llegar al centro de datos de Azure más cercano). El resultado es un acceso más rápido a los datos a los que usuarios de cualquier parte del mundo tienen acceso con mayor frecuencia.

<h2><a id="identity" ></a>Identidad</h2>


El trabajo con identidades forma parte de la mayoría de las aplicaciones. Por ejemplo, saber quién es un usuario permite que una aplicación decida cómo debe interactuar con ese usuario. Para ayudar en esta tarea, Microsoft proporciona Azure Active Directory.

Como la mayoría de los servicios de directorio, Azure Active Directory almacena información acerca de los usuarios y de las organizaciones a las que pertenecen. Permite que los usuarios inicien sesión y les proporciona tokens que pueden presentar a las aplicaciones para demostrar su identidad. También permite sincronizar la información del usuario con Windows Server Active Directory al ejecutarse en la red local. Aunque los mecanismos y los formatos de datos que emplea Azure Active Directory no son idénticos a los usados en Windows Server Active Directory, las funciones que realiza son bastante parecidas.

Es importante comprender que Azure Active Directory está diseñado principalmente para su uso en aplicaciones en la nube. Así por ejemplo, se puede utilizar en aplicaciones que ejecutan Azure o en otras plataformas de nube. También se utiliza en las propias aplicaciones en la nube de Microsoft, como las de Office 365. Sin embargo, si desea extender su centro de datos a la nube mediante Máquinas virtuales de Azure y Red virtual de Azure, Azure Active Directory no es la opción adecuada. En su lugar, deberá ejecutar Windows Server Active Directory en las VM de nube, como se ha descrito anteriormente.

Para que las aplicaciones puedan tener acceso a la información que contiene, Azure Active Directory proporciona una API de RESTful conocida como Azure Active Directory Graph. Esta API permite que las aplicaciones que se ejecutan en cualquier plataforma tengan acceso a los objetos de directorio y a las relaciones entre ellos. Por ejemplo, una aplicación autorizada podría utilizar esta API para obtener información acerca de un usuario, los grupos a los que pertenece, etc. Las aplicaciones también pueden ver las relaciones entre los usuarios (su gráfico social), lo que les permite trabajar de una forma más inteligente con las conexiones entre la gente.

Otra funcionalidad de este servicio, el Servicio de control de acceso de Azure AD , permite que una aplicación pueda aceptar fácilmente información de identidad de Facebook, Google, Windows Live ID y otros proveedores de identidad conocidos. No es necesario que la aplicación comprenda los diversos formatos de datos y protocolos que utiliza cada uno de estos proveedores, el Control de acceso convierte todos ellos en un único formato común. También permite que una aplicación acepte inicios de sesión de uno o varios dominios de Active Directory. Por ejemplo, un proveedor que proporciona una aplicación SaaS podría utilizar el Servicio de control de acceso de Azure AD para dar a los usuarios de cada uno de sus clientes un inicio de sesión único en la aplicación.

Los servicios de directorio son uno de los pilares de la informática local, por lo que no debe sorprendernos de que sean también importantes en la nube.

<h2><a id="media" ></a>Multimedia</h2>


Gran parte del tráfico de Internet actual lo constituye el vídeo, y ese porcentaje será incluso mayor en el futuro. Pero proporcionar vídeo en la Web no es tan sencillo. Depende de muchas variables, como el algoritmo de codificación y la resolución de pantalla del monitor del usuario. El vídeo también suele experimentar una demanda a ráfagas, como los picos del sábado por la noche cuando cientos de personas desean ver una película en Internet.

Dada su popularidad, es seguro que se van crear muchas aplicaciones que utilicen vídeo. Pero todas ellas necesitarán resolver algunos de los mismos problemas, y hacer que cada una resuelva esos problemas por su cuenta no tiene sentido. Un enfoque mejor es crear una plataforma que proporcione soluciones comunes para su uso por muchas aplicaciones. Y crear esta plataforma en la nube tienen algunas ventajas obvias. Puede estar ampliamente disponible según un criterio de pago por uso, y también puede gestionar la demanda variable a la que con frecuencia se enfrentan las aplicaciones de vídeo.

Servicios multimedia de Azure soluciona este problema al proporcionar un conjunto de componentes de nube que permiten que la gente pueda crear y ejecutar fácilmente aplicaciones que utilizan vídeo y otro contenido multimedia. La ilustración 7 muestra la tecnología.

![Servicios multimedia de Azure](./media/intro-to-azure/IntroAzure8.png)

 **Ilustración 7: Servicios multimedia es una plataforma para aplicaciones que proporcionan vídeo y otro contenido multimedia a clientes de todo el mundo.**

Como muestra la ilustración, Servicios multimedia ofrece un conjunto de componentes para aplicaciones que trabajan con vídeo y otro contenido multimedia. Por ejemplo, incluye un componente de ingestión multimedia para cargar vídeo en Servicios multimedia (donde se almacena en blobs de Azure), un componente de codificación que es compatible con diversos formatos de audio y vídeo, un componente de protección de contenido que permite la administración de los derechos digitales, un componente para insertar anuncios en una secuencia de vídeo, componentes para la transmisión por secuencias y mucho más. Los socios de Microsoft también proporcionan componentes para la plataforma y luego piden a Microsoft que los distribuya y facture en su nombre.

Las aplicaciones que utilizan esta plataforma se pueden ejecutar en Azure o en cualquier otra plataforma. Por ejemplo, una aplicación de escritorio para una productora de vídeo podría permitir que sus usuarios descargaran vídeo de Servicios multimedia para luego procesarlo de varias formas. De manera alternativa, un servicio de administración de contenido basado en la nube que se ejecuta en Azure podría confiar en Servicios multimedia para procesar y distribuir vídeo. Con independencia de dónde se ejecute y para qué sirva, cada aplicación elige los componentes que necesita utilizar, y obtiene acceso a ellos a través de interfaces de RESTful.

Para distribuir lo que produce, una aplicación puede utilizar CDN de Azure, otro CDN, o simplemente enviar bits directamente a los usuarios. Sea cual sea la forma en que llegue allí, el vídeo creado usando Servicios multimedia se puede consumir a través de varios sistemas cliente, como Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash y Silverlight. El objetivo es facilitar la creación de aplicaciones multimedia modernas.

Para obtener una imagen de cómo funciona Servicios multimedia, descargue [Azure Media Services Poster (en inglés)][2].

<h2><a id="HPC" ></a>Informática de alto rendimiento</h2>


Una de las formas más atractivas de utilizar una plataforma de nube es para la informática de alto rendimiento (HPC). La esencia de HPC es la ejecución de código en muchas máquinas al mismo tiempo. En Azure, esto significa ejecutar muchas máquinas virtuales a la vez, trabajando todas en paralelo para resolver algún problema. Para hacer esto, es necesario programar de algún modo las aplicaciones, es decir, distribuir su trabajo entre estas instancias. Para este propósito, Azure proporciona HPC Scheduler.

Este componente puede funcionar con aplicaciones HPC creadas para utilizar la interfaz de paso de mensajes (MPI) estándar de la industria. El software que realiza análisis de elementos limitados, como las simulaciones de accidentes de coches, es un ejemplo de este tipo de aplicación, pero hay muchos otros. HPC Scheduler se puede utilizar también con las tan inadecuadamente llamadas aplicaciones en paralelo, como las simulaciones de Monte Carlo. Cualquiera que sea el problema abordado, el valor que proporciona es el mismo: HPC Scheduler trata el complejo problema de programar en paralelo el trabajo informático entre muchas máquinas virtuales de Azure. El objetivo: facilitar la creación de aplicaciones HPC que se ejecutan en la nube.

<h2><a id="commerce" ></a>Comercio</h2>


El surgimiento del software como servicio está transformando el modo en que creamos las aplicaciones. Y también está transformando el modo en que las vendemos. Dado que una aplicación SaaS reside en la nube, tiene sentido que sus clientes potenciales deban buscar soluciones en línea. Y este cambio se aplica tanto a los datos como a las aplicaciones. &iquest;Por qué no buscar en la nube conjuntos de datos
comercialmente disponibles? Microsoft soluciona ambas cuestiones con [Azure Marketplace][3] y la [Tienda Azure](/en-us/store/overview/), que se muestran en la ilustración 8.

![Comercio de Azure](./media/intro-to-azure/IntroAzure9.png)   
 **Ilustración 8: Azure Marketplace y la Tienda Azure le permiten encontrar y comprar aplicaciones y conjuntos de datos comerciales de Azure.**

La diferencia entre los dos es que Marketplace se encuentra fuera del Portal de administración de Azure, mientras que a la Tienda se puede tener acceso desde el portal. Los posibles clientes pueden buscar en uno u otro para encontrar aplicaciones de Azure que satisfagan sus necesidades, y luego registrarse para utilizarlas a través del creador de la aplicación o directamente a través de Marketplace o de la Tienda. Los clientes también pueden buscar en cualquiera de ellos conjuntos de datos comerciales, como datos demográficos, datos financieros, datos geográficos y muchos más. Y, si encuentran algo que les guste, pueden tener acceso a ello desde el proveedor, directamente a través de las ubicaciones web de Marketplace o de la Tienda o, en algunos casos, desde el Portal de administración. Las aplicaciones pueden utilizar también la API de Búsqueda de Bing a través de Marketplace, que les proporciona acceso a los resultados de las búsquedas web.

<h2><a id="sdk" ></a>SDK</h2>


En 2008, la primera versión preliminar de Azure solo admitía el desarrollo con .NET. En la actualidad, sin embargo, puede crear aplicaciones de Azure prácticamente con cualquier lenguaje. Microsoft proporciona actualmente SDK específicos del lenguaje para .NET, Java, PHP, Node.js, Ruby y Python. También hay un SDK general de Azure que proporciona compatibilidad básica con cualquier lenguaje, como C++.

Estos SDK le ayudan a crear, implementar y administrar aplicaciones de Azure. Se pueden encontrar en www.windowsazure.com o GitHub, y se pueden utilizar con Visual Studio y Eclipse. Azure ofrece también herramientas de línea de comandos que los desarrolladores pueden utilizar con cualquier editor o entorno de desarrollo, como herramientas para implementar aplicaciones en Azure desde sistemas Linux y Macintosh.

Además de ayudarle a crear aplicaciones de Azure, estos SDK también proporcionan bibliotecas de cliente que le ayudan a crear software que se ejecuta fuera de la nube y que utiliza servicios de Azure. Por ejemplo, puede crear una aplicación que se ejecute en un anfitrión que se base en blobs de Azure, o crear una herramienta que implemente aplicaciones de Azure a través de la interfaz de administración de Azure.

<h2><a id="start" ></a>Tareas iniciales</h2>


Ahora que ha captado la idea general, el siguiente paso es programar su primera aplicación de Azure. Elija su lenguaje, [obtenga el SDK adecuado](/en-us/downloads/) y ¡a por ello!. La informática en nube es el nuevo estándar, comience ya.



[1]: http://www.microsoft.com/en-us/download/details.aspx?id=35473
[2]: http://www.microsoft.com/en-us/download/details.aspx?id=38195
[3]: http://datamarket.azure.com/
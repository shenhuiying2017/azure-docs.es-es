
# Solución de problemas en Azure

El término *Solución de problemas* se refiere a la tarea general basada en detectar y comprender el comportamiento inesperado de las aplicaciones. Los desarrolladores prueban, ejecutan y depuran las aplicaciones compiladas antes de implementarlas en un entorno de producción. Estas tareas se realizan tanto si la aplicación se ejecuta en un PC de escritorio como en un servidor en la nube. No obstante, puede resultar complicado depurar una aplicación de instancias múltiples ampliamente distribuida, ya que ello requiere más que enfoques y herramientas estándar.

Por este motivo, cabe tener en cuenta la solución de problemas al diseñar las aplicaciones en la nube. La forma de diseñar el soporte para la solución de problemas en la aplicación depende, en primer lugar, de dónde se ejecuta la aplicación y, en segundo lugar, de los idiomas o los tiempos de ejecución con que se compila o se usa.
	
Si va a compilar una aplicación para ejecutarla en una Máquina virtual de Azure, en muchos casos, puede enfocar el diseño y la depuración de la solución de problemas como lo haría en el sistema operativo si este se ejecutara en servidores propios.

Las aplicaciones que se ejecutan en Azure son aplicaciones de instancias múltiples ampliamente distribuidas cuya depuración puede resultar complicada. Estos tipos de aplicaciones requieren más que enfoques y herramientas estándar para la solución de problemas. En este tema se examinan algunas prácticas probadas sobre la solución de problemas, con vínculos a información más extensa al respecto.

**Nota**: en este tema, se supone que va a diseñar una aplicación, o
bien que ha implementado correctamente un aplicación de Azure y que ahora se ha producido un comportamiento inesperado. No obstante, no se aborda la forma de implementar una aplicación en Azure. Para obtener más información acerca del desarrollo y la implementación de una aplicación de Azure, consulte
[https://www.windowsazure.com/es-es/develop/overview/][1].

En este tema primero se describen algunos procedimientos recomendados diseñados para ayudarle a diseñar una aplicación, de tal manera que pueda solucionar con eficacia los problemas que surjan. (Si no diseña una aplicación que le permita seguir el flujo de códigos de antemano, puede resultar muy complicado detectar los problemas cuando se produzcan). Estos procedimientos recomendados son válidos para todos los tipos de aplicaciones ejecutadas en Azure, con independencia del lenguaje o el modelo de aplicación que se use.

En las secciones siguientes se describen enfoques específicos para desarrollar todo tipo de aplicaciones compatibles de Azure: Sitios web, Servicios en la nube o Máquinas virtuales.

En cada sección se describen procedimientos recomendados de alto nivel, con vínculos a recursos que demuestran tales procedimientos de manera pormenorizada o que describen cómo implementarlos.

## En este documento

* [Procedimientos recomendados para solucionar problemas en
  Azure](#BestPractices)
* [Sitios web Azure](#Websites)
* [Servicios en la nube de Azure](#CloudServices)
* [Máquinas virtuales de Azure](#Vms)
* [Servicios de Azure](#PlatformServices)
* [Solución de problemas de Base de datos SQL](#SQLTroubleshooting)

<h2><a id="BestPractices" ></a>Procedimientos recomendados para solucionar problemas en Azure</h2>


En la presente sección se describen los procedimientos recomendados para aplicaciones de Azure, con independencia del modelo de hospedaje o del lenguaje que use. Asimismo, contiene recursos que ofrecen información más extensa acerca de tales procedimientos.

Para sentar las bases de una solución de problemas eficiente en Azure, concentre la atención en estas tres cuestiones principales:

* Identificación correcta de los errores: *cada servicio de componente
  debe tener la capacidad de soportar los errores de la infraestructura
  o los servicios dependientes*.
* Seguimiento, registro y supervisión: *cada servicio componente debe
  contar con la depuración, el seguimiento y el registro de eventos y
  errores apropiados.*
* Depuración de errores siempre que sea posible: *primero hay que
  impulsar la producción, pero también a nivel de componente y red
  durante la ejecución.*

Diseñar una aplicación sin olvidar estas ideas permite que la aplicación le facilite la información necesaria para localizar un comportamiento inesperado cuando este ocurra.

### Diseño concebido para identificar errores correctamente

Siempre que puedan, las aplicaciones deben identificar correctamente las condiciones de error. Esto adquiere más importancia si cabe por la naturaleza distribuida de Azure. Una solución de problemas eficaz comienza con un buen diseño concebido para identificar errores transitorios. Los errores transitorios constituyen una de las principales áreas en que las aplicaciones en la nube dejan de comportarse según lo previsto debido a unas condiciones de error transitorio inherentes a las aplicaciones de Internet.

Los errores transitorios están relacionados con las latencias y las conexiones de red intermitentes inherentes a los recursos compartidos en Internet. A continuación, se indican algunos ejemplos:

* Los recursos informáticos compartidos, como las plataformas Servicios
  en la nube y Base de datos SQL de Azure (por citar dos ejemplos), a
  veces pueden ser algo más dinámicos y otras veces, algo menos.
* La capacidad de respuesta se retrasa debido a la durabilidad de los
  servicios. Por ejemplo, SQL Azure mantiene la coherencia entre varias
  copias de las bases de datos, a fin de ofrecer durabilidad, y esto
  repercute en la capacidad de respuesta.
* Los retrasos se producen porque las conexiones del protocolo HTTP o de
  otros protocolos terminan antes de completar el trabajo. Por ejemplo,
  las solicitudes HTTP pueden no alcanzar un extremo y volver antes del
  periodo de tiempo de espera.

Para ayudar a aliviar el impacto de los errores transitorios, las aplicaciones de Azure deben:

* Estar acopladas sin conexión directa, de forma que los componentes no
  dependan a nivel local de los servicios que experimentan errores con
  mayor frecuencia que en los entornos locales.
* Realizar llamadas asincrónicas siempre que sea posible para evitar que
  los procesos lleguen a depender de respuestas inmediatas.
* Usar un enfoque de identificación de errores transitorios que detecte
  determinadas categorías de errores y pueda implementar un
  comportamiento de reintento para tales errores, en virtud de una
  directiva de reintento con cierta capacidad de configuración.

Las llamadas a los servicios deben compilar o usar una capa de identificación de errores transitorios para detectar escenarios de errores comunes y reintentar la llamada en función de una opción de configuración. Para los desarrolladores de .NET, una biblioteca recomendada es el [paquete de integración de Microsoft Enterprise Library 5.0 para Azure (en inglés)][2]. Microsoft Enterprise Library es una colección de bloques de aplicaciones reutilizables que aborda los problemas transversales comunes con el desarrollo de software empresarial. El paquete de integración de Microsoft Enterprise Library para Azure es una extensión de Microsoft Enterprise Library 5.0 que se puede usar con Azure. Comprende el Bloque de aplicación de escalado automático, el Bloque de aplicación de identificación de errores transitorios, el origen de configuración de blob, el proveedor de configuración protegida y materiales de formación. Otra biblioteca de .NET más sencilla con menos características es [Cloud Application Framework & Extensions (CloudFx)][3]. CloudFx ofrece una serie de componentes de calidad de producción y bloques de compilación diseñados para impulsar la implementación de soluciones y servicios extensibles, confiables y con varias características basados en Azure.

### Realización de un seguimiento y un registro apropiados

Habida cuenta de la complejidad de las aplicaciones distribuidas y de escala, los depuradores tradicionales que funcionan contra el proceso resultan muchos menos útiles para investigar problemas que se producen mientras la aplicación se encuentra en ejecución. Por tanto, el seguimiento y el registro son de suma importancia. La ejecución de la aplicación y de sus datos se comparte entre muchos servicios, que se hospedan en muchas máquinas diversas. En una aplicación distribuida a gran escala, resulta complicado, o incluso imposible, determinar a qué instancia de servicio conectarse y cuál de ellas depurar. El seguimiento y el registro le permiten realizar un seguimiento de la ejecución de la aplicación y del flujo de datos, lo que le permite conocer mejor el estado de la aplicación.

Las aplicaciones de Azure diseñadas correctamente integran una estrategia de registro y seguimiento desde el principio. Esto reduce el tiempo y el esfuerzo necesarios para localizar cualquier problema y repararlo rápidamente sin tener que recurrir al soporte de Microsoft.

**Nota**: trazar el seguimiento y hacer registros de manera amplia repercute en el rendimiento; además, hacerlo de manera intensiva tiene mayor impacto aún. Por tanto, el diseño de la aplicación debe incluir una directiva de seguimiento y registro configurable que se pueda ajustar según las necesidades. Lo ideal es que el nivel de registro se pueda ajustar en el archivo **ServiceConfiguration.cscfg**, para que se pueda cambiar sin tener que volver a realizar la implementación.

Contar con volúmenes de registros no garantiza una detección y reparación rápidas de los errores; se tarda mucho en descifrar un gran volumen de datos, y recopilarlos incide en el rendimiento de la aplicación. Los registros ajustables controlan el coste por el almacenamiento y el tamaño de los datos de registro.

En el artículo de MSDN Magazine [Tome el control de registro y seguimiento en Azure][4], Mike Kelly describe cuatro tipos de resultados de diagnóstico que cabe considerar:

* Resultado de depuración: solo aparece en la creación de depuración e
  incluye aserciones
* Seguimiento: rastrea el flujo de control durante la ejecución
* Registro de eventos: eventos importantes en el curso de la ejecución
* Registro de errores: situación excepcional o peligrosa

Otros lenguajes, plataformas de aplicación y sistemas operativos pueden tener una terminología diferente para el seguimiento y el registro. Si usa alguna de estas plataformas de desarrollo en Azure, use las herramientas y la estrategia equivalentes para el lenguaje o la plataforma que utiliza.

Las aplicaciones de modo mixto son aquellas que se ejecutan en una combinación de Máquinas virtuales, Sitios web y Servicios en la nube de Azure. Al compilar aplicaciones de este tipo, el seguimiento y el registro adquieren aún más importancia porque están distribuidas a nivel más amplio. Para solucionar los problemas de estas aplicaciones de modo mixto, cabe hacer un seguimiento de la ejecución y los datos generales a fin de identificar los problemas. La mecánica del seguimiento y el registro de una aplicación de modo mixto depende del modelo de hospedaje  componente.

### Supervisión de una aplicación

El seguimiento y el registro se engloban en el área más amplia de la supervisión. La supervisión le permite:

* Detectar aplicaciones de Azure
* Determinar el estado de cada instancia de rol
* Recopilar y supervisar información acerca del rendimiento, incluidas
  la latencia y la capacidad de proceso
* Recopilar y supervisar eventos
* Recopilar y supervisar mensajes de seguimiento
* Supervisar el uso de recursos
* Supervisar la calidad de las medidas de servicio
* Realizar una planificación de capacidad
* Realizar un análisis del tráfico (usuarios, vistas y horas pico)
* Estimar la facturación
* Realizar auditorías

La supervisión se realiza con una herramienta o un conjunto de herramientas. La herramienta utilizada depende de la plataforma o los lenguajes que la aplicación usa y de los objetivos y los requisitos de la supervisión.

**Módulo de supervisión de Microsoft System Center para aplicaciones de Azure**

Este [módulo de supervisión (en inglés)][5] le permite usar [Microsoft System Center Operations Manager][6] para supervisar la disponibilidad y el rendimiento de las aplicaciones de Azure.

Usar Microsoft System Center Operations Manager 2007 es la mejor forma de supervisar el estado de una aplicación de Azure.

**Herramienta de administración de la Plataforma de Azure**

Otra herramienta es [Azure Platform Management Tool (MMC)][7], que le permite administrar las cuentas de almacenamiento y los servicios hospedados de Azure. Esta herramienta se proporciona como una muestra con código fuente completo, para que pueda ver cómo ejecutar varias tareas de configuración y administración mediante las API de diagnóstico y administración de Azure.

**Herramientas Cerebrata**

Cerebrata ofrece una serie de herramientas que le permiten supervisar y administrar las aplicaciones de Azure. Estas herramientas comprenden [Azure Diagnostics Manager][8], [Cloud Storage Studio][9] y [Azure Management Cmdlets][10].

Azure Diagnostics Manager 2 es un cliente basado en Windows (WPF) para administrar Diagnósticos de Azure. Le permite ver, descargar y administrar los datos de diagnóstico recopilados por las aplicaciones que se ejecutan en Azure. Consulte [http://www.cerebrata.com][11] para obtener más información acerca de estos productos.

Cloud Storage Studio 2 es un cliente basado en Windows (WPF) pensado para administrar el servicio Almacenamiento de Azure.

Azure Management Cmdlets es un conjunto de cmdlets de Windows PowerShell diseñado para administrar el almacenamiento, los servicios hospedados, las instancias de la Base de datos SQL y el diagnóstico de Azure. También ofrece cmdlets para hacer copias de seguridad de las cuentas de almacenamiento y restaurarlas.

**Supervisión de red: AlertBot, Gomez, Keynote y Pingdom**

La plataforma Application Performance Management de Compuware [Gomez][12], [Keynote][13], [Pingdom][14] y [AlertBot][15] son soluciones para supervisar la aplicación de Azure de forma remota. Le permiten supervisar la disponibilidad de la aplicación y optimizar el rendimiento. Algunos servicios, como Pingdom, habilitan la notificación por correo electrónico, SMS o una aplicación de escritorio cuando se detecta un error. Este tipo de supervisión simula lo que un usuario final hace para supervisar correctamente una aplicación.

**Herramientas Apica**

Apica proporciona herramientas que supervisan la aplicación web de Azure "desde fuera". Para obtener más información, consulte [http://www.apicasystem.com/integration-partners/][16].

**AVIcode**

Microsoft adquirió AVIcode, y esta solución ahora forma parte de Microsoft System Center. [AVIcode][17] ofrece capacidades de supervisión del rendimiento de aplicaciones .NET con un conjunto completo de capacidades de supervisión de aplicaciones.

**Generación de perfiles de rendimiento**

Puede [generar perfiles][18] de la aplicación de Azure cuando esta se ejecuta en Azure para determinar todos los problemas de rendimiento. Si publica la aplicación de Azure desde Visual Studio, puede elegir generar perfiles de la aplicación y seleccionar la configuración de la generación de perfiles que necesite.

**VM Assistant de Azure**

La herramienta [VM Assistant][19] es un proyecto de CodePlex que recopila en un lugar los datos pertinentes sobre la solución de problemas con el Escritorio remoto en una instancia de máquina virtual. El botón **VM Health** proporciona el estado actual de la instancia.

### Depuración de errores siempre que sea posible

Antes de implementar una aplicación en Azure, este es un procedimiento recomendado para depurar la aplicación a nivel local. El SDK de Azure contiene emuladores que imitan el entorno de la nube de Azure, lo que le permite ejecutar la aplicación y realizar pruebas rudimentarias sin tener que implementar la aplicación. Las herramientas de depuración que usa varían en función del lenguaje de programación y las herramientas de desarrollo utilizados.

Después de haber implementado una aplicación, puede depurarla en la nube con un depurador como Visual Studio. Esto requiere crear una compilación de depuración e implementarla. Para ello, debe conectarse a una instancia de rol específica. Si tiene una aplicación compleja con varios roles e instancias de roles, puede resultar muy difícil determinar a qué instancia de rol conectarse. Visual Studio Ultimate admite IntelliTrace, que permite que los roles de .NET realicen un seguimiento de la información de depuración. Cuando surge un problema, puede descargar esta información y cargarla en Visual Studio. Puede consultar cada registro de IntelliTrace de la instancia de rol para determinar dónde ha ocurrido el problema. Si bien la depuración en la nube presenta algunos inconvenientes, hay algunas circunstancias en que resulta necesaria. No todos los servicios Azure tienen un emulador (por ejemplo, el Bus de servicio), y no todas las herramientas de desarrollo compatibles (por ejemplo, Mac y Linux) integran emuladores.

Después de haber depurado la aplicación a nivel local, es muy probable que tenga que basarse en la instrumentación integrada en la aplicación para determinar dónde hay problemas.

**Depuración de Node.js**

Para depurar las aplicaciones Node.JS, puede usar la herramienta Node-Inspector que se encuentra disponible en [GitHub][20]. Node-Inspector puede ejecutarse a nivel local en la máquina de desarrollo con el emulador de almacenamiento de Azure. Para obtener más información, consulte el blog de Jim Wang: [Debugging Node in the Azure Emulator][21].

Si va a depurar la aplicación en Azure, instale la versión completa de IISNode desde [GitHub][22] en el rol web, el rol de trabajo o la instancia de VM. Como se ha comentado anteriormente, no es recomendable depurar la aplicación si esta se encuentra en fase de producción ni escalarla a múltiples instancias, ya que puede no saber qué instancia de rol o VM depurar.

Para usar Node-Inspector en un rol web, instale el paquete en el rol web y use la herramienta Node-Inspector como de costumbre. Para obtener más información acerca de cómo depurar con Node-Inspector, consulte [Debugging with Node-Inspector][23].

**IntelliTrace**

Microsoft Visual Studio Ultimate contiene [IntelliTrace][24], que puede habilitarse para depurar aplicaciones antes de implementarlas en producción. IntelliTrace admite ASP.NET y las aplicaciones WCF. Intellitrace no es compatible cuando está habilitado en un servicio de producción, pero se puede usar para obtener excepciones para una aplicación tras implementarla en Azure. En la entrada de blog de Jim Nakashima se describe cómo usar IntelliTrace para depurar los Servicios en la nube de Azure (en inglés).

**Fiddler**

[Fiddler][25] es un proxy de depuración web que registra todo el tráfico HTTP(S) entre el PC e Internet. Fiddler le permite inspeccionar el tráfico, establecer puntos de interrupción y depurar con Fiddler los datos de entrada y salida. Fiddler resulta particularmente útil para solucionar problemas con el servicio de Almacenamiento de Azure.

Para usar Fiddler en el entramado de desarrollo local, use ipv4.fiddler en lugar de 127.0.0.1:

* Inicie Fiddler.
* Inicie el servicio en el entramado de desarrollo.
* Vaya a http://ipv4.fiddler:/. Fiddler debe realizar el seguimiento de
  la solicitud.

Para usar Fiddler en el almacenamiento de desarrollo local, modifique el archivo de configuración de servicio para que apunte a Fiddler:

Abra el archivo ServiceConfiguration.cscfg y cambie la cadena de conexión a:

    Value="UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://ipv4.fiddler"

* Inicie Fiddler.
* Inicie el servicio. Fiddler debe realizar un seguimiento de las
  solicitudes de almacenamiento.

## Solución de problemas y modelos de hospedaje de Azure

En esta sección se describen los procedimientos recomendados para depurar aplicaciones con los diferentes modelos de hospedaje de Azure.

<h2><a id="Websites" ></a>Sitios web Azure</h2>


Para diseñar un Sitio web Azure compatible, siempre que sea posible, siga las recomendaciones proporcionadas anteriormente en este documento. Tales recomendaciones comprenden comprobar e identificar errores, aplicar la lógica de reintento de errores transitorios, realizar un seguimiento y ejecutar registros. Para solucionar problemas en los Sitios web Azure, es necesario configurar dichos sitios para que muestren errores de las aplicaciones, configurar el diagnóstico para un sitio web, recopilar datos de diagnóstico y, a continuación, analizar dichos datos para identificar y resolver los problemas.

Los Sitios web Azure permiten configurar las siguientes opciones de diagnóstico:

* Registro del servidor web
* Mensajes de error detallados
* Seguimiento de solicitudes con error

Para obtener más información acerca de estos temas, consulte: [Solución de problemas de un Sitio web Azure](/en-us/develop/net/best-practices/troubleshooting-web-sites/).

Si los registros de servidor web para los Sitios web Azure están habilitados, el sitio web registrará todas las transacciones HTTP en un archivo de registro con el [formato de archivo de registro extendido W3C
(en inglés)][26]. A continuación, puede usar el [analizador del registro
(en inglés)][27] para consultar el archivo de registro. En [Log Parser
Plus][28] y [Log Parser Examples en TechNet][29] puede encontrar ejemplos de consultas del analizador del registro. Si desea generar el tipo de resultado CHART en un equipo en que se ejecuta Office 2007/2010, instale [Office 2003 Web Components][30]; para ello, siga las instrucciones descritas en [Log Parser Plus][31].

Sitios web Azure usa la misma funcionalidad de seguimiento de solicitudes con error que ha estado disponible con IIS 7.0 y posterior. No obstante, no es posible configurar esta funcionalidad como el caso del seguimiento de solicitudes con error de IIS. Si habilita el seguimiento de solicitudes con error para los Sitios web Azure, se capturan **todas** las solicitudes con error.

<h2><a id="Cloudservices" ></a>Servicios en la nube de Azure</h2>


Habida cuenta de la naturaleza distribuida de la plataforma Servicios en la nube de Azure, es importante proteger la aplicación con la realización de llamadas asincrónicas y controlar los reintentos de errores transitorios, según se ha descrito anteriormente.

La técnica de depuración usada para Servicios en la nube de Azure depende del tipo de problema de que se trata. La mejor forma de investigar los problemas que impliquen un rol o una instancia de rol específicos, como por ejemplo un rol que presenta errores de inicio o en los ciclos, consiste en usar Escritorio remoto. En estos casos, sabrá qué rol o instancia de rol son problemáticos y, por tanto, puede conectarse al rol afectado. En cambio, si surge un problema y no está seguro de la instancia de rol que lo causa, el seguimiento y el registro son los métodos más adecuados para solucionar el problema. Diagnósticos de Azure ofrece un mecanismo para recopilar y administrar la información de seguimiento y registro.

Se han agregado algunas características nuevas de depuración a Azure SDK 1.7, entre otras, facilitar la búsqueda de seguimientos de la pila cuando ocurren excepciones y mejoras en la conectividad de Escritorio remoto. Los seguimientos de la pila ahora se incluyen en el registro de eventos de Windows, por lo que resulta más fácil saber qué ha ocurrido exactamente. Asimismo, se ha mejorado la conectividad de Escritorio remoto. Si el rol se anula o este se encuentra en un recorrido cíclico, podrá usar Escritorio remoto para conectarse al rol que plantea el problema a fin de investigarlo.

El Portal de Azure ofrece acceso a datos de supervisión que ayudan a los desarrolladores y profesionales informáticos a anticiparse a los problemas y a diagnosticarlos en la plataforma Servicios en la nube de Azure. De forma predeterminada, la VM host recopila valores como
"Porcentaje de CPU", "Data In", "Data Out", "Disk Read Throughput" y "Disk Write Throughput". No es necesario realizar ninguna configuración para habilitar estas métricas para las instancias de rol, y esto tampoco incide en el coste en que los clientes incurren. También se puede recopilar información adicional acerca del rendimiento. Para recopilar información de diagnóstico detallada, debe disponer de una cadena de conexión de diagnóstico válida, ya que esta información se almacenará en la plataforma Almacenamiento de Azure, por lo que se incurrirá en costes de almacenamiento adicionales. Si el usuario habilita la supervisión detallada, el portal configurará de forma remota las instancias de rol para que recopilen el conjunto predeterminado de contadores de rendimiento.

### Diagnóstico de Azure

La versión original de Azure SDK 1.0 incluía la funcionalidad basada en recopilar datos de diagnóstico y almacenarlos en el servicio de Almacenamiento de Azure, unas tareas que de manera conjunta constituyen lo que se conoce como Diagnósticos de Azure. Este software, basado en el marco de Seguimiento de eventos para Windows (ETW), satisface dos requisitos de diseño integrados en la arquitectura de escala de Azure:

* Guardar los datos de diagnóstico que podrían perderse durante el
  restablecimiento de una imagen de la instancia.
* Proporcionar un repositorio central para los diagnósticos de varias
  instancias.

Después de configurar Diagnósticos en el rol, este software recopila los datos de diagnóstico de todas las instancias de dicho rol concreto. Estos datos se pueden usar para depurar y solucionar problemas, medir el rendimiento, supervisar el uso de los recursos, el análisis del tráfico y la planificación de capacidad, y realizar auditorías. Las transferencias a la cuenta de almacenamiento de Azure a efectos de persistencia pueden programarse, o bien pueden realizarse a petición.

Diagnósticos de Azure cambia el paradigma del servidor en cuatro aspectos importantes:

* El software Diagnósticos debe habilitarse en el momento en que se crea
  la aplicación.
* Se necesitan herramientas/pasos específicos para visualizar los
  resultados de diagnóstico.
* Los bloqueos ocasionarán la pérdida de los datos de diagnóstico, a
  menos que se escriban en un almacenamiento duradero (Almacenamiento de
  Azure frente al almacenamiento en cada instancia).
* El almacenamiento de diagnósticos incurre en un coste mensual si estos
  se almacenan en la plataforma Almacenamiento de Azure.

El coste reviste particular importancia porque uno de los principales beneficios de Azure es la reducción de costes. La única forma de eliminar el coste que genera usar diagnósticos hoy en día consiste en dejar los datos en la máquina virtual. Esto puede realizarse para una implementación pequeña, pero no resulta práctico cuando hay muchas instancias. A continuación, presentamos algunas formas de minimizar el impacto financiero:

* Asegúrese de que la cuenta de almacenamiento se encuentra en el mismo
  centro de datos que la aplicación. Si, por algún motivo, no están en
  el mismo centro de datos, actúe con prudencia y elija el intervalo de
  transferencias programadas. Unos tiempos de transferencia más cortos
  aumentarán la relevancia de los datos, pero dicho equilibrio puede no
  ser lo suficientemente amplio para justificar el ancho de banda
  adicional y la sobrecarga de procesamiento.
* Copie y borre periódicamente los datos de diagnóstico de la plataforma
  Almacenamiento de Azure. Los datos de diagnóstico pasarán por la
  plataforma Almacenamiento de Azure, pero no residirán ahí
  innecesariamente. Para esta tarea, tiene a su disposición una serie de
  herramientas: Módulo de supervisión de Microsoft System Center para
  Azure, Azure Diagnostics Manager de Cerebrata y los cmdlets de
  PowerShell para Azure.
* Elija solo los datos de diagnóstico que necesitará para solucionar
  problemas y supervisar la aplicación. Capturar demasiados datos puede
  dificultar aún más la solución de problemas, aparte de incrementar los
  costes significativamente.
* Controle la recopilación y la extensión de los datos de diagnóstico;
  para ello, implemente un cambio a petición en la aplicación.
* Use el nivel de registro (detallado, información, advertencia y error)
  para que toda la información esté disponible; a continuación, use la
  configuración de Diagnósticos posterior a la implementación, a fin de
  recopilar los datos de manera selectiva.

<h2><a id="Vms" ></a>Máquinas virtuales de Azure</h2>


Para solucionar los problemas de las aplicaciones que se ejecutan en la plataforma Máquinas virtuales de Azure, se usan las mismas técnicas que usaría con los sistemas operativos y las plataformas que ya usa. Por ejemplo:

* Windows Server 2008 R2 x64 (RTM y SP1) y Windows 8 Service x64
  (Datacenter y Core). Hay disponible un extenso volumen de información
  en este sentido, además de muchas herramientas para ayudarle a
  realizar el trabajo con rapidez.

* Para obtener más información acerca de estos enfoques, consulte [Cómo:
  Depurar aplicaciones de servicios de Windows][32].

* Para ver un vídeo donde se ilustran los mejores enfoques, consulte la
  [presentación de Daniel Pearson titulada Windows Debugging and
  Troubleshooting (en inglés)][33].

* OpenSuse Linux. También hay una inmensa cantidad de recursos
  disponibles para solucionar problemas de aplicaciones en Suse Linux,
  tanto en la [documentación de Suse Linux (en inglés)][34] como en
  Internet.
* Ubuntu Linux. También hay un gran volumen de información. Para leer la
  documentación del producto, consulte [https://help.ubuntu.com/][35].
* CentOS Linux. Para obtener más información, consulte
  [http://centos.org/][36].

<h2><a id="PlatformServices" ></a>Solución de problemas con los servicios Azure</h2>


Muchos de los servicios Azure, como Base de datos SQL de Azure, Azure Active Directory y Almacenamiento de Azure, incluyen recomendaciones sobre solución de problemas específicas para la finalidad con la que se usan, con independencia de que la aplicación se ejecute en Azure, del lenguaje de programación y las bibliotecas con las que se ha creado la aplicación o de que la ejecución tenga lugar en un sistema operativo que no es de Microsoft. En la siguiente información se ofrecen procedimientos recomendados específicos para algunos de estos servicios.

Existen muchas bibliotecas compatibles que implementan procedimientos recomendados para llamadas asincrónicas, seguimientos y registro de eventos, tal y como se ha descrito en la sección de este documento dedicada al diseño.

#### Solución de problemas de almacenamiento de Azure

Los siguientes vínculos remiten a información acerca de los diseños o las prácticas para mitigar problemas que es preciso solucionar o ubicaciones en que debe aplicar tareas de seguimiento o registro.

* Códigos de estado y de error relacionados con el almacenamiento:
  [http://msdn.microsoft.com/es-es/library/windowsazure/dd179382.aspx][37]

* Análisis de almacenamiento:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx][38]
  (Consulte los tres vínculos "for more information" que aparecen en la
  parte inferior).

* Overview of retry policies in the Azure Storage Client Library:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx][39]

* How to get the most out of Azure Tables:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx][40]
  
* Consulte estas secciones: Best Practices; Tips for Programming
    Azure; and Timeouts and ServerBusy - Is it normal?

* Protecting Your Blobs Against Application Errors:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx][41]

**Exploradores de almacenamiento de Azure**

Hay diferentes formas de explorar el almacenamiento de Azure. El equipo de Almacenamiento de Azure ha elaborado una [lista de exploradores de almacenamiento (en inglés)][42]. Cualquiera de ellos le permitirá ver los archivos de Diagnósticos y los archivos de análisis de almacenamiento de Azure. [Explorer for Azure Blob Storage][43], de Cloudberry Lab, ofrece una interfaz de usuario que permite habilitar el análisis de almacenamiento directamente en la aplicación al hacer clic en **Storage Settings**.

<h2><a id="Misc" ></a>Solución de problemas con Bus de servicio de Azure</h2>


En esta sección se ofrecen instrucciones acerca de cómo desarrollar una aplicación que usa el Bus de servicio de Azure de una forma eficaz y sostenible que minimizará los problemas comunes. También se ofrecen detalles acerca de cómo identificar y solucionar errores comunes del Bus de servicio.

### Instrucciones generales

El Bus de servicio es un [bus de servicios de empresa][44] que admite las funcionalidades de mensajería asíncrona y retransmitida. El Bus de servicio implementa cuotas y umbrales a un nivel de sistema para ambos tipos de mensajería. Si la aplicación supera estas cuotas, esta se limitará o las solicitudes o los mensajes se rechazarán. Para obtener acceso a todos los detalles acerca de las cuotas del Bus de servicio y el comportamiento que observará cuando se excedan tales cuotas, consulte [Cuotas de Azure Service Bus][45]. Algunas cuotas las definen los
propios usuarios, como el tamaño de una cola o un tema, que se define cuando se crea la entidad.

Para ver los datos de la entidad de mensajería del Bus de servicio y la forma en que se procesan, puede usar el [Explorador del Bus de servicio][46] o el Explorador de servidores en Azure Tools para Visual Studio (versión 1.7 o posterior) para crear, eliminar y probar colas, temas, suscripciones y reglas. Se trata de un método excelente para solucionar los problemas de una aplicación que ejecuta, pero que no procesa, los datos según lo previsto. Estas herramientas comprenden una funcionalidad que le permite probar colas, temas y entidades de relé, realizar un seguimiento de las operaciones ejecutadas por tareas individuales de remitente y destinatario, supervisar el progreso y el rendimiento de la ejecución de pruebas en curso y generar registros detallados de los resultados, incluidos todos los mensajes de error.

### Relé del Bus de servicio

El "relé" del Bus de servicio se ejecuta en la nube y admite diversos protocolos de transporte y estándares de servicios web, entre otros, SOAP, WS-\* y REST. Puede usar el relé del Bus de servicio como un delegado para escuchar las sesiones de entrada y las solicitudes enviadas a un servicio WCF. En el patrón de mensajería retransmitida, un servicio local o basado en la nube se conecta al servicio de relé mediante un puerto de salida y crea un socket bidireccional para la comunicación enlazada a una dirección de encuentro concreta. El cliente no tiene que saber si el servicio existe, y el servicio local no necesita ningún puerto de entrada abierto en el firewall. No obstante, en función de la configuración de red, puede experimentar problemas al conectarse al relé del Bus de servicio si el firewall está activado o a través de un servidor proxy. En [Hospedaje detrás de un firewall con Service Bus][47] se describe la solución de los problemas de conexión y cómo resolverlos.

### Temas y colas del Bus de servicio

Los temas y las colas del Bus de servicio ofrecen la funcionalidad de la mensajería asíncrona: los mensajes se insertan en el tema o en la cola del Bus de servicio y se mantienen ahí de forma fiable hasta que el destinatario está listo para usarlos. Los remitentes y destinatarios de los mensajes no tienen que estar en línea al mismo tiempo; la infraestructura de mensajería almacena los mensajes de manera fiable hasta que el destinatario está listo para recibirlos. La API de mensajería puede experimentar una serie de errores que podrían afectar a la aplicación. En general, pueden agruparse en las siguientes categorías:

* Error de usuario: por ejemplo, un código que indica que un argumento
  no era válido. Acción recomendada: pruebe a corregir el código antes
  de continuar.
* Error de instalación/configuración: por ejemplo, una cola o un tema
  asociados con la acción no existen o se han eliminado. Acción
  recomendada: revise la configuración y cámbiela si es necesario.
* Error transitorio: por ejemplo, una respuesta que indica que el
  servicio no ha podido procesar la solicitud en ese momento. Acción
  recomendada: intente de nuevo la operación o informe a los usuarios.
  Para obtener más información, consulte [Controlar errores de
  comunicación transitorios][48].
* Otros errores: por ejemplo, errores de tiempo de espera o errores que
  indican que se ha perdido un mensaje bloqueado. Acción recomendada:
  por lo general, el usuario no controla estas excepciones para realizar
  limpiezas o anulaciones. Deben utilizarse a efectos de seguimiento.

En [Excepciones de mensajería][49] se ofrece información general acerca de las excepciones que los usuarios de las bibliotecas cliente de .NET del Bus de servicio pueden encontrar, además de recomendaciones sobre cómo controlar cada tipo de excepción. Habida cuenta de que las bibliotecas cliente de .NET se ajustan en gran medida a las bibliotecas del Bus de servicio para otros lenguajes, estas instrucciones pueden resultar útiles incluso aunque la programación no se realice en un lenguaje .NET. En algunos casos, por ejemplo, con los errores transitorios, puede intentar la acción de nuevo. Puede seguir las instrucciones para el control de errores transitorios descritas anteriormente en este artículo a fin de controlar estos errores con eficacia. Además, para obtener más detalles, procedimientos recomendados y código de muestra sobre cómo controlar los errores transitorios del Bus de servicio en una aplicación .NET, consulte la sección [Controlar errores de comunicación transitorios][48] en el artículo de información orientativa para desarrolladores de Azure [Prácticas recomendadas para aprovechar la API de mensajería negociada de Service Bus de Azure][50].

Otro aspecto en que cabe centrar la atención al desarrollar una aplicación que usa mensajería asíncrona es asegurarse de implementar una lógica fiable de recepción de mensajes que pueda controlar con precisión y eficacia las anomalías de los mensajes. En la sección "Implementing Reliable Message Receive Loops" del artículo [Prácticas recomendadas para aprovechar la API de mensajería negociada de Service Bus de Azure][50], se describen una serie de técnicas para usar el modo de recepción **PeekLock**, que es el modo que admite varias entregas del mensaje en caso de que este no se haya entregado correctamente en el primer intento. El artículo también comprende procedimientos recomendados que ayudarán a garantizar que la aplicación no procese mensajes duplicados. También ayuda a evitar problemas que pueden producirse debido a tiempos de espera por bloqueo y a mejorar el rendimiento general en el modo **PeekLock**, ya que garantiza un procesamiento oportuno de los mensajes. En el artículo también se ofrece código de muestra que usan las bibliotecas cliente de .NET del Bus de servicio.

### Otros recursos de solución de problemas

Para obtener detalles adicionales acerca de los errores comunes del Bus de servicio y de las formas de investigarlos y solucionarlos, consulte [Solución de problemas del Service Bus][51].

## Servicio de control de acceso de Azure Active Directory

Solución de problemas

* Códigos de error:
  [http://msdn.microsoft.com/es-es/library/windowsazure/gg185949.aspx][52]
* Limitaciones de servicio de ACS:
  [http://msdn.microsoft.com/es-es/library/windowsazure/gg185909.aspx][53]

<h2><a id="SQLTroubleshooting" ></a>Solución de problemas de Base de datos SQL de Azure</h2>


Al interactuar con una base de datos SQL de Azure, cabe extremar precauciones al abordar la naturaleza distribuida de las aplicaciones del servicio Base de datos SQL de Azure. En la presente sección se tratan varios aspectos a los que se debe prestar atención. No obstante, no solo existen estos aspectos. La clave para escribir código admitido de Base de datos SQL de Azure es examinar los códigos de retorno y asegurarse de que dispone de código de reintento sólido para controlar los errores.

La aplicación debe identificar correctamente los errores de inicio de sesión. Las instancias de Base de datos SQL requieren el uso de Autenticación de SQL. Si no puede iniciar sesión, el motivo puede ser que las credenciales no son válidas o que la base de datos solicitada no está disponible.

La aplicación debe identificar si no es posible obtener acceso al servicio. Si el servidor ya se ha aprovisionado y el servicio Base de datos SQL de Azure está disponible (puede comprobarlo en la página de [estado de Azure][54]), la causa puede ser un problema de configuración en la instalación in situ. Por ejemplo, es posible que no pueda resolver el nombre (que puede probarse con herramientas como tracert), que el firewall haya bloqueado el puerto 1433 utilizado por la Base de datos SQL, o bien que utilice un servidor proxy que no está configurado correctamente. Para solucionar estos problemas, use las mismas técnicas que aplicaría con SQL Server. Para obtener más información, consulte [SQL Database Connectivity Troubleshooting Guide][55] y [Solucionar problemas de Base de datos SQL de Azure][56].

La aplicación debe identificar errores de red generales. Puede recibir errores de red generales porque el servicio Base de datos SQL de Azure desconecta a los usuarios en determinadas circunstancias:

* Cuando una conexión está inactiva durante mucho tiempo
* Cuando una conexión consume una cantidad excesiva de recursos o
  mantiene una transacción durante un largo periodo de tiempo
* Si el servidor está demasiado ocupado

Para mejorar el rendimiento del servicio Base de datos SQL de Azure, use las mismas técnicas que aplicaría con SQL Server. Para obtener más información, consulte los temas siguientes:

* [Solucionar problemas de consultas][57] en los Libros en pantalla de
  SQL Server
* [Troubleshoot and Optimize Queries with SQL Database][58]
* [SQL Database Performance Considerations and Troubleshooting][59]
* [Improving Your I/O Performance][60]
* [System Center Monitoring Pack for SQL Database][61]

El servicio Base de datos SQL de Azure usa un subconjunto de mensajes de error de SQL Server. Para obtener más información acerca de SQL Server, consulte [Referencia de errores y eventos del motor de base de datos][62] en los Libros en pantalla de SQL Server.

Si necesita recuperar nombres de inicio de sesión o contraseñas, póngase en contacto con el administrador de servicios, que puede concederle el acceso necesario al servidor y la base de datos. Los administradores de servicios también pueden restablecer sus propias contraseñas con el Portal de administración de Azure.

Las consultas a Base de datos SQL pueden experimentar errores por diversos motivos, entre otros, una consulta formulada incorrectamente o problemas de red. Algunos errores son transitorios, lo que significa que un problema suele desaparecer por sí mismo. Para este subconjunto de errores, tiene sentido reintentar la consulta después de un corto intervalo. Si la consulta aún experimenta errores tras varios reintentos, podría informar de un error. Es evidente que no todos los errores son transitorios. El error 102 de SQL, "Sintaxis incorrecta", no desaparecerá por muchas veces que envíe la misma consulta. En resumen, hay que reflexionar un poco para implementar una lógica de reintento robusta. Si es posible, se envía un token de error de flujo TDS antes de desconectar a los usuarios. Para mejorar la experiencia con la aplicación, le recomendamos que implemente la lógica de reintento en las aplicaciones de Base de datos SQL para capturar estos errores. Si se produce un error, restablezca la conexión y, a continuación, vuelva a ejecutar los comandos y la consulta que han generado el error. Para obtener más información, consulte los vínculos siguientes:

* [Retry Logic for Transient Failures in SQL Database][63]
* [SQL Database Retry Logic Sample][64]
* [The Transient Fault Handling Application Block][65]

### Estrategia para la restauración y copia de seguridad de SQL de Azure

Base de datos SQL de Azure necesita una estrategia propia de copia de seguridad y restauración, debido al entorno y las herramientas disponibles. De muchas formas, la base de datos que se encuentra en los centros de datos de Microsoft ha mediado en los riesgos. La herramienta de que disponemos actualmente cubre otros factores de riesgos; no obstante, se habilitarán próximamente herramientas mejoradas para facilitar aún más el trabajo. Red-gate ha publicado recientemente una herramienta gratuita para la restauración y las copias de seguridad de Base de datos SQL, que se encuentra disponible aquí: [http://www.red-gate.com/products/dba/sql-azure-backup/][66].

SQL Data Sync le permite crear y programar con facilidad sincronizaciones bidireccionales desde el sitio web de Data Sync sin necesidad de escribir una única línea de código. Puede encontrar más información aquí:
[http://msdn.microsoft.com/es-es/library/windowsazure/hh456371.aspx][67].

Para obtener más información acerca de las estrategias de copia de seguridad y restauración de Base de datos SQL, consulte los artículos siguientes:

* En este tema se ofrece información general sobre las estrategias de
  copia de seguridad y restauración de Base de datos SQL:
  [http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx][68]
* En este tema se explica cómo realizar la copia de seguridad de una
  base de datos en otra en el mismo servidor:
  [http://msdn.microsoft.com/es-es/library/windowsazure/ff951631.aspx][69]
* En este tema se explica cómo exportar en un blob una instancia
  existente en Base de datos SQL en una cuenta de almacenamiento
  determinada:
  [http://msdn.microsoft.com/es-es/library/windowsazure/hh335292.aspx][70]
* En este tema se explica cómo importar una instancia existente en Base
  de datos SQL desde un archivo bacpac almacenado en un blob:
  [http://msdn.microsoft.com/es-es/library/windowsazure/hh335291.aspx][71]
* En este tema se describen las capacidades que ofrece Base de datos SQL
  para la continuidad de negocio:
  [http://msdn.microsoft.com/es-es/library/windowsazure/hh852669.aspx][72]

<h2><a id="Cache" ></a>Almacenamiento en caché de Azure</h2>


El servicio Almacenamiento en caché de Azure presenta dos modelos: Almacenamiento en caché compartido de Azure y Almacenamiento en caché de Azure basado en roles (Vista previa). El almacenamiento en caché compartido es un servicio multiinquilino de Azure que ofrece servicios de almacenamiento en caché. Almacenamiento en caché de Azure (Vista previa) hospeda el almacenamiento en caché en un rol; para ello, usa una parte de la memoria de las máquinas virtuales que hospedan las instancias de rol. Para solucionar los problemas de Almacenamiento en caché de Azure, compruebe los códigos de error y las excepciones de almacenamiento en caché para observar el comportamiento de la caché. Cuando use la opción de Almacenamiento en caché basado en roles (Vista previa), también puede usar contadores de rendimiento. Los problemas relacionados con el almacenamiento en caché suelen englobarse en alguna de las siguientes categorías:

* Errores relacionados con cuotas: se ha superado una cuota
  (Almacenamiento en caché compartido)
* Limitación: se produce cuando no hay memoria física suficiente para
  admitir elementos adicionales en caché
* Expulsión: los elementos se expulsan forzosamente para liberar espacio
  para los nuevos elementos, de una manera que merma el rendimiento de
  la aplicación
* Expiración: los periodos de expiración que se definen son demasiado
  cortos o largos

Para obtener más información acerca de los errores relacionados con las cuotas, consulte [Cuotas del Azure Shared Caching][73].



[1]: https://www.windowsazure.com/en-us/develop/overview/
[2]: http://msdn.microsoft.com/en-us/library/hh680918%28v=pandp.50%29.aspx
[3]: http://nuget.org/packages/Microsoft.Experience.CloudFx
[4]: http://msdn.microsoft.com/en-us/magazine/ff714589.aspx
[5]: http://www.microsoft.com/download/en/details.aspx?id=11324
[6]: http://www.microsoft.com/en-us/server-cloud/system-center/operations-manager.aspx
[7]: http://wapmmc.codeplex.com/
[8]: http://cerebrata.com/Products/AzureDiagnosticsManager/
[9]: http://cerebrata.com/Products/CloudStorageStudio/
[10]: http://cerebrata.com/Products/AzureManagementCmdlets/
[11]: http://www.cerebrata.com
[12]: http://www.compuware.com/application-performance-management/
[13]: http://www.keynote.com/solutions/
[14]: http://pingdom.com/
[15]: http://www.alertbot.com/products/website-monitoring/default.aspx
[16]: http://www.apicasystem.com/integration-partners/
[17]: http://www.microsoft.com/en-us/server-cloud/system-center/avicode.aspx
[18]: http://msdn.microsoft.com/en-us/library/windowsazure/hh369930.aspx
[19]: http://azurevmassist.codeplex.com/
[20]: https://github.com/dannycoates/node-inspector
[21]: http://weblogs.asp.net/jimwang/archive/2012/04/17/debugging-node-node-inspector-in-the-azure-emulator.aspx
[22]: https://github.com/windowsazure/iisnode/downloads
[23]: http://howtonode.org/debugging-with-node-inspector
[24]: http://msdn.microsoft.com/en-us/library/dd264915.aspx
[25]: http://www.fiddler2.com/fiddler2/
[26]: http://go.microsoft.com/fwlink/?LinkID=90561
[27]: http://go.microsoft.com/fwlink/?LinkId=246619
[28]: http://logparserplus.com/Examples
[29]: http://technet.microsoft.com/en-us/library/ee692659.aspx
[30]: http://www.microsoft.com/downloads/en/details.aspx?familyid=7287252C-402E-4F72-97A5-E0FD290D4B76&displaylang=enBlockquote
[31]: http://logparserplus.com/article/2
[32]: http://msdn.microsoft.com/en-us/library/7a50syb3%28v=vs.90%29.aspx
[33]: http://technet.microsoft.com/en-us/edge/Video/hh867800
[34]: https://www.suse.com/documentation/
[35]: https://help.ubuntu.com/
[36]: http://centos.org/
[37]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179382.aspx
[38]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx
[39]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx
[40]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
[41]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx
[42]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/17/windows-azure-storage-explorers.aspx
[43]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[44]: http://en.wikipedia.org/wiki/Enterprise_service_bus
[45]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732538.aspx
[46]: http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a
[47]: http://msdn.microsoft.com/en-us/library/windowsazure/ee706729.aspx
[48]: http://msdn.microsoft.com/en-us/library/hh851746(VS.103).aspx
[49]: http://msdn.microsoft.com/en-us/library/hh418082.aspx
[50]: http://msdn.microsoft.com/en-us/library/windowsazure/hh545245.aspx
[51]: http://msdn.microsoft.com/en-us/library/windowsazure/ee706702.aspx
[52]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185949.aspx
[53]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185909.aspx
[54]: http://go.microsoft.com/fwlink/p/?LinkId=168847
[55]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-connectivity-troubleshooting-guide.aspx
[56]: http://msdn.microsoft.com/en-us/library/ee730906.aspx
[57]: http://msdn.microsoft.com/en-us/library/ms186351(SQL.100).aspx
[58]: http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-sql-azure.aspx
[59]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI314
[60]: http://blogs.msdn.com/b/sqlazure/archive/2010/07/27/10043069.aspx?PageIndex=2#comments
[61]: http://www.microsoft.com/en-us/download/details.aspx?id=10631
[62]: http://go.microsoft.com/fwlink/p/?LinkId=166622
[63]: http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-sql-azure.aspx
[64]: http://code.msdn.microsoft.com/windowsazure/SQL-Azure-Retry-Logic-2d0a8401
[65]: http://msdn.microsoft.com/en-us/library/hh680934(PandP.50).aspx
[66]: http://www.red-gate.com/products/dba/sql-azure-backup/
[67]: http://msdn.microsoft.com/en-us/library/windowsazure/hh456371.aspx
[68]: http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx
[69]: http://msdn.microsoft.com/en-us/library/windowsazure/ff951631.aspx
[70]: http://msdn.microsoft.com/en-us/library/windowsazure/hh335292.aspx
[71]: http://msdn.microsoft.com/en-us/library/windowsazure/hh335291.aspx
[72]: http://msdn.microsoft.com/en-us/library/windowsazure/hh852669.aspx
[73]: http://msdn.microsoft.com/en-us/library/gg185683.aspx
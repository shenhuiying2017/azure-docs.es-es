<properties pageTitle="Comparaci&oacute;n entre Sitios web Azure, Servicios en la nube y M&aacute;quinas virtuales" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Aprenda cu&aacute;ndo utilizar Sitios web Azure, Servicios en la nube y M&aacute;quinas virtuales para hospedar aplicaciones web." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Servicios en la nube" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra" />

# Comparación entre Sitios web Azure, Servicios en la nube y Máquinas virtuales

## Información general

Azure ofrece varias maneras de hospedar sitios web: [Sitios web Azure][Sitios web Azure], [Servicios en la nube][Servicios en la nube] y [Máquinas virtuales][Máquinas virtuales]. Este artículo le ayuda a comprender las opciones y a tomar la decisión correcta para su aplicación web.

Sitios web Azure es la opción más adecuada para la mayoría de aplicaciones web. La implementación y la administración están integradas en la plataforma, los sitios pueden escalarse rápidamente para asumir altas cargas de tráfico y el equilibrio de carga y el administrador de tráfico incluidos ofrecen una gran disponibilidad. Puede mover los sitios actuales a Sitios web Azure fácilmente con una [herramienta de migración en línea][herramienta de migración en línea], utilizar una aplicación de código abierto de la galería de aplicaciones web o crear un sitio nuevo utilizando el marco y las herramientas que prefiera. La característica [Trabajos web][Trabajos web] (actualmente en vista previa) facilita la tarea de agregar procesamiento de trabajo en segundo plano a su aplicación.

Si necesita más control sobre el entorno del servidor web, como la posibilidad de tener acceso remoto al servidor o configurar las tareas de inicio del servidor, Servicios en la nube de Azure es por lo general la mejor opción.

Si tiene actualmente una aplicación que requeriría cambios sustanciales para ejecutarse en Sitios web Azure o Servicios en la nube de Azure, podría elegir Máquinas virtuales de Azure a fin de simplificar la migración a la nube. Sin embargo, se requiere más tiempo para configurar, proteger y mantener adecuadamente las Máquinas virtuales, además de mayores conocimientos informáticos, en comparación con Sitios web Azure y Servicios en la nube. Si está considerando la opción de Máquinas virtuales Azure, tenga en cuenta el esfuerzo constante de mantenimiento requerido para aplicar revisiones al entorno de Máquina virtual, así como para actualizarlo y administrarlo.

El diagrama siguiente ilustra el control relativo de control frente a la facilidad de uso para cada una de estas opciones de hospedaje web en Azure.

![ChoicesDiagram][ChoicesDiagram]

## Tabla de contenido

-   [Situaciones y recomendaciones][Situaciones y recomendaciones]
-   [Comparación de características][Comparación de características]
-   [Pasos siguientes][Pasos siguientes]

## <a name="scenarios"></a>Situaciones y recomendaciones

Aquí se presentan algunas situaciones habituales de aplicaciones con recomendaciones acerca de la opción de hospedaje web de Azure que podría ser más apropiada en cada caso.

-   [Necesito un front-end web con procesamiento en segundo plano y back-end de base de datos para ejecutar aplicaciones empresariales integradas con activos locales.][Necesito un front-end web con procesamiento en segundo plano y back-end de base de datos para ejecutar aplicaciones empresariales integradas con activos locales.]
-   [Necesito un método confiable para hospedar mi sitio web corporativo que se escale correctamente y ofrezca un alcance global.][Necesito un método confiable para hospedar mi sitio web corporativo que se escale correctamente y ofrezca un alcance global.]
-   [Tengo una aplicación IIS6 ejecutándose en Windows Server 2003.][Tengo una aplicación IIS6 ejecutándose en Windows Server 2003.]
-   [Soy dueño de un negocio pequeño y necesito una forma económica de hospedar mi sitio, pero con un crecimiento a futuro en mente.][Soy dueño de un negocio pequeño y necesito una forma económica de hospedar mi sitio, pero con un crecimiento a futuro en mente.]
-   [Soy diseñador gráfico o web y deseo diseñar y crear sitios web para mis clientes.][Soy diseñador gráfico o web y deseo diseñar y crear sitios web para mis clientes.]
-   [Estoy migrando mi aplicación de niveles múltiples con un front-end web a la nube.][Estoy migrando mi aplicación de niveles múltiples con un front-end web a la nube.]
-   [Mi aplicación depende de entornos Windows o Linux altamente personalizados y deseo moverla a la nube.][Mi aplicación depende de entornos Windows o Linux altamente personalizados y deseo moverla a la nube.]
-   [Mi sitio usa software de código abierto y deseo hospedarlo en Azure.][Mi sitio usa software de código abierto y deseo hospedarlo en Azure.]
-   [Tengo una aplicación de línea de negocio que necesita conectarse a la red corporativa.][Tengo una aplicación de línea de negocio que necesita conectarse a la red corporativa.]
-   [Deseo hospedar una API de REST o un servicio web para los clientes móviles.][Deseo hospedar una API de REST o un servicio web para los clientes móviles.]

### <span id="onprem"></span></a>Necesito un front-end web con procesamiento en segundo plano y back-end de base de datos para ejecutar aplicaciones empresariales integradas con activos locales.

Sitios web Azure es una excelente solución para aplicaciones empresariales complejas. Le permite desarrollar aplicaciones que se escalan automáticamente en una plataforma con equilibrio de carga, se protegen con Active Directory y se conectan con sus recursos locales. Esta opción consigue que la administración de estas aplicaciones resulte sencilla gracias a un portal y unas API de categoría superior, y le permite obtener información acerca del uso que los clientes están haciendo de ellas con herramientas específicamente diseñadas. La nueva característica [Trabajos web][Trabajos web] le permite ejecutar tareas y procesos en segundo plano en el marco de su nivel web, mientras que la conectividad híbrida y las [características de VNET][características de VNET] facilitan la reconexión con los recursos locales. Sitios web Azure proporciona un contrato de nivel de servicio del 99,9 % que le permite lo siguiente:

-   Ejecutar sus aplicaciones de manera confiable en una plataforma en la nube que se mantiene por sí misma y aplica revisiones automáticamente.
-   Escalar automáticamente entre una red global de centros de datos.
-   Realizar copias de seguridad y restaurar para la recuperación ante desastres.
-   Cumplir con ISO, SOC2 y PCI.
-   Integrarse con Active Directory

### <span id="corp"></span></a>Necesito un método confiable para hospedar mi sitio web corporativo que se escale correctamente y ofrezca un alcance global.

Sitios web Azure es una excelente solución para hospedar sitios web corporativos. Permite a los sitios escalarse de manera rápida y sencilla para atender con facilidad la demanda entre una red global de centros de datos. Ofrece alcance local, tolerancia a errores y administración del tráfico inteligente. Todo en una plataforma que proporciona herramientas de administración de clase superior, con lo que podrá obtener información sobre el estado y el tráfico del sitio rápidamente y con toda facilidad. Sitios web Azure proporciona un contrato de nivel de servicio del 99,9 % que le permite lo siguiente:

-   Ejecutar sus sitios web de manera confiable en una plataforma en la nube que se mantiene por sí misma y aplica revisiones automáticamente.
-   Escalar automáticamente entre una red global de centros de datos.
-   Realizar copias de seguridad y restaurar para la recuperación ante desastres.
-   Administrar registros y tráfico con herramientas integradas.
-   Cumplir con ISO, SOC2 y PCI.
-   Integrarse con Active Directory

### <span id="iis6"></span></a>Tengo una aplicación IIS6 ejecutándose en Windows Server 2003.

Sitios web Azure permite evitar fácilmente los costes de infraestructura asociados a la migración de aplicaciones IIS6 antiguas. Microsoft ha creado [herramientas de migración fáciles de utilizar y una detallada guía sobre migración][herramientas de migración fáciles de utilizar y una detallada guía sobre migración] que le permiten comprobar la compatibilidad e identificar aquellos cambios que deban realizarse. La integración con Visual Studio, TFS y las herramientas CMS más habituales facilitan la implementación de aplicaciones IIS6 directamente en la nube. Una vez implementadas, el portal de administración de Azure proporciona potentes herramientas de administración que permiten reducir verticalmente para administrar costes y aumentar verticalmente para atender la demanda cuando sea necesario. Con la herramienta de migración puede hacer lo siguiente:

-   Migrar con rapidez y sencillez su aplicación web de Windows Server 2003 heredada a la nube.
-   Optar por dejar la base de datos SQL adjunta en el entorno local para crear una aplicación híbrida.
-   Mover automáticamente su base de datos SQL junto con la aplicación heredada.

### <span id="smallbusiness"></span></a>Soy dueño de un negocio pequeño y necesito una forma económica de hospedar mi sitio, pero con un crecimiento a futuro en mente.

Sitios web Azure es una excelente solución para este escenario, porque puede empezar a usarlo gratis y luego agregar más capacidades cuando las necesite. Cada sitio web gratuito incluye un dominio proporcionado por Azure (*su\_compañía*.azurewebsites.net), y la plataforma incluye tanto herramientas de administración e implementación integradas como una galería de aplicaciones que permiten empezar a usarla sin complicaciones. Hay muchos otros servicios y opciones de escalado que permiten que el sitio evolucione con cuando aumente la demanda de los usuarios. Con Sitios web Azure, puede hacer lo siguiente:

-   Comenzar con el nivel gratis y luego escalar, según sea necesario.
-   Usar la Galería de aplicaciones para configurar rápidamente aplicaciones web conocidas, como WordPress.
-   Agregar servicios y características de Azure adicionales a su aplicación, según sea necesario.
-   Proteger su sitio web con HTTPS.

### <span id="designer"></span></a>Soy un diseñador gráfico o web y deseo diseñar y crear sitios web para mis clientes

Para diseñadores y desarrolladores web, Sitios web Azure se integra fácilmente con diversos marcos y herramientas, incluye soporte para implementación de Git y FTP y ofrece una estrecha integración con herramientas y servicios como Visual Studio y Base de datos SQL. Con Sitios web, puede hacer lo siguiente:

-   Usar herramientas de línea de comandos para [tareas automatizadas][tareas automatizadas].
-   Trabajar con lenguajes populares como [.Net][.Net], [PHP][PHP], [Node.js][Node.js] y [Python][Python].
-   Seleccionar tres niveles de escala diferentes para escalar hasta capacidades muy altas.
-   Integrarse con otros servicios de Azure, como [Base de datos SQL][Base de datos SQL], [Bus de servicio][Bus de servicio] y [Almacenamiento][Almacenamiento] u ofertas de asociados de la [Tienda de Azure][Tienda de Azure], como MySQL y MongoDB.
-   Integrarse con herramientas como Visual Studio, Git, WebMatrix, WebDeploy, TFS y FTP.

### <span id="multitier"></span></a>Estoy migrando mi aplicación de niveles múltiples con un front-end web a la nube.

Si está ejecutando una aplicación de niveles múltiples, como por ejemplo un servidor web que se conecta con una base de datos, Sitios web Azure es una excelente opción que ofrece una estrecha integración con Base de datos SQL. Y puede utilizar la nueva característica Trabajos web (en vista previa) para ejecutar procesos de back-end.

Elija Servicios en la nube para uno o varios de sus niveles si necesita más control sobre el entorno del servidor, como la posibilidad de tener acceso remoto al servidor o configurar las tareas de inicio del servidor.

Elija Máquinas virtuales para uno o varios de sus niveles si desea utilizar su propia imagen de máquina o ejecutar servicios o software de servidor que no puede configurar en Servicios en la nube.

### <span id="custom"></span></a>Mi aplicación depende de entornos Windows o Linux altamente personalizados y deseo moverla a la nube.

Si su aplicación requiere una instalación o configuración compleja del software y el sistema operativo, Máquinas virtuales es probablemente la mejor solución. Con Máquinas virtuales, puede hacer lo siguiente:

-   Usar la galería de Máquina virtual para que se inicie con un sistema operativo, como Windows o Linux y, a continuación, personalizarla según los requisitos de su aplicación.
-   Crear y cargar una imagen personalizada de un servidor en un entorno local existente para que ejecute en una máquina virtual en Azure.

### <span id="oss"></span></a>Mi sitio usa software de código abierto y deseo hospedarlo en Azure

Si el marco de código abierto se admite en Sitios web, los lenguajes y los marcos requeridos por su aplicación se configuran automáticamente sin necesidad de que realice ninguna acción. Sitios web le permite hacer lo siguiente:

-   Usar muchos lenguajes de código abierto populares, como [.NET][.Net], [PHP][PHP], [Node.js][Node.js] y [Python][Python].
-   Configurar WordPress, Drupal, Umbraco, DNN y muchas otras aplicaciones web de terceros.
-   Migrar una aplicación existente o crear una nueva a partir de la galería de aplicaciones.

Si no se admite el marco de código abierto en Sitios web, puede ejecutar la aplicación en cualquiera de las dos otras opciones de hospedaje web de Azure. Con Servicios en la nube, se utilizan tareas de inicio para instalar y configurar cualquier software de código abierto requerido que se ejecute Windows. Con Máquinas virtuales, puede instalar y configurar el software en una imagen de la máquina, que puede basarse en Windows o Linux.

### <span id="lob"></span></a>Tengo una aplicación de línea de negocio que necesita conectarse a la red corporativa

Si desea crear una aplicación de línea de negocio, puede que su sitio web requiera un acceso directo a los servicios o datos de la red corporativa. Es posible hacerlo en Sitios web, Servicios en la nube y Máquinas virtuales utilizando el [servicio de Red virtual de Azure][servicio de Red virtual de Azure]. En Sitios web puede utilizar la nueva [característica de integración de VNET][característica de integración de VNET], que permite la ejecución de las aplicaciones de Azure como si se encontraran en su red corporativa.

### <span id="mobile"></span></a>Deseo hospedar una API de REST o un servicio web para los clientes móviles

Los servicios web HTTP le permiten admitir una amplia variedad de clientes, incluidos los clientes móviles. Los marcos como ASP.NET Web API se integran con Visual Studio para facilitar la creación y el consumo de los servicios REST. Estos servicios se exponen desde un extremo web, por lo que es posible usar cualquier técnica de hospedaje web en Azure para admitir este escenario. Sin embargo, Sitios web es una excelente elección para hospedar las API de REST. Con Sitios web, puede hacer lo siguiente:

-   Crear rápidamente un sitio web para hospedar el servicio web HTTP en uno de los centros de datos globalmente distribuidos de Azure.
-   Migrar los servicios existentes o crear otros nuevos.
-   Conseguir contratos de nivel de servicio para disponibilidad con una sola instancia, o escalar horizontalmente a varias máquinas dedicadas.
-   Usar el sitio publicado para proporcionar API de REST a cualquier cliente HTTP, incluidos los clientes móviles.

## <a name="features"></a>Comparación de características

La siguiente tabla compara las capacidades de Sitios web, Servicios en la nube y Máquinas virtuales para ayudarle a tomar la mejor decisión. Para obtener más información acerca de los contratos de nivel de servicio para cada opción, consulte [Contratos de nivel de servicio de Azure][Contratos de nivel de servicio de Azure].

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Característica</th>
<th align="left">Sitios web</th>
<th align="left">Servicios en la nube (roles web)</th>
<th align="left">Máquinas virtuales</th>
<th align="left">Notas</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Implementación casi instantánea</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">La implementación de una aplicación o la actualización de una aplicación a un Servicio en la nube, o la creación de una máquina virtual, toma varios minutos; la implementación de una aplicación a un Sitio web tarda segundos.</td>
</tr>
<tr class="even">
<td align="left"><p>Escalado horizontal a máquinas más grandes sin volver a implementar</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Las instancias de un servidor web comparten contenido y configuración; esto significa que no es necesario volver a implementar o configurar a medida que escale.</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Varios entornos de implementación (producción y ensayo)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Administración de actualización automática del SO</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Intercambio fluido entre plataformas (mover fácilmente entre 32 bits y 64 bits)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Código de implementación con GIT, FTP</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Código de implementación con Web Deploy</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">Servicios en la nube admite el uso de Web Deploy para implementar actualizaciones en instancias de rol individuales. Sin embargo, no puede utilizarlo para la implementación inicial de un rol, y si utiliza Web Deploy para una actualización, tiene que realizar la implementación por separado para cada instancia de un rol. Se requieren múltiples instancias para optar al contrato de nivel de servicio de Servicio en la nube para entornos de producción.</td>
</tr>
<tr class="odd">
<td align="left"><p>Soporte para WebMatrix</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Acceso a servicios como Bus de servicio, Almacenamiento, Base de datos SQL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Web de host o nivel de servicios web de una arquitectura multinivel</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Nivel medio del host de una arquitectura multinivel</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Sitios web puede hospedar fácilmente un nivel medio de API de REST, y la característica <a href="http://go.microsoft.com/fwlink/?linkid=390226">Trabajos web</a> (actualmente en vista previa) puede hospedar trabajos de procesamiento en segundo plano. Puede ejecutar Trabajos web en un sitio web dedicado para alcanzar una escalabilidad independiente para el nivel.</td>
</tr>
<tr class="odd">
<td align="left"><p>Soporte integrado de MySQL como servicio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Servicios en la nube puede integrar MySQL como servicio mediante las ofertas de ClearDB, pero no como parte del flujo de trabajo del Portal de administración.</td>
</tr>
<tr class="even">
<td align="left"><p>Soporte para ASP.NET, ASP clásico, Node.js, PHP, Python</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Escalado horizontal a varias instancias sin volver a implementar</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Máquinas virtuales puede escalar horizontalmente hasta varias instancias, pero los servicios que se ejecutan en este servicio se deben escribir para controlar este escalado horizontal. Tiene que configurar un equilibrador de carga para que dirija solicitudes entre las máquinas y crear un Grupo de afinidad para evitar que todas las instancias se reinicien simultáneamente debido a errores de mantenimiento o hardware.</td>
</tr>
<tr class="even">
<td align="left"><p>Soporte para SSL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">En el caso de Sitios web, solo se admite SSL para nombres de dominio personalizados para el modo Básico y Estándar. Para obtener más información sobre el uso de SSL con Sitios web, consulte <a href="../web-sites-configure-ssl-certificate/">Configuración de un certificado SSL para un Sitio web Azure</a>.</td>
</tr>
<tr class="odd">
<td align="left"><p>Integración de Visual Studio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Depuración remota</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Código de implementación con TFS</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Aislamiento de red con <a href="/es-es/services/virtual-network/">Red virtual de Azure</a></p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Consulte también <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Integración de redes virtuales de Sitios web Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><p>Soporte para el <a href="/es-es/services/traffic-manager/">Administrador de tráfico de Azure</a></p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Supervisión de extremo integrado</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Acceso de escritorio remoto a los servidores</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Instalación de cualquier MSI personalizado</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Capacidad de definir/ejecutar tareas de inicio</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Posibilidad de utilizar eventos ETW personalizados para el seguimiento y la depuración</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
</tbody>
</table>

## <span id="nextsteps"></span></a>Pasos siguientes

Para obtener más información acerca de las tres opciones de hospedaje web, consulte los recursos siguientes:

-   [Introducing Azure][Introducing Azure]
-   [Modelos de ejecución de Azure][Modelos de ejecución de Azure]

Para conocer con mayor profundidad las opciones que ha elegido para su aplicación, consulte los recursos siguientes:

-   [Sitios web Azure][2]
-   [Servicios en la nube de Azure][Servicios en la nube de Azure]
-   [Máquinas virtuales de Azure][Máquinas virtuales de Azure]

  [Sitios web Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Servicios en la nube]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Máquinas virtuales]: http://go.microsoft.com/fwlink/?LinkID=306053
  [herramienta de migración en línea]: https://www.migratetoazure.net/
  [Trabajos web]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Situaciones y recomendaciones]: #scenarios
  [Comparación de características]: #features
  [Pasos siguientes]: #nextsteps
  [Necesito un front-end web con procesamiento en segundo plano y back-end de base de datos para ejecutar aplicaciones empresariales integradas con activos locales.]: #onprem
  [Necesito un método confiable para hospedar mi sitio web corporativo que se escale correctamente y ofrezca un alcance global.]: #corp
  [Tengo una aplicación IIS6 ejecutándose en Windows Server 2003.]: #iis6
  [Soy dueño de un negocio pequeño y necesito una forma económica de hospedar mi sitio, pero con un crecimiento a futuro en mente.]: #smallbusiness
  [Soy diseñador gráfico o web y deseo diseñar y crear sitios web para mis clientes.]: #designer
  [Estoy migrando mi aplicación de niveles múltiples con un front-end web a la nube.]: #multitier
  [Mi aplicación depende de entornos Windows o Linux altamente personalizados y deseo moverla a la nube.]: #custom
  [Mi sitio usa software de código abierto y deseo hospedarlo en Azure.]: #oss
  [Tengo una aplicación de línea de negocio que necesita conectarse a la red corporativa.]: #lob
  [Deseo hospedar una API de REST o un servicio web para los clientes móviles.]: #mobile
  [características de VNET]: ../fundamentals-networking/
  [herramientas de migración fáciles de utilizar y una detallada guía sobre migración]: https://www.movemetowebsites.net/
  [tareas automatizadas]: http://www.windowsazure.com/es-es/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/es-es/develop/net/
  [PHP]: http://www.windowsazure.com/es-es/develop/php/
  [Node.js]: http://www.windowsazure.com/es-es/develop/nodejs/
  [Python]: http://www.windowsazure.com/es-es/develop/python/
  [Base de datos SQL]: http://www.windowsazure.com/es-es/documentation/services/sql-database/
  [Bus de servicio]: http://www.windowsazure.com/es-es/documentation/services/service-bus/
  [Almacenamiento]: http://www.windowsazure.com/es-es/documentation/services/storage/
  [Tienda de Azure]: http://www.windowsazure.com/es-es/gallery/store/
  [servicio de Red virtual de Azure]: /es-es/services/virtual-network/
  [característica de integración de VNET]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
  [Contratos de nivel de servicio de Azure]: /es-es/support/legal/sla/
  [Introducing Azure]: ../fundamentals-introduction-to-azure/
  [Modelos de ejecución de Azure]: ../fundamentals-application-models/
  [2]: /es-es/documentation/services/websites/
  [Servicios en la nube de Azure]: /es-es/documentation/services/cloud-services/
  [Máquinas virtuales de Azure]: /es-es/documentation/services/virtual-machines/

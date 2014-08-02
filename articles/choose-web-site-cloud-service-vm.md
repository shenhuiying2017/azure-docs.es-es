<properties linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="When to Choose Azure Web Sites, Cloud Services, or Virtual Machines" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

Comparación entre Sitios web Azure, Servicios en la nube y Máquinas virtuales
=============================================================================

Azure ofrece varias formas de hospedar sus aplicaciones web, como por ejemplo [Sitios web Azure](http://go.microsoft.com/fwlink/?LinkId=306051), [Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=306052) y [Máquinas virtuales](http://go.microsoft.com/fwlink/?LinkID=306053). Después de ver estas diversas opciones, es posible que no esté seguro de cuál se adapta mejor a sus necesidades o puede que no tenga claros diferentes conceptos como IaaS o PaaS. Este artículo le ayudará a comprender sus opciones y a tomar la decisión correcta para su escenario web. Aunque las tres opciones le permiten ejecutar aplicaciones web altamente escalables en Azure, existen diferencias que pueden guiar en su decisión.

En muchas situaciones, los Sitios web Azure son la mejor opción. Proporciona opciones sencillas y flexibles para la implementación y la administración, y es capaz de hospedar sitios web de alto volumen. Puede crear rápidamente un sitio web nuevo con software popular, como por ejemplo WordPress, desde la galería de aplicaciones web o puede mover un sitio web existente a Sitios web Azure. Con el uso del [SDK de WebJobs de Azure (en inglés)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) (actualmente en versión preliminar) puede también agregar procesamiento de trabajo en segundo plano.

Además, tiene la opción de hospedar aplicaciones web en los Servicios en la nube de Azure o Máquinas virtuales de Azure. Es recomendable optar por estas opciones cuando su nivel web requiere el nivel adicional de control y personalización que proporcionan; sin embargo, este mayor control significa una mayor complejidad en la creación, administración e implementación de las aplicaciones. El siguiente diagrama ilustra las diferencias entre las tres opciones.

![ChoicesDiagram](./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png)

Sitios web es más fácil de configurar, administrar y supervisar, pero presenta menos opciones de configuración. El punto clave es que cuando no se tiene un motivo convincente para poner el front-end web en los Servicios en la nube o las Máquinas virtuales, se puede usar Sitios web Azure. La información necesaria para tomar una decisión informada se proporciona en el resto de este documento. En ella se incluye:

-   [Escenarios](#scenarios)
-   [Resúmenes de servicio](#services)
-   [Comparación de características](#features)

<a name="scenarios"></a>Escenarios
----------

### Soy dueño de un negocio pequeño y necesito una forma económica de hospedar mi sitio, pero con un crecimiento a futuro en mente.

Sitios web Azure (WAWS) es una excelente solución para este escenario, porque puede empezar a usarlo gratis y luego agregar más capacidades cuando las necesite. Por ejemplo, cada sitio web gratis viene con un dominio proporcionado por Azure (*su\_compañía*.azurewebsites.net). Cuando esté listo para empezar a usar su propio dominio, puede agregarlo por \$9,80 al mes (a partir de enero de 2014). Hay muchos otros servicios y opciones de escalado que permiten que el sitio evolucione con cuando aumente la demanda de los usuarios. Con **Sitios web Azure**, puede:

-   Comenzar con el nivel gratis y luego escalar, según sea necesario.
-   Usar la Galería de aplicaciones para configurar rápidamente aplicaciones web conocidas, como WordPress.
-   Agregar servicios y características de Azure adicionales a su aplicación, según sea necesario.
-   Proteger su sitio web con HTTPS usando el certificado que se proporcionó con el nombre de dominio *su\_compañía*.azurewebsites.net.

### Soy un diseñador gráfico o web y deseo diseñar y crear sitios web para mis clientes

Para los desarrolladores web, Sitios web Azure ofrece todo aquello que necesitan para crear sofisticadas aplicaciones web. Sitios web ofrece una estrecha integración con herramientas como Visual Studio y Base de datos SQL. Con **Sitios web**, los desarrolladores pueden:

-   Usar herramientas de línea de comandos para [tareas automatizadas](http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites).
-   Trabajar con lenguajes populares como [.Net](http://www.windowsazure.com/en-us/develop/net/), [PHP](http://www.windowsazure.com/en-us/develop/php/), [Node.js](http://www.windowsazure.com/en-us/develop/nodejs/) y [Python](http://www.windowsazure.com/en-us/develop/python/).
-   Seleccionar tres niveles de escala diferentes para escalar hasta capacidades muy altas.
-   Integrarse con otros servicios de Azure, como [Base de datos SQL](http://www.windowsazure.com/en-us/documentation/services/sql-database/), [Bus de servicio](http://www.windowsazure.com/en-us/documentation/services/service-bus/) y [Almacenamiento](http://www.windowsazure.com/en-us/documentation/services/storage/) u ofertas de asociados de la [Tienda de Azure](http://www.windowsazure.com/en-us/gallery/store/), como MySQL y MongoDB.
-   Integrarse con herramientas como Visual Studio, Git, WebMatrix, WebDeploy, TFS y FTP.

### Estoy migrando mi aplicación de niveles múltiples con una front-end web a la nube

Si está ejecutando una aplicación de niveles múltiples, como por ejemplo un servidor web que se comunica con un servidor de base de datos para almacenar y recuperar datos del sitio web, tiene varias opciones en Azure. Estas opciones arquitectónicas incluyen Sitios web, Servicios en la nube y Máquinas virtuales. Primero, **Sitios web** es una buena opción para el nivel web de su solución y se puede usar con Base de datos SQL de Azure para crear una arquitectura de dos niveles. Sitios web le permite también ejecutar procesos en segundo plano o de ejecución prolongada con la versión preliminar de SDK de WebJobs de Azure. Si necesita una arquitectura más compleja u opciones de escalado más flexibles, Servicios en la nube o Máquinas virtuales son una mejor opción.

**Servicios en la nube** le permite:

-   Hospedar servicios web, de nivel medio y back-end en roles web y de trabajo escalables.
-   Hospedar solo los servicios de nivel medio y back-end en roles de trabajo y mantener el front-end en Sitios web Azure.
-   Escalar los servicios de front-end y back-end de manera independiente.

**Máquinas virtuales** le permite:

-   Migrar con mayor facilidad entornos altamente personalizados como imagen de una máquina virtual.
-   Ejecutar software o servicios que no se pueden configurar en Sitios web o Servicios en la nube.

### Mi aplicación depende de entornos Windows o Linux altamente personalizados

Si su aplicación requiere una instalación o configuración compleja del software y el sistema operativo, Máquinas virtuales es probablemente la mejor solución. Con **Máquinas virtuales**, puede:

-   Usar la galería de Máquina virtual para que se inicie con un sistema operativo, como Windows o Linux y, a continuación, personalizarla según los requisitos de su aplicación.
-   Crear y cargar una imagen personalizada de un servidor en un entorno local existente para que ejecute en una máquina virtual en Azure.

### Mi sitio usa software de código abierto y deseo hospedarlo en Azure

Las tres opciones le permiten hospedar lenguajes y marcos de código abierto. **Servicios en la nube** requiere que use tareas de inicio para instalar y configurar cualquier software de código abierto necesario que se ejecute Windows. Con **Máquinas virtuales**, puede instalar y configurar el software en una imagen de la máquina, que puede basarse en Windows o Linux. Si su marco de código abierto es compatible con Sitios web, esto ofrece una forma más sencilla de hospedar estos tipos de aplicaciones debido a que Sitios web se puede configurar automáticamente con los lenguajes y los marcos que necesita su aplicación. **Sitios web** le permite:

-   Usar muchos lenguajes de código abierto populares, como [.NET](http://www.windowsazure.com/en-us/develop/net/), [PHP](http://www.windowsazure.com/en-us/develop/php/), [Node.js](http://www.windowsazure.com/en-us/develop/nodejs/) y [Python](http://www.windowsazure.com/en-us/develop/python/).
-   Instalar WordPress, Drupal, Umbraco, DNN y muchas otras aplicaciones web de terceros.
-   Migrar una aplicación existente o crear una nueva a partir de la galería de aplicaciones.

### Tengo una aplicación de línea de negocio que necesita conectarse a la red corporativa

Si desea crear una aplicación de línea de negocio, puede que su sitio web requiera un acceso directo a los servicios o datos de la red corporativa. Esto es posible en **Sitios web**, **Servicios en la nube** y **Máquinas virtuales**. Existen diferencias en el enfoque que utilice, que incluyen las siguientes:

-   Sitios web puede conectarse de manera segura a los recursos de un entorno local mediante el uso del relé del bus de servicio. Esto permite que los servicios de una red corporativa realicen tareas en representación de la aplicación web sin mover todo hacia la nube ni configurar una red virtual.
-   Servicios en la nube y Maquinas virtuales pueden aprovechar la red virtual. De hecho, la red virtual permite que las máquinas que se ejecutan en Azure se conecten a una red en un entorno local. Azure se convierte entonces en una extensión de su centro de datos corporativo.

### Deseo hospedar una API de REST o un servicio web para los clientes móviles

Los servicios web HTTP le permiten admitir una amplia variedad de clientes, incluidos los clientes móviles. Los marcos como ASP.NET Web API se integran con Visual Studio para facilitar la creación y el consumo de los servicios REST. Estos servicios se exponen desde un extremo web, por lo que es posible usar cualquier técnica de hospedaje web en Azure para admitir este escenario. Sin embargo, **Sitios web** es una excelente elección para hospedar las API de REST. Con Sitios web, puede:

-   Crear rápidamente un sitio web para hospedar el servicio web HTTP en uno de los centros de datos globalmente distribuidos de Azure.
-   Migrar servicios existentes o crear nuevos, aprovechar potencialmente ASP.NET Web API en Visual Studio.
-   Conseguir contratos de nivel de servicio para disponibilidad con una sola instancia, o escalar horizontalmente a varias máquinas dedicadas.
-   Usar el sitio publicado para proporcionar API de REST a cualquier cliente HTTP, incluidos los clientes móviles.

<a name="services"></a>Resúmenes de servicio
---------------------

[Sitios web Azure](http://go.microsoft.com/fwlink/?LinkId=306051) le permite generar sitios web altamente escalables rápidamente en Azure. Puede usar el Portal de Azure o las herramientas de línea de comandos para configurar un sitio web con lenguajes populares como .NET, PHP, Node.js y Python. Los marcos compatibles ya están implementados y no requieren más pasos de instalación. La galería de Sitios web Azure contiene muchas aplicaciones de terceros, como Drupal y WordPress, además de marcos de desarrollo como Django y CakePHP. Después de crear un sitio, puede migrar un sitio web existente o generar un sitio web completamente nuevo. Sitios web elimina la necesidad de administrar el hardware físico y también proporciona varias opciones de escalado. Puede cambiar desde un modelo multiinquilino compartido hasta un modo estándar donde máquinas dedicadas atienden el tráfico entrante. Sitios web le permite también integrarse con otros servicios de Azure, como Base de datos SQL, Bus de servicio y Almacenamiento. Con la versión preliminar de [SDK de WebJobs de Azure (en inglés)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs), puede agregar procesamiento en segundo plano. En resumen, Sitios web Azure facilita aún más el enfoque en el desarrollo de aplicaciones al admitir una amplia gama de lenguajes, aplicaciones de código abierto y metodologías de implementación (FTP, Git, Web Deploy o TFS). Si no tiene requisitos especializados que requieren Servicios en la nube o Máquinas virtuales, un Sitio web de Azure es, probablemente, la mejor opción.

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=306052) le permite crear aplicaciones web altamente disponibles y escalables en un entorno enriquecido de plataforma como servicio (PaaS). A diferencia de Sitios web, un servicio en la nube se crea primero en un entorno de desarrollo, como Visual Studio, antes de implementarse en Azure. Los marcos, como PHP, requieren tareas o pasos de implementación personalizados que instalen el marco en un inicio de rol. La principal ventaja de Servicios en la nube es la capacidad de admitir más arquitecturas multinivel complejas. Un solo servicio en la nube puede constar de un rol web front-end y uno o más roles de trabajo. Cada nivel se puede escalar de manera independiente. Existe también un mayor nivel de control sobre la infraestructura de la aplicación web. Por ejemplo, puede tener un escritorio remoto en las máquinas que ejecutan las instancias de rol. También puede generar una secuencia de comandos de IIS más avanzada y de cambios de configuración de la máquina que se ejecutan en el inicio de rol, incluidas las tareas que requieren control del administrador.

[Máquinas virtuales](http://go.microsoft.com/fwlink/?LinkID=306053) le permite ejecutar aplicaciones web en máquinas virtuales en Azure. Esta capacidad se conoce también como infraestructura como servicio (IaaS). Cree nuevas máquinas de Windows Server o Linux a través del portal o cargue la imagen de una máquina virtual existente. Maquinas virtuales le otorga el máximo control sobre el sistema operativo, la configuración y el software y los servicios instalados. Esta es una buena opción para migrar rápidamente complejas aplicaciones web desde entornos locales a la nube, porque las máquinas se pueden mover como un todo. Con Redes virtuales, puede conectar también estas máquinas virtuales a las redes corporativas en un entorno local. Como ocurre con Servicios en la nube, tiene acceso remoto a estas máquinas y la capacidad de realizar cambios en la configuración en el nivel administrativo. Sin embargo, a diferencia de Sitios web y Servicios en la nube, deberá administrar las imágenes de su máquina virtual y la arquitectura de la aplicación completamente en el nivel de infraestructura. Un ejemplo básico es que es usted quien debe hacerse cargo de la aplicación de sus propias revisiones en el sistema operativo.

<a name="features"></a>Comparación de características
------------------------------

La siguiente tabla compara las capacidades de Sitios web, Servicios en la nube y Máquinas virtuales para ayudarle a tomar la mejor decisión. Las notas a continuación de la tabla ofrecen una explicación adicional de las casillas con un asterisco.

<table  cellspacing="0" border="1">
<tr>
   <th  align="left" valign="middle">Característica</th>

   <th  align="left" valign="middle">Sitios web</th>

   <th  align="left" valign="middle">Servicios en la nube (roles web)</th>

   <th  align="left" valign="middle">Máquinas virtuales</th>

</tr>

<tr>
   <td  valign="middle"><p>Acceso a servicios como Bus de servicio, Almacenamiento, Base de datos SQL</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Web de host o nivel de servicios web de una arquitectura multinivel</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Nivel medio del host de una arquitectura multinivel</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Soporte integrado de MySQL como servicio</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>1</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Soporte para ASP.NET, ASP clásico, Node.js, PHP, Python</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Escalado horizontal a varias instancias sin volver a implementar</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>2</sup>
</td>

</tr>

<tr>
   <td  valign="middle"><p>Soporte para SSL</p>
</td>

   <td  valign="middle">X <sup>3</sup>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Integración de Visual Studio</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Depuración remota</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Código de implementación con TFS</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Código de implementación con GIT, FTP</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Código de implementación con Web Deploy</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle"><sup>4</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Soporte para WebMatrix</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Implementación casi instantánea</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Las instancias comparten el contenido y la configuración</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Escalado horizontal a máquinas más grandes sin volver a implementar</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Varios entornos de implementación (producción y ensayo)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Aislamiento de red con Red virtual de Azure</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Soporte para Administrador de tráfico de Azure</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Acceso de escritorio remoto a los servidores</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Capacidad de definir/ejecutar tareas de inicio</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Administración de actualización automática del SO</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Supervisión de extremo integrado</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Cambio de plataforma fluido (32 bits/64 bits)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

</table>

<sup>1</sup> Los roles web o de trabajo pueden integrar MySQL como servicio mediante las ofertas de ClearDB, pero no como parte del flujo de trabajo del Portal de administración.

<sup>2</sup> Aunque Máquinas virtuales puede escalar horizontalmente hacia varias instancias, los servicios que se ejecutan en estas máquinas se deben escribir para controlar este escalado horizontal. Es necesario configurar un equilibrador de carga adicional para enrutar las solicitudes a través de las máquinas. Por último, se debe crear un Grupo de afinidad para todas las máquinas que participan en el mismo rol a fin de protegerlas contra reinicios simultáneos debidos a mantenimiento o errores de hardware.

<sup>3</sup> Para Sitios web, solo se admite SSL para nombres de dominio personalizados para el modo estándar. Para obtener más información sobre el uso de SSL con Sitios web, consulte [Configuración de un certificado SSL para un sitio web de Azure](http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/).

<sup>4</sup> Web Deploy es compatible para los servicios en la nube cuando se implementa en roles de una sola instancia. No obstante, los roles de producción requieren varias instancias para satisfacer el contrato de nivel de servicio de Azure. Por lo tanto, Web Deploy no es un mecanismo de implementación adecuado para los servicios en la nube en producción.

  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png
  
  [Sitios web Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  
  [Servicios en la nube]: http://go.microsoft.com/fwlink/?LinkId=306052
  
  [Máquinas virtuales]: http://go.microsoft.com/fwlink/?LinkID=306053
  
  [ClearDB]: http://www.cleardb.com/
  
  [SDK de WebJobs de Azure (en inglés)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
 
  [Configuración de un certificado SSL para un sitio web de Azure]: http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/

  [azurestore]: http://www.windowsazure.com/en-us/gallery/store/

  [scripting]: http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites

  [dotnet]: http://www.windowsazure.com/en-us/develop/net/

  [nodejs]: http://www.windowsazure.com/en-us/develop/nodejs/

  [PHP]: http://www.windowsazure.com/en-us/develop/php/

  [Python]: http://www.windowsazure.com/en-us/develop/python/

  [servicebus]: http://www.windowsazure.com/en-us/documentation/services/service-bus/

  [sqldatabase]: http://www.windowsazure.com/en-us/documentation/services/sql-database/

  [Storage]: http://www.windowsazure.com/en-us/documentation/services/storage/
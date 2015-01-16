<properties title="Operating System Functionality Available to Applications on Azure Websites" pageTitle="Funcionalidad del sistema operativo disponible para aplicaciones en Sitios web Azure" description="Obtener información acerca de la funcionalidad del sistema operativo disponible para las aplicaciones web en sitios web de Azure" metaKeywords="Azure,Web Sites,web applications,operating system functionality" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/11/2014" ms.author="cephalin" />

# Funcionalidad del sistema operativo disponible para aplicaciones en Sitios web Azure #

En este artículo se describe la funcionalidad del sistema operativo de línea de base común que está disponible para todas las aplicaciones que se ejecutan en Sitios web Azure. Esta funcionalidad incluye archivo, red, acceso de registro, registros de diagnóstico y eventos. 

##Tabla de contenido

* [Modos de sitio web](#websitemodes)
* [Marcos de desarrollo](#developmentframeworks)
* [Acceso a archivos](#FileAccess)
	* [Unidades locales](#LocalDrives)
	* [Unidades de red (también conocidas como recursos compartidos UNC)](#NetworkDrives)
	* [Archivo en varias instancias](#multipleinstances)
	* [Tipos de acceso a archivo concedidos a una aplicación web](#TypesOfFileAccess)
* [Acceso de red](#NetworkAccess)
* [Memoria, procesos y ejecución de código](#Code)
* [Eventos y registros de diagnóstico](#Diagnostics)
* [Acceso al registro](#RegistryAccess)

<a id="websitemodes"></a>
<h2>Modos de sitio web</h2>
Sitios web Azure ejecuta los sitios web para clientes en un entorno de hospedaje multiinquilino. Los sitios web implementados en los modos de escalado gratis y compartido ejecutan procesos de trabajo en máquinas virtuales compartidas, mientras que los sitios web implementados en el modo de escalado del sitio web estándar se ejecutan en las máquinas virtuales dedicadas específicamente para los sitios web asociados a un único cliente.

Puesto que los sitios web de Azure son compatibles con una experiencia de escalado impecable entre distintos modos, la configuración de seguridad impuesta para los sitios web sigue siendo la misma. Esto garantiza que las aplicaciones web no se comportan de manera diferente, provocando errores de manera inesperada, cuando un sitio web cambia entre un modo de sitio web y otro.

<a id="developmentframeworks"></a>
<h2>Marcos de desarrollo</h2>

Los modos de sitio web controlan la cantidad de recursos de proceso (CPU, almacenamiento en disco, memoria y salida de red) disponible para los sitios web. Sin embargo, la amplitud de la funcionalidad del marco disponible para las aplicaciones sigue siendo la misma independientemente del modo del sitio web.

Sitios web Azure es compatible con una gran variedad de marcos de desarrollo, incluidos ASP.NET, ASP clásico, node.js, PHP y python (todos se ejecutan como extensiones en IIS). Para simplificar y normalizar la configuración de seguridad, Sitios web Azure ejecuta normalmente los distintos marcos de desarrollo con su configuración predeterminada. Un enfoque para configurar Sitios web Azure podría ser personalizar la funcionalidad y la zona de la superficie de la API para cada marco de desarrollo individual. Sitios web Azure adopta un enfoque más genérico mediante la habilitación de una línea base común de la funcionalidad del sistema operativo independientemente de un marco de desarrollo de la aplicación del sitio web.

Las siguientes secciones resumen los tipos generales de funcionalidad del sistema operativo disponibles para sitios web Azure.


<a id="FileAccess"></a>
<h2>Acceso a archivos</h2>

Existen varias unidades en Sitios web Azure, incluidas las unidades locales y las unidades de red.

<a id="LocalDrives"></a>
<h3>Unidades locales</h3>

Principalmente, Sitios web Azure es un servicio que se ejecuta en la parte superior de la infraestructura PaaS (plataforma como servicio) de Azure. Como resultado, las unidades locales "conectadas" a una máquina virtual son los mismos tipos de unidades disponibles para cualquier rol de trabajo que se ejecute en Azure. Esto incluye un sistema operativo (la unidad D:\), una unidad de aplicación que contiene archivos cspkg del paquete de Azure que usan exclusivamente Sitios web Azure (y a los que los clientes no pueden obtener acceso) y una unidad de "usuario" (la unidad C:\), cuyo tamaño varía según el tamaño de la máquina virtual.

<a id="NetworkDrives"></a>
<h3>Unidades de red (también conocidas como recursos compartidos UNC)</h3>

Uno de los aspectos exclusivos de Sitios web Azure que hace que el mantenimiento y la implementación de aplicaciones sean sencillos es que todo el contenido del usuario se almacena en un conjunto de recursos compartidos UNC. Este modelo se asigna perfectamente al patrón común de almacenamiento de contenido usado en entornos locales de hospedaje web que disponen de varios servidores con equilibrio de carga. 

En Sitios web Azure existen varios recursos compartidos UNC creados en cada centro de datos. Un porcentaje del contenido de usuario para todos los clientes en cada centro de datos se asigna a cada recurso compartido UNC. Además, todo el contenido del archivo para una suscripción de cliente única se coloca siempre en el mismo recurso compartido UNC. 

Tenga en cuenta que según la forma en la que funcionen los servicios en la nube, la máquina virtual específica responsable del hospedaje de un recurso UNC cambiará con el tiempo. Se garantiza que distintas máquinas virtuales realizarán el montaje de los recursos compartidos UNC, ya que estos aumentan y disminuyen durante el curso normal de operaciones en la nube. Por este motivo, las aplicaciones web nunca realizan suposiciones de forma rígida sobre que la información de la máquina en una ruta de acceso del UNC permanezca estable con el tiempo. Deben usar la ruta de acceso absoluta *faux* **D:\home\site** adecuada que proporciona Sitios web Azure. Esta ruta de acceso absoluta faux proporciona un método portátil independiente del usuario y el sitio para hacer referencia al sitio propio de alguien. Si usa **D:\home\site**, puede transferir archivos compartidos de un sitio a otro sin tener que configurar una nueva ruta de acceso absoluta para cada transferencia.

<a id="TypesOfFileAccess"></a>
<h3>Tipos de acceso a archivo concedidos a una aplicación web</h3>

Cada suscripción de cliente tiene una estructura de directorio reservada en un recurso compartido UNC específico dentro de un centro de datos. Un cliente puede disponer de varios sitios web creados en un centro de datos determinado, por lo que todos los directorios que pertenecen a una única suscripción de cliente se crean en el mismo recurso compartido UNC. El recurso compartido puede incluir directorios como los de contenido, registros de diagnóstico y error, y versiones anteriores del sitio web creadas por el control de origen. Lo esperado es que los directorios del sitio web del cliente dispongan de acceso de lectura y escritura en el tiempo de ejecución del código de aplicación del sitio web.

En las unidades locales conectadas a una máquina virtual que ejecute un sitio web, Sitios web Azure reserva un fragmento de espacio en la unidad C:\ para el almacenamiento local temporal específico del sitio web. Aunque el sitio web dispone de acceso de lectura/escritura completo en su propio almacenamiento local temporal, el almacenamiento no se ha creado realmente para que lo use directamente el código de la aplicación. El objetivo real es proporcionar un almacenamiento temporal de archivos para marcos de aplicaciones web e IIS. Sitios web Azure también limita la cantidad de almacenamiento local temporal disponible para cada sitio web a fin de evitar que los sitios web consuman cantidades excesivas de almacenamiento de archivos locales.

Los dos ejemplos de cómo Sitios web Azure usa el almacenamiento local temporal son el directorio para los archivos temporales ASP.NET y el directorio para archivos comprimidos IIS. El sistema de compilación ASP.NET usa el directorio de archivos temporales ASP.NET como ubicación temporal de la memoria caché de compilación. IIS usa el directorio de archivos temporales comprimidos IIS para almacenar los resultados comprimidos de la respuesta. Ambos tipos de uso de archivo (al igual que otros) se reasignan en Sitios web Azure según el almacenamiento local temporal del sitio web. Esta reasignación garantiza que la funcionalidad continúa según lo esperado.

Los sitios web de Sitios web Azure se ejecutan como una identidad de proceso de trabajo con privilegios reducidos, exclusiva y aleatoria denominada "identidad de grupo de aplicaciones", que se describe a continuación: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). El código de la aplicación usa esta identidad para el acceso básico de solo lectura para la unidad del sistema operativo (la unidad D:\). Esto significa que el código de aplicación puede enumerar estructuras de directorio comunes y leer archivos comunes en la unidad del sistema operativo. Aunque esto puede parecer ser un tipo de nivel amplio de acceso, puede obtenerse acceso a los mismos directorios y archivos cuando realiza el aprovisionamiento de un rol de trabajo en un servicio hospedado de Azure y lee el contenido de la unidad. 

<a name="multipleinstances"></a>
### Acceso al archivo en varias instancias

El directorio particular incluye el contenido de un sitio y las aplicaciones web pueden escribir en él. Si un sitio web se ejecuta en varias instancias, el directorio particular se comparte entre todas las instancias de manera que todas las instancias ven el mismo directorio. Por lo tanto, por ejemplo, si un sitio web guarda los archivos cargados en el directorio particular, esos archivos se encuentran inmediatamente disponibles para todas las instancias. 

<a id="NetworkAccess"></a>
<h2>Acceso de red</h2>
El código de aplicación puede usar protocolos basados en TCP/IPyd UDP para convertir las conexiones de red salientes en extremos accesibles a Internet que expongan servicios externos. Las aplicaciones pueden usar los mismos protocolos para conectarse a los servicios en Azure&#151;por ejemplo, mediante el establecimiento de conexiones HTTPS a SQL Azure.

Existe también una capacidad limitada para las aplicaciones de establecer una conexión de bucle invertido local y disponer de una escucha de la aplicación en ese socket de bucle invertido local. Esta característica existe principalmente para habilitar aplicaciones que escuchan en sockets de bucle invertido local como parte de su funcionalidad. Tenga en cuenta que cada aplicación de cliente ve una conexión de bucle invertido "privada"; la aplicación "A" no puede escuchar en un socket de bucle invertido establecido por la aplicación "B".

Las canalizaciones con nombre también son compatibles como un mecanismo de comunicación entre procesos (IPC) entre los distintos procesos que ejecutan conjuntamente un sitio web. Por ejemplo, el módulo FastCGI de IIS se basa en conductos con nombre para coordinar los procesos individuales que ejecutan páginas PHP.


<a id="Code"></a>
<h2>Memoria, procesos y ejecución de código</h2>
Como se indicó anteriormente, los sitios web se ejecutan dentro de los procesos de trabajo con privilegios reducidos mediante una identidad aleatoria de grupo de aplicaciones. El código de aplicación dispone de acceso al espacio de memoria asociado al proceso de trabajo, así como a procesos secundarios que pueden dividirse en procesos CGI u otras aplicaciones. Sin embargo, las aplicaciones de un sitio web no pueden obtener acceso a la memoria o los datos de otro sitio web del cliente incluso si se encuentran en la misma máquina virtual.

Las aplicaciones pueden ejecutar scripts o páginas escritas con marcos de desarrollo de aplicaciones web compatibles. Sitios web Azure no configura los ajustes del marco de aplicaciones web en modos más restringidos. Por ejemplo, los sitios de ASP.NET que se ejecuten en Sitios web Azure con una confiabilidad "completa" en oposición a un modo de confiabilidad más restringido. Los marcos de aplicación, incluidos ASP clásico y ASP.NET, pueden llamar a componentes COM en proceso (pero no componentes COM fuera del proceso) como ADO (objetos de datos ActiveX) que se registran de manera predeterminada en el sistema operativo de Windows.

Las aplicaciones web pueden dividir y ejecutar código arbitrario. Una aplicación web puede realizar acciones como la división de un shell de comandos o ejecutar un script PowerShell. Sin embargo, aunque el código arbitrario y los procesos puedan dividirse desde una aplicación web, los programas ejecutables y los scripts siguen restringiéndose a los privilegios concedidos en el grupo principal de aplicaciones. Por ejemplo, un sitio web puede dividir un ejecutable que realice una llamada HTTP saliente, pero el mismo ejecutable no puede intentar desvincular la dirección IP de una máquina virtual desde la NIC. Se permite la realización de una llamada de red saliente al código de privilegios reducidos, pero el intento de volver a configurar los ajustes de red en una máquina virtual requiere privilegios administrativos.


<a id="Diagnostics"></a>
<h2>Eventos y registros de diagnóstico</h2>
La información de registro es otro conjunto de datos a los que las aplicaciones web intentan obtener acceso. Los tipos de información de registro disponibles para el código que se ejecuta en Sitios web Azure incluyen la información de registro y diagnóstico que genera un sitio web que también puede obtener acceso de manera sencilla a un sitio web. 

Por ejemplo, los registros HTTP de W3C generados por un sitio web activo se encuentran disponibles en un directorio de registro en la ubicación de recursos compartidos de red para el sitio web o están disponibles en el almacenamiento de blobs si un cliente ha configurado el registro de W3C en el almacenamiento. La última opción permite que se recopile una gran cantidad de registros sin que exista el riesgo de exceder los límites de almacenamiento de archivos asociados a un recurso compartido de red.

De forma similar, la información de diagnóstico en tiempo real de aplicaciones .NET también puede registrarse mediante la infraestructura de diagnóstico y seguimiento .NET con opciones para escribir la información de seguimiento en el recurso compartido de la red del sitio web o, de manera alternativa, en una ubicación del almacenamiento de blobs.

Las zonas de seguimiento y registro de diagnóstico que no están disponibles para las aplicaciones web en Azure son eventos ETW de Windows y registros de eventos comunes de Windows (por ejemplo, registros de eventos del sistema, de la aplicación y de seguridad). Puesto que es posible que la información de seguimiento ETW se visualice en toda la máquina (con ACL correctas), el acceso de lectura y escritura a los eventos ETW se encuentra bloqueado. Los desarrolladores pueden experimentar que las llamadas a la API para leer y escribir eventos ETW y registros de eventos comunes de Windows parezcan funcionar, pero eso se debe a que Sitios web Azure "emula" las llamadas de manera que parezcan ser correctas. En realidad, el código de la aplicación del sitio web no dispone de acceso a estos datos de eventos.

<a id="RegistryAccess"></a>
<h2>Acceso al registro</h2>
Las aplicaciones disponen de acceso de solo lectura a la mayoría (no a todos) de los registros de la máquina virtual que están ejecutando. En la práctica, esto significa que las aplicaciones web pueden disponer de acceso a las claves de registro que permiten solo acceso de lectura al grupo de usuarios local. Un área de registro no compatible actualmente para el acceso de lectura y escritura es el subárbol HKEY_CURRENT_USER.

El acceso de escritura al registro está bloqueado, incluido el acceso a las claves de registro por usuario. Desde una perspectiva de la aplicación, el acceso de escritura al registro no debe basarse nunca en un entorno en la nube, puesto que las aplicaciones pueden migrarse (y realizarse) en distintas máquinas virtuales. El único almacenamiento persistente en el que se puede escribir y que puede depender de la aplicación web es la estructura de directorios de contenido por sitio web almacenada en recursos compartidos UNC de Sitios web Azure. 

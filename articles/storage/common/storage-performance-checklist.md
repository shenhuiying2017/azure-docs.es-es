---
title: "Lista de comprobación de rendimiento y escalabilidad de Azure Storage | Microsoft Docs"
description: "Lista de comprobación de prácticas probadas para su uso con Azure Storage para desarrollar aplicaciones de alto rendimiento."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 959d831b-a4fd-4634-a646-0d2c0c462ef8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 6f5a136d1be7a4bb4093baad820271770305b718
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Lista de comprobación de rendimiento y escalabilidad de Microsoft Azure Storage
## <a name="overview"></a>Información general
Desde el lanzamiento de los servicios de Microsoft Azure Storage, Microsoft ha desarrollado numerosos procedimientos para usar estos servicios de una manera eficiente. Este artículo pretende consolidar lo más importante de ellos en una lista de tipo “lista de comprobación”. El objetivo de este artículo es ayudar a los desarrolladores de aplicaciones a comprobar que están usando prácticas probadas con Azure Storage y ayudarles a identificar otras prácticas probadas cuya adopción deben plantearse. Este artículo no pretende cubrir todas las posibilidades de rendimiento y escalabilidad posibles y excluye aquellas cuyo impacto es pequeño o tienen poco margen de aplicación. En la medida en que el comportamiento de la aplicación se puede predecir durante el diseño, es útil recordar esto en las primeras etapas para evitar diseños que tengan problemas de rendimiento.  

Todos los desarrolladores de aplicaciones que usen Azure Storage deben invertir tiempo en leer este artículo y comprobar que su aplicación sigue todas las prácticas probadas que se enumeran a continuación.  

## <a name="checklist"></a>Lista de comprobación
Este artículo organiza las prácticas probadas en los siguientes grupos. Prácticas probadas aplicables a:  

* Todos los servicios de Azure Storage (Blob, Table, Queue y Files)
* Blobs
* Tablas
* Colas  

| ¡Listo! | Ámbito | Categoría | Pregunta |
| --- | --- | --- | --- |
| &nbsp; | Todos los servicios |Objetivos de escalabilidad |[¿Está su aplicación diseñada para evitar aproximarse a los objetivos de escalabilidad?](#subheading1) |
| &nbsp; | Todos los servicios |Objetivos de escalabilidad |[Is your naming convention designed to enable better load-balancing? (¿Está su convención de nomenclatura diseñada para habilitar un mejor equilibrio de carga?)](#subheading47) |
| &nbsp; | Todos los servicios |Redes |[¿Tienen los dispositivos del cliente un ancho de banda grande y una latencia baja suficientes como para lograr el rendimiento necesario?](#subheading2) |
| &nbsp; | Todos los servicios |Redes |[¿Tienen los dispositivos del lado cliente un enlace con una calidad suficientemente alta?](#subheading3) |
| &nbsp; | Todos los servicios |Redes |[¿Está la aplicación cliente ubicada "cerca" de la cuenta de almacenamiento?](#subheading4) |
| &nbsp; | Todos los servicios |Distribución de contenido |[¿Usa una red CDN para distribución de contenido?](#subheading5) |
| &nbsp; | Todos los servicios |Acceso directo del cliente |[¿Usa SAS y CORS para permitir el acceso directo al almacenamiento en lugar de proxy?](#subheading6) |
| &nbsp; | Todos los servicios |Almacenamiento en caché |[¿Su aplicación almacena datos en caché que se usan repetidamente y rara vez cambian?](#subheading7) |
| &nbsp; | Todos los servicios |Almacenamiento en caché |[¿Su aplicación procesa por lotes las actualizaciones (almacenándolas en caché en el cliente y, a continuación, cargándolas en conjuntos más grandes)?](#subheading8) |
| &nbsp; | Todos los servicios |Configuración .NET |[¿Ha configurado el cliente para usar un número suficiente de conexiones simultáneas?](#subheading9) |
| &nbsp; | Todos los servicios |Configuración .NET |[¿Ha configurado .NET para usar un número suficiente de subprocesos?](#subheading10) |
| &nbsp; | Todos los servicios |Configuración .NET |[¿Usa .NET 4.5 o posterior, que ha mejorado la recolección de elementos no usados?](#subheading11) |
| &nbsp; | Todos los servicios |Paralelismo |[¿Se ha asegurado de que el paralelismo está vinculado correctamente de forma que no se sobrecarguen las funcionalidades del cliente o los objetivos de escalabilidad?](#subheading12) |
| &nbsp; | Todos los servicios |Herramientas |[¿Usa la versión más reciente de las herramientas y bibliotecas de cliente proporcionadas por Microsoft?](#subheading13) |
| &nbsp; | Todos los servicios |Reintentos |[¿Usa una directiva de reintentos de retroceso exponencial para errores de limitación y tiempos de espera?](#subheading14) |
| &nbsp; | Todos los servicios |Reintentos |[¿Evita su aplicación reintentos para errores que no se pueden reintentar?](#subheading15) |
| &nbsp; | Blobs |Objetivos de escalabilidad |[¿Tiene un gran número de clientes que acceden simultáneamente a un único objeto?](#subheading46) |
| &nbsp; | Blobs |Objetivos de escalabilidad |[¿Permanece su aplicación dentro del ancho de banda o de los objetivos de escalabilidad de las operaciones para un solo blob?](#subheading16) |
| &nbsp; | Blobs |Copia de blobs |[¿Copia blobs de una manera eficiente?](#subheading17) |
| &nbsp; | Blobs |Copia de blobs |[¿Usa AzCopy para copias de blobs en masa?](#subheading18) |
| &nbsp; | Blobs |Copia de blobs |[¿Usa Microsoft Azure Import/Export para transferir grandes volúmenes de datos?](#subheading19) |
| &nbsp; | Blobs |Usar metadatos |[¿Almacena metadatos usados frecuentemente acerca de blobs en los metadatos de estos?](#subheading20) |
| &nbsp; | Blobs |Carga rápida |[Cuando intenta cargar un blob rápidamente, ¿carga blobs en paralelo?](#subheading21) |
| &nbsp; | Blobs |Carga rápida |[Cuando intenta cargar muchos blobs rápidamente, ¿carga blobs en paralelo?](#subheading22) |
| &nbsp; | Blobs |Tipo de blob correcto |[¿Usa blobs en páginas o blobs en bloques cuando es apropiado?](#subheading23) |
| &nbsp; | Tablas |Objetivos de escalabilidad |[¿Se aproxima a los objetivos de escalabilidad para entidades por segundo?](#subheading24) |
| &nbsp; | Tablas |Configuración |[¿Usa JSON para sus solicitudes de tabla?](#subheading25) |
| &nbsp; | Tablas |Configuración |[¿Ha desactivado el algoritmo de Nagle para mejorar el rendimiento de solicitudes pequeñas?](#subheading26) |
| &nbsp; | Tablas |Tablas y particiones |[¿Ha particionado sus datos correctamente?](#subheading27) |
| &nbsp; | Tablas |Particiones calientes |[¿Evita los patrones Solo anexar y Solo anteponer?](#subheading28) |
| &nbsp; | Tablas |Particiones calientes |[¿Se despliegan sus inserciones y actualizaciones por muchas particiones?](#subheading29) |
| &nbsp; | Tablas |Ámbito de las consultas |[¿Ha diseñado su esquema para permitir el uso de consultas puntuales en la mayoría de los casos y el uso de consultas de tabla con moderación?](#subheading30) |
| &nbsp; | Tablas |Densidad de las consultas |[¿Normalmente sus consultas solamente examinan y devuelven filas que usará su aplicación?](#subheading31) |
| &nbsp; | Tablas |Limitación de datos devueltos |[¿Usa filtros para evitar la devolución de entidades que no se necesitan?](#subheading32) |
| &nbsp; | Tablas |Limitación de datos devueltos |[¿Usa proyección para evitar la devolución de propiedades que no se necesitan?](#subheading33) |
| &nbsp; | Tablas |Desnormalización |[¿Ha desnormalizado los datos de forma que evite consultas ineficientes o varias solicitudes de lectura cuando intenta obtener datos?](#subheading34) |
| &nbsp; | Tablas |Inserción, actualización y eliminación |[¿Procesa por lotes solicitudes que necesitan ser transaccionales o se pueden realizar al mismo tiempo para reducir recorridos de ida y vuelta?](#subheading35) |
| &nbsp; | Tablas |Inserción, actualización y eliminación |[¿Evita la recuperación de una entidad simplemente para determinar si llama a la inserción o a la actualización?](#subheading36) |
| &nbsp; | Tablas |Inserción, actualización y eliminación |[¿Ha pensado en almacenar series de datos que se recuperarán frecuentemente de forma conjunta en una sola entidad como propiedades en lugar de varias entidades?](#subheading37) |
| &nbsp; | Tablas |Inserción, actualización y eliminación |[Para entidades que siempre se recuperarán conjuntamente y que se pueden escribir en lotes (por ejemplo, datos de serie de temporales), ¿ha pensado en usar blobs en lugar de tablas?](#subheading38) |
| &nbsp; | Colas |Objetivos de escalabilidad |[¿Se aproxima a los objetivos de escalabilidad para mensajes por segundo?](#subheading39) |
| &nbsp; | Colas |Configuración |[¿Ha desactivado el algoritmo de Nagle para mejorar el rendimiento de solicitudes pequeñas?](#subheading40) |
| &nbsp; | Colas |Tamaño de los mensajes |[¿Son sus mensajes compactos para mejorar el rendimiento de la cola?](#subheading41) |
| &nbsp; | Colas |Recuperación en masa |[¿Recupera varios mensajes en una sola operación "Get"?](#subheading42) |
| &nbsp; | Colas |Frecuencia de sondeo |[¿Realiza sondeos con la suficiente frecuencia para reducir la latencia percibida de su aplicación?](#subheading43) |
| &nbsp; | Colas |Actualizar mensaje |[¿Usa UpdateMessage para almacenar el progreso en mensajes de procesamiento evitando tener que volver a procesar todo el mensaje si se produce un error?](#subheading44) |
| &nbsp; | Colas |Arquitectura |[¿Usa colas para hacer que toda su aplicación sea más escalable manteniendo cargas de trabajo de ejecución prolongada fuera de la ruta de acceso crítica y escala después de forma independiente?](#subheading45) |

## <a name="allservices"></a>Todos los servicios
En esta sección se enumeran las prácticas probadas aplicables al uso de cualquier servicio de Azure Storage (Blob, Table, Queue o Files).  

### <a name="subheading1"></a>Objetivos de escalabilidad
Cada uno de los servicios de Azure Storage tiene objetivos de escalabilidad en lo que se refiere a capacidad (GB), tasa de transacciones y ancho de banda. Si su aplicación se aproxima o supera cualquiera de estos objetivos de escalabilidad, puede encontrar un aumento en la limitación o latencias de transacción. Cuando un servicio de Storage limita su aplicación, comienza a devolver códigos de error “503 Servidor ocupado” o “500 Tiempo de espera de la operación” en algunas transacciones de Storage. En esta sección se describen tanto el enfoque general para abordar los objetivos de escalabilidad como los objetivos de escalabilidad de ancho de banda en particular. En secciones posteriores, que abordan cada uno de los servicios de Storage, se describen objetivos de escalabilidad en el contexto de ese servicio específico:  

* [Ancho de banda de blob y solicitudes por segundo](#subheading16)
* [Entidades de tabla por segundo](#subheading24)
* [Mensajes de cola por segundo](#subheading39)  

#### <a name="sub1bandwidth"></a>Objetivo de escalabilidad de ancho de banda para todos los servicios
En el momento de escribir estas líneas, los objetivos de ancho de banda en EE. UU. para una cuenta de almacenamiento con redundancia geográfica (GRS) son 10 gigabits por segundo (Gbps) para entrada (datos enviados a la cuenta de almacenamiento) y 20 Gbps para salida (datos enviados desde la cuenta de almacenamiento). Para una cuenta de almacenamiento con redundancia local (LRS), los límites son mayores: 20 Gbps para entrada y 30 Gbps para salida.  Los límites de ancho de banda internacionales pueden ser menores y puede encontrarlos en nuestra [página de objetivos de escalabilidad](http://msdn.microsoft.com/library/azure/dn249410.aspx).  Para más información acerca de las opciones de redundancia de almacenamiento, consulte los vínculos de [Recursos útiles](#sub1useful) que se indican a continuación.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>¿Qué debo hacer cuando un objetivo de escalabilidad está próximo?
Si su aplicación se aproxima a los objetivos de escalabilidad para una sola cuenta de almacenamiento, plantéese la adopción de uno de los siguientes enfoques:  

* Reconsidere la carga de trabajo que hace que su aplicación se aproxime al objetivo de escalabilidad o lo supere. ¿Puede designarla de forma diferente para que use menos ancho de banda o capacidad, o menos transacciones?
* Si una aplicación debe superar uno de los objetivos de escalabilidad, debe crear varias cuentas de almacenamiento y particionar los datos de su aplicación entre dichas cuentas. Si usa este patrón, entonces debe asegurarse de designar la aplicación de forma que pueda agregar más cuentas de almacenamiento en el futuro para equilibrio de carga. En el momento escribir estas líneas, cada suscripción de Azure puede tener hasta 100 cuentas de almacenamiento.  Las cuentas de almacenamiento tampoco tienen ningún costo que no sea el de su uso en términos de datos almacenados, transacciones realizadas o datos transferidos.
* Si su aplicación alcanza los objetivos de ancho de banda, plantéese comprimir los datos contenidos en el cliente para reducir el ancho de banda requerido para enviar los datos al servicio de almacenamiento.  Tenga en cuenta que aunque esto puede ahorrar ancho de banda y mejorar el rendimiento de la red, también puede tener algunos impactos negativos.  Debe evaluar el impacto de rendimiento de todo esto debido a los requisitos de procesamiento adicionales para comprimir y descomprimir datos en el cliente. Además, el almacenamiento de datos comprimidos puede dificultar la solución de problemas porque podría ser más complejo ver datos almacenados usando herramientas estándar.
* Si su aplicación alcanza los objetivos de escalabilidad, asegúrese de usar un retroceso exponencial para reintentos (consulte [Reintentos](#subheading14)).  Es mejor asegurarse de que nunca se acerca a los objetivos de escalabilidad (para lo que debe usar uno de los métodos anteriores), pero esto garantizará que la aplicación deja de realizar reintentos constantes, ya que ello empeora la limitación.  

#### <a name="useful-resources"></a>Recursos útiles
Los siguientes vínculos proporcionan detalles adicionales sobre objetivos de escalabilidad:

* Consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md) para obtener información sobre los objetivos de escalabilidad.
* Consulte [Replicación de Azure Storage](storage-redundancy.md) y la entrada de blog [Azure Storage Redundancy Options and Read Access Geo Redundant Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) (Opciones de redundancia de Azure Storage y almacenamiento con redundancia geográfica con acceso de lectura) para obtener información sobre las opciones de redundancia de almacenamiento.
* Para obtener información actual sobre los precios de los servicios de Azure, consulte [Precios de Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Convención de nomenclatura de particiones
Azure Storage usa un esquema de particiones basado en intervalo para escalar y equilibrar la carga del sistema. La clave de partición se usa para particionar datos en intervalos con una carga equilibrada en el sistema. Esto significa que convenciones de nomenclatura como la ordenación léxica (p. ej., msftpayroll, msftperformance, msftemployees, etc.) o el uso de marcas de tiempo (log20160101, log20160102, log20160102, etc.) se prestarán a las particiones potencialmente colocadas en el mismo servidor de particiones, hasta que una operación de equilibrio de carga las divida en intervalos más pequeños. Por ejemplo, un servidor único puede atender a todos los blobs de un contenedor hasta que la carga de estos requiera un reequilibrado adicional de los intervalos de partición. De forma similar, un servidor único puede atender a un grupo de cuentas ligeramente cargadas con sus nombres organizados en orden léxico hasta que la carga de una o de todas ellas requiera su división en varios servidores de particiones. Cada una de las operaciones de equilibrio de carga puede afectar a la latencia de las llamadas de almacenamiento realizadas durante la operación. La capacidad del sistema para controlar un aumento súbito del tráfico en una partición la limita la escalabilidad de un servidor de una sola partición hasta que la operación de equilibrio de carga se inicia y se vuelve a equilibrar el intervalo de claves de la partición.  

Puede seguir algunas prácticas recomendadas para reducir la frecuencia de dichas operaciones.  

* Examine estrechamente la convención de nomenclatura que usa para las cuentas, contenedores, blobs, tablas y colas. Considere la posibilidad de prefijar nombres de cuenta con un hash de 3 dígitos con la función hash que mejor se adapte a sus necesidades.  
* Si organiza sus datos mediante marcas de tiempo o identificadores numéricos, debe asegurarse de no usar patrones de tráfico Solo anexar (o Solo anteponer). Estos patrones no son adecuados para un sistema de creación de particiones basado en intervalo y podrían dar lugar a todo el tráfico que se dirige a una sola partición e impide un eficaz equilibrio de carga del sistema. Por ejemplo, si tiene operaciones diarias que usan un objeto blob con una marca de tiempo como aaaammdd, todo el tráfico de esa operación diaria se dirige a un solo objeto atendido por un solo servidor de particiones. Compruebe si los límites por blob y por partición cubren sus necesidades y considere la posibilidad de dividir esta operación en varios blobs según sea necesario. De forma similar, si almacena datos de series temporales en sus tablas, todo el tráfico podría dirigirse a la última parte del espacio de nombres clave. Si tiene que usar marcas de tiempo o identificadores numéricos, prefíjelos con un hash de 3 dígitos o, en el caso de las marcas de tiempo, prefije la parte referente a los segundos de la hora como, por ejemplo, ssaaaammdd. Si se realizan operaciones de enumeración y consulta de manera habitual, elija una función hash que limite su número de consultas. En otros casos, un prefijo aleatorio puede ser suficiente.  
* Para obtener información adicional sobre el esquema de particiones usado en Azure Storage, lea el documento de SOSP [aquí](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Redes
Si bien las llamadas de API son importantes, a menudo las restricciones de red físicas de la aplicación tienen un impacto significativo en el rendimiento. A continuación se describen algunas de las limitaciones que pueden encontrar los usuarios.  

#### <a name="client-network-capability"></a>Capacidad de red del cliente
##### <a name="subheading2"></a>Capacidad de proceso
Para el ancho de banda, el problema suele residir en las capacidades del cliente. Por ejemplo, mientras una sola cuenta de almacenamiento puede controlar 10 Gbps, o más, de entrada (consulte [destinos de escalabilidad de ancho de banda](#sub1bandwidth)), la velocidad de la red en una instancia del rol de trabajo de Azure “pequeña” solo puede controlar aproximadamente 100 Mbps. Las instancias de Azure mayores tienen NIC con mayor capacidad, por lo que debe plantearse la posibilidad de usar una instancia mayor o más máquinas virtuales si necesita aumentar los límites de red de una sola máquina. Si accede a un servicio de Storage desde una aplicación local, se aplica la misma regla: comprender las funcionalidades de red del dispositivo cliente y la conectividad de red con la ubicación de Azure Storage y, bien mejorarlas según sea necesario o diseñar la aplicación para que funcione según sus funcionalidades.  

##### <a name="subheading3"></a>Calidad del enlace
Como con cualquier uso de red, sea consciente de que las condiciones de la red dan lugar a errores y la pérdida de paquetes reducirá el rendimiento efectivo.  El uso de WireShark o NetMon puede ayudar a diagnosticar este problema.  

##### <a name="useful-resources"></a>Recursos útiles
Para obtener información sobre los tamaños de máquina virtual y el ancho de banda asignado, consulte los artículos sobre [Tamaños de las máquinas virtuales Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Tamaños de las máquinas virtuales Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Ubicación
En cualquier entorno distribuido, la ubicación del cliente cerca del servidor ofrece el mejor rendimiento. Para acceder a Azure Storage con la mínima latencia, la mejor ubicación para el cliente es dentro de la misma región de Azure. Por ejemplo, si tiene un sitio web Azure que usa Azure Storage, debe colocar ambos dentro de una sola región (por ejemplo Oeste de EE. UU. o Sudeste de Asia). Esto reduce la latencia y el costo (en el momento de escribir estas líneas, el uso de ancho de banda dentro de una sola región es gratuito).  

Si las aplicaciones cliente no están hospedadas dentro de Azure (como por ejemplo aplicaciones de dispositivos móviles o servicios empresariales locales), una vez más la ubicación de la cuenta de almacenamiento en una región próxima a los dispositivos a los que obtendrá acceso, generalmente reducirá la latencia. Si los clientes están muy distribuidos (por ejemplo algunos en Norteamérica y otros en Europa), debe plantearse el uso de varias cuentas de almacenamiento: una ubicada en una región de Norteamérica y otra en una región de Europa. Esto ayudará a reducir la latencia para los usuarios de ambas regiones. Este enfoque normalmente es más sencillo de implementar si los datos que almacena la aplicación son específicos de usuarios individuales y no se requiere la replicación de datos entre cuentas de almacenamiento.  Para distribución de contenido amplio, se recomienda una red CDN (consulte la siguiente sección para obtener más detalles).  

### <a name="subheading5"></a>Distribución de contenido
Algunas veces, una aplicación necesita servir el mismo contenido a muchos usuarios (por ejemplo, un vídeo de demostración de producto o usado en la página principal de un sitio web) ubicados bien en la misma región, bien en varias regiones. En este escenario, debe usar una red de entrega de contenido (CDN), como por ejemplo Azure CDN, y dicha red CDN debe usar Azure Storage como origen de los datos. A diferencia de la cuenta de Azure Storage que existe en una sola región y que no puede entregar contenido con baja latencia a otras regiones, la red CDN de Azure usa servidores en varios centros de datos alrededor del mundo. Además, una red CDN normalmente puede admitir límites de salida mucho más altos que una sola cuenta de almacenamiento.  

Para obtener más información acerca de la red CDN de Azure, consulte [Red CDN de Azure](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Uso de SAS y CORS
Cuando hace falta autorizar código, como por ejemplo JavaScript, en el explorador web de un usuario o en una aplicación para teléfonos móviles para acceder a los datos en Azure Storage, un enfoque es usar una aplicación de rol web como proxy: el dispositivo del usuario se autentica con el rol web que, a su vez, se autentica con el servicio de Storage. De esta forma, puede evitar la exposición de sus claves de cuenta de almacenamiento en dispositivos no seguros. Sin embargo, este enfoque coloca una gran sobrecarga sobre el rol web, ya que todos los datos transferidos entre el dispositivo del usuario y el servicio de Storage deben pasar a través de dicho rol. Puede evitar el uso de un rol web como un proxy para el servicio de almacenamiento usando firmas de acceso compartido (SAS), algunas veces junto con encabezados de uso compartido de recursos entre orígenes (CORS). Mediante SAS se puede permitir que el dispositivo del usuario realice solicitudes directamente a un servicio de Storage por medio de un token de acceso limitado. Por ejemplo, si un usuario desea cargar una foto en su aplicación, su rol web puede generar y enviar al dispositivo del usuario un token SAS que conceda el permiso para escribir en un blob o contenedor concretos durante los próximos 30 minutos (tras los cuales el token SAS expira).

Normalmente, un explorador no permitirá que haya código JavaScript en una página hospedada por un sitio web en un dominio que realice operaciones específicas, como “PUT”, en otro dominio. Por ejemplo, si hospeda un rol web en “contosomarketing.cloudapp.net” y desea usar el código JavaScript del cliente para cargar un blob en la cuenta de almacenamiento en “contosoproducts.blob.core.windows.net”, la “misma directiva de origen” del explorador prohibirá esta operación. CORS es una característica de explorador que permite al dominio de destino (en este caso la cuenta de almacenamiento) comunicar al explorador en el que confía solicitudes que se originan en el dominio de origen (en este caso el rol web).  

Estas dos tecnologías pueden ayudarle a evitar una carga innecesaria (y cuellos de botella) en la aplicación web.  

#### <a name="useful-resources"></a>Recursos útiles
Para obtener más información acerca de SAS, consulte [Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido](../storage-dotnet-shared-access-signature-part-1.md).  

Para obtener más información sobre CORS, vea [Compatibilidad del Uso compartido de recursos entre orígenes (CORS) para los servicios de Azure Storage](http://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Almacenamiento en caché
#### <a name="subheading7"></a>Obtención de datos
En general, obtener datos de un servicio una vez es mejor que obtenerlos dos veces. Considere el ejemplo de una aplicación web de MVC que se ejecuta en un rol web que ya haya recuperado un blob de 50 MB del servicio de almacenamiento para servir como contenido al usuario. La aplicación podría recuperar después ese mismo blob cada vez que un usuario lo solicitara o podría almacenarlo en caché localmente en el disco y volver a usar la versión almacenada en caché para posteriores solicitudes del usuario. Además, cada vez que un usuario solicita los datos, la aplicación podría emitir un comando GET con un encabezado condicional con la hora de modificación, lo que evitaría tener que obtener todo el blob si no se ha modificado. Puede aplicar este mismo patrón para trabajar con entidades de tabla.  

En algunos casos, puede decidir que su aplicación pueda asumir que el blob sigue siendo válido durante un corto período después de recuperarlo y que durante dicho período la aplicación no necesite comprobar si el blob se modificó.

Los datos de configuración y búsqueda, así como otros datos que siempre usa la aplicación, son magníficos candidatos para el almacenamiento en caché.  

Para ver un ejemplo de cómo obtener las propiedades de un blob para detectar la fecha de la última modificación mediante .NET, consulte [Establecer y recuperar propiedades y metadatos](../blobs/storage-properties-metadata.md). Para obtener más información sobre las descargas condicionales, consulte [Actualización condicional de una copia local de un blob](http://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Carga de datos en lotes
En algunos escenarios de aplicación, puede agregar datos localmente y, a continuación, cargarlos periódicamente en un lote en lugar de cargar cada parte de datos inmediatamente. Por ejemplo, una aplicación web podría guardar un archivo de registro de actividades: la aplicación podría cargar detalles de cada actividad como sucede como entidad de tabla (lo que requiere muchas operaciones de almacenamiento), o bien podría guardar detalles de la actividad en un archivo de registro local y, a continuación, cargar todos los detalles de actividad periódicamente como un archivo delimitado en un blob. Si cada entrada de registro tiene un tamaño de 1 KB, puede cargar miles en una sola transacción “Put Blob” (puede cargar un blob de hasta 64 MB en una sola transacción). Por supuesto, si se bloquea el equipo local antes de la carga, podría perder algunos datos de registro: el diseño del desarrollador de aplicaciones debe tener en cuenta los errores de carga o dispositivo de cliente.  Si los datos de actividad necesitan descargarse para períodos de tiempo (no simplemente una actividad), se recomiendan los blobs sobre las tablas.

### <a name="net-configuration"></a>Configuración .NET
Si usa .NET Framework, esta sección enumera varias configuraciones rápidas que puede usar para realizar mejoras de rendimiento significativas.  Si usa otros lenguajes, compruebe si se aplican conceptos similares en el lenguaje elegido.  

#### <a name="subheading9"></a>Aumento del límite de conexiones predeterminado
En .NET, el siguiente código aumenta el límite de conexiones predeterminado (que normalmente es 2 en un entorno cliente o 10 en un entorno servidor) a 100. Normalmente, debe establecer el valor en aproximadamente el número de subprocesos usados por su aplicación.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Debe establecer el límite de conexiones antes de abrir cualquier conexión.  

En el caso de otros lenguajes de programación, consulte la documentación del lenguaje en cuestión para determinar cómo establecer el límite de conexiones.  

Para obtener más información, vea la entrada de blog [Servicios web: conexiones simultáneas](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Aumento de los subprocesos mínimos ThreadPool si se usa código sincrónico con tareas asincrónicas
Este código aumentará los subprocesos mínimos del grupo de subprocesos:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obtener más información, consulte [Método ThreadPool.SetMinThreads](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Aprovechamiento de la recolección de elementos no usados de .NET 4.5
Use .NET 4.5 o posterior para que la aplicación cliente aproveche las mejoras de rendimiento en la recolección de elementos no usados del servidor.

Para obtener más información, consulte el artículo [Información general de las mejoras de rendimiento en .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Paralelismo no vinculado
Aunque el paralelismo puede ser magnífico para el rendimiento, tenga cuidado cuando use paralelismo no vinculado (sin límite en el número de subprocesos y/o solicitudes paralelas) para cargar o descargar datos y cuando use varios trabajadores para obtener acceso a varias particiones (contenedores, colas o particiones de tabla) en la misma cuenta de almacenamiento o para obtener acceso a varios elementos de la misma partición. Si el paralelismo no está vinculado, la aplicación puede superar las funcionalidades del dispositivo cliente o los objetivos de escalabilidad de la cuenta de almacenamiento, lo que puede provocar a latencias más largas y limitaciones.  

### <a name="subheading13"></a>Herramientas y bibliotecas de cliente de Storage
Use siempre las herramientas y bibliotecas de cliente más recientes proporcionadas por Microsoft. En el momento de escribir estas líneas, hay bibliotecas de cliente disponibles para .NET, Windows Phone, Windows Runtime, Java y C ++, así como bibliotecas de vista previa para otros lenguajes. Además, Microsoft ha lanzado cmdlets de PowerShell y comandos de la CLI de Azure para trabajar con Azure Storage. Microsoft desarrolla de forma activa estas herramientas pensando en el rendimiento, manteniéndolas actualizadas con las versiones de servicio más recientes, y garantiza que administran muchas de las prácticas de rendimiento probadas internamente.  

### <a name="retries"></a>Reintentos
#### <a name="subheading14"></a>Limitación y servidor ocupado
En algunos casos, el servicio de Storage puede limitar su aplicación o simplemente puede no ser capaz de atender la solicitud debido a alguna condición transitoria y devolver un mensaje “503 Servidor ocupado” o “500 Se agotó el tiempo de espera”.  Esto puede ocurrir si la aplicación se está aproximando a cualquiera de los objetivos de escalabilidad o si el sistema está equilibrando los datos particionados para permitir un mayor rendimiento.  Normalmente, la aplicación cliente debe volver a intentar la operación que provoca el error: al volver a enviar la solicitud, puede que se lleve a cabo correctamente. Sin embargo, si el servicio de almacenamiento está limitando la aplicación porque está superando los objetivos de escalabilidad, o incluso si el servicio no pudo atender a la solicitud por alguna otra razón, lo intentos agresivos normalmente empeorarán el problema. Por esta razón, debe usar un retroceso exponencial (las bibliotecas de cliente se comportan así de forma predeterminada). Por ejemplo, su aplicación puede llevar a cabo los reintentos después de 2 segundos, luego 4 segundos, después 10 segundos, luego 30 segundos y, después, renunciar. Este comportamiento da lugar a una reducción significativa de la carga de la aplicación en el servicio en lugar de agravar los problemas.  

Tenga en cuenta que los errores de conectividad se pueden reintentar inmediatamente porque no se derivan de la limitación y se espera que sean transitorios.  

#### <a name="subheading15"></a>Errores que no se pueden reintentar
Las bibliotecas de cliente saben qué errores se pueden reintentar y cuáles no. Sin embargo, si está escribiendo su propio código en la API de REST de almacenamiento, recuerde que hay algunos errores que no se deben reintentar: por ejemplo, una respuesta 400 (solicitud incorrecta) indica que la aplicación cliente envió una solicitud que no se pudo procesar porque no tenía el formato esperado. El reenvío de esta solicitud dará lugar a la misma respuesta cada vez, por lo que no tiene sentido reintentarla. Si escribe su propio código contra la API REST de almacenamiento, sepa lo que significan los códigos de error y la forma adecuada de reintentar (o no) cada uno de ellos.  

#### <a name="useful-resources"></a>Recursos útiles
Para más información acerca de los códigos de error de almacenamiento, consulte [Códigos de estado y de error](http://msdn.microsoft.com/library/azure/dd179382.aspx) en el sitio web de Microsoft Azure.  

## <a name="blobs"></a>Blobs
Además de las prácticas probadas para [Todos los servicios](#allservices) descritas anteriormente, las siguientes prácticas probadas se aplican específicamente a Blob service.  

### <a name="blob-specific-scalability-targets"></a>Objetivos de escalabilidad específicos de blob
#### <a name="subheading46"></a>Acceso simultáneo a un único objeto por parte de varios clientes
Si tiene un gran número de clientes que acceden de forma simultánea a un único objeto, debe considerar los objetivos de escalabilidad por objeto y por cuenta de almacenamiento. El número exacto de los clientes que pueden tener acceso a un único objeto variará en función de factores como el número de clientes que soliciten el objeto simultáneamente, el tamaño del objeto o las condiciones de la red, entre otros.

Si el objeto se puede distribuir a través de una red CDN, como imágenes o vídeos procedentes de un sitio web, deberá utilizar una CDN. Consulte [aquí](#subheading5).

En otros escenarios, como las simulaciones científicas, donde los datos son confidenciales, tiene dos opciones. La primera es escalonar el acceso de la carga de trabajo para que se acceda al objeto a lo largo de un período, en lugar de hacerlo simultáneamente. Como alternativa, puede copiar el objeto temporalmente en varias cuentas de almacenamiento, lo que aumenta el número total de IOPS por objeto y entre cuentas de almacenamiento. En algunas pruebas descubrimos que unas 25 VM podrían descargar simultáneamente un blob de 100 GB en paralelo (cada VM ha puesto en paralelo la descarga mediante 32 subprocesos). Si tiene 100 clientes que necesitan tener acceso al objeto, primero cópielo en una segunda cuenta de almacenamiento y después permita que el primer grupo de 50 VM acceda al primer blob y que el segundo grupo de 50 VM acceda al segundo blob. Los resultados varían según el comportamiento de las aplicaciones, por lo que debe realizar las pruebas durante el diseño. 

#### <a name="subheading16"></a>Ancho de banda y operaciones por blob
Puede realizar operaciones de lectura y escritura en un solo blob a una velocidad máxima de 60 MB/segundos (lo que equivale a 480 Mbps aproximadamente, lo cual supera las capacidades de muchas redes de cliente, incluida la tarjeta NIC física del dispositivo cliente). Además, un solo blob admite hasta 500 solicitudes por segundo. Si tiene varios clientes que necesitan leer el mismo blob y podría superar estos límites, debe plantearse el uso de una red CDN para distribuir dicho blob.  

Para obtener más información sobre el rendimiento objetivo para blobs, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Copia y movimiento de blobs
#### <a name="subheading17"></a>Copia de blobs
La versión del 12/02/2012 de la API de REST de almacenamiento introdujo la capacidad útil de copiar blobs entre cuentas: una aplicación cliente puede indicar al servicio de almacenamiento que copie un blob desde otro origen (posiblemente, en una cuenta de almacenamiento diferente) y, a continuación, dejar que el servicio realice la copia asincrónicamente. Esto puede reducir significativamente el ancho de banda necesario para la aplicación cuando migra datos desde otras cuentas de almacenamiento porque no necesita descargar y cargar los datos.  

Una consideración, sin embargo, es que, cuando realiza copias entre cuentas de almacenamiento, no hay una garantía de tiempo sobre cuándo se completará la copia. Si la aplicación necesita completar una copia de blobs rápidamente bajo su control, puede ser mejor copiar dicho blob descargándolo a la máquina virtual y, a continuación, cargándolo en el destino.  Para conseguir previsibilidad completa en esa situación, asegúrese de que la copia se realiza por una máquina virtual que se ejecuta en la misma región de Azure ya que, de lo contrario, las condiciones de la red pueden (y probablemente lo harán) afectar al rendimiento de la copia.  Además, puede supervisar el progreso de una copia asincrónica mediante programación.  

Tenga en cuenta que las copias dentro de la misma cuenta de almacenamiento, por lo general, se completan rápidamente.  

Para obtener más información, consulte [Copia de blobs](http://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Uso de AzCopy
El equipo de Azure Storage ha lanzado "AzCopy", una herramienta de línea de comandos diseñada para facilitar la transferencia masiva de muchos blobs a, desde y entre cuentas de almacenamiento.  Esta herramienta está optimizada para este escenario y puede lograr altas tasas de transferencia.  Su uso es muy recomendable para escenarios de carga, descarga y copia en masa. Para obtener más información, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Servicio de importación y exportación de Azure
Para volúmenes muy grandes de datos (más de 1 TB), Azure Storage ofrece el servicio Import/Export, que permite realizar operaciones de carga y descarga desde Blob Storage enviando unidades de disco duro.  Puede poner sus datos en una unidad de disco duro y enviarla a Microsoft para cargarlos o enviar una unidad de disco duro vacía a Microsoft para descargar datos.  Para más información, consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](../storage-import-export-service.md).  Esto puede ser mucho más eficiente que cargar y descargar este volumen de datos a través de la red.  

### <a name="subheading20"></a>Uso de metadatos
El Blob service admite solicitudes de encabezado, que pueden incluir metadatos acerca del blob. Por ejemplo, si su aplicación necesita los datos EXIF fuera de una foto, podría recuperar la foto y extraerlos. Para ahorrar ancho de banda y mejorar el rendimiento, la aplicación puede almacenar los datos de EXIF en los metadatos del blob cuando la aplicación haya cargado la foto: posteriormente, puede recuperar los datos de EXIF de los metadatos mediante una única solicitud HEAD, lo que permite ahorrar mucho ancho de banda y el tiempo de procesamiento necesario para extraer los datos de EXIF cada vez que se lee el blob. Esto sería útil en escenarios donde solamente necesita los metadatos y no el contenido completo de un blob.  Tenga en cuenta que solamente se pueden almacenar 8 KB de metadatos por blob (el servicio no aceptará una solicitud para almacenar más que eso), por lo que si los datos no caben en ese tamaño, no podrá usar este enfoque.  

Para ver un ejemplo de cómo obtener los metadatos de un blob mediante .NET, consulte [Establecer y recuperar propiedades y metadatos](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Carga rápida
Para cargar blobs rápidamente, la primera pregunta es: ¿carga un blob o muchos?  Utilice las instrucciones siguientes para determinar el método correcto que va a utilizar dependiendo del escenario.  

#### <a name="subheading21"></a>Carga de un blob grande rápidamente
Para cargar un solo blob grande rápidamente, la aplicación cliente debe cargar sus bloques o páginas en paralelo (teniendo en cuenta los objetivos de escalabilidad para blobs individuales y la cuenta de almacenamiento como un todo).  Tenga en cuenta que las bibliotecas del cliente de Storage RTM proporcionadas por Microsoft (.NET y Java) tienen la capacidad de hacer esto.  Para cada una de las bibliotecas, use el objeto o propiedad que se especifica a continuación para establecer el nivel de simultaneidad:  

* .NET: establezca ParallelOperationThreadCount en un objeto BlobRequestOptions para usar.
* Java/Android: use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: use parallelOperationThreadCount en las opciones de solicitud o Blob service.
* C++: use el método blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Carga de muchos blobs rápidamente
Para cargar muchos blobs rápidamente, cárguelos en paralelo. Este método es más rápido que cargar blobs de uno en uno con cargas de bloque paralelas porque distribuye la carga entre varias particiones del servicio Storage. Un solo blob únicamente admite un rendimiento de 60 MB/segundo (aproximadamente 480 Mbps). En el momento de escribir estas líneas, una cuenta LRS con sede en Estados Unidos admite entradas de hasta 20 Gbps, que es mucho más que la capacidad de proceso admitida por un blob individual.  [AzCopy](#subheading18) realiza cargas en paralelo de forma predeterminada y se recomienda para este escenario.  

### <a name="subheading23"></a>Elección del tipo correcto de blob
Azure Storage admite dos tipos de blobs: *de página* y *de bloque*. Para un escenario de uso dado, el tipo de blob que elija afectará al rendimiento y escalabilidad de la solución. Los blobs en bloques son apropiados si desea cargar eficazmente grandes cantidades de datos: por ejemplo, una aplicación cliente puede necesitar cargar fotos o vídeos a Blob Storage. Los blobs de página son apropiados si la aplicación necesita realizar operaciones de escritura aleatorias en los datos: por ejemplo, los discos duros virtuales de Azure se almacenan como blobs de página.  

Para más información, consulte [Descripción de los blobs en bloques, en anexos y en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tablas
Además de las prácticas probadas para [Todos los servicios](#allservices) descritas anteriormente, las siguientes prácticas probadas se aplican específicamente a Table service.  

### <a name="subheading24"></a>Objetivos de escalabilidad específicos de tabla
Además de las limitaciones de ancho de banda de una cuenta de almacenamiento completa, las tablas tienen el siguiente límite de escalabilidad específico.  Tenga en cuenta que el sistema equilibrará la carga a medida que el tráfico aumente, pero si este tráfico tiene ráfagas repentinas, no podrá obtener este volumen de rendimiento inmediatamente.  Si su patrón tiene ráfagas, es de esperar que se produzcan limitaciones y/o tiempos de espera durante dichas ráfagas, ya que el servicio Storage equilibrará la carga automáticamente fuera de la tabla.  El aumento lento generalmente tiene mejores resultados ya que proporciona al sistema tiempo para equilibrar la carga apropiadamente.  

#### <a name="entities-per-second-account"></a>Entidades por segundo (cuenta)
El límite de escalabilidad para el acceso a tablas es de hasta 20.000 unidades (1 KB cada una) por segundo para una cuenta.  En general, cada entidad que se inserta, actualiza, elimina o examina, cuenta para este objetivo.  Por tanto, una inserción por lotes que contiene 100 entidades contaría como 100 entidades.  Una consulta que examina 1.000 y devuelve 5, contaría como 1.000 entidades.  

#### <a name="entities-per-second-partition"></a>Entidades por segundo (partición)
Dentro de una sola partición, el objetivo de escalabilidad para obtener acceso a las tablas es de 2.000 entidades (1 KB cada una) por segundo, usando el mismo recuento descrito en la sección anterior.  

### <a name="configuration"></a>Configuración
En esta sección se enumeran varias configuraciones rápidas que puede usar para realizar mejoras de rendimiento significativas en Table service:  

#### <a name="subheading25"></a>Uso de JSON
A partir de la versión del 15 de agosto de 2013 del servicio Storage, Table service admite el uso de JSON en lugar del formato AtomPub basado en XML para transferir datos de tabla. Esto puede reducir los tamaños de carga hasta en un 75 % y puede mejorar significativamente el rendimiento de la aplicación.

Para más información, consulte la publicación [Tablas de Microsoft Azure: introducción a JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) y [Payload Format for Table Service Operations](http://msdn.microsoft.com/library/azure/dn535600.aspx) (Formato de carga para las operaciones de Table service).

#### <a name="subheading26"></a>Desactivación de Nagle
El algoritmo de Nagle está ampliamente implementado en redes TCP/IP como medio de mejorar el rendimiento de la red. Sin embargo, no es óptimo en todas las situaciones (como por ejemplo en entornos altamente interactivos). En el caso de Azure Storage, el algoritmo de Nagle tiene un impacto negativo en el rendimiento de solicitudes que se realizan a los servicios Table y Queue; si es posible, debe deshabilitarlo.  

Para más información, consulte la entrada del blog para ver la entrada del blog [Nagle's Algorithm is Not Friendly towards Small Requests](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) (El algoritmo de Nagle no es idóneo para pequeñas solicitudes), donde se explican los motivos por los que el algoritmo de Nagle no interactúa bien con solicitudes de tabla y cola, y cómo deshabilitarlo en una aplicación cliente.  

### <a name="schema"></a>SCHEMA (ESQUEMA)
La forma de representar los datos y realizar consultas en los mismos es el factor más importante por sí mismo que afecta al rendimiento de Table service. Aunque cada aplicación es diferente, en esta sección se describen algunas prácticas probadas generales relacionadas con:  

* Diseño de tablas
* Consultas eficientes
* Actualizaciones de datos eficientes  

#### <a name="subheading27"></a>Tablas y particiones
Las tablas se dividen en dos particiones. Cada entidad almacenada en una partición comparte la misma clave de partición y tiene una clave de fila única para identificarla dentro de esa partición. Las particiones proporcionan ventajas pero también presentan limitaciones de escalabilidad.  

* Ventajas: puede actualizar entidades de la misma partición en una sola transacción por lotes atómica que contenga hasta 100 operaciones de almacenamiento independientes (límite de tamaño total de 4 MB). Suponiendo que se recupera el mismo número de entidades, también puede consultar datos dentro de una sola partición más eficientemente que los datos que se extienden por particiones (siga leyendo para conocer más recomendaciones sobre la consulta de datos de tabla).
* Limite de escalabilidad: no se puede realizar el equilibrio de carga en el acceso a entidades almacenadas en una sola partición porque las particiones admiten transacciones por lotes atómicas. Por esta razón, el objetivo de escalabilidad para una partición de tabla individual es inferior al de Table service como un todo.  

Debido a estas características de tablas y particiones, debe adoptar los siguientes principios de diseño:  

* Los datos que su aplicación cliente actualiza o consulta frecuentemente en la misma unidad lógica de trabajo se deben ubicar en la misma partición.  Esto puede ser porque la aplicación agrega escrituras o porque desea aprovechar las operaciones por lotes atómicas.  Asimismo, los datos de una sola partición pueden ser consultados de forma más eficiente en una sola consulta que los datos que se encuentran repartidos por particiones.
* Los datos que la aplicación cliente no inserta, actualiza o consulta en la misma unidad lógica de trabajo (una consulta o actualización por lotes) se deben encontrar en particiones independientes.  Una nota importante es que no hay límite en el número de claves de partición en una sola tabla, por lo que tener millones de claves de partición no es un problema y no afectará al rendimiento.  Por ejemplo, si la aplicación es un sitio web conocido con inicio de sesión de usuario, el uso del identificador de usuario como clave de partición podría ser una buena elección.  

#### <a name="hot-partitions"></a>Particiones calientes
Una partición caliente es aquella que recibe un porcentaje desproporcionado de tráfico en una cuenta y no se puede realizar un equilibrio de carga en ella porque es una sola partición.  En general, las particiones calientes se crean de una de dos formas:  

##### <a name="subheading28"></a>Patrones Solo anexar y Solo anteponer
El patrón "Solo anexar" es aquel en el que todo (o prácticamente todo) el tráfico que llega a una PK dada aumenta y disminuye en función de la hora.  Un ejemplo es si la aplicación usó la fecha actual como clave de partición para datos de registro.  El resultado de esto es que todas las inserciones van a la última partición de la tabla y el sistema no puede equilibrar la carga porque todas las escrituras van al final de la tabla.  Si el volumen del tráfico en esa partición supera el objetivo de escalabilidad de nivel de partición, dará lugar a una limitación.  Es mejor garantizar que el tráfico se envía a varias particiones para habilitar el equilibrio de carga de las solicitudes en la tabla.  

##### <a name="subheading29"></a>Datos con mucho tráfico
Si el esquema de participaciones da lugar a una sola partición que solamente tiene datos y que se utiliza mucho más que otras particiones, también puede ver limitaciones cuando esa partición se aproxime al objetivo de escalabilidad para una sola partición.  Conviene asegurarse de que el esquema de partición no provoca que las particiones individuales se aproximen a los objetivos de escalabilidad.  

#### <a name="querying"></a>Consultas
En esta sección se describen prácticas probadas para realizar consultas en Table service.  

##### <a name="subheading30"></a>Ámbito de la consulta
Hay varias formas de especificar el intervalo de entidades para consultar.  A continuación se muestra un análisis de los usos de cada una de ellas.  

En general, evite las exploraciones (consultas mayores que una sola entidad), pero si debe realizarlas, intente organizar los datos de forma que dichas exploraciones recuperen los datos necesarios sin explorar o devolver grandes cantidades de entidades innecesarias.  

###### <a name="point-queries"></a>Consultas puntuales
Una consulta puntual recupera exactamente una entidad. Lo hace especificando tanto la clave de partición como la clave de fila de la entidad que se va a recuperar. Estas consultas son muy diferentes y debe usarlas siempre que sea posible.  

###### <a name="partition-queries"></a>Consultas de partición
Una consulta de partición es una consulta que recupera un conjunto de datos que comparte una clave de partición común. Normalmente, la consulta especifica un intervalo de valores de clave de fila o un intervalo de valores para alguna propiedad de entidad además de una clave de partición. Son menos eficientes que las consultas puntuales y se deben usar con moderación.  

###### <a name="table-queries"></a>Consultas de tabla
Una consulta de tabla es una consulta que recupera un conjunto de entidades que no comparte una clave de partición común. Estas consultas no son diferentes y debe evitarlas siempre que sea posible.  

##### <a name="subheading31"></a>Densidad de consulta
Otro factor clave en la eficiencia de las consultas es el número de entidades devueltas comparado con el número de entidades examinadas para encontrar el conjunto devuelto. Si la aplicación realiza una consulta de tabla con un filtro para un valor de propiedad que solamente comparte el 1 % de los datos, la consulta examinará 100 entidades por cada entidad que devuelva. Los objetivos de escalabilidad de tabla tratados anteriormente están relacionados con el número de entidades que se analizan y no con el número de entidades que se devuelven: una densidad de consulta baja fácilmente puede provocar que Table service limite la aplicación porque debe analizar muchas entidades para recuperar la entidad que está buscando.  Consulte la sección siguiente sobre [desnormalización](#subheading34) para obtener más información sobre cómo evitar esto.  

##### <a name="limiting-the-amount-of-data-returned"></a>Limitación de la cantidad de datos devueltos
###### <a name="subheading32"></a>Filtros
Donde sepa que una consulta va a devolver entidades que no necesita en la aplicación cliente, plantéese el uso de un filtro para reducir el tamaño del conjunto devuelto. Aunque las entidades no devueltas al cliente siguen contando para los límites de escalabilidad, el rendimiento de la aplicación mejorará debido a la reducción del tamaño de la carga de red y a la reducción del número de entidades que la aplicación cliente debe procesar.  No obstante, tenga en cuenta la nota anterior del apartado [Densidad de las consultas](#subheading31): los objetivos de escalabilidad están relacionados con el número de entidades examinadas, de forma que una consulta que filtre muchas entidades, puede seguir dando lugar a limitaciones aunque se devuelvan pocas entidades.  

###### <a name="subheading33"></a>Proyección
Si la aplicación cliente solamente necesita un conjunto limitado de propiedades de las entidades de la tabla, puede usar proyección para limitar el tamaño del conjunto de datos devuelto. Como en el caso de los filtros, esto ayuda a reducir la carga de red y el procesamiento del cliente.  

##### <a name="subheading34"></a>Desnormalización
A diferencia del trabajo con bases de datos relacionales, las prácticas probadas para consultar datos de tabla de forma eficiente conducen a la desnormalización de los datos. Es decir, duplicar los mismos datos en varias entidades (una por cada clave que puede usar para encontrar los datos) para minimizar el número de entidades que una consulta debe examinar para encontrar los datos que el cliente necesita, en lugar de tener que examinar grandes números de entidades para encontrar los datos que la aplicación necesita.  Por ejemplo, en un sitio web de comercio electrónico, puede que le interese encontrar un pedido tanto por el identificador del cliente (ver los pedidos de este cliente) como por la fecha (ver los pedidos con esa fecha).  En Table Storage es mejor almacenar la entidad (o una referencia a ella) dos veces, una vez con el nombre de tabla, la clave de partición y la clave de fila para facilitar la búsqueda por identificador de cliente y una vez para facilitar su búsqueda por la fecha.  

#### <a name="insertupdatedelete"></a>Inserción, actualización y eliminación
En esta sección se describen prácticas probadas para modificar entidades almacenadas en Table service.  

##### <a name="subheading35"></a>Lotes
En Azure Storage, las transacciones por lotes son conocidas como transacciones con grupos de entidades (ETG); todas las operaciones dentro de una ETG deben estar en una sola partición y, a su vez, en una única tabla. Cuando sea posible, use ETG para realizar inspecciones, actualizaciones y eliminaciones por lotes. Esto reduce el número de recorridos de ida y vuelta de la aplicación cliente al servidor, disminuye el número de transacciones facturables (una ETG cuenta como una sola transacción desde el punto de vista de facturación y puede contener hasta 100 operaciones de almacenamiento) y permite actualizaciones atómicas (todas las operaciones se realizan correctamente o ninguna de ellas se realiza correctamente dentro de una ETG). Los entornos con altas latencias, como por ejemplo dispositivos móviles, sacarán un enorme provecho al uso de ETG.  

##### <a name="subheading36"></a>Upsert
Use operaciones **Upsert** de tabla siempre que sea posible. Hay dos tipos de operaciones **Upsert**; ambos pueden ser más eficientes que las operaciones **Insert** y **Update** tradicionales:  

* **InsertOrMerge**: se usa cuando se desea cargar un subconjunto de propiedades de la entidad sin tener la certeza de que la entidad existe. Si la entidad ya existe, esta llamada actualiza las propiedades incluidas en la operación **Upsert** y deja todas las propiedades existentes tal y como están; si la entidad no existe, inserta la nueva entidad. Esto es similar a usar proyección en una consulta, donde solamente necesita actualizar las propiedades que cambian.
* **InsertOrReplace**: se usa cuando se desea cargar una entidad completamente nueva sin tener la certeza de que existe. Solamente debe usar este tipo cuando sabe que la entidad recién cargada es totalmente correcta porque sobrescribe la entidad antigua completamente. Por ejemplo, desea actualizar la entidad que almacena la ubicación actual de un usuario independientemente de si la aplicación ha almacenado los datos de ubicación del usuario anteriormente; la entidad de la nueva ubicación está completa y no necesita información de ninguna otra entidad anterior.

##### <a name="subheading37"></a>Almacenamiento de series de datos en una sola entidad
A veces, una aplicación almacena una serie de datos que necesita recuperar de una sola vez con frecuencia: por ejemplo, un aplicación podría hacer un seguimiento del uso de CPU a lo largo del tiempo para representar un gráfico dinámico de los datos de las últimas 24 horas. Un enfoque es tener una entidad de tabla por hora, de forma que cada entidad represente una hora específica y almacene el uso de CPU para esa hora. Para representar estos datos, la aplicación necesita recuperar las entidades que contienen los datos de las 24 horas más recientes.  

Como alternativa, la aplicación podría almacenar el uso de CPU por cada hora como propiedad independiente de una sola entidad: para actualizar cada hora, la aplicación puede usar una sola llamada **InsertOrMerge Upsert** para actualizar el valor de la hora más reciente. Para representar los datos, la aplicación solamente necesita recuperar una sola entidad en lugar de 24, creando una consulta realmente eficiente (consulte el análisis anterior acerca del [ámbito de las consultas](#subheading30)).

##### <a name="subheading38"></a>Almacenamiento de datos estructurados en blobs
Algunas veces, da la sensación de que los datos estructurados deben ir en tablas, pero los intervalos de entidades siempre se recuperan conjuntamente y se pueden insertar por lotes.  Un buen ejemplo de esto es un archivo de registro.  En este caso, puede procesar por lotes varios minutos de registros, insertarlos y, después, siempre estará recuperando varios minutos de registros simultáneamente.  En este caso, para mejorar el rendimiento, conviene usar blobs, en lugar de tablas, ya que puede reducir considerablemente el número de objetos escritos y devueltos, así como, por lo general, el número de solicitudes que necesita realizar.  

## <a name="queues"></a>Colas
Además de las prácticas probadas para [Todos los servicios](#allservices) descritas anteriormente, las siguientes prácticas probadas se aplican específicamente al Queue service.  

### <a name="subheading39"></a>Límites de escalabilidad
Una sola cola puede procesar aproximadamente 2.000 mensajes (1 KB cada uno) por segundo (AddMessage GetMessage y DeleteMessage se cuentan como mensajes aquí). Si no es suficiente para la aplicación, debe usar varias colas y propagar los mensajes entre ellas.  

Vea los objetivos de escalabilidad actuales en [Objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md).  

### <a name="subheading40"></a>Desactivación de Nagle
Consulte la sección sobre la configuración de tablas que analiza el algoritmo de Nagle (el algoritmo de Nagle suele ser malo para el rendimiento de solicitudes de cola y debe deshabilitarlo).  

### <a name="subheading41"></a>Tamaño de los mensajes
El rendimiento y la escalabilidad de la cola se reducen a medida que aumenta el tamaño de los mensajes. Debe colocar únicamente la información que necesita el receptor de un mensaje.  

### <a name="subheading42"></a>Recuperación por lotes
Puede recuperar hasta 32 mensajes de una cola en una sola operación. Esto puede reducir el número de recorridos de ida y vuelta de la aplicación cliente, lo cual es especialmente útil para entornos, como por ejemplo dispositivos móviles, con alta latencia.  

### <a name="subheading43"></a>Intervalo de sondeo de la cola
La mayoría de aplicaciones sondean los mensajes de una cola, que puede ser uno de los principales orígenes de las transacciones de la aplicación. Seleccione el intervalo de sondeo con cuidado: un sondeo demasiado frecuente puede provocar que la aplicación alcance los objetivos de escalabilidad para la cola. Sin embargo, a 200.000 transacciones por 0,01 USD (en el momento de redactar), un solo procesador que sondeara una vez cada pocos segundos durante un mes costaría menos de 15 centavos, así que el coste de sondeo no suele ser un factor que afecte a la elección del intervalo de sondeo.  

Para obtener información de costo actualizada, consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Puede usar **UpdateMessage para** aumentar el tiempo de espera de invisibilidad o para actualizar la información de estado de un mensaje. Aunque esto es muy eficiente, recuerde que cada operación **UpdateMessage** cuenta para el objetivo de escalabilidad. Sin embargo, esto puede ser un enfoque mucho más eficiente que tener un flujo de trabajo que pasa un trabajo de una cola a la siguiente, cuando cada paso del trabajo se completa. El uso de la operación **UpdateMessage** permite que la aplicación guarde el estado del trabajo en el mensaje y, a continuación, continúe trabajando, en lugar de volver a poner en cola el mensaje para el próximo paso del trabajo cada vez que se completa un paso.  

Para obtener más información, consulte [Cambio del contenido de un mensaje en cola](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Arquitectura de la aplicación
Debe usar colas para que la arquitectura de la aplicación sea escalable. A continuación se enumeran algunas formas de usar colas para que la aplicación sea más escalable:  

* Puede usar colas para crear trabajos pendientes para el procesamiento y aliviar las cargas de la aplicación. Por ejemplo, podría poner en cola solicitudes de usuarios para realizar un trabajo que requiere muchos recursos de procesador, como, por ejemplo, cambiar el tamaño de las imágenes cargadas.
* Puede usar colas para desacoplar partes de la aplicación para poder escalarlas por separado. Por ejemplo, un front-end web podría poner resultados de encuesta de usuarios en una cola para analizarlos y almacenarlos posteriormente. Podría agregar más instancias de rol de trabajo para procesar los datos de cola según sea necesario.  

## <a name="conclusion"></a>Conclusión
En este artículo se analizaron algunas de las prácticas probadas más comunes para optimizar el rendimiento cuando se usa Azure Storage. Animamos a todos los desarrolladores de aplicaciones a que evalúen sus aplicaciones tomando como referencia todas las prácticas anteriores y que se planteen seguir las recomendaciones para obtener un magnífico rendimiento para aquellas aplicaciones que usan Azure Storage.
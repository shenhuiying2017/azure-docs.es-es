<properties linkid="storage-introduction" urlDisplayName="Introduction to Azure Storage" pageTitle="Introduction to Storage | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure " description="An overview of Microsoft Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" />

Introducción al almacenamiento de Microsoft Azure
=================================================

Este artículo ofrece una introducción al almacenamiento de Microsoft Azure para desarrolladores, profesionales de TI y responsables de la toma de decisiones empresariales. Al leerlo, aprenderá acerca de:

-   Qué es el almacenamiento de Azure y cómo puede aprovecharlo en las aplicaciones en la nube, móviles, de servidor y de escritorio
-   Qué tipos de datos puede almacenar con los servicios de almacenamiento de Azure: almacenamiento de blobs, tablas y colas
-   Cómo se administra el acceso a los datos del almacenamiento de Azure
-   Cómo se protegen los datos del almacenamiento de Azure mediante la redundancia y la replicación
-   Qué pasos seguir a continuación para crear una primera aplicación de almacenamiento de Azure

¿Qué es el almacenamiento de Azure?
-----------------------------------

La informática en la nube posibilita nuevos escenarios para aplicaciones que requieren un almacenamiento escalable, duradero y de alta disponibilidad para sus datos; esta es exactamente la razón por la que Microsoft ha desarrollado el almacenamiento de Azure. Además de permitir a los desarrolladores crear aplicaciones a gran escala compatibles con los nuevos escenarios, este tipo de almacenamiento proporciona la base de almacenamiento para la infraestructura de Microsoft como servicio, lo que es prueba de su solidez.

El almacenamiento de Azure se puede escalar de forma masiva para almacenar y procesar cientos de terabytes de datos y admitir así los escenarios de datos de gran tamaño requeridos por las aplicaciones científicas, de análisis financiero y multimedia. Igualmente, permite almacenar las pequeñas cantidades de datos que se necesitan en un sitio web de una pequeña empresa. Sean cual sean sus necesidades, solo tendrá que pagar por los datos que almacene. Actualmente, el almacenamiento de Azure alberga decenas de billones de objetos de cliente únicos y administra, por término medio, millones de solicitudes por segundo.

Otra de sus características es la elasticidad, lo que le permite diseñar aplicaciones para una gran audiencia global y escalarlas según sea necesario, tanto respecto a la cantidad de almacenamiento como al número de transacciones requeridas. Solo pagará por lo que use y únicamente cuando lo use.

El almacenamiento de Azure utiliza un sistema de creación automática de particiones que equilibra la carga de los datos automáticamente en función del tráfico. Esto significa que, conforme crece la demanda en la aplicación, el almacenamiento de Azure asigna automáticamente los recursos adecuados para ajustarse a este requisito.

Puede obtener acceso a este servicio desde cualquier lugar del mundo y cualquier tipo de aplicación, ya se ejecute en la nube, en el escritorio, en un servidor local o en un dispositivo móvil o tableta. El almacenamiento de Azure se puede usar en escenarios móviles donde la aplicación almacena un subconjunto de datos en el dispositivo y los sincroniza con todo un conjunto de datos almacenados en la nube.

El almacenamiento de Azure admite clientes que utilizan una gran variedad de sistemas operativos (incluidos Windows y Linux) y diversos lenguajes de programación (incluidos .NET, Java y C++) para un desarrollo más práctico. Además, expone recursos de datos a través de sencillas API REST, disponibles para cualquier cliente capaz de enviar y recibir datos mediante HTTP/HTTPS.

Introducción de los servicios de almacenamiento de Azure
--------------------------------------------------------

Los servicios de almacenamiento de Azure están formados por almacenamiento de blobs, de tablas y de colas:

-   El **almacenamiento de blobs** almacena datos de archivos. Un blob puede ser un tipo cualquiera de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación.
-   El **almacenamiento de tablas** almacena conjuntos de datos estructurados. Se trata de un almacén de datos de clave-atributo NoSQL, que permite el desarrollo rápido de grandes cantidades de datos y el acceso inmediato a los mismos.
-   El **almacenamiento de colas** ofrece una solución de mensajería confiable para el procesamiento de flujos de trabajo y para la comunicación entre los componentes de los servicios en la nube.

Estos tres servicios se incluyen en todas las cuentas de almacenamiento. Una cuenta de almacenamiento es un espacio de nombres único que le proporciona acceso al almacenamiento de Azure. Cada cuenta de almacenamiento puede contener hasta 200 TB de datos de blobs, colas y tablas combinados.

Para poder crear una cuenta de almacenamiento, debe tener una suscripción de Azure, que es un plan que le proporciona acceso a diversos servicios de Azure. Puede crear hasta 20 cuentas de almacenamiento con nombre único con una única suscripción.

Para comenzar con Azure, puede usar una [evaluación gratuita](/en-us/pricing/free-trial/). Una vez que decida adquirir un plan, puede elegir entre diversas [opciones de compra](/en-us/pricing/purchase-options/). Si ya es [suscriptor de MSDN](/en-us/pricing/member-offers/msdn-benefits-details/), obtendrá créditos mensuales gratuitos que podrá usar con los servicios de Azure, incluido el almacenamiento de Azure.

Almacenamiento de blobs
-----------------------

El almacenamiento de blobs ofrece una solución rentable y escalable a aquellos usuarios con grandes cantidades de datos no estructurados para almacenar en la nube. Esta característica se puede usar para almacenar contenido como:

-   Documentos
-   Datos de contenido social, como fotos, vídeos, música y blogs
-   Copias de seguridad de archivos, equipos, bases de datos y dispositivos
-   Imágenes y texto para las aplicaciones web
-   Datos de configuración para las aplicaciones en la nube
-   Datos de gran tamaño, como registros y otros conjuntos de datos grandes

Cada blob se organiza en un contenedor. Los contenedores también ofrecen una forma útil de asignar directivas de seguridad a grupos de objetos. Una cuenta de almacenamiento puede incluir un número cualquiera de contenedores y, a su vez, un contenedor puede incluir un número cualquiera de blobs, hasta alcanzar el límite de capacidad de 200 TB de la cuenta de almacenamiento.

El almacenamiento de blobs ofrece dos tipos de blobs: blobs en bloques y blobs en páginas (discos). Los blobs en bloques están optimizados para el streaming y para el almacenamiento de objetos de nube, y constituyen una opción idónea para almacenar documentos, archivos multimedia y copias de seguridad, entre otros. El tamaño máximo de un blob de este tipo es de 200 GB. Los blobs en páginas están optimizados para representar discos IaaS y admitir la escritura aleatoria. Pueden tener un tamaño máximo de 1 TB. Un disco IaaS asociado a una red de máquinas virtuales de Azure es un VHD almacenado como blob en páginas.

Para conjuntos de datos de gran tamaño donde las restricciones de red hacen imposible la carga o descarga de datos en el almacenamiento de blobs a través de una conexión, puede enviar una unidad de disco duro a Microsoft para importar o exportar datos directamente desde el centro de datos con el [servicio de importación y exportación de Azure](http://www.windowsazure.com/documentation/articles/storage-import-export-service/). También puede copiar datos de blobs en la cuenta de almacenamiento o entre cuentas de este tipo.

Almacenamiento de tablas
------------------------

A menudo, las aplicaciones modernas demandan almacenes de datos con una flexibilidad y escalabilidad superiores a las que requerían las generaciones anteriores de software. El almacenamiento de tablas ofrece un tipo de almacenamiento de alta disponibilidad y escalabilidad masiva, de forma que las aplicaciones pueden escalarse automáticamente para ajustarse a la demanda de los usuarios. Este tipo de almacenamiento se basa en un almacén de claves/atributos NoSQL de Microsoft con un diseño sin esquema que lo diferencia de las bases de datos relacionales tradicionales. Si se tiene un almacén de datos sin esquema, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. El almacenamiento de tablas es fácil de usar, por lo que los desarrolladores pueden crear aplicaciones de forma rápida. El acceso a los datos es rápido y rentable para todos los tipos de aplicaciones y, además, su coste es muy inferior al del SQL tradicional para volúmenes de datos similares.

Este tipo de almacenamiento se basa en un almacén de clave-atributo, lo que significa que cada entidad de una tabla se almacena con un nombre de propiedad donde la clave se puede usar para filtrar y especificar criterios de selección. Dicha clave, junto con una colección de propiedades y sus valores, componen una entidad. Puesto que este tipo de almacenamiento no tiene esquema, las entidades de una misma tabla pueden contener distintas colecciones de propiedades y dichas propiedades pueden ser de distintos tipos.

El almacenamiento de tablas se puede usar para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos y cualquier otro tipo de metadatos requerido por el servicio. Una tabla puede almacenar un número cualquiera de entidades y una cuenta de almacenamiento puede incluir un número cualquiera de tablas, hasta alcanzar el límite de capacidad de 200 TB de este tipo de cuenta.

Al igual que ocurre con el almacenamiento de blobs y colas, los desarrolladores pueden administrar el almacenamiento de tablas y obtener acceso al mismo a través de los protocolos REST estándar. Sin embargo, este tipo de almacenamiento también admite un subconjunto del protocolo OData, lo que simplifica las capacidades de consulta avanzadas y permite los formatos JSON y AtomPub (basados en XML).

Para las aplicaciones actuales basadas en Internet, las bases de datos NoSQL como las del almacenamiento de tabla ofrecen una alternativa popular a las bases de datos relacionales tradicionales.

Almacenamiento de colas
-----------------------

A la hora de diseñar aplicaciones para escala, los componentes de las mismas suelen desacoplarse para poder escalarlos de forma independiente. El almacenamiento de colas ofrece una solución de mensajería de confianza para la comunicación asincrónica entre los componentes de las aplicaciones, independientemente de que se ejecuten en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Además, este tipo de almacenamiento admite la administración de tareas asincrónicas y la creación de flujos de trabajo de procesos.

Una cuenta de almacenamiento puede contener un número cualquiera de colas y, a su vez, una cola puede contener un número cualquiera de mensajes, hasta alcanzar el límite de capacidad de 200 TB de este tipo de cuenta. Los mensajes individuales pueden tener un tamaño máximo de 64 KB.

Acceso a los recursos de blobs, tablas y colas
----------------------------------------------

De forma predeterminada, solo el propietario de la cuenta de almacenamiento puede obtener acceso a los recursos de la cuenta. A fin de mantener la seguridad de los datos, cada solicitud en relación con los recursos de la cuenta deberá autenticarse. Esta autenticación se basa en un modelo de clave compartida. Los blobs también pueden configurarse para admitir la autenticación anónima.

Al crear una cuenta de almacenamiento, se le asignan dos claves de acceso privado que se utilizan para la autenticación. Tener dos claves garantiza que la aplicación siga estando disponible durante el proceso habitual de regeneración de las claves, una práctica común de administración de las claves de seguridad.

Si debe permitir el acceso controlado de los usuarios a los recursos de almacenamiento, puede crear una [firma de acceso compartido](../storage-dotnet-shared-access-signature-part-1/). Una firma de acceso compartido es un token que se puede asociar a una dirección URL que permite el acceso delegado a un contenedor, un blob, una tabla o una cola. Cualquier usuario que tenga el token puede obtener acceso al recurso que señala con los permisos que especifica durante el plazo válido correspondiente.

Por último, puede especificar que un contenedor y sus blobs, o un blob específico, estén disponibles para el acceso público. Cuando se indica que un contenedor o blob es público, todos los usuarios pueden leerlo de forma anónima: no se requiere autenticación. Los contenedores y los blobs públicos son útiles para exponer recursos, como archivos multimedia y documentos hospedados en sitios web. Si desea reducir la latencia de red para una audiencia global, puede almacenar en caché los datos de blobs usados por los sitios web con el servicio CDN de Azure.

Replicación para obtener durabilidad y alta disponibilidad
----------------------------------------------------------

Los datos de la cuenta de almacenamiento se replican para garantizar la durabilidad y una alta disponibilidad y deben cumplir los [contratos de nivel de servicio para almacenamiento de Azure](/en-us/support/legal/sla/), incluso en caso de errores transitorios del hardware. Tiene tres opciones para replicar los datos en la cuenta de almacenamiento:

-   El *almacenamiento con redundancia local (LRS)* se replica tres veces en un mismo centro de datos. Al escribir datos en un blob, cola o tabla, la operación de escritura se realiza de forma sincrónica en las tres réplicas. LRS protege los datos frente a errores comunes del hardware.
-   El *almacenamiento con redundancia geográfica (GRS)* se replica tres veces en una misma región; además, también se replica asincrónicamente en una segunda región situada a cientos de kilómetros de la región principal. Este tipo de almacenamiento conserva 6 copias (réplicas) de los datos (3 en cada región). Además, permite a Microsoft realizar la conmutación por error en una segunda región en caso de no poder restaurar la primera debido a una interrupción o desastre. Se recomienda el uso del almacenamiento con redundancia geográfica antes que el almacenamiento con redundancia local.
-   El *almacenamiento geográficamente redundante con acceso de lectura (RA-GRS)* ofrece todas las ventajas del almacenamiento con redundancia geográfica indicado anteriormente pero, además, permite el acceso de lectura a los datos de la región secundaria en caso de que la primera deje de estar disponible. Para una disponibilidad y durabilidad máximas se recomienda el almacenamiento geográficamente redundante con acceso de lectura.

Las diferencias de precio entre LRS, GRS y RA-GRS pueden encontrarse en la página de [Detalles de precios de Storage](/en-us/pricing/details/storage/).

Precios
-------

El importe que se cobra a los clientes por el almacenamiento de Azure se basa en cuatro factores: la capacidad de almacenamiento utilizada, la opción de replicación seleccionada, el número de solicitudes realizadas en el servicio y la salida de los datos.

La capacidad de almacenamiento se refiere a cuánto de la asignación correspondiente a cuentas de almacenamiento utiliza para almacenar datos. El coste de simplemente almacenar los datos está determinado por la cantidad de datos que almacena y la manera en que se replican. Todas las operaciones de lectura y escritura realizadas en el almacenamiento de Azure constituyen una solicitud al servicio. La salida de los datos se refiere a los datos transferidos fuera de una región de Azure. Cuando una aplicación que no está en ejecución en la misma región tiene acceso a los datos en su cuenta de almacenamiento, se le cobra por la salida de los datos, independientemente de si la aplicación es un servicio en la nube u otro tipo de aplicación. (En el caso de los servicios de Azure, puede llevar a cabo pasos para agrupar los datos y servicios en los mismos centros de datos a fin de reducir o eliminar los cargos en concepto de salida y proceso de los datos).

La página [Detalles de precios de Storage](/en-us/pricing/details/storage/) proporciona información detallada sobre los precios de la capacidad, replicación y transacciones de almacenamiento. La página [Detalles de precios de Transferencias de datos](/en-us/pricing/details/data-transfers/) proporciona información detallada sobre los precios para la salida de datos. Puede usar la [calculadora de precios de almacenamiento de Azure](/en-us/pricing/calculator/?scenario=data-management) para ayudarle a calcular los costes.

Desarrollo y almacenamiento
---------------------------

El almacenamiento de Azure expone los recursos de almacenamiento a través de una [API REST](http://msdn.microsoft.com/library/windowsazure/dd179355.aspx) a la cual puede llamar cualquier lenguaje que pueda realizar solicitudes HTTP/HTTPS. Además, ofrece bibliotecas de programación para varios lenguajes de amplio uso. Estas bibliotecas simplifican muchos aspectos relacionados con el uso del almacenamiento de Azure, ya que abordan detalles como la invocación sincrónica y asincrónica, el procesamiento por lotes de las operaciones, la administración de excepciones, los reintentos automáticos y el comportamiento operativo, entre otros. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas, además de otros previstos:

-   [.NET](http://msdn.microsoft.com/library/dn495001(v=azure.10).aspx)
-   [Código nativo](http://msdn.microsoft.com/library/dn495438.aspx)
-   [Java](/en-us/develop/java/)
-   [Node.js](../storage/#node)
-   [PHP](../storage/#php)
-   [Ruby](../storage/#ruby)
-   [Python](../storage/#python)
-   [PowerShell](http://msdn.microsoft.com/library/dn495240.aspx)

Pasos siguientes
----------------

Para comenzar a usar el almacenamiento de Azure, explore estos recursos:

-   [Documentación del almacenamiento de Azure](/en-us/documentation/services/storage/)
-   [Azure Storage Scalability and Performance Targets](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx)

### Para desarrolladores de .NET

-   [Uso del almacenamiento de blobs en .NET](../storage-dotnet-how-to-use-blobs-20/)
-   [Uso del almacenamiento de tablas en .NET](../storage-dotnet-how-to-use-tables-20/)
-   [Uso del almacenamiento en cola en .NET](../storage-dotnet-how-to-use-queues-20/)

### Para desarrolladores de Java

-   [Uso del almacenamiento de blobs en Java](../storage-java-how-to-use-blob-storage/)
-   [Uso del almacenamiento de tablas en Java](..storage-java-how-to-use-table-storage/)
-   [Uso del almacenamiento de colas en Java](..storage-java-how-to-use-queue-storage/)

### Para desarrolladores de Node.js

-   [Uso del almacenamiento de blobs en Node.js](../storage-nodejs-how-to-use-blob-storage/)
-   [Uso del almacenamiento de tablas en Node.js](../storage-nodejs-how-to-use-table-storage/)
-   [Uso del almacenamiento de colas en Node.js](../storage-nodejs-how-to-use-queue-storage/)

### Para desarrolladores de PHP

-   [Uso del almacenamiento de blobs en PHP](../storage-php-how-to-use-blob-storage/)
-   [Uso del almacenamiento de tablas en PHP](..storage-php-how-to-use-table-storage/)
-   [Uso del almacenamiento de colas en PHP](..storage-php-how-to-use-queue-storage/)

### Para desarrolladores de Ruby

-   [Uso del almacenamiento de blobs en Ruby](../storage-ruby-how-to-use-blob-storage/)
-   [Uso del almacenamiento de tablas en Ruby](..storage-ruby-how-to-use-table-storage/)
-   [Uso del almacenamiento de colas en Ruby](..storage-ruby-how-to-use-queue-storage/)

### Para desarrolladores de Python

-   [Uso del almacenamiento de blobs en Python](../storage-python-how-to-use-blob-storage/)
-   [Uso del almacenamiento de tablas en Python](..storage-python-how-to-use-table-storage/)
-   [Uso del almacenamiento de colas en Python](..storage-python-how-to-use-queue-storage/)


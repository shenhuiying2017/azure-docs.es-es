<properties 
	pageTitle="Gestión de la capacidad y rendimiento de la Base de datos de documentos | Azure" 
	description="Obtenga información acerca de cómo escalar Base de datos de documentos de forma elástica para cumplir con las necesidades capacidad de su aplicación." 
	services="documentdb" 
	authors="mimig1, johnfmacintyre" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mimig"/>

#Gestión de la capacidad y rendimiento de la Base de datos de documentos
Base de datos de documentos es un servicio de bases de datos diferentes a SQL orientado a documentos totalmente administrados y escalables.  Con la Base de datos de documentos, no tendrá que alquilar máquinas virtuales, implementar software, supervisar bases de datos o preocuparse por la recuperación ante desastres. Los ingenieros de Microsoft operan y supervisan de forma continua Base de datos de documentos para ofrecer disponibilidad, rendimiento y protección de datos universales.  

Puede empezar con Base de datos de documentos creando una cuenta de base de datos a través del [Portal de administración de vista previa de Microsoft Azure](https://portal.azure.com/). Base de datos de documentos se ofrece en unidades de capacidad (CU) apilables de almacenamiento respaldado y capacidad de proceso de unidad de estado sólido (SSD). Puede ampliar la Base de datos de documentos de forma elástica para cumplir con las necesidades de rendimiento y almacenamiento de su aplicación. 

Cada unidad de capacidad viene con una cuota de colecciones para almacenar datos de documentos, almacenamiento de documentos aprovisionado y capacidad de proceso aprovisionada como unidades de solicitud por segundo. Si los requisitos de capacidad de su aplicación cambian, puede ampliar o reducir la cantidad de capacidad aprovisionada en la cuenta de base de datos. La capacidad aprovisionada con una cuenta de base de datos está disponible para todas las bases de datos y colecciones que existen o se han creado dentro de la cuenta.

> [AZURE.NOTE] Cada colección puede escalar en el almacenamiento y el rendimiento hasta el [límite](documentdb-limits.md) máximo permitido. El rendimiento se distribuirá uniformemente a todas las colecciones hasta el límite máximo por colección.

Cuando la aplicación supera los niveles de rendimiento de una o varias colecciones, se limitarán las solicitudes para cada colección. Esto significa que algunas solicitudes de aplicación pueden tener éxito mientras que otras quedarán limitadas.

##<a name="DBaccount"></a>Cuenta de base de datos y recursos administrativos
Como suscriptor de Azure, puede aprovisionar una o más cuentas de base de datos de la Base de datos de documentos. Cada cuenta de base de datos viene con una cuota de recursos administrativos que incluyen bases de datos, usuarios y permisos. Estos recursos están sujetos a [cuotas y límites](documentdb-limits.md). Si necesita recursos administrativos adicionales, póngase en contacto con el soporte técnico.   

##<a name="DBstorage"></a> Bases de datos con almacenamiento ilimitado de documentos
Una única base de datos de la Base de datos de documentos puede contener una cantidad prácticamente ilimitada de almacenamiento de documentos con particiones por colecciones. Las colecciones forman los dominios de transacción para los documentos que las contienen. Una base de datos de Base de datos de documentos es elástica de forma predeterminada, desde unos pocos GB hasta terabytes potenciales de almacenamiento de documentos respaldado y capacidad de proceso aprovisionada de SSD. A diferencia de la base de datos RDBMS tradicional, una base de datos en la Base de datos de documentos no está dirigida a un único equipo.   

Con Base de datos de documentos, a medida que aumenten sus necesidades de escala de la aplicación, podrá crear más colecciones, bases de datos o ambas. De hecho, varias aplicaciones propias dentro de Microsoft han utilizado la Base de datos de documentos a nivel de consumidor creando bases de datos de la Base de datos de documentos extremadamente grandes y cada una contenía miles de colecciones con terabytes de almacenamiento de documentos. Puede ampliar o reducir una base de datos agregando o eliminando colecciones para cumplir con los requisitos de escala de su aplicación. Puede crear cualquier número de colecciones dentro de una base de datos en función de la disponibilidad de oferta y de la cantidad de unidades de capacidad que adquiera. El almacenamiento respaldado y la capacidad de proceso aprovisionado de SSD para usted se pueden propagar en todas las colecciones de las bases de datos de su cuenta de base de datos. 

##<a name="DBCollections"></a>Colecciones de base de datos
Cada base de datos de la Base de datos de documentos puede contener una o más colecciones. Una colección proporciona el ámbito del almacenamiento de documentos y la ejecución de consultas. Una colección también es un dominio de transacciones para todos los documentos contenidos en la misma. Puede crear cualquier número de colecciones para cumplir con los requisitos de escala de sus aplicaciones. Para crear colecciones, primero debe comprar una o más unidades de capacidad (CU). Cada unidad de capacidad incluye una cuota de colecciones; si alcanza a la cuota de colecciones de su cuenta, puede comprar unidades de capacidad adicionales.  

>[AZURE.NOTE] Cada colección admite el almacenamiento de hasta 10 GB de datos de documentos. 

##<a name="ProvStorage"></a>Almacenamiento aprovisionado y rendimiento como unidades de capacidad
Puede aprovisionar unidades apilables de almacenamiento de documentos respaldado y capacidad de proceso de SSD como unidades de capacidad (CU). Puede ampliar la Base de datos de documentos de forma elástica con rendimiento predecible comprando más unidades de capacidad para cumplir con las necesidades de su aplicación para leer la escala, el almacenamiento y la capacidad de proceso.  
 
Cada CU viene con 3 colecciones elásticas, 10 GB de almacenamiento de documentos aprovisionado respaldado de SSD y 2000 unidades de solicitud (RU) por el valor de la capacidad de proceso aprovisionada. El almacenamiento aprovisionado y la capacidad de proceso asociados con una CU se distribuyen en todas las colecciones de la Base de datos de documentos que cree.   

##<a name="ProvThroughput"></a>Solicitud de unidades y las operaciones de base de datos
Base de datos de documentos permite un amplio conjunto de operaciones de base de datos, incluidas las consultas relacionales y jerárquicas con UDF, procedimientos almacenados y desencadenadores. Todo funciona con los documentos dentro de una colección de base de datos. El coste de procesamiento asociado a cada una de estas operaciones variará en función de la CPU, las E/S y la memoria que se necesite para completar la operación. En lugar de administrar y pensar sobre los recursos de hardware, puede pensar en una unidad de solicitud (RU) como una medida única para los recursos necesarios para realizar varias operaciones de la base de datos y dar servicio a una solicitud de la aplicación.

Las unidades de solicitud se aprovisionan para cada Cuenta de base de datos según el número de unidades de capacidad que adquiera. En la vista previa de Base de datos de documentos, cada colección creada se asigna un límite de unidad de la solicitud. La asignación se calcula mediante la asignación uniforme de todas las unidades de la solicitud de las unidades de la capacidad adquirida al número de colecciones creadas dentro de una cuenta. Por ejemplo, para una cuenta con 2 unidades de capacidad adquiridas y 4 colecciones creadas, a cada colección se asignan 2 unidades de capacidad x 2000 unidades de solicitud / 4 colecciones para una asignación de 1000 unidades de solicitud por colección. Cuando Base de datos de documentos de Azure está disponible, el servicio ya no propaga unidades de solicitud entre las colecciones dentro de una cuenta. Las unidades de solicitud se adquirirán y se asignarán a colecciones específicas basadas en los niveles de rendimiento. Para obtener más información, consulte [Niveles de coherencia de Base de datos de documentos](documentdb-performance-levels.md). 

>[AZURE.NOTE] Durante la versión de vista previa de Base de datos de documentos de Azure a cada colección no se puede asignar más de 2000 unidades de solicitud. 

El consumo de la unidad de solicitud se evalúa como frecuencia por segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para su cuenta se limitarán hasta que la frecuencia caiga por debajo del nivel reservado para cada colección. Si su aplicación necesita un nivel mayor de capacidad de proceso, puede adquirir unidades de capacidad adicionales.

Una unidad de solicitud es una medida normalizada del costo de procesamiento de solicitudes. Una única unidad de solicitud representa la capacidad de procesamiento necesaria para leer un solo documento JSON de 1 KB que consta de 10 valores de propiedad únicos. El cargo de la unidad de solicitud supone un nivel de coherencia establecido en el valor predeterminado de "Sesión" y todos los documentos se indizan automáticamente. Una solicitud para insertar, reemplazar o eliminar el mismo documento consumirá más procesamiento del servicio y, por tanto, más unidades de solicitud. Cada respuesta de solicitud del servicio incluye un encabezado personalizado (x-ms-request-charge) que mide las unidades de solicitud usadas por la solicitud. Este encabezado también es accesible a través de los SDK. En el SDK de .NET, RequestCharge es una propiedad del objeto ResourceResponse.

Hay varios factores que afectan a las unidades de solicitud utilizadas por una operación en una cuenta de base de datos de Base de datos de documentos. Entre estos factores se incluyen:

- Tamaño de documento: cuando aumenta el tamaño del documento también aumentan las unidades utilizadas para leer o escribir los datos.
- Recuento de propiedades: suponiendo que la indización predeterminada de todas las propiedades, las unidades usadas para escribir un documento aumentarán conforme aumenta el recuento de propiedades.
- Coherencia de datos: al usar los niveles de coherencia de datos Alta y De uso vinculado, se usarán unidades adicionales para leer documentos.
- Propiedades indizadas: una directiva de índice en cada colección determina qué propiedades se indizan de forma predeterminada. Puede reducir el consumo de unidades de solicitud limitando el número de las propiedades indizadas.
- Indización de documentos: de forma predeterminada, todos los documentos se indizan automáticamente, por tanto consumirá menos unidades de solicitud si no desea indizar algunos de los documentos.

Las consultas, procedimientos almacenados y desencadenadores consumirán unidades de solicitud según la complejidad de las operaciones que se están llevando a cabo. Cuando desarrolla su aplicación, inspeccione el encabezado request charge para entender mejor cómo consumen las operaciones la capacidad de unidad de solicitud.

##<a name="Collections"></a>Colecciones y rendimiento aprovisionado
La capacidad de proceso aprovisionada para su cuenta de base de datos se asigna uniformemente en todas las colecciones hasta el nivel máximo de capacidad de proceso (Unidades de solicitud) para una única colección. Por ejemplo, si compra una única unidad de capacidad y crea una única colección, toda la capacidad de proceso aprovisionada para la CU estará disponible en la colección. Si se crea una colección adicional, la capacidad de proceso aprovisionada se asignará uniformemente con cada colección que reciba la mitad o toda la capacidad de proceso aprovisionada.  

##<a name="Consistency"></a>Opción de nivel de coherencia y rendimiento
La selección del nivel de coherencia predeterminado tiene un impacto en la capacidad de proceso y en la latencia.  Puede establecer el nivel de coherencia predeterminado mediante programación y a través del portal de Azure. También puede reemplazar el nivel de coherencia en función de la solicitud. De forma predeterminada, el nivel de coherencia es el de la sesión que proporciona lecturas/escrituras monotónicas y lee las garantías de escritura. La coherencia de sesión es mayor para aplicaciones centradas en el usuario y proporciona un equilibrio ideal de intercambios de coherencia y rendimiento.    

Para obtener instrucciones acerca de cómo cambiar el nivel de coherencia en el Portal de administración de vista previa de Azure, consulte Administración de la configuración de coherencia de Base de datos de documentos en [Administración de una cuenta de Base de datos de documentos](documentdb-manage-account.md)

##<a name="IndexOverhead"></a>Sobrecarga de índice y almacenamiento de documentos aprovisionados
Con cada CU adquirida su cuenta se aprovisiona con 10 GB de almacenamiento de documentos respaldado de SSD. Los 10 GB de almacenamiento de documentos incluyen más almacenamiento para el índice. De forma predeterminada, una colección de la Base de datos de documentos se configura para que indexe automáticamente todos los documentos sin solicitar de forma explícita ningún índice o esquema secundario. Basándose en el uso de producción de las aplicaciones propias del consumidor mediante la Base de datos de documentos, la sobrecarga del índice normal está entre el 2 y el 20 %. La tecnología de indexación que utiliza la Base de datos de documentos garantiza que, a pesar de los valores de las propiedades, la sobrecarga de índice no supera el 80 % del tamaño de los documentos con la configuración predeterminada.  

De forma predeterminada, la Base de datos de documentos indexa todos los documentos automáticamente. Sin embargo, en caso de que desee ajustar la sobrecarga de índice, puede elegir eliminar determinados documentos de la indexación en el momento de insertar o reemplazar un documento. Puede configurar una colección de la Base de datos de documentos para que excluya todos los documentos de la colección de la indexación. También puede configurar una colección de la Base de datos de documentos para indexar de forma selectiva solo determinadas propiedades o rutas con caracteres comodín de sus documentos JSON.  Excluir propiedades o documentos también mejora la capacidad de proceso de escritura; lo que significa que consumirá menos unidades de solicitud.   
 
##<a name="NextSteps"></a>Pasos siguientes
Para obtener instrucciones acerca de cómo administrar una cuenta de Base de datos de documentos en el Portal de vista previa de Azure, consulte Administración de la configuración de coherencia de Base de datos de documentos en [Administración de una cuenta de Base de datos de documentos](documentdb-manage-account.md)

Para obtener instrucciones acerca de cómo supervisar los niveles de rendimiento en el Portal de vista previa de Azure, consulte [Supervisión de una cuenta de Base de datos de documentos](documentdb-monitor-accounts.md).

<!--HONumber=49-->
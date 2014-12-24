<properties title="Manage DocumentDB capacity and performance" pageTitle="Gestión de la capacidad y rendimiento de la Base de datos de documentos | Azure" description="Learn how you can elastically scale DocumentDB to meet the performance and storage needs of your application." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Gestión de la capacidad y rendimiento de la Base de datos de documentos
La Base de datos de documentos es un servicio de bases de datos diferentes a SQL orientado a documentos totalmente gestionados de gran escalabilidad.  Con la Base de datos de documentos, no tendrá que alquilar máquinas virtuales, implementar software, supervisar bases de datos o preocuparse por la recuperación ante desastres. Los ingenieros de Microsoft operan y supervisan de forma continua la Base de datos de documentos para ofrecer disponibilidad, rendimiento y protección de datos universales.  

Puede empezar con la Base de datos de documentos creando una cuenta de base de datos a través del portal de Microsoft Azure. La Base de datos de documentos se ofrece en unidades de capacidad (CU) apilables de almacenamiento respaldado y capacidad de proceso de SSD. Puede ampliar la Base de datos de documentos de forma elástica para cumplir con las necesidades de rendimiento y almacenamiento de su aplicación. 

Cada unidad de capacidad viene con una cuota de colecciones elásticas para almacenar datos de documentos, almacenamiento de documentos aprovisionado y capacidad de proceso aprovisionada como unidades de solicitud por segundo. Si los requisitos de capacidad de su aplicación cambian, puede ampliar o reducir la cantidad de capacidad aprovisionada en la cuenta de base de datos. La capacidad aprovisionada con una cuenta de base de datos está disponible para todas las bases de datos y colecciones que existen o se han creado dentro de la Cuenta.  

#Cuenta de base de datos y cuota administrativa
Como suscriptor de Azure, puede aprovisionar una o más cuentas de base de datos de la Base de datos de documentos. Cada cuenta de base de datos viene con una cuota de recursos administrativos que incluyen 100 bases de datos, 500 000 usuarios y 2 000 000 de permisos.   

#Bases de datos con almacenamiento de documentos ilimitado
Una única base de datos de la Base de datos de documentos puede contener una cantidad prácticamente ilimitada de almacenamiento de documentos con particiones por colecciones. Las colecciones forman los dominios de transacción para los documentos que las contienen. Una base de datos de la Base de datos de documentos es elástica de forma predeterminada, desde unos pocos GB hasta petabytes potenciales de almacenamiento de documentos respaldado y capacidad de proceso aprovisionada de SSD. A diferencia de la base de datos RDBMS tradicional, una base de datos en la Base de datos de documentos no está dirigida a un único equipo.   

Con la Base de datos de documentos, a medida que aumenten sus necesidades de escala de la aplicación, podrá crear más colecciones, bases de datos o ambas. De hecho, varias aplicaciones propias dentro de Microsoft han utilizado la Base de datos de documentos a nivel de consumidor creando bases de datos de la Base de datos de documentos extremadamente grandes y cada una contenía miles de colecciones con terabytes de almacenamiento de documentos. Puede ampliar o reducir una base de datos agregando o eliminando colecciones para cumplir con los requisitos de escala de su aplicación. Puede crear cualquier número de colecciones dentro de una base de datos en función de la disponibilidad de oferta y de la cantidad de unidades de capacidad que adquiera. El almacenamiento respaldado y la capacidad de proceso aprovisionado de SSD para usted se pueden propagar en todas las colecciones de las bases de datos de su cuenta de base de datos. 

#Colecciones elásticas
Cada base de datos de la Base de datos de documentos puede contener una o más colecciones. Una colección proporciona el ámbito del almacenamiento de documentos y la ejecución de consultas. Una colección también es un dominio de transacciones para todos los documentos contenidos en la misma. Las colecciones son elásticas y pueden aumentar o reducirse en almacenamiento y capacidad de proceso. Puede crear cualquier número de colecciones para cumplir con los requisitos de escala de sus aplicaciones. Para crear colecciones, primero debe comprar una o más unidades de capacidad (CU). Cada unidad de capacidad incluye una cuota de colecciones; si alcanza a la cuota de colecciones de su cuenta, puede comprar unidades de capacidad adicionales.  

>[AZURE.NOTE] Tenga en cuenta que en la versión preliminar las colecciones se pueden aumentar o reducir desde 0 a 10 GB. 

#Almacenamiento aprovisionado y capacidad de proceso como unidades de capacidad
Puede aprovisionar unidades apilables de almacenamiento de documentos respaldado y capacidad de proceso de SSD como unidades de capacidad (CU). Puede ampliar la Base de datos de documentos de forma elástica con rendimiento predecible comprando más unidades de capacidad para cumplir con las necesidades de su aplicación para leer la escala, el almacenamiento y la capacidad de proceso.  
 
Cada CU viene con 3 colecciones elásticas, 10 GB de almacenamiento de documentos aprovisionado respaldado de SSD y 2000 unidades de solicitud (RU) por el valor de la capacidad de proceso aprovisionada. El almacenamiento aprovisionado y la capacidad de proceso asociados con una CU se distribuyen en todas las colecciones de la Base de datos de documentos que cree.   

#Capacidad de proceso aprovisionada, unidades de solicitud y operaciones de la base de datos
A diferencia de los almacenamientos que no son SQL de valor de clave que ofrecen operaciones GET y PUT simples, la Base de datos de documentos permite un conjunto operaciones de base de datos más grande incluyendo consultas relacionales y jerárquicas con UDF, procedimientos almacenados y desencadenadores; todos ellos con funciones en los documentos de una colección de base de datos. El coste asociado a cada una de estas operaciones variará en función de la CPU, E/S y memoria necesarios para completar la operación.  En lugar de administrar y pensar sobre los recursos de hardware, puede pensar en una unidad de solicitud (RU) como una medida única para los recursos necesarios para realizar varias operaciones de la base de datos y dar servicio a una solicitud de la aplicación.   

Las unidades de solicitud se aprovisionan para cada Cuenta de base de datos según el número de unidades de capacidad que adquiera. El consumo de la unidad de solicitud se evalúa como frecuencia por segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para su cuenta se limitarán hasta que la frecuencia caiga por debajo del nivel reservado para la Cuenta. Si su aplicación necesita un nivel mayor de capacidad de proceso, puede adquirir unidades de capacidad adicionales.  

En la tabla siguiente se incluye una lista de las diversas operaciones de la base de datos y de las unidades de solicitud por unidad de capacidad (CU). La tabla ayuda a planificar cómo una operación determinada de la base de datos consume la capacidad de proceso aprovisionada con una CU.  Se supone que el tamaño del documento es de 1 KB y consiste en 10 valores de propiedad únicos con el nivel de consistencia predeterminado establecido en "Sesión" y todos los documentos indizados automáticamente por Base de datos de documentos. 

|Operaciones de la base de datos|Número de operaciones por segundo por CU|
|-------------------|--------------------------------------|
|Lectura de un único documento	|2000
|Inserción/reemplazo/eliminación de un único documento	|500
|Consulta de una colección con un predicado simple y devolución de un único documento	|1000
|Procedimiento almacenado con 50 inserciones de documento	|20

Observe en la tabla que las unidades de solicitud consumidas por las operaciones de inserción/reemplazo/eliminación para los documentos indexados automáticamente son de 4 veces más que la de la lectura de un documento. La tabla anterior es únicamente una referencia. Sin embargo, en la práctica,  

-	los tamaños de los documentos pueden variar (y no corresponder a 1 KB), 
-	sus documentos pueden tener más de 10 propiedades.
-	Puede elegir establecer el nivel de coherencia predeterminado en Alto, Uso vinculado u Ocasional
-	Puede elegir no indexar solo algunos de sus documentos 
-	Puede elegir indexar solo algunas de sus propiedades y no permitir que la Base de datos de documentos indexe todo automáticamente
-	Sus consultas y procedimientos almacenados pueden ser muy complejos.  

 Puede adquirir las unidades de solicitud durante una solicitud determinada inspeccionando el encabezado de respuesta x-ms-request-charge de la solicitud.  

#Colecciones y capacidad de proceso aprovisionada
La capacidad de proceso aprovisionada para su cuenta de base de datos se asigna uniformemente en todas las colecciones hasta el nivel máximo de capacidad de proceso (Unidades de solicitud) para una única colección. Por ejemplo, si compra una única unidad de capacidad y crea una única colección, toda la capacidad de proceso aprovisionada para la CU estará disponible en la colección. Si se crea una colección adicional, la capacidad de proceso aprovisionada se asignará uniformemente con cada colección que reciba la mitad o toda la capacidad de proceso aprovisionada.  

#Elección del nivel de coherencia y capacidad de proceso
La selección del nivel de coherencia predeterminado tiene un impacto en la capacidad de proceso y en la latencia.  Puede establecer el nivel de coherencia predeterminado mediante programación y a través del portal de Azure. También puede reemplazar el nivel de coherencia en función de la solicitud. De forma predeterminada, el nivel de coherencia es el de la sesión que proporciona lecturas/escrituras monotónicas y lee las garantías de escritura. La coherencia de sesión es mayor para aplicaciones centradas en el usuario y proporciona un equilibrio ideal de intercambios de coherencia y rendimiento.   

-	Los niveles de coherencia de Sesión y Ocasional proporcionan aproximadamente 2000 solicitudes de lectura y 500 inserciones/reemplazos/eliminaciones de documentos.
-	Los niveles de coherencia Alto y Uso vinculado proporcionan aproximadamente 1200 solicitudes de lectura de documentos y aproximadamente 500 inserciones/reemplazos/eliminaciones de documentos. Las inserciones/reemplazos/eliminaciones con Uso vinculado son bastante más bajas en latencia que Alto.  

#Almacenamiento de documentos aprovisionado y sobrecarga de índice
Con cada CU adquirida su cuenta se aprovisiona con 10 GB de almacenamiento de documentos respaldado de SSD. Los 10 GB de almacenamiento de documentos incluyen más almacenamiento para el índice. De forma predeterminada, una colección de la Base de datos de documentos se configura para que indexe automáticamente todos los documentos sin solicitar de forma explícita ningún índice o esquema secundario. Basándose en el uso de producción de las aplicaciones propias del consumidor mediante la Base de datos de documentos, la sobrecarga del índice normal está entre el 2 y el 20 %. La tecnología de indexación que utiliza la Base de datos de documentos garantiza que, a pesar de los valores de las propiedades, la sobrecarga de índice no supera el 80 % del tamaño de los documentos con la configuración predeterminada.  

De forma predeterminada, la Base de datos de documentos indexa todos los documentos automáticamente. Sin embargo, en caso de que desee ajustar la sobrecarga de índice, puede elegir eliminar determinados documentos de la indexación en el momento de insertar o reemplazar un documento. Puede configurar una colección de la Base de datos de documentos para que excluya todos los documentos de la colección de la indexación. También puede configurar una colección de la Base de datos de documentos para indizar de manera selectiva solo determinadas propiedades o rutas de acceso con caracteres comodines de sus documentos JSON.  La exclusión de propiedades o documentos también mejora el rendimiento de escritura, lo que significa que se consumirán menos unidades de solicitud.   
 

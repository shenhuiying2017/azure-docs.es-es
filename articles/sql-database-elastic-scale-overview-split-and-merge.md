<properties 
title="Splitting and Merging with Elastic Scale" 
pageTitle="División y combinación con escalado elástico" 
description="Explica cómo manipular las particiones y mover los datos a través de un servicio autohospedado mediante las API de escala elástica." 
metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" 
services="sql-database" documentationCenter="" 
manager="jhubbard" 
authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="torsteng" />

# División y combinación con escalado elástico

Las aplicaciones basadas en Base de datos SQL de Azure enfrenta desafíos cuando sus necesidades de datos o de procesamiento ya no se ajustan a una unidad de escalado único en Base de datos SQL de Azure. Algunos ejemplos incluyen aplicaciones que se vuelven virales o donde un conjunto determinado de inquilinos crecen más allá de los límites de una sola base de datos de Base de datos SQL de Azure. El **servicio División y combinación** de Escalado elástico alivia este problema. 

Esta descripción del servicio División y combinación administra la reducción horizontal y el escalado horizontal al cambiar el número de bases de datos de Base de datos de Azure y equilibrar la distribución de **shardlets** entre ellas. (Para obtener definiciones de términos, consulte el [Glosario de Escalado elástico](sql-database-elastic-scale-glossary.md)). 

Con las opciones actuales entre ediciones de Base de datos SQL de Azure, la capacidad se puede administrar escalando o reduciendo verticalmente la capacidad de una sola base de datos de Base de datos SQL de Azure. El servicio División y combinación no trata la dimensión del escalado/reducción vertical de la administración de la capacidad elástica; en su lugar, consulte [Elasticidad de partición de Escalado elástico](sql-database-elastic-scale-elasticity.md)). 
 
## Novedades de la División y combinación

La versión más reciente de División y combinación ofrece las siguientes mejoras:
* Ahora se admiten los mapas de particiones de lista.
* Los límites de intervalo de solicitudes pueden coincidir con mayor facilidad con intervalos almacenados en el mapa de particiones.
* Ahora se admiten varias instancias de rol de trabajo para mejorar la disponibilidad.
* Las credenciales almacenadas como parte de la operación de División y combinación se cifran ahora en reposo.

## Procedimiento de actualización

Para actualizar a la versión más reciente de División y combinación, siga estos pasos:

1. Descargue la versión más reciente del paquete de División y combinación de NuGet, como se describe en la sección del tutorial de Introducción de División y combinación "Descargar los paquetes de División y combinación".
2. Cambie el archivo de configuración del servicio en la nube para que la implementación de división y combinación refleje los nuevos parámetros de configuración. Un nuevo parámetro requerido es la información acerca del certificado utilizado para el cifrado. Una manera fácil de hacerlo consiste en comparar el nuevo archivo de plantilla de configuración de la descarga con la configuración existente. Asegúrese de agregar la configuración para "DataEncryptionPrimaryCertificateThumbprint" y "DataEncryptionPrimary" tanto para la web como para el rol de trabajo.
3. Antes de implementar la actualización en Azure, asegúrese de que se han terminado todas las operaciones de división y combinación actualmente en ejecución. Puede hacerlo fácilmente consultando las tablas RequestStatus y PendingWorkflows en la base de datos de metadatos de División y combinación para solicitudes en curso.
4. Actualice la implementación existente del servicio en la nube para división y combinación en la suscripción de Azure con el nuevo paquete y el archivo de configuración del servicio actualizado.

No es necesario aprovisionar una nueva base de datos de metadatos para que se actualice la división y combinación. La nueva versión actualizará automáticamente su base de datos de metadatos existente a la nueva versión. 

## Escenarios de división/combinación 
Las aplicaciones necesitan extenderse de manera flexible más allá de los límites de una sola base de datos de Base de datos SQL de Azure, tal como se ilustra en los siguientes escenarios: 

* **Aumento de la capacidad: intervalos de división**: la capacidad de aumentar la capacidad agregada en la capa de datos aborda las crecientes necesidades de capacidad. En este escenario, la aplicación proporciona la capacidad adicional a través de la partición de los datos y de la distribución de los mismos cada vez en más base de datos hasta satisfacer las necesidades de capacidad. La característica 'split' del servicio de división/combinación de escalado elástico aborda este escenario. 

* **Reducción de la capacidad: intervalos de combinación**: La capacidad fluctúa debido a la naturaleza estacional de un negocio. Este escenario subraya la necesidad de escalar fácilmente a menos unidades de escalado cuando el negocio va lento. La función 'merge' del Servicio de división y combinación de escalado elástico abarca este requisito. 

* **Administración de zonas activas: desplazamiento de shardlets**: Con varios inquilinos por base de datos, la asignación de shardlets a particiones puede generar cuellos de botella en la capacidad de algunas particiones. Esto requiere volver a asignar shardlets o mover shardlets ocupados a particiones nuevas o menos utilizadas. 

A continuación, nos referiremos a cualquier procesamiento del servicio junto con estas funcionalidades como solicitudes de **división/combinación/desplazamiento**. 


Ilustración 1: Información general conceptual de División y combinación


![Overview][1] 


**Nota**:  No todos los escenarios de **aumento de capacidad** requieren el servicio División y combinación. Por ejemplo, si crea periódicamente nuevas particiones en su entorno para almacenar datos nuevos con valores clave de particionamiento en crecimiento, puede usar las API de cliente de Administración de mapa de particiones para dirigir nuevos rangos de datos a particiones creadas recientemente. El servicio División y combinación solo es necesario cuando también se deben mover los datos existentes.

## Conceptos y características clave

**Servicios hospedados por el cliente**: División y combinación se brinda como un servicio hospedado en el cliente. Debe implementar y hospedar el servicio en su suscripción a Microsoft Azure. El paquete que descarga de NuGet contiene una plantilla de configuración que se debe completar con la información correspondiente a la implementación específica. Consulte el [tutorial sobre División y combinación](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obtener más información. Dado que el servicio se ejecuta en su suscripción de Azure, es posible controlar y configurar la mayoría de los aspectos de seguridad del servicio. La plantilla predeterminada incluye las opciones para configurar SSL, autenticación de cliente basada en certificados, cifrado para credenciales almacenadas, protección ante denegación de servicio y restricciones de IP. Puede encontrar más información sobre los aspectos de seguridad en el siguiente documento [Consideraciones de seguridad del Escalado elástico](sql-database-elastic-scale-configure-security.md).

El servicio implementado de manera predeterminada se ejecuta con un rol de trabajo y un rol web. Cada uno de ellos usa el tamaño A1 de máquina virtual en Servicios en la nube de Azure. A pesar de que no puede modificar esta configuración cuando implementa el paquete, sí puede cambiarla después de una implementación exitosa en el servicio en la nube que está en ejecución (a través del portal de Azure). Tenga en cuenta que el rol de trabajo, por razones técnicas, no se debe configurar para más de una instancia. 

**Integración de mapas de particiones**: El servicio División y combinación interactúa con el mapa de particiones de la aplicación. Cuando use el servicio División y combinación para dividir o combinar intervalos o para mover shardlets entre particiones, el servicio mantiene actualizado automáticamente el mapa de particiones. Para ello, el servicio se conecta a la base de datos del administrador de mapa de particiones de la aplicación y mantiene los intervalos y asignaciones a medida que progresan las solicitudes de división/combinación/desplazamiento. Esto garantiza que el mapa de particiones siempre presente una vista actualizada cuando las operaciones de División y combinación se estén realizando. Las operaciones de división, combinación y desplazamiento de shardlets se implementan moviendo un lote de shardlets desde la partición de origen a la partición de destino. Durante la operación de desplazamiento de shardlets, los shardlets sujetos al lote actual se marcan como sin conexión en el mapa de particiones y no están disponibles para las conexiones de enrutamiento dependiente de los datos mediante la API **OpenConnectionForKey**. 

**Conexiones coherentes de shardlets**: cuando se inicia el desplazamiento de datos para un lote de shardlets nuevo, se cierra toda conexión de enrutamiento dependiente de los datos proporcionada por el mapa de particiones a la partición que almacena el shardlet, además, las conexiones subsiguientes desde las API de mapa de particiones a estos shardlets se bloquean mientras el desplazamiento de los datos está en curso a fin de evitar incoherencias. También se cierran las conexiones a otros shardlets de la misma partición, pero se realizarán correctamente de nuevo inmediatamente al reintentarlo. Una vez que se mueve el lote, los shardlets se marcan nuevamente como en línea para la partición de destino y los datos de origen se quitan de la partición de origen. El servicio pasa por estos pasos para cada lote, hasta que se han movido todos los shardlets. Esto dará lugar a operaciones de cierre de varias conexiones durante el curso de la operación de división/combinación/desplazamiento completa.   

**Administración de la disponibilidad de shardlets**: limitar el cierre de las conexiones al lote actual de shardlets, tal como se mencionó anteriormente, restringe el ámbito de la no disponibilidad a un lote de shardlets a la vez. Esto se prefiere sobre un enfoque en el que la partición completa deba permanecer sin conexión para todos sus shardlets durante el curso de una operación de división/combinación. El tamaño de un lote, definido como el número de los distintos shardlets que se deben mover a la vez, es un parámetro de configuración. Se puede definir para cada operación de división/combinación, dependiendo de la disponibilidad de la aplicación y de las necesidades de rendimiento. Tenga en cuenta que el intervalo que se está bloqueando en el mapa de particiones puede ser más grande que el tamaño del bloque especificado. Esto se debe a que el servicio escoge el tamaño del intercalo de manera tal que el número real de valores clave de particionamiento en los datos coincida, de manera aproximada, con el tamaño del lote. Es importante recordar esto, principalmente para las claves de particionamiento rellenas de manera dispersa. 

**Almacenamiento de metadatos**: el servicio División y combinación usa una base de datos para mantener su estado y conservar registros durante el procesamiento de las solicitudes. El usuario crea esta base de datos en su suscripción y proporciona la cadena de conexión para esta en el archivo de configuración para la implementación del servicio. Los administradores de la organización del usuario también pueden conectarse a esta base de datos para revisar el progreso de la solicitud y para investigar información detallada relacionada con posibles errores.

**Reconocimiento de particionamiento**: el servicio División y combinación hace la diferencia entre (1) tablas particionadas, (2) tablas de referencia y (3) tablas normales. La semántica de una operación de división/combinación/desplazamiento depende del tipo de tabla usada y se define de la siguiente manera: 

* **Tablas particionadas**: las operaciones de división/combinación/desplazamiento mueven los shardlets desde la partición de origen a la partición de destino. Tras finalizar correctamente la solicitud general, esos shardlets dejan de estar presentes en la partición de origen. Observe que las tablas de destino deben existir en la partición de destino y no pueden contener datos en el intervalo de destino antes del procesamiento de la operación. 

* **Tablas de referencia**: en el caso de las tablas de referencia, las operaciones de división, combinación y desplazamiento copian los datos de la partición de origen a la partición de destino. Observe que, sin embargo, no se producen cambios en la partición de destino de una tabla determinada si ya hay presente alguna fila en esta tabla en la partición de destino. La tabla debe estar vacía para que se procese la operación de copia de cualquier tabla de referencia.

* **Otras tablas**: es posible que haya presente otras tablas, ya sea en el origen o el destino de una operación de división/combinación. La división/combinación omite estas tablas en cualquier operación de copia o de desplazamiento de datos. Sin embargo, observe que pueden interferir con estas operaciones en caso de restricciones.

Las API **SchemaInfo** del mapa de particiones proporcionan la información en las tablas de referencia en comparación con las tablas particionadas. El siguiente ejemplo ilustra el uso de estas API en un objeto smm de administrador de mapa de particiones determinado: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Las tablas 'region' y 'nation' están definidas como tablas de referencia y se copiarán con las operaciones de división/combinación/desplazamiento. A su vez, 'customer' y 'orders' se definen como tablas particionadas. C_CUSTKEY y O_CUSTKEY actúan como la clave de particionamiento 

**Integridad referencial**: el servicio División y combinación analiza las dependencias entre las tablas y usa relaciones de clave externa-clave principal para realizar las operaciones de mover las tablas de referencia y los shardlets. En general, las tablas de referencia primero se copian en orden de dependencia, luego los shardlets se copia según el orden de sus dependencias dentro de cada lote. Esto es necesario para que las restricciones de clave foránea-clave principal de la partición de destino se procesen tan pronto como lleguen los datos nuevos. 

**Coherencia de mapa de particiones y eventual finalización**: si hay errores, el servicio División y combinación reanuda las operaciones después de cualquier interrupción y apunta a completar cualquier solicitud en curso. Sin embargo, puede haber situaciones irrecuperables, por ejemplo, cuando la partición de destino se pierde o se ve comprometida más allá de la reparación. En esas circunstancias, algunos shardlets que iban a moverse podrían seguir residiendo en la partición de origen. El servicio asegura que las asignaciones de shardlets solo se actualicen después de que se copian los datos necesarios a la partición de destino. Los shardlets se eliminan del origen solo una vez que todos los datos se han copiado al destino y que se han actualizado correctamente las asignaciones correspondientes. La operación de eliminación ocurre en segundo plano mientras el intervalo ya está en línea en la partición de destino. El servicio División y combinación siempre garantiza la exactitud de las asignaciones almacenadas en el mapa de particiones.

## Obtención de los archivos binarios del servicio

Los archivos binarios del servicio División y combinación se proporcionan a través de [Nuget](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Consulte el [tutorial paso a paso de División y combinación](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obtener más información sobre la descarga de los archivos binarios.

## La interfaz de usuario de División y combinación

Además de su rol de trabajo, el paquete del servicio División y combinación también incluye un rol web que se puede usar para enviar solicitudes de División y combinación de manera interactiva. Los componentes principales de la interfaz de usuario son los siguientes:

-    Tipo de operación: el tipo de operación es un botón de radio que controla la clase de operación que realiza el servicio para esta solicitud. Puede elegir entre los escenarios de división, combinación y desplazamiento analizados en Conceptos y características clave. Además, también puede cancelar una operación anteriormente enviada.  Puede usar las solicitudes de división, combinación y movimiento para los mapas de particiones de intervalo. Los mapas de particiones de lista solo admiten operaciones de movimiento.

-    Mapa de particiones: la sección siguiente de parámetros de solicitud abarca información acerca del mapa de particiones y de la base de datos que hospeda el mapa de particiones. En concreto, debe proporcionar el nombre del servidor de Base de datos SQL de Azure y la base de datos que hospeda el mapa de particiones, las credenciales para conectarse a la base de datos del mapa de particiones y, finalmente, el nombre del mapa de particiones. Actualmente, la operación solo acepta un conjunto de credenciales. Estas credenciales deben tener permisos suficientes para realizar cambios en el mapa de particiones, así como también en los datos de usuario en las particiones.

-    Intervalo de origen (División y combinación): Una operación de división y combinación procesa un intervalo con su clave superior e inferior. Para especificar una operación con un valor de clave superior desvinculado, active la casilla "La clave superior es máxima" y deje en blanco el campo de clave superior. Los valores de clave de intervalo que especifique no tienen que coincidir de manera precisa con una asignación y su límites en el mapa de particiones. Si no especifica ningún límite de intervalo, todo el servicio deducirá el intervalo más próximo automáticamente. Puede usar el script de PowerShell GetMappings.ps1 para recuperar las asignaciones actuales de un mapa de partición determinado.

-    Comportamiento y clave de división (división): en el caso de las operaciones de división, también necesita definir en qué punto desea dividir el intervalo de origen. Para ello, proporcione la clave de particionamiento donde desee que se produzca la partición. Utilice el botón de radio cercano para definir si desea mover la parte inferior del intervalo (excluyendo la clave de división) o si desea mover la parte superior (incluida la clave de división).

-    Shardlet de origen (desplazamiento): las operaciones de desplazamiento son diferentes a las operaciones de división o combinación, puesto que no requieren un rango para describir el origen. Un origen para el desplazamiento simplemente se identifica con el valor de la clave de particionamiento que planea mover.

-    Partición de destino (división): una vez que haya proporcionado la información sobre el origen de la operación de división, debe definir dónde desea copiar los datos, para lo cual debe proporcionar el nombre de la base de datos y el servidor de Base de datos SQL de Azure para el destino.

-    Intervalo de destino (combinación): por su parte, las operaciones de combinación mueven shardlets a una partición existente. Para identificar la partición existente, proporcione lo límites de intervalo del intervalo existente con el que desea combinar.

-    Tamaño de lote: el tamaño del lote controla el número de shardlets que quedarán sin conexión a la vez durante el desplazamiento de los datos. Este es un valor entero en el que puede usar valores más pequeños cuando sea susceptible ante largos períodos de tiempo de inactividad para los shardlets. Los valores mayores aumentarán el tiempo durante el cual un shardlet determinado está sin conexión, pero podrían mejorar el rendimiento.

-    Identificador de operación (cancelación): si tiene en curso una operación que ya no es necesaria, puede cancelarla si en este campo proporciona su identificador de operación. Puede recuperar el identificador de operación en la tabla de estado de solicitud (consulte la sección 8.1) o desde la salida del explorador web donde envió la solicitud.


## Requisitos y limitaciones 

La implementación actual del servicio División y combinación está sujeta a los siguientes requisitos y limitaciones: 

* Actualmente, las particiones deben existir y estar registradas en el mapa de particiones antes de que se pueda realizar una operación de División y combinación en estas particiones. 

* El servicio División y combinación actualmente no crea tablas ni ningún otro objeto de base de datos de manera automática como parte de sus operaciones. Esto significa que el esquema para todas las tablas particionadas y las tablas de referencia debe existir en la partición de destino antes de cualquier operación de división/combinación/desplazamiento. En especial, las tablas particionadas deben estar vacías en el intervalo donde una operación de división/combinación/desplazamiento agregará nuevos shardlets. De otro modo, la operación no pasará la comprobación de coherencia inicial en la partición de destino. Tenga en cuenta, además, que los datos de referencia solo se copian si la tabla de referencia está vacía y si no hay garantías de coherencia respecto de otras operaciones de escritura simultáneas en las tablas de referencia. Recomendamos que, en el momento de ejecutar operaciones de división/combinación, no haya otras operaciones de escritura realizando cambios en las tablas de referencia.

* El servicio se basa actualmente en la identidad de las filas establecida por una clave o índice único que incluye la clave de particionamiento para mejorar el rendimiento y la confiabilidad de shardlets de gran tamaño. Esto permite que el servicio mueva datos en una granularidad incluso más fina que solo el valor de clave de particionamiento. De este modo se reduce la cantidad máxima de espacio de registro y bloqueos que se requieren durante la operación. Considere la posibilidad de crear un índice único o una clave principal que incluya la clave de particionamiento en una tabla determinada si desea usar esa tabla con solicitudes de división/combinación/desplazamiento. Por motivos de rendimiento, la clave de particionamiento debe ser la columna inicial de la clave o del índice.

* Durante el procesamiento de la solicitud, algunos datos de shardlets pueden estar presentes tanto en la partición de origen como en la de destino. Esto actualmente es necesario como protección contra errores durante el desplazamiento de los shardlets. Tal como se explicó anteriormente, la integración de División y combinación con el mapa de particiones de Escalado elástico asegura que las conexiones mediante las API de enrutamiento dependiente de los datos y que usan el método **OpenConnectionForKey** en el mapa de particiones no ven ningún estado intermedio incoherente. Sin embargo, cuando se conecten a la partición de origen o a la partición de destino sin usar el método **OpenConnectionForKey**, es posible que los estados intermedios incoherentes sean visibles cuando las solicitudes de división/combinación/desplazamiento estén en desarrollo. Estas conexiones pueden mostrar resultados parciales o duplicados, dependiendo del tiempo o de la partición subyacente a la conexión. Esta limitación incluye actualmente las conexiones realizadas por Consultas a través de particiones múltiples de Escalado elástico.

* No se debe compartir la base de datos de metadatos para División y combinación entre diferentes roles. Por ejemplo, un rol del servicio de División y combinación que se ejecuta en ensayo tiene que señalar a una base de datos de metadatos diferente que el rol de producción.
 

## Facturación 

Debido a que el servicio División y combinación se ejecuta como un servicio en la nube en la suscripción de Microsoft Azure, los cambios normales de los servicios en la nube se aplican a las instancias de División y combinación. A menos que realice operaciones de división/combinación/desplazamiento de manera frecuente, le recomendamos eliminar el servicio en la nube División y combinación. Esto le ayudará a ahorrar costos relacionados con instancias de servicios en la nube en ejecución o implementadas. Puede volver a implementar e iniciar la configuración inmediatamente ejecutable cada vez que necesite realizar operaciones de División y combinación. 
  
## Supervisión 
### Tablas de estado 

El servicio División y combinación proporciona la tabla **RequestStatus** en la base de datos de repositorio de metadatos para supervisar las solicitudes completadas y en curso. La tabla contiene una fila para cada solicitud de División y combinación que se ha enviado a esta instancia del servicio División y combinación. Brinda la siguiente información para cada solicitud:

* **TimeStamp**: la hora y la fecha en que se inició la solicitud.

* **OperationId**: un GUID que identifica de manera única la solicitud. Esta solicitud también puede utilizarse para cancelar la operación mientras que todavía está en curso.

* **Estado**: el estado actual de la solicitud. Para las solicitudes en curso, también muestra la fase actual en que está la solicitud.

* **CancelRequest**: marca que indica si se ha cancelado la solicitud.

* **Progreso**: una estimación de porcentaje de la finalización de la operación. Un valor de 50 indica que la operación está aproximadamente un 50% completada.

* **Detalles**: un valor XML que proporciona un informe de progreso más detallado. El informe de progreso se actualiza periódicamente a medida que los conjuntos de filas se copian desde el origen al destino. En caso de errores o excepciones, esta columna también incluye información más detallada sobre el error.


### Diagnóstico de Azure 

La plantilla de servicio para División y combinación está preconfigurada para utilizar el almacenamiento WAD (diagnóstico de Microsoft Azure) para el registro detallado adicional y almacenamiento de diagnósticos. Puede controlar la configuración de WAD, como la cuenta de almacenamiento y las credenciales, a través de su archivo de configuración de servicio para División y combinación. La configuración de WAD para el servicio sigue las instrucciones de [Elementos fundamentales de servicios en la nube](http://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Incluye las definiciones para registrar los contadores de rendimiento, registros IIS, registros de eventos de Windows y registros de eventos de la aplicación División y combinación. Se pueden tener acceso fácilmente a estos registros desde el Explorador de servidores de Visual Studio en la parte de Azure del árbol del Explorador:

![Azure Diagnostics][2]   

WADLogsTable resaltado en la figura anterior contiene los eventos detallados del registro de aplicación del servicio División y combinación. Sin embargo, tenga en cuenta que la configuración predeterminada proporcionada como parte del paquete descargado está orientada a una implementación de producción. Como consecuencia, el intervalo en el que se extraen contadores y registros de las instancias de servicio es grande (cinco minutos). Para pruebas y desarrollo, puede reducir el intervalo ajustando la configuración de diagnóstico de la Web o el rol de trabajo a sus necesidades. Puede hacerlo con un clic con el botón derecho en el rol en el Explorador de servidores de Visual Studio (vea más arriba) y, a continuación, ajustando el período de transferencia en el cuadro de diálogo para las opciones de configuración de diagnósticos:

![Configuration][3]


Las distintas pestañas del cuadro de diálogo controlan los distintos tipos de registro, donde cada uno de ellos tiene su propia configuración de período de transferencia. 
La visibilidad de los registros y los contadores de WAD suele ser necesaria para los equipos de Microsoft en caso de que haya problemas con la implementación del servicio División y combinación. Puede usar herramientas como el [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/) para exportar registros WAD en archivos CSV que se pueden compartir fácilmente. 

## Rendimiento

El rendimiento de las operaciones de división, combinación y desplazamiento depende de varios factores. En general, se espera un mejor rendimiento de los niveles de servicio más altos y con mayor rendimiento de Base de datos SQL de Azure. Las asignaciones de memoria, CPU y E/S para los niveles de servicio más altos beneficiarán las operaciones de copia y eliminación masiva que el servicio División y combinación usa internamente. Por ese motivo, puede resultar atractivo aumentar el nivel de servicio para un determinado conjunto de bases de datos durante un período de tiempo limitado bien definido para realizar rápidamente operaciones de servicio de División y combinación programadas para intervalos hospedados en estas bases de datos.

Tenga en cuenta que el servicio también realiza consultas de validación como parte de su funcionamiento normal. Estas consultas de validación, entre otras cosas, comprueban la presencia inesperada de datos en el intervalo de destino y se aseguran de que cualquier operación de división/combinación/desplazamiento comience desde un estado coherente. Estas consultas realizando todo el trabajo sobre los intervalos de clave de particionamiento definidos por el ámbito de la operación de división/combinación/desplazamiento y el tamaño de lote que se proporcionan como parte de la definición de la solicitud. Estas consultas tienen un mejor rendimiento cuando existe un índice que tiene la clave de particionamiento como la columna inicial. 

Además, una propiedad de unicidad con la clave de particionamiento como la columna inicial permitirá que el servicio utilice un enfoque optimizado que limite el consumo de recursos en términos de memoria y espacio de registro. Esta propiedad de unicidad es necesaria para mover los tamaños de datos de gran tamaño (normalmente más de 1 GB). 

## Prácticas recomendadas y solución de problemas 
-    Considere la posibilidad de definir un inquilino de prueba y ejecute las operaciones de división/combinación/desplazamiento más importantes con el inquilino de prueba entre varias particiones. Esto le ayudará a asegurarse de que todos los metadatos estén correctamente definidos en el mapa de particiones y que las operaciones no infrinjan las restricciones o las claves externas.
-    Mantenga el tamaño del inquilino de prueba sobre el tamaño de datos máximo del inquilino de mayor tamaño para asegurarse de no encontrar problemas relacionados con el tamaño de los datos. Esto también le permitirá evaluar un límite superior en el tiempo necesario para mover a un solo inquilino. 
-    El servicio División y combinación requiere la capacidad de quitar los datos de la partición de origen una vez que los datos se han copiado correctamente en el destino. Revise el esquema detalladamente para asegurarse de que permite eliminaciones. Por ejemplo, eliminar desencadenadores puede impedir que el servicio elimine los datos en el origen y puede provocar errores en operaciones.
-    Asegúrese de que la clave de particionamiento sea la columna inicial de la clave principal o la definición de índice único. Eso asegurará el mejor rendimiento para las consultas de validación de división y combinación y para las operaciones de desplazamiento y eliminación de datos reales que siempre funcionan en intervalos de clave de particionamiento.
-    Por motivos de rendimiento y costo, colocar el servicio División y combinación en la región y el centro de datos donde residen las bases de datos suele ser la mejor opción. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

## Referencias 

* [Tutorial de División y combinación](sql-database-elastic-scale-configure-deploy-split-and-merge.md)

* [Consideraciones de seguridad de Escalado elástico](sql-database-elastic-scale-configure-security.md)  


<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-split-and-merge/diagnostics-config.png

<!--HONumber=47-->

<properties 
	pageTitle="Aspectos básicos sobre la API de Lote de Azure" 
	description="Conceptos para introducir a los desarrolladores al servicio Batch y a las API de Batch de Azure" 
	services="batch" 
	documentationCenter=".net" 
	authors="yidingzhou" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="batch" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-compute" 
	ms.date="07/14/2015" 
	ms.author="yidingz"/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# Aspectos básicos sobre la API de Lote de Azure

El servicio Lote de Azure proporciona un marco para la programación de trabajos diseñado para un cálculo escalable y distribuido. El servicio Lote mantiene un conjunto de máquinas virtuales que se encuentran en diferentes clústeres y centros de datos de Azure. El servicio Lote realiza el cálculo distribuido ejecutando uno o varios programas, ya sea a petición o programados, para su ejecución en un momento concreto en una colección determinada de estos nodos. El servicio Lote administra estos nodos para que ejecuten las tareas de cálculo según los requisitos, las especificaciones y las restricciones de recursos que especifique el usuario.

Al utilizar el servicio Lote, puede eliminar la necesidad de escribir código para la puesta en cola, programación, asignación y administración de recursos de proceso. Esto le permite centrarse en la aplicación específica y no preocuparse por la complejidad de la administración de recursos y la programación de trabajos en la plataforma subyacente. Además, esto también permite al servicio Lote optimizar la ubicación de estos trabajos, así como el acceso a los datos que necesitan para el procesamiento.

Estos son algunos de los escenarios que se pueden habilitar mediante el servicio Lote:

- Procesamiento paralelo de cálculo intensivo

- Limpieza diaria de archivos

- Procesamiento por lotes

## <a name="resource"></a> Recursos del servicio Lote

Cuando usa el servicio Lote, se beneficia de los siguientes recursos:

- [Cuenta](#account)

- [Nodo de proceso](#computenode)

- [Grupo](#pool)

- [Trabajo](#job)

- [Task](#task)

	- [Tarea de inicio](#starttask)
	
	- [Trabajo de ManagerTask](#jobmanagertask)

- [JobSchedule](#jobschedule)

### <a name="account"></a>Cuenta

Una cuenta de Lote es una entidad identificada de forma exclusiva en el servicio Lote. Todo el procesamiento se realiza a través de una cuenta de Lote. Al realizar operaciones con el servicio Lote, necesita el nombre y la clave de la cuenta. Para crear una cuenta de Lote, consulte la sección sobre la cuenta de Lote en la [Información general de Lote de Azure][].


### <a name="computenode"></a>Nodo de proceso

Un nodo de proceso es un nodo de Azure dedicado a una carga de trabajo concreta de la aplicación. El tamaño de un nodo determina el número de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asigna al nodo. Un nodo puede ser una máquina virtual pequeña, grande o extragrande, tal como se describe en [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/dn197896.aspx).

Entre los tipos de programa que un nodo puede ejecutar se incluyen archivos ejecutables (.exe), archivos de comandos (.cmd), archivos por lotes (.bat) y archivos de script. Un nodo tiene también los siguientes atributos:

- Carpetas del sistema de archivos que son específicas de la tarea y compartidas. Se crean variables de entorno y una estructura de carpetas en cada nodo del grupo. Se crea la siguiente estructura de carpetas con una carpeta "compartida" para las aplicaciones y los datos que se comparten entre las tareas, además de una carpeta para cada tarea.

<pre><code>─ % AZ_BATCH_NODE_ROOT_DIR %
   ├─Shared
   ├─Startup
   └─&lt;JOB_ID>
     ├─&lt;TASK_ID_1>
     │ └─wd
     └─&lt;TASK_ID_2>
       └─wd
</code></pre>


- Archivos stdout.txt y stderr.txt que se escriben en una carpeta específica de la tarea

- Variables de entorno para procesamiento

- Configuración de firewall que se configura para controlar el acceso

>Acceso al nodo
>
>Si el acceso a un nodo es necesario, por ejemplo, para depuración, se puede obtener el archivo RDP para acceder al nodo a través de Escritorio remoto.


### <a name="pool"></a>Grupo

Un grupo es una colección de nodos en donde se ejecuta la aplicación. Puede ser el usuario quien cree el grupo, pero también es posible que el servicio Lote lo cree automáticamente cuando se especifica el trabajo que se debe realizar. Puede crear y administrar un grupo que satisfaga las necesidades de su aplicación. Un grupo solo se puede usar con la cuenta de Lote en la que se creó. Una cuenta de Lote puede tener más de un grupo.

Los grupos de Lote de Azure se crean a partir de la plataforma principal de proceso de Azure. Los grupos de Lote ofrecen asignación a gran escala, instalación de datos y aplicaciones, movimiento de datos, supervisión de estado y escalado flexible de los nodos.

A cada nodo que se agrega a un grupo se le asigna un nombre único y una dirección IP asociada. Al quitarlo de un grupo, un nodo pierde los cambios realizados en el sistema operativo, todos los archivos locales, el nombre y la dirección IP. Cuando un nodo abandona un grupo, su vigencia finaliza.

Puede configurar un grupo que permita la comunicación entre los nodos que contiene. Si se solicita la comunicación dentro del grupo para un grupo, el servicio Lote habilita puertos superiores a 1100 en cada nodo del grupo. Cada nodo del grupo se configura para que permita conexiones entrantes y las restrinja únicamente a este intervalo de puertos, y solo si proceden de otros nodos del grupo. Si la aplicación no requiere comunicación entre nodos, el servicio Lote puede asignar potencialmente un gran número de nodos en diferentes centros de datos o clústeres al grupo para habilitar un mayor procesamiento paralelo.

Cuando se crea un grupo, puede especificar los siguientes atributos:

- El **tamaño de los nodos** del grupo.
	- El tamaño adecuado del nodo debe elegirse en función de las características y los requisitos de las aplicaciones que se van a usar en el nodo. Normalmente, el tamaño del nodo se seleccionará suponiendo que en cada nodo se va a ejecutar una tarea cada vez. Por ejemplo, el hecho de que la aplicación sea multiproceso y requiera una cantidad concreta de memoria será lo que determine el tamaño más adecuado y rentable para el nodo. Es posible tener varias tareas asignadas y varias instancias de aplicación ejecutándose en paralelo, en cuyo caso habrá que elegir un nodo de mayor tamaño. Vea a continuación la información sobre "el número máximo de tareas por nodo". 
	- Todos los nodos de un grupo deben tener el mismo tamaño. Si hay diferentes aplicaciones que deben ejecutarse con diferentes requisitos de sistema o con distintas cargas, habrá que crear grupos separados.
	- Todos los tamaños de nodo de servicio en la nube pueden configurarse para un grupo, excepto A0.

- La familia y la versión del sistema operativo que se ejecuta en los nodos.
	- Al igual que con los roles de trabajo, es posible configurar la familia y la versión del sistema operativo.
	- La familia del sistema operativo también determina qué versiones de .NET están instaladas con el sistema operativo.
	- Al igual que con los roles de trabajo, para la versión del SO se recomienda usar "*" de forma que los nodos se actualicen automáticamente y haya que realizar ningún trabajo para proporcionar nuevas versiones. El caso de uso principal para seleccionar una versión específica del sistema operativo es asegurarse de que se mantenga la compatibilidad de las aplicaciones y que se puedan realizar pruebas de compatibilidad con versiones anteriores antes de permitir la actualización de la versión. Una vez validada, se puede actualizar la versión del sistema operativo para el grupo e instalar la nueva imagen del sistema operativo. Cualquier tarea en ejecución se interrumpe y se vuelve a poner en cola.

- El número objetivo de nodos que debe estar disponible para el grupo.

- La directiva de escalado para el grupo. Además del número de nodos, también se puede especificar una fórmula de escalado automático para cada grupo. El servicio Lote ejecutará la fórmula para ajustar el número de lotes en función de las estadísticas del grupo y de los elementos de trabajo.

- Configuración de programación
	- La configuración predeterminada únicamente permite que se ejecute una sola tarea a la vez en un nodo del grupo, pero hay escenarios donde resulta ventajoso tener más de una tarea ejecutándose al mismo tiempo en un nodo. Por ejemplo, conviene aumentar el uso del nodo si una aplicación tiene que esperar la E/S. El hecho de tener más de una aplicación en ejecución aumentará el uso de CPU. También conviene reducir el número de nodos del grupo. Así se puede reducir la cantidad de copias de datos necesarias para grandes conjuntos de datos de referencia. Si A1 sería el tamaño correcto para la aplicación, podría elegir A4 y la configuración se definiría para ejecutar hasta 8 tareas a la vez, y cada una consumiría un núcleo.
	- La configuración del "número máximo de tareas por nodo" determina la cantidad máxima de tareas que se pueden ejecutar en paralelo.
	- También se puede especificar una "directiva de relleno", que determina si el servicio Lote rellena primero los nodos o si las tareas se reparten entre todos los nodos.
 
- El estado de comunicación de los nodos del grupo.
 	- En gran parte de los escenarios, las tareas funcionan de forma independiente y no es necesario que se comuniquen con otras tareas, pero existen algunas aplicaciones donde las tareas sí establecerán comunicaciones (por ejemplo, las aplicaciones que usan MPI).
	- Hay una configuración que controla si los nodos podrán comunicarse, que se utiliza para configurar la infraestructura de red subyacente y repercute en la ubicación de los nodos.

- La tarea de inicio de los nodos del grupo.

Cuando se crea un grupo, puede especificar la cuenta de almacenamiento que desea asociar al grupo. El servicio Lote asigna nodos desde los centros de datos con mejor conectividad de red y capacidad de ancho de banda a la cuenta de almacenamiento especificada. Esto permite a las cargas de trabajo tener acceso a datos de forma más eficaz.

### <a name="job"></a>Trabajo

Un trabajo es una colección de tareas. También especifica cómo se realiza el cálculo en los nodos de proceso de un grupo.

- El trabajo especifica el grupo en el que se va a ejecutar el trabajo. El grupo puede ser uno existente creado con anterioridad que muchos trabajos usen, pero también se puede crear un grupo para cada trabajo asociado a una programación de trabajos o para todos los trabajos asociados a una programación de trabajos.
- Se puede especificar una prioridad opcional. Cuando se envía un trabajo con una prioridad más alta que la de otros trabajos en curso, las tareas de trabajo de mayor prioridad se insertan en la cola por delante de las tareas de trabajo de menor prioridad. Las tareas de menor prioridad que ya se está ejecutando no se adelantarán.
- Restricciones
	- Se puede asignar un tiempo máximo para los trabajos. Si los trabajos se ejecutan durante más tiempo del máximo especificado, se pondrá fin al trabajo y a todas las tareas asociadas.
	- Lote de Azure puede detectar tareas que hayan generado errores y probar a ejecutarlas de nuevo. El número máximo predeterminado de reintentos para la tarea se puede especificar como una restricción, incluida la especificación de que una tarea se vuelva a intentar siempre o que no se vuelva a intentar nunca. Volver a intentar una tarea significa que la tarea se vuelve a poner en cola y se ejecutará de nuevo.
- El cliente puede agregar al trabajo tareas que deban ejecutarse para el trabajo, pero también se puede especificar una tarea del Administrador de trabajos. Una tarea del Administrador de trabajos usa la API de lote y contiene el código para crear las tareas necesarias para un trabajo con la tarea en ejecución en uno de los nodos del grupo. El servicio Lote controla específicamente las tareas del Administrador de trabajos: se pone en cola en cuanto se crea el trabajo y se reinicia si se produce un error por algún motivo. Se requiere un Administrador de trabajos para los trabajos con una programación de trabajos asociada, ya que es la única manera de definir las tareas antes de que se cree una instancia de trabajo.


### <a name="task"></a>Tarea

Una tarea es una unidad de cálculo que está asociada a un trabajo y se ejecuta en un nodo. Las tareas se asignan a un nodo para su ejecución o se ponen en cola hasta que quede libre un nodo. Una tarea usa los siguientes recursos:

- El programa que se especificó en el elemento de trabajo.

- Los archivos de recursos que contienen los datos que se procesan. Estos archivos se copian automáticamente en el nodo del almacenamiento de blobs. Para obtener más información, consulte Archivos y directorios.

- La configuración del entorno que necesita el programa. Para obtener más información, consulte Configuración del entorno para las tareas.

- Las restricciones en las que debe realizarse el cálculo. Por ejemplo, el tiempo máximo en el que la tarea se puede ejecutar, el número máximo de veces que una tarea debe intentarse si se produce un error y el tiempo máximo que se conservan los archivos del directorio de trabajo.

Además de las tareas que se pueden definir para realizar cálculos en un nodo, puede usar las siguientes tareas especiales que ofrece el servicio Lote:

- [Tarea de inicio](#starttask)

- [Tareas del Administrador de trabajos](#jobmanagertask)

#### <a name="starttask"></a>Tarea de inicio

Puede configurar el sistema operativo de los nodos de un grupo asociando una tarea de inicio al grupo. Instalar software e iniciar procesos en segundo plano son algunas de las acciones que puede realizar una tarea de inicio. La tarea de inicio se ejecuta cada vez que se inicia un nodo mientras este permanezca en el grupo.

Como con cualquier tarea por lotes, se puede especificar una lista de archivos en el almacenamiento de Azure, además de una línea de comandos que ejecute el servicio Lote. Lote de Azure copiará primero los archivos desde el almacenamiento de Azure y, a continuación, ejecutará la línea de comandos. Para una tarea de inicio de grupo, la lista de archivos suele contener el paquete o los archivos de las aplicaciones, pero también podría incluir datos de referencia que se usen en todas las tareas en ejecución en los nodos del grupo. La línea de comandos podría ejecutar cualquier script de PowerShell o robocopy, por ejemplo, para copiar archivos de la aplicación en la carpeta "compartida". También podría ejecutar un MSI.

Normalmente, es conveniente que el servicio Lote espere a que la tarea de inicio se complete y después considere que el nodo está preparado para asignarle tareas, pero esto se puede configurar.

Si falla una tarea de inicio en un nodo del grupo, el estado del nodo se actualiza para reflejar el error y el nodo no estará disponible para asignarle tareas. Una tarea de inicio puede fallar si hay un problema al copiar los archivos especificados para la tarea de inicio o si el proceso de la tarea de inicio devuelve un valor distinto de cero.

El hecho de declarar toda la información necesaria para configurar los nodos e instalar las aplicaciones se traduce en que aumentar el número de nodos de un grupo es tan sencillo como especificar la nueva cantidad necesaria. El servicio Lote tiene toda la información que necesita para configurar los nodos y prepararlos para aceptar tareas.

Una tarea de inicio se define mediante la adición de una sección JSON al cuerpo de solicitud para la operación Agregar grupo. En el ejemplo siguiente se muestra una definición básica de una tarea de inicio:

	{
		“commandLine”:”mypoolsetup.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”mypoolsetup.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“maxTaskRetryCount”:0
	}

Una interfaz de C# tiene el siguiente aspecto:

	CloudPool pool = pm.CreatePool(poolId, targetDedicated: 3, virtualMachineSize: "small", osFamily: "3");
	pool.StartTask = new StartTask();
	pool.StartTask.CommandLine = "mypoolsetup.exe";
	pool.StartTask.ResourceFiles = new List<IResourceFile>();
	pool.StartTask.ResourceFiles.Add(new ResourceFile("http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d", "mypoolsetup.exe"));
	pool.Commit();


#### <a name="jobmanagertask"></a>Tarea del Administrador de trabajos

Una tarea del administrador de trabajos se inicia antes que cualquier otra. La tarea del administrador de trabajos ofrece las siguientes ventajas:

- Se crea automáticamente por parte del servicio Lote cuando se crea el trabajo.

- Se programa antes que otras tareas en el trabajo.

- El nodo asociado a ella es el último que se quita de un grupo cuando se reduce el tamaño de dicho grupo.

- Se le concede la prioridad más alta cuando hay que reiniciarla. Si un nodo inactivo no está disponible, el servicio Lote puede finalizar una de las tareas en ejecución del grupo para dejarle espacio para que se ejecute.

- Su finalización se puede vincular a la finalización de todas las tareas en el trabajo.

Una tarea del administrador de trabajos en un mismo trabajo no tiene prioridad sobre las tareas de otros trabajos. Entre trabajos, solo se tienen en cuenta las prioridades a nivel de trabajo. Una tarea del administrador de trabajos se define mediante la adición de una sección de XML al cuerpo de solicitud para la operación de Agregar elemento de trabajo. En el ejemplo siguiente se muestra una definición básica de una tarea del administrador de trabajos:

	{
		“name”:”jmTask”,
		“commandLine”:”myapp1.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp1.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“taskConstraints”:
		{
			“maxWallClockTime”:”PT1H”,
			“maxTaskRetryCount”:0,
			“retentionTime”:”PT1H”
		},
		“killJobOnCompletion”:true,
		“runElevated”:false,
		“runExclusive”:true
	}


### <a name="jobschedule"></a>Programación de trabajos

Una programación de trabajos es una manera de crear varios trabajos con una programación. Al crear una programación de trabajos, se crea un trabajo por instancia de la programación.

## <a name="workflow"></a>Flujo de trabajo del servicio Lote

Se necesita una cuenta de Lote para utilizar el servicio Lote, y se usan varios recursos del servicio para programar un cálculo. Utilice el siguiente flujo de trabajo básico al crear un escenario de cálculo distribuido con el servicio Lote:

1\. Cargue los archivos que desea utilizar en el escenario de cálculo distribuido a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio de Lote pueda tener acceso a ellos. El servicio Lote los carga en un nodo cuando se ejecuta la tarea.

2\. Cargue los archivos binarios dependientes en la cuenta de almacenamiento. Los archivos binarios incluyen el programa que ejecutará la tarea y los ensamblados dependientes. Estos archivos también deben tener acceso desde el almacenamiento y se cargan en el nodo.

3\. Cree un grupo de nodos. Puede asignar el tamaño de la máquina virtual de tareas que se utilizará cuando se crea el grupo. Cuando se ejecuta una tarea, se asigna un nodo de este grupo.

4\. Cree un elemento de trabajo. Cuando se crea un elemento de trabajo, se crea automáticamente un trabajo. Un elemento de trabajo le permite administrar un trabajo de tareas.

5\. Agregue tareas al elemento de trabajo. Cada tarea usa el programa que ha cargado para procesar la información de un archivo cargado.

6\. Supervise los resultados de la salida.

## <a name="files"></a>Archivos y directorios

Cada tarea tiene un directorio de trabajo en el que crea cero o más directorios y archivos para almacenar el programa que ejecuta una tarea, los datos procesados por una tarea y el resultado del procesamiento realizado por una tarea. Estos archivos y directorios a continuación se ponen a disposición de otras tareas para que los utilicen durante la ejecución de un trabajo. Todas las tareas, archivos y directorios de un nodo pertenecen a una cuenta de usuario único.

El servicio Lote expone parte del sistema de archivos en un nodo como directorio raíz. El directorio raíz del nodo está disponible para una tarea mediante la variable de entorno AZ\_BATCH\_NODE\_ROOT\_DIR. Para obtener más información acerca del uso de variables de entorno, consulte Configuración del entorno para las tareas.

El directorio raíz contiene los siguientes subdirectorios:

- **Tareas**: en esta ubicación se almacenan todos los archivos que pertenecen a las tareas que se ejecutan en el nodo. Para cada tarea, el servicio Lote crea un directorio de trabajo con la ruta de acceso única en forma de %AZ\_BATCH\_TASK\_ROOT\_DIR%. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio, y este directorio se conserva en función de la restricción RetentionTime especificada para la tarea.

- **Compartido**: esta ubicación es un directorio compartido para todas las tareas de la cuenta. En el nodo, el directorio compartido se encuentra en %AZ\_BATCH\_NODE\_SHARED\_DIR%. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio.

- **Inicio**: una tarea de inicio utiliza esta ubicación como directorio de trabajo. Todos los archivos descargados por el servicio Lote para iniciar la tarea de inicio también se almacenan en este directorio. En el nodo, el directorio de inicio se encuentra en %AZ\_BATCH\_NODE\_START\_DIR%. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio, y este directorio puede utilizarse por las tareas de inicio para configurar el sistema operativo.

Cuando se quita un nodo del grupo, se quitan todos los archivos que están almacenados en el nodo.

## <a name="lifetime"></a>Duración del nodo y el grupo

Una decisión de diseño esencial consiste en determinar cuándo se crean los grupos y cuánto tiempo se mantienen disponibles los nodos.

En un extremo, se podría crear un grupo por cada trabajo cuando se envíe el trabajo y eliminar los nodos a medida que las tareas finalicen su ejecución. Así se maximizará el uso, ya que los nodos solo se asignan cuando es absolutamente necesario y se cierran en cuanto quedan inactivos. Esto significa que el trabajo debe esperar a que se asignen los nodos, aunque es importante tener en cuenta que las tareas se programarán en los nodos en cuanto cada uno de ellos esté disponible y asignado, y se complete la tarea de inicio; es decir, el servicio Lotes no espera hasta que estén disponible todos los nodos de un grupo, ya que esto generaría un uso deficiente.

Si la prioridad estriba en que los trabajos inicien su ejecución inmediatamente, hay que crear un grupo y los nodos deben estar disponibles antes de enviar el trabajo. Las tareas se pueden empezar inmediatamente, pero, según la carga, los nodos podrían estar inactivos en espera de las tareas de trabajo.

Un patrón habitual cuando hay una cantidad variable de carga continua es tener un grupo al que se envían varios trabajos, y escalar o reducir verticalmente el número de nodos en función de la carga. Esto se puede realizar de forma reactiva o proactiva (en caso de que se pueda predecir la carga).

## <a name="scaling"></a>Escalado de aplicaciones

La aplicación se puede escalar o reducir verticalmente de forma automática para ajustarse al cálculo que necesite. Puede ajustar dinámicamente el número de nodos de un grupo en función de la carga de trabajo actual y las estadísticas de uso de los recursos. También puede optimizar el costo general de ejecución de la aplicación configurándola para que se escale automáticamente. Puede especificar los valores de escalado para un grupo cuando se crea y puede actualizar la configuración en cualquier momento.

Para disminuir el número de nodos, hay que tener en cuenta las tareas que pueden estar ejecutándose en los nodos. Se especifica una directiva de desasignación que determina si las tareas en ejecución se detienen para quitar inmediatamente el nodo o si las tareas pueden finalizar antes de quitar los nodos. El hecho de establecer en cero el número objetivo de nodos al final de un trabajo, pero permitir la ejecución de tareas hasta que finalicen, maximizará el uso.

Puede especificar el escalado automático de una aplicación mediante un conjunto de fórmulas de escalado. Se trata de fórmulas que pueden emplearse para determinar el número de nodos que se encuentran en el grupo durante el siguiente intervalo de escalado. Por ejemplo, necesita enviar un gran número de tareas para que se programen en un grupo. Puede asignar una fórmula de escalado al grupo que especifica su tamaño en función del número actual de tareas pendientes y la tasa de finalización de las tareas. El servicio Lote evalúa periódicamente la fórmula y cambia el tamaño del grupo en función de la carga de trabajo.

Una fórmula puede basarse en las métricas siguientes:

- **Métricas de tiempo**: basadas en las estadísticas recopiladas cada cinco minutos en el número especificado de horas.

- **Métricas de recursos**: basadas en el uso de CPU, el uso de ancho de banda, el uso de memoria y el número de nodos.

- **Métricas de tareas**: basadas en el estado de las tareas, como Activo, Pendiente y Completado.

Para obtener más información acerca de cómo escalar automáticamente una aplicación, vea Configuración del escalado automático de máquinas virtuales de tareas.

>Eliminar nodos
>
>No se suele ser necesario, pero es posible especificar que se quiten nodos individuales de un grupo. Por ejemplo, en caso de que se sospeche que un nodo es poco confiable, podría quitarse.

## <a name="cert"></a>Certificados para aplicaciones

Normalmente, deberá utilizar certificados al cifrar la información secreta. Pueden instalarse certificados en los nodos. Los secretos cifrados se pasan a las tareas en los parámetros de línea de comandos o se insertan en uno de los recursos, y se pueden utilizar los certificados instalados para descifrarlos. Un ejemplo de información secreta es la clave de una cuenta de almacenamiento.

Utilice la operación Agregar certificado para agregar un certificado a una cuenta de Lote. Se puede asociar el certificado a un grupo nuevo o existente. Cuando se asocia un certificado a un grupo, el servicio Lote instala el certificado en cada nodo del grupo. El servicio Lote instala los correspondientes certificados cuando se inicie el nodo, antes de que inicie ninguna tarea, lo cual incluye tareas de inicio y tareas del Administrador de trabajos.

## <a name="scheduling"></a>Prioridad de programación

Cuando se crea un elemento de trabajo, puede asignarle una prioridad. Cada trabajo del elemento de trabajo se crea con esta prioridad. El servicio Lote utiliza los valores de prioridad del trabajo para determinar el orden de programación de trabajos dentro de una cuenta. Los valores de prioridad pueden oscilar entre -1000 y 1000, siendo -1000 la prioridad más baja y 1000 la prioridad más alta. Puede actualizar la prioridad de un trabajo mediante la operación UpdateJob.

Dentro de la misma cuenta, los trabajos de mayor prioridad tienen primacía de programación sobre los trabajos con menor prioridad. Un trabajo con un valor de prioridad determinado en una cuenta no tiene primacía de programación sobre otro trabajo con un valor de prioridad inferior de una cuenta diferente.

La programación de los diferentes grupos es independiente. En grupos diferentes, no se garantiza que un trabajo con una prioridad más alta se programe antes si el grupo asociado tiene pocos nodos inactivos. En el mismo grupo, los trabajos con el mismo nivel de prioridad tienen la misma probabilidad de ser programados.

## <a name="environment"></a>Configuración del entorno para las tareas

Puede especificar la configuración del entorno que se utiliza en el contexto de una tarea. La configuración del entorno para una tarea de inicio y las tareas que se ejecutan en un trabajo se define mediante la adición de una sección de XML al cuerpo de la solicitud de las operaciones Agregar tarea o Actualizar tarea.

En el ejemplo siguiente se muestra la definición de una configuración de entorno:

Para cada tarea programada en un trabajo, se establece un conjunto específico de variables de entorno por parte del servicio Lote. En la tabla siguiente se enumeran las variables de entorno establecidas por el servicio Lote para todas las tareas.

| Nombre de la variable de entorno | Descripción |
|------------------------------------|--------------------------------------------------------------------------|
| AZ\_BATCH\_ACCOUNT\_NAME | El nombre de la cuenta a la que pertenece la tarea. |
| AZ\_BATCH\_JOB\_ID | El nombre del trabajo al que pertenece la tarea. |
| AZ\_BATCH\_TASK\_ID | El nombre de la tarea actual. |
| AZ\_BATCH\_POOL\_ID | El nombre del grupo en el que se ejecuta la tarea. |
| AZ\_BATCH\_NODE\_ID | Nombre del nodo en el que se ejecuta la tarea. |
| AZ\_BATCH\_NODE\_ROOT\_DIR | Ruta de acceso completa del directorio raíz en el nodo. |
| AZ\_BATCH\_NODE\_SHARED\_DIR | Ruta de acceso completa del directorio compartido en el nodo. |
| AZ\_BATCH\_NODE\_STARTUP\_DIR | Ruta de acceso completa del directorio de la tarea de inicio del nodo de grupo en el nodo. |
| AZ\_BATCH\_NODE\_TASK\_DIR | Ruta de acceso completa del directorio de tareas en el nodo. |
| AZ\_BATCH\_NODE\_TASK\_WORKING\_DIR | Ruta de acceso completa del directorio de trabajo en el nodo. |
| AZ\_BATCH\_NODE\_JOB\_PREP\_DIR | Ruta de acceso completa del directorio de tareas de preparación del trabajo en el nodo. |
| AZ\_BATCH\_NODE\_JOB\_PREP\_WORKING\_DIR | Ruta de acceso completa del directorio de trabajo de tareas de preparación del trabajo en el nodo. |

**Nota:**

No se pueden sobrescribir estas variables definidas por el sistema.

Puede recuperar el valor de la configuración del entorno mediante una operación Obtener tarea.

## <a name="errorhandling"></a>Control de errores

###Tarea de control de errores
Los errores en las tareas se dividen en las siguientes categorías:

- Errores de programación:
	- Si se especifican archivos para la tarea, podría producirse un error en la copia de uno o varios de los archivos. Podría deberse a que los archivos se han movido, la cuenta de almacenamiento ya no está disponible, etc.
	- En este caso, se define un "Error de programación" para la tarea.
- Errores de aplicación:
	- El proceso de tarea especificado por la línea de comandos también puede fallar. El proceso se considera que ha generado un error cuando se devuelve un código de salida distinto de cero.
	- Para los errores de aplicaciones, es posible configurar el servicio Lote para volver a intentar automáticamente la tarea hasta un número especificado de veces. 
- Errores de restricción:
	- Se puede especificar una restricción para la cantidad máxima de tiempo que se puede ejecutar un trabajo o tarea. Esto puede ser útil para finalizar una tarea que se haya bloqueado.
	- Cuando se supere la cantidad máxima de tiempo, la tarea se marcará como completada, pero el código de salida se marcará como `0xC000013A` y el campo schedulingError se marcará como `{ category:“ServerError”, code=“TaskEnded”}`.

###Depuración de errores de aplicación

Una aplicación puede generar diagnósticos que se pueden utilizar para solucionar problemas. A menudo, las aplicaciones escribirán información en los archivos stdout y stderr o generarán salidas en archivos personalizados. En estos casos, se proporciona una API para obtener los archivos, especificando la tarea o el nodo.

También es posible iniciar sesión en nodos del grupo. Una API devuelve el archivo RDP de un nodo, que se puede usar para iniciar sesión en el nodo.

###Resolución de problemas y errores en las tareas

Tareas pueden generar errores o interrumpirse por diversas razones. Por ejemplo, la propia aplicación de tarea puede fallar, el nodo en el que se ejecuta la tarea se reinicia o el nodo se elimina debido a un cambio de tamaño del grupo con la directiva de desasignación establecida para quitar el nodo inmediatamente sin esperar a que finalice la tarea. En todos los casos, el servicio Lote puede volver a poner en cola la tarea automáticamente y realizar la ejecución en otro nodo.

También es posible que un problema intermitente haga que una tarea deje de responder o tarde demasiado tiempo en ejecutarse. El tiempo de ejecución máximo se puede definir para una tarea. Si se supera este valor, el servicio Lote interrumpirá la aplicación de la tarea. Actualmente, no es posible volver a poner en cola de forma automática este caso, pero el caso lo puede detectar el cliente, el cual puede enviar una nueva tarea.

###Resolución de nodos "defectuosos"

Cada nodo de un grupo tiene un nombre único y el nodo en el que se ejecuta una tarea se incluye en los metadatos de la tarea. En caso de que exista un nodo que, por alguna razón, esté causando errores en las tareas, el cliente puede determinar la causa y eliminar el nodo del grupo. Si una tarea se ejecutaba en el nodo que se eliminó, se podrá automáticamente en la cola de nuevo y se ejecutará en otra máquina virtual.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[Información general de Lote de Azure]: batch-technical-overview.md

<!---HONumber=August15_HO6-->
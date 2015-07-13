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
	ms.date="03/19/2015" 
	ms.author="yidingz"/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# Aspectos básicos sobre la API de Lote de Azure

El servicio Lote de Azure proporciona un marco para la programación de trabajos diseñado para un cálculo escalable y distribuido. El servicio Lote mantiene un conjunto de máquinas virtuales que se encuentran en diferentes clústeres y centros de datos de Azure. El servicio Lote realiza el cálculo distribuido ejecutando uno o varios programas, ya sea a petición o programados para ejecutarse en un momento concreto en una colección específica de estas máquinas virtuales. El servicio Lote administra estas máquinas virtuales para ejecutar las tareas de cálculo según los requisitos de recursos, especificaciones y restricciones proporcionadas por el usuario.

Al utilizar el servicio Lote, puede eliminar la necesidad de escribir código para la puesta en cola, programación, asignación y administración de recursos de proceso. Esto le permite centrarse en la aplicación específica y no preocuparse por la complejidad de la administración de recursos y la programación de trabajos en la plataforma subyacente. Además, esto también permite al servicio Lote optimizar la ubicación de estos trabajos, así como el acceso a los datos que necesitan para el procesamiento.

Estos son algunos de los escenarios que se pueden habilitar mediante el servicio Lote:

- Procesamiento paralelo de cálculo intensivo

- Limpieza diaria de archivos

- Procesamiento por lotes

## <a name="resource"></a> Recursos del servicio Lote

Cuando usa el servicio Lote, se beneficia de los siguientes recursos:

- [Cuenta](#account)

- [Máquina virtual de tareas](#taskvm)

- [Grupo](#pool)

- [Elemento de trabajo](#workitem)

- [Trabajo](#job)

- [Task](#task)

	- [Tarea de inicio](#starttask)
	
	- [Trabajo de ManagerTask](#jobmanagertask)

### <a name="account"></a>Cuenta

Una cuenta de Lote es una entidad identificada de forma exclusiva en el servicio Lote. Todo el procesamiento se realiza a través de una cuenta de Lote. Al realizar operaciones con el servicio Lote, necesita el nombre y la clave de la cuenta. Para crear una cuenta de Lote, consulte la sección sobre la cuenta de Lote en la [Información general de Lote de Azure][].


### <a name="taskvm"></a>Máquina virtual de tareas

Una máquina virtual de tareas (TVM) es una máquina virtual de Azure dedicada a una carga de trabajo específica de la aplicación. El tamaño de una TVM determina el número de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asigna a la TVM. Una TVM puede ser una máquina virtual pequeña, grande o extragrande, como se describe en [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/dn197896.aspx).

Los tipos de programas que se puede ejecutar una TVM incluyen archivos ejecutables (.exe), archivos de comandos (.cmd), archivos por lotes (.bat) y archivos de script. Una TVM también tiene los siguientes atributos:

- Carpetas del sistema de archivos que son específicas de la tarea y compartidas. Se crean variables de entorno y una estructura de carpetas en cada máquina virtual del grupo. Se crea la siguiente estructura de carpetas con una carpeta "compartida" para las aplicaciones y los datos que se comparten entre las tareas, además de una carpeta para cada tarea.

![][1]

- Archivos stdout.txt y stderr.txt que se escriben en una carpeta específica de la tarea

- Variables de entorno para procesamiento

- Configuración de firewall que se configura para controlar el acceso

>Acceso a la máquina virtual
>
>Si el acceso a una máquina virtual es obligatorio, por ejemplo, para depuración, se puede obtener el archivo RDP para acceder a la máquina virtual a través de escritorio remoto.


### <a name="pool"></a>Grupo

Un grupo es una colección de TVM en el que se ejecuta la aplicación. Puede ser el usuario quien cree el grupo, pero también es posible que el servicio Lote lo cree automáticamente cuando se especifica el trabajo que se debe realizar. Puede crear y administrar un grupo que satisfaga las necesidades de su aplicación. Un grupo solo se puede usar con la cuenta de Lote en la que se creó. Una cuenta de Lote puede tener más de un grupo.

Los grupos de Lote de Azure se crearon a partir de la plataforma principal de proceso de Azure. Los grupos de Lote proporcionan asignación a gran escala, instalación de datos y aplicación, movimiento de datos, supervisión de estado y escalado flexible de la máquina virtual.

A cada TVM que se agrega a un grupo se le asigna un nombre único y una dirección IP asociada. Cuando se quita una TVM de un grupo, esta pierde los cambios realizados en el sistema operativo, todos sus archivos locales, su nombre y su dirección IP. Cuando una TVM abandona un grupo, su vigencia finaliza.

Puede configurar un grupo para permitir la comunicación entre las TVM que contiene. Si se solicita la comunicación dentro del grupo para un grupo, el servicio Lote habilita puertos superiores a 1100 en cada TVM del grupo. Cada TVM del grupo está configurada para permitir o restringir las conexiones entrantes a solo este intervalo de puertos y solo desde otras TVM del grupo. Si la aplicación no requiere comunicación entre TVM, el servicio Lote puede asignar potencialmente un gran número de TVM en los diferentes centros de datos o clústeres al grupo para habilitar un mayor procesamiento paralelo.

Cuando se crea un grupo, puede especificar los siguientes atributos:

- El **tamaño de las máquinas virtuales** incluidas en el grupo.
	- El tamaño adecuado de la máquina virtual se debe elegir en función de las características y los requisitos de la aplicación o las aplicaciones que se van a usar en la máquina virtual. Normalmente, se seleccionará el tamaño de la máquina virtual suponiendo que en cada máquina virtual se va a ejecutar una tarea cada vez. Por ejemplo, el hecho de que la aplicación sea de varios subprocesos y requiera una cantidad concreta de memoria será lo que determinará el tamaño más adecuado y rentable para la máquina virtual. Es posible tener varias tareas asignadas y varias instancias de aplicación ejecutándose en paralelo, en cuyo caso habrá que elegir una máquina virtual de mayor tamaño. Consulte a continuación la información sobre "el número máximo de tareas por máquina virtual". 
	- Todas las máquinas virtuales de un grupo deben tener el mismo tamaño. Si hay diferentes aplicaciones que deben ejecutarse con diferentes requisitos de sistema o con distintas cargas, habrá que crear grupos separados.
	- Todos los tamaños de máquina virtual de servicio en la nube pueden configurarse para un grupo, excepto A0.

- La familia del sistema operativo y la versión que se ejecuta en las máquinas virtuales.
	- Al igual que con los roles de trabajo, es posible configurar la familia y la versión del sistema operativo.
	- La familia del sistema operativo también determina qué versiones de .NET están instaladas con el sistema operativo.
	- Al igual que con los roles de trabajo, para la versión del SO se recomienda utilizar "*" de forma que las máquinas virtuales se actualicen automáticamente y no sea necesario realizar ningún trabajo para proporcionar nuevas versiones. El caso de uso principal para seleccionar una versión específica del sistema operativo es asegurarse de que se mantenga la compatibilidad de las aplicaciones y que se puedan realizar pruebas de compatibilidad con versiones anteriores antes de permitir la actualización de la versión. Una vez validada, se puede actualizar la versión del sistema operativo para el grupo e instalar la nueva imagen del sistema operativo. Cualquier tarea en ejecución se interrumpe y se vuelve a poner en cola.

- El número objetivo de máquinas virtuales que debe estar disponible para el grupo.

- La directiva de escalado para el grupo. Además del número de máquinas virtuales, también se puede especificar una fórmula de escalado automático para cada grupo. El servicio Lote ejecutará la fórmula para ajustar el número de máquinas virtuales en función de las estadísticas del grupo y de los elementos de trabajo.

- Configuración de programación
	- La configuración predeterminada permite que se ejecute solo una tarea a la vez en un grupo de máquinas virtuales, pero hay escenarios donde resulta ventajoso tener más de una tarea ejecutándose al mismo tiempo en una máquina virtual. Por ejemplo, es adecuado para aumentar la utilización de la máquina virtual si una aplicación tiene que esperar la E/S. El hecho de tener más de una aplicación en ejecución aumentará la utilización de la CPU. También es útil para reducir el número de máquinas virtuales del grupo. Así se puede reducir la cantidad de copias de datos requeridas para grandes conjuntos de datos de referencia. Si A1 sería el tamaño correcto para la aplicación, podría elegir A4 y la configuración se definiría para ejecutar hasta 8 tareas a la vez, y cada una consumiría un núcleo.
	- La configuración del "número máximo de tareas por máquina virtual" determina la cantidad máxima de tareas que se pueden ejecutar en paralelo.
	- También se puede especificar una "directiva de relleno", que determina si el servicio Lote debe rellenar primero la máquina virtual o si las tareas se reparten entre todas las máquinas virtuales.
 
- El estado de comunicación de las máquinas virtuales en el grupo.
 	- En gran parte de los escenarios, las tareas funcionan de forma independiente y no es necesario que se comuniquen con otras tareas, pero existen algunas aplicaciones donde las tareas sí establecerán comunicaciones (por ejemplo, las aplicaciones que usan MPI).
	- Hay una configuración que controla si la máquina virtual podrá comunicarse, que se utiliza para configurar la infraestructura de red subyacente y repercute en la ubicación de las máquinas virtuales.

- La tarea de inicio de las TVM del grupo.

Cuando se crea un grupo, puede especificar la cuenta de almacenamiento que desea asociar al grupo. El servicio Lote asigna TVM desde los centros de datos con mejor conectividad de red y capacidad de ancho de banda a la cuenta de almacenamiento especificada. Esto permite a las cargas de trabajo tener acceso a datos de forma más eficaz.

### <a name="workitem"></a>Elemento de trabajo

Un elemento de trabajo especifica cómo se realiza el cálculo en las TVM de un grupo.

- Un elemento de trabajo puede tener uno o varios trabajos asociados a él. Puede especificar una programación opcional para un elemento de trabajo, en cuyo caso se crea un trabajo para cada aparición de la programación. Si no se especifica ninguna programación para el trabajo a petición, se crea un trabajo inmediatamente.
- El elemento de trabajo especifica el grupo en el que se va a ejecutar el trabajo. El grupo puede ser uno existente que se haya creado con anterioridad y que lo usen muchos elementos de trabajo, pero también se puede crear un grupo para cada trabajo asociado con el elemento de trabajo o para todos los trabajos asociados con él.
- Se puede especificar una prioridad opcional. Cuando se envía un elemento de trabajo con una prioridad más alta que la de otros elementos de trabajo en curso, las tareas de elemento de trabajo con prioridad más alta se insertan en la cola por delante de las tareas de elemento de trabajo con prioridad inferior. Las tareas de menor prioridad que ya se está ejecutando no se adelantarán.
- Se pueden especificar restricciones para aplicarlas al trabajo o a los trabajos asociados.
	- Se puede asignar un tiempo máximo para los trabajos. Si los trabajos se ejecutan durante más tiempo del máximo especificado, se pondrá fin al trabajo y a todas las tareas asociadas.
	- Lote de Azure puede detectar tareas que hayan generado errores y probar a ejecutarlas de nuevo. El número máximo predeterminado de reintentos para la tarea se puede especificar como una restricción, incluida la especificación de que una tarea se vuelva a intentar siempre o que no se vuelva a intentar nunca. Volver a intentar una tarea significa que la tarea se vuelve a poner en cola y se ejecutará de nuevo.
- Las tareas que se deben ejecutar para el elemento de trabajo las puede especificar el cliente de la misma manera que se ha creado el elemento de trabajo, pero, alternativamente, se puede especificar una tarea del Administrador de trabajos. Una tarea del administrador de trabajos utiliza la API del lote y contiene el código para crear las tareas necesarias para un trabajo con la tarea que se está ejecutando en una de las máquinas virtuales del grupo. El servicio Lote controla específicamente las tareas del Administrador de trabajos: se pone en cola en cuanto se crea el trabajo y se reinicia si se produce un error por algún motivo. Se requiere un Administrador de trabajos para los elementos de trabajo que tienen una programación asociada, ya que es la única manera de definir las tareas antes de que se cree una instancia de trabajo.

### <a name="job"></a>Trabajo

Un trabajo es una instancia en ejecución de un elemento de trabajo y consta de una colección de tareas. El servicio Lote crea una instancia de un trabajo en función de la configuración del elemento de trabajo. El trabajo utiliza TVM desde el grupo que está asociado con el elemento de trabajo.

### <a name="task"></a>Tarea

Una tarea es una unidad de cálculo que está asociada con un trabajo y se ejecuta en una TVM. Las tareas se asignan a una máquina virtual para su ejecución o se ponen en cola hasta que quede libre una máquina virtual. Una tarea usa los siguientes recursos:

- El programa que se especificó en el elemento de trabajo.

- Los archivos de recursos que contienen los datos que se procesan. Estos archivos se copian automáticamente en la TVM del almacenamiento de blobs. Para obtener más información, consulte Archivos y directorios.

- La configuración del entorno que necesita el programa. Para obtener más información, consulte Configuración del entorno para las tareas.

- Las restricciones en las que debe realizarse el cálculo. Por ejemplo, el tiempo máximo en el que la tarea se puede ejecutar, el número máximo de veces que una tarea debe intentarse si se produce un error y el tiempo máximo que se conservan los archivos del directorio de trabajo.

Además de las tareas que se pueden definir para realizar cálculos en una TVM, puede utilizar las siguientes tareas especiales proporcionadas por el servicio Lote:

- [Tarea de inicio](#starttask)

- [Tareas del Administrador de trabajos](#jobmanagertask)

#### <a name="starttask"></a>Tarea de inicio

Puede configurar el sistema operativo de las máquinas virtuales incluidas en un grupo mediante la asociación de una tarea de inicio con el grupo. Instalar software e iniciar procesos en segundo plano son algunas de las acciones que puede realizar una tarea de inicio. La tarea de inicio se ejecuta cada vez que se inicia una máquina virtual, siempre que esta permanezca en el grupo.

Como con cualquier tarea por lotes, se puede especificar una lista de archivos en el almacenamiento de Azure, además de una línea de comandos que ejecute el servicio Lote. Lote de Azure copiará primero los archivos desde el almacenamiento de Azure y, a continuación, ejecutará la línea de comandos. Para una tarea de inicio de grupo, la lista de archivos suele contener los archivos de las aplicaciones o los paquetes, pero también podría incluir datos de referencia que se utilizarán en todas las tareas que se ejecuten en las máquinas virtuales del grupo. La línea de comandos podría ejecutar cualquier script de PowerShell o robocopy, por ejemplo, para copiar archivos de la aplicación en la carpeta "compartida". También podría ejecutar un MSI.

Normalmente, es conveniente que el servicio Lote espere a que la tarea de inicio se complete y, a continuación, considere que la máquina virtual está preparada para asignarle tareas, pero esto se puede configurar.

Si falla una tarea de inicio en un grupo de máquinas virtuales, el estado de la máquina virtual se actualiza para reflejar el error, y la máquina virtual no estará disponible para asignarle tareas. Una tarea de inicio puede fallar si hay un problema al copiar los archivos especificados para la tarea de inicio o si el proceso de la tarea de inicio devuelve un valor distinto de cero.

El hecho de declarar toda la información necesaria para configurar la máquina virtual e instalar las aplicaciones significa que aumentar el número de máquinas virtuales en un grupo es tan simple como especificar la nueva cantidad necesaria. El servicio Lote tiene toda la información que necesita para configurar la máquina virtual y prepararla para aceptar tareas.

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

	ICloudPool pool = pm.CreatePool(poolName, targetDedicated: 3, vmSize: "small", osFamily: "3");
	pool.StartTask = new StartTask();
	pool.StartTask.CommandLine = "mypoolsetup.exe";
	pool.StartTask.ResourceFiles = new List<IResourceFile>();
	pool.StartTask.ResourceFiles.Add(new ResourceFile("http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d", "mypoolsetup.exe"));
	pool.Commit();


#### <a name="jobmanagertask"></a>Tarea del Administrador de trabajos

Una tarea del administrador de trabajos se inicia antes que cualquier otra. La tarea del administrador de trabajos ofrece las siguientes ventajas:

- Se crea automáticamente por parte del servicio Lote cuando se crea el trabajo.

- Se programa antes que otras tareas en el trabajo.

- Su TVM asociada es la última que se quita de un grupo cuando se reduce el tamaño de dicho grupo.

- Se le concede la prioridad más alta cuando hay que reiniciarla. Si una TVM inactiva no está disponible, el servicio Lote puede finalizar una de las tareas en ejecución del grupo para dejarle espacio para su ejecución.

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




## <a name="workflow"></a>Flujo de trabajo del servicio Lote

Se necesita una cuenta de Lote para utilizar el servicio Lote, y se usan varios recursos del servicio para programar un cálculo. Utilice el siguiente flujo de trabajo básico al crear un escenario de cálculo distribuido con el servicio Lote:

1. Cargue los archivos que desea utilizar en el escenario de cálculo distribuido a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio de Lote pueda tener acceso a ellos. El servicio Lote los carga en una TVM cuando se ejecuta la tarea.

2. Cargue los archivos binarios dependientes en la cuenta de almacenamiento. Los archivos binarios incluyen el programa que ejecutará la tarea y los ensamblados dependientes. También es necesario tener acceso a estos archivos desde el almacenamiento y se cargan en la TVM.

3. Cree un grupo de máquinas virtuales de tareas. Puede asignar el tamaño de la máquina virtual de tareas que se utilizará cuando se crea el grupo. Cuando se ejecuta una tarea, se le asigna una TVM de este grupo.

4. Cree un elemento de trabajo. Cuando se crea un elemento de trabajo, se crea automáticamente un trabajo. Un elemento de trabajo le permite administrar un trabajo de tareas.

5. Agregue tareas al elemento de trabajo. Cada tarea usa el programa que ha cargado para procesar la información de un archivo cargado.

6. Supervise los resultados de la salida.

## <a name="files"></a>Archivos y directorios

Cada tarea tiene un directorio de trabajo en el que crea cero o más directorios y archivos para almacenar el programa que ejecuta una tarea, los datos procesados por una tarea y el resultado del procesamiento realizado por una tarea. Estos archivos y directorios a continuación se ponen a disposición de otras tareas para que los utilicen durante la ejecución de un trabajo. Todas las tareas, archivos y directorios de una TVM pertenecen a una cuenta de usuario único.

El servicio Lote expone una parte del sistema de archivos en una TVM como directorio raíz. El directorio raíz de la máquina virtual de tareas está disponible para una tarea mediante la variable de entorno WATASK_TVM_ROOT_DIR. Para obtener más información acerca del uso de variables de entorno, consulte Configuración del entorno para las tareas.

El directorio raíz contiene los siguientes subdirectorios:

- **Tareas**: esta ubicación es donde se almacenan todos los archivos que pertenecen a las tareas que se ejecutan en la máquina virtual de tareas. Para cada tarea, el servicio Lote crea un directorio de trabajo con la ruta de acceso única en forma de %WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio, y este directorio se conserva en función de la restricción RetentionTime especificada para la tarea.

- **Compartido**: esta ubicación es un directorio compartido para todas las tareas de la cuenta. En la máquina virtual de tareas, el directorio compartido se encuentra en %WATASK_TVM_ROOT_DIR%/shared. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio.

- **Inicio**: una tarea de inicio utiliza esta ubicación como directorio de trabajo. Todos los archivos descargados por el servicio Lote para iniciar la tarea de inicio también se almacenan en este directorio. En la máquina virtual de tareas, el directorio de inicio se encuentra en %WATASK_TVM_ROOT_DIR%/start. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio, y este directorio puede utilizarse por las tareas de inicio para configurar el sistema operativo.

Cuando se quita una TVM del grupo, se quitan todos los archivos que están almacenados en la TVM.

## <a name="lifetime"></a>Vida útil de las máquinas virtuales y del grupo

Una decisión de diseño fundamental es cuándo se crean los grupos y cuánto tiempo se deben mantener disponibles las máquinas virtuales.

En un extremo se pudo crear un grupo para cada trabajo cuando se envía el trabajo y la máquina virtual se elimina a medida que las tareas finalizan su ejecución. Esto maximizará la utilización, ya que la máquina virtual se asigna solo cuando es absolutamente necesario y se cierra tan pronto como queda inactiva. Esto significa que el trabajo debe esperar a que se asigne la máquina virtual, aunque es importante tener en cuenta que las tareas se programarán para la máquina virtual en cuanto estén disponibles de forma individual, cuando se hayan asignado y cuando la tarea de inicio se haya completado; es decir, el servicio Lotes no espera hasta que estén disponible todas las máquinas virtuales de un grupo, ya que esto generaría una utilización deficiente.

Si la prioridad es que los trabajos inicien su ejecución inmediatamente, hay que crear un grupo y una máquina virtual debe estar disponible del antes de enviar el trabajo. Las tareas se pueden iniciar inmediatamente, pero la máquina virtual podría estar inactiva en espera de las tareas de trabajo, según la carga.

Un patrón habitual cuando hay una cantidad variable de carga continua es tener un grupo al que se envían varios trabajos, y escalar o reducir verticalmente el número de máquinas virtuales en función de la carga. Esto se puede realizar de forma reactiva o proactiva (en caso de que se pueda predecir la carga).

## <a name="scaling"></a>Escalado de aplicaciones

La aplicación se puede escalar o reducir verticalmente de forma automática para ajustarse al cálculo que necesite. Puede ajustar de forma dinámica el número de TVM de un grupo en función de la carga de trabajo actual y la estadística de uso de los recursos. También puede optimizar el costo general de ejecución de la aplicación configurándola para que se escale automáticamente. Puede especificar los valores de escalado para un grupo cuando se crea y puede actualizar la configuración en cualquier momento.

Para disminuir el número de máquinas virtuales, hay que tener en cuenta las tareas que pueden estar ejecutándose en las máquinas virtuales. Se especifica una política de cancelación de la asignación que determina si las tareas en ejecución se deben detener para eliminar la máquina virtual inmediatamente o si las tareas pueden finalizar antes de eliminar la máquina virtual. El hecho de establecer en cero el número de destino de máquinas virtuales al final de un trabajo, pero permitir la ejecución de tareas hasta que finalicen, permite maximizar la utilización.

Puede especificar el escalado automático de una aplicación mediante un conjunto de fórmulas de escalado. Las fórmulas que pueden utilizarse para determinar el número de TVM que se encuentran en el grupo para el siguiente intervalo de escalado. Por ejemplo, necesita enviar un gran número de tareas para que se programen en un grupo. Puede asignar una fórmula de escalado al grupo que especifica su tamaño en función del número actual de tareas pendientes y la tasa de finalización de las tareas. El servicio Lote evalúa periódicamente la fórmula y cambia el tamaño del grupo en función de la carga de trabajo.

Una fórmula puede basarse en las métricas siguientes:

- **Métricas de tiempo**: basadas en las estadísticas recopiladas cada cinco minutos en el número especificado de horas.

- **Métricas de recursos**: basadas en el uso de CPU, el uso de ancho de banda, el uso de memoria y el número de TVM.

- **Métricas de tareas**: basadas en el estado de las tareas, como Activo, Pendiente y Completado.

Para obtener más información acerca de cómo escalar automáticamente una aplicación, vea Configuración del escalado automático de máquinas virtuales de tareas.

>Eliminación de una máquina virtual
>
>Esto no se suele hacer con frecuencia, pero es posible especificar una máquina virtual individual para quitarla de un grupo. Por ejemplo, si hay una máquina virtual que se sospecha que es menos confiable, se puede quitar.

## <a name="cert"></a>Certificados para aplicaciones

Normalmente, deberá utilizar certificados al cifrar la información secreta. Los certificados pueden instalarse en TVM. Los secretos cifrados se pasan a las tareas en los parámetros de línea de comandos o se insertan en uno de los recursos, y se pueden utilizar los certificados instalados para descifrarlos. Un ejemplo de información secreta es la clave de una cuenta de almacenamiento.

Utilice la operación Agregar certificado para agregar un certificado a una cuenta de Lote. Se puede asociar el certificado a un grupo nuevo o existente. Cuando un certificado está asociado a un grupo, el servicio Lote instala el certificado en cada TVM del grupo. El servicio Lote instala los certificados apropiados cuando se inicia la TVM, antes de que se inicie cualquier tarea, lo cual incluye tareas de inicio y tareas de administrador de trabajos.

## <a name="scheduling"></a>Prioridad de programación

Cuando se crea un elemento de trabajo, puede asignarle una prioridad. Cada trabajo del elemento de trabajo se crea con esta prioridad. El servicio Lote utiliza los valores de prioridad del trabajo para determinar el orden de programación de trabajos dentro de una cuenta. Los valores de prioridad pueden oscilar entre -1000 y 1000, siendo -1000 la prioridad más baja y 1000 la prioridad más alta. Puede actualizar la prioridad de un trabajo mediante la operación UpdateJob.

Dentro de la misma cuenta, los trabajos de mayor prioridad tienen primacía de programación sobre los trabajos con menor prioridad. Un trabajo con un valor de prioridad determinado en una cuenta no tiene primacía de programación sobre otro trabajo con un valor de prioridad inferior de una cuenta diferente.

La programación de los diferentes grupos es independiente. En grupos diferentes, no se garantiza que un trabajo con una prioridad superior se programe antes si su grupo asociado tiene escasez de TVM inactivas. En el mismo grupo, los trabajos con el mismo nivel de prioridad tienen la misma probabilidad de ser programados.

## <a name="environment"></a>Configuración del entorno para las tareas

Puede especificar la configuración del entorno que se utiliza en el contexto de una tarea. La configuración del entorno para una tarea de inicio y las tareas que se ejecutan en un trabajo se define mediante la adición de una sección de XML al cuerpo de la solicitud de las operaciones Agregar tarea o Actualizar tarea.

En el ejemplo siguiente se muestra la definición de una configuración de entorno:

Para cada tarea programada en un trabajo, se establece un conjunto específico de variables de entorno por parte del servicio Lote. En la tabla siguiente se enumeran las variables de entorno establecidas por el servicio Lote para todas las tareas.

<table>
  <tr>
   <th>Nombre de la variable de entorno
   </th>
   <th>
   Descripción
   </th>
  </tr>
 <tr>
  <td>
  WATASK_ ACCOUNT_NAME
  </td>
  <td>
  El nombre de la cuenta a la que pertenece la tarea.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_WORKITEM_NAME
  </td>
  <td >El nombre del elemento de trabajo al que pertenece la tarea.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_JOB_NAME
  </td>
  <td >El nombre del trabajo al que pertenece la tarea.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TASK_NAME
  </td>
  <td >El nombre de la tarea actual.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_POOL_NAME
  </td>
  <td >El nombre del grupo en el que se ejecuta la tarea.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_NAME
  </td>
  <td >El nombre de la TVM en la que se ejecuta la tarea.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_ROOT_DIR
  </td>
  <td >La ruta de acceso completa del directorio raíz en la TVM.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_PORTRANGE_LOW WATASK_PORTRANGE_HIGH
</td>
<td>
  El intervalo de puertos (incluidos ambos extremos) que puede utilizar la tarea para la comunicación cuando se habilita la comunicación dentro del grupo.
</td>
</table>

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

Una aplicación puede generar diagnósticos que se pueden utilizar para solucionar problemas. A menudo, las aplicaciones escribirán información en los archivos stdout y stderr o generarán salidas en archivos personalizados. En estos casos, se proporciona una API para obtener los archivos, mediante la especificación de la tarea o la máquina virtual.

También es posible iniciar sesión en las máquinas virtuales del grupo. Una API devuelve el archivo RDP para una máquina virtual, que se puede usar para iniciar sesión en la máquina virtual.

###Resolución de problemas y errores en las tareas

Tareas pueden generar errores o interrumpirse por diversas razones. Por ejemplo, la propia aplicación de tarea puede fallar, la máquina virtual en la que se ejecuta la tarea se reinicia o se elimina la máquina virtual a causa de un cambio de tamaño en el grupo con la directiva de eliminación de la asignación establecida para quitar la máquina virtual inmediatamente sin esperar a que finalice la tarea. En todos los casos, el servicio Lote puede volver a poner en cola la tarea automáticamente y realizar la ejecución en otra máquina virtual.

También es posible que un problema intermitente haga que una tarea deje de responder o tarde demasiado tiempo en ejecutarse. El tiempo de ejecución máximo se puede definir para una tarea. Si se supera este valor, el servicio Lote interrumpirá la aplicación de la tarea. Actualmente, no es posible volver a poner en cola de forma automática este caso, pero el caso lo puede detectar el cliente, el cual puede enviar una nueva tarea.

###Restauración de la máquina virtual "mala"

Las máquinas virtuales incluidas en un grupo tienen un nombre único, y la máquina virtual en la que se ejecuta una tarea incluidos en los metadatos de la tarea. En caso de que haya una máquina virtual que, por alguna razón, esté causando errores en las tareas, el cliente puede determinar la causa y eliminar la máquina virtual del grupo. Si se estaba ejecutando una tarea en la máquina virtual que se ha eliminado, se podrá automáticamente en la cola de nuevo y se ejecutará en otra máquina virtual.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[Información general de Lote de Azure]: batch-technical-overview.md

<!---HONumber=62-->
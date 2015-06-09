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

## <a name="resource"></a> Recursos del servicio de proceso por lotes

Cuando usa el servicio Lote, se beneficia de los siguientes recursos:

- [Cuenta](#account)

- [Máquina Virtual de tareas](#taskvm)

- [Grupo de servidores](#pool)

- [Elemento de trabajo](#workitem)

- [Trabajo](#job)

- [Task](#task)

	- [Tarea de inicio](#starttask)
	
	- [Trabajo ManagerTask](#jobmanagertask)

### <a name="account"></a>Cuenta

Una cuenta de Lote es una entidad identificada de forma exclusiva en el servicio Lote. Todo el procesamiento se realiza a través de una cuenta de Lote. Al realizar operaciones con el servicio Lote, necesita el nombre y la clave de la cuenta. Para crear una cuenta de proceso por lotes, consulte la sección de cuenta de proceso por lotes de [información general del proceso de Azure][].


### <a name="taskvm"></a>Máquina Virtual de tareas

Una máquina virtual de tareas (TVM) es una máquina virtual de Azure dedicada a una carga de trabajo específica de la aplicación. El tamaño de una TVM determina el número de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asigna a la TVM. Una TVM puede ser una máquina virtual pequeña, grande o extralarge, como se describe en [Máquina Virtual y tamaños de servicio de nube de Azure](http://msdn.microsoft.com/library/dn197896.aspx).

Los tipos de programas que se puede ejecutar una TVM incluyen archivos ejecutables (.exe), archivos de comandos (.cmd), archivos por lotes (.bat) y archivos de script. Una TVM también tiene los siguientes atributos:

- Carpetas de sistema de archivos que son específicos de la tarea y compartidos. Un variables de entorno y estructura de carpeta se crean en cada máquina virtual del grupo. Se crea la siguiente estructura de carpetas con una carpeta "compartida" para aplicaciones y datos que se comparten entre tareas, además de una carpeta para cada tarea.

![][1]

- Archivos stdout.txt y stderr.txt que se escriben en una carpeta específica de la tarea

- Variables de entorno para procesamiento

- Configuración de firewall que se configura para controlar el acceso

>Acceso de VM
>
>Si el acceso a una máquina virtual es obligatorio, por ejemplo la depuración, se puede obtener el archivo RDP que, a continuación, puede utilizarse para tener acceso a la máquina virtual a través de escritorio remoto.


### <a name="pool"></a>Grupo de servidores

Un grupo es una colección de TVM en el que se ejecuta la aplicación. Puede ser el usuario quien cree el grupo, pero también es posible que el servicio Lote lo cree automáticamente cuando se especifica el trabajo que se debe realizar. Puede crear y administrar un grupo que satisfaga las necesidades de su aplicación. Un grupo solo se puede usar con la cuenta de Lote en la que se creó. Una cuenta de Lote puede tener más de un grupo.

Azure grupos por lotes crearon a partir de la plataforma de proceso de Azure principal; Agrupaciones de lote proporcionan la asignación a gran escala, aplicación & instalación de los datos, movimiento de datos, la supervisión de estado y un escalado flexible de la máquina virtual.

A cada TVM que se agrega a un grupo se le asigna un nombre único y una dirección IP asociada. Cuando se quita una TVM de un grupo, esta pierde los cambios realizados en el sistema operativo, todos sus archivos locales, su nombre y su dirección IP. Cuando una TVM abandona un grupo, su vigencia finaliza.

Puede configurar un grupo para permitir la comunicación entre las TVM que contiene. Si se solicita la comunicación dentro del grupo para un grupo, el servicio Lote habilita puertos superiores a 1100 en cada TVM del grupo. Cada TVM del grupo está configurada para permitir o restringir las conexiones entrantes a solo este intervalo de puertos y solo desde otras TVM del grupo. Si la aplicación no requiere comunicación entre TVM, el servicio Lote puede asignar potencialmente un gran número de TVM en los diferentes centros de datos o clústeres al grupo para habilitar un mayor procesamiento paralelo.

Cuando se crea un grupo, puede especificar los siguientes atributos:

- El **tamaño de VM** en el grupo.
	- El tamaño adecuado de la máquina virtual se debe elegir, dependiendo de las características y los requisitos de la aplicación o aplicaciones que se van a usar en la máquina virtual. Normalmente se seleccionará el tamaño de VM suponiendo que una tarea se ejecutará a la vez en la máquina virtual; Por ejemplo, si la aplicación es de varios subprocesos y cuánto memoria requiere determinará el tamaño de memoria virtual más adecuado y rentable. Es posible tener varias tareas asignadas y varias instancias de aplicación se elegirá normalmente se ejecute en paralelo, en cuyo caso una máquina virtual mayor: consulte a continuación en "tareas máxima por máquina virtual". 
	- Todas la máquina virtual en un grupo debe tener el mismo tamaño. Si son diferentes aplicaciones para ejecutarse con diferentes requisitos de sistema o con distintos de carga deben crearse grupos distintos.
	- Todos los tamaños de máquina virtual de servicio de nube pueden configurarse para un grupo, excepto A0.

- La familia del sistema operativo y la versión que se ejecute en las máquinas virtuales.
	- Al igual que con los roles de trabajo, pueden configurarse la familia de SO y versión del sistema operativo.
	- La familia del SO también determina qué versiones de .NET se instalan con el sistema operativo.
	- Como con los roles de trabajo, la versión del SO se recomienda que "*" utilizar de forma que son de la máquina virtual automáticamente actualizado y allí no es ningún trabajo necesario para satisfacer las nuevas versiones. Es el caso de uso principal para seleccionar una versión específica del sistema operativo para asegurarse de que se mantiene la compatibilidad de aplicaciones, permitiendo que las pruebas para realizarse antes de permitir que la versión de actualización de compatibilidad con versiones anteriores. Una vez validado, puede actualizar la versión del SO para el grupo y la nueva imagen de sistema operativo instalado, cualquier tarea en ejecución se interrumpe y se vuelve a en cola.

- Número de máquinas virtuales que deben estar disponibles para el grupo de destino.

- La directiva de escalado para el grupo. Además de número de máquinas virtuales, también puede especificar una fórmula de escalado automático para cada grupo. Servicio de proceso por lotes ejecutará la fórmula para ajustar el número de máquinas virtuales que se basa en las estadísticas de grupo y un elemento de trabajo.

- Configuración de programación
	- La configuración predeterminada es para que una tarea para ejecutarse en un grupo de máquinas virtuales en cualquier momento, pero hay escenarios donde resulta ventajoso tener más de una tarea se pueda ejecutar al mismo tiempo en una máquina virtual. Un ejemplo es aumentar la utilización de la máquina virtual si una aplicación tiene que esperar la E/S; tener más de una aplicación se ejecute, aumentará la utilización de CPU. Otro ejemplo es reducir el número de máquinas virtuales en el grupo; Esto puede reducir la cantidad de copias de datos requeridas para referencia grandes conjuntos de datos. Si un A1 sería el tamaño correcto para la aplicación, a continuación, podría elegir un A4 y la configuración establecida para que se ejecute hasta 8 tareas a la vez, cada consumir un núcleo.
	- La configuración de "tareas máxima por máquina virtual" determina el número máximo de tareas que se pueden ejecutar en paralelo.
	- Una "Directiva de relleno" también puede especificarse que determina si lote debe rellenar primero la máquina virtual o si las tareas se reparten entre todos la máquina virtual.
 
- El estado de comunicación de las máquinas virtuales en el grupo.
 	- En una gran proporción de escenarios de tareas funcionan de forma independiente y no es necesario para comunicarse con otras tareas, pero existen algunas aplicaciones donde las tareas comunicarán (por ejemplo, las aplicaciones con MPI).
	- No hay configuración que controla si la máquina virtual podrán comunicarse, que se utiliza para configurar la ubicación de impactos y la infraestructura de red subyacente de la máquina virtual.

- La tarea de inicio de las TVM del grupo.

Cuando se crea un grupo, puede especificar la cuenta de almacenamiento que desea asociar al grupo. El servicio Lote asigna TVM desde los centros de datos con mejor conectividad de red y capacidad de ancho de banda a la cuenta de almacenamiento especificada. Esto permite a las cargas de trabajo tener acceso a datos de forma más eficaz.

### <a name="workitem"></a>Elemento de trabajo

Un elemento de trabajo especifica cómo se realiza el cálculo en las TVM de un grupo.

- Un elemento de trabajo puede tener uno o varios trabajos asociados a él. Puede especificar una programación opcional para un elemento de trabajo, en cuyo caso se crea un trabajo para cada aparición de la programación. Si no se especifica ninguna programación, para el trabajo a petición, se crea un trabajo inmediatamente.
- El elemento de trabajo especifica el grupo en el que se va a ejecutar el trabajo. El grupo puede ser una existente, ya creado grupo utilizado por muchos elementos de trabajo, pero también se puede crear un grupo para cada trabajo asociado con el elemento de trabajo o para todos los trabajos asociados con el elemento de trabajo.
- Se puede especificar una prioridad opcional. Cuando se envía un elemento de trabajo con una prioridad más alta que otros elementos de trabajo en curso, las tareas de elemento de trabajo de prioridad más altos se insertan en la cola por delante de las tareas de elemento de trabajo de prioridad inferiores. No se da tareas de menor prioridad que ya se está ejecutando.
- Pueden especificar las restricciones que se aplicará a las tareas o el trabajo asociado.
	- Una vez wallclock máximo puede establecerse para los trabajos. Si los trabajos se ejecuta durante más tiempo que el tiempo de wallclock máximo especificado, se terminará el trabajo y todas las tareas asociadas.
	- Lote de Azure puede detectar las tareas que se producirá un error y reintentar las tareas. El valor predeterminado el número máximo de reintentos de tarea se puede especificar como una restricción, incluida la especificación de que una tarea siempre se vuelve a intentar o nunca vuelve a intentar. Volver a intentar una tareas significa que la tarea se vuelva a en cola y se volverá a ejecutar.
- Las tareas que debe ejecutarse para el elemento de trabajo se pueden especificar el cliente de la misma manera que se ha creado el elemento de trabajo, pero también se puede especificar una tarea del Administrador de trabajos. Una tarea de la tarea de administrador utiliza la API del lote y contiene el código para crear las tareas necesarias para una tarea con la tarea que se ejecute en uno del máquina virtual del grupo. Las tareas de administrador se controla específicamente por lotes: se pone en cola en cuanto el trabajo se crea y se reinicia si se produce un error por algún motivo. Un administrador de trabajos es obligatorio para los elementos de trabajo con una programación asociada ya que es la única manera de definir las tareas antes de que se crea una instancia de trabajo.

### <a name="job"></a>Trabajo

Un trabajo es una instancia en ejecución de un elemento de trabajo y consta de una colección de tareas. El servicio Lote crea una instancia de un trabajo en función de la configuración del elemento de trabajo. El trabajo utiliza TVM desde el grupo que está asociado con el elemento de trabajo.

### <a name="task"></a>Tarea

Una tarea es una unidad de cálculo que está asociada con un trabajo y se ejecuta en una TVM. Las tareas se asignan a una máquina virtual en ejecución o se ponen en cola hasta que quede libre una máquina virtual. Una tarea usa los siguientes recursos:

- El programa que se especificó en el elemento de trabajo.

- Los archivos de recursos que contienen los datos que se procesan. Estos archivos se copian automáticamente en la TVM del almacenamiento de blobs. Para obtener más información, consulte Archivos y directorios.

- La configuración del entorno que necesita el programa. Para obtener más información, consulte Configuración del entorno para las tareas.

- Las restricciones en las que debe realizarse el cálculo. Por ejemplo, el tiempo máximo en el que la tarea se puede ejecutar, el número máximo de veces que una tarea debe intentarse si se produce un error y el tiempo máximo que se conservan los archivos del directorio de trabajo.

Además de las tareas que se pueden definir para realizar cálculos en una TVM, puede utilizar las siguientes tareas especiales proporcionadas por el servicio Lote:

- [Tarea de inicio](#starttask)

- [Tareas del Administrador de trabajos](#jobmanagertask)

#### <a name="starttask"></a>Tarea de inicio

Puede configurar el sistema operativo de máquinas virtuales en un grupo mediante la asociación de una tarea de inicio con el grupo. Instalar software e iniciar procesos en segundo plano son algunas de las acciones que puede realizar una tarea de inicio. La tarea de inicio se ejecuta cada vez que inicia una máquina virtual para mientras permanece en el grupo.

Como con cualquier tarea por lotes una lista de archivos en el almacenamiento de Azure puede especificar además de una línea de comandos que se ejecuta por lotes. Lote de Azure se copie primero los archivos del almacenamiento de Azure y, a continuación, ejecutar la línea de comandos. Para una tarea de inicio del grupo, la lista de archivos suele contener los archivos de las aplicaciones o paquetes, pero también podría incluir datos de referencia que se utilizarán en todas las tareas que se ejecutan en el máquina virtual del grupo. La línea de comandos podría realizar secuencias de comandos de PowerShell o robocopy, por ejemplo, para copiar archivos de la aplicación en la carpeta "compartida"; También podría ejecutar un archivo MSI.

Normalmente es conveniente por lote esperar a que la tarea de inicio completar y, a continuación, considere la posibilidad de la máquina virtual preparada para ser asignado a las tareas, pero esto es configurable.

Si se produce un error en una tarea de inicio para un grupo de máquinas virtuales, a continuación, el estado de la máquina virtual se actualiza para reflejar el error y no estará disponible para las tareas que se asignará la máquina virtual. Una tarea de inicio puede fallar si hay un problema al copiar los archivos especificados para la tarea de inicio o el proceso de la tarea de inicio devuelve distinto de cero.

El hecho de que toda la información necesaria para configurar la máquina virtual e instalar las aplicaciones se declara significa que aumenta el número de máquinas virtuales en un grupo es tan simple como especificar el número necesario de nuevo; Lote tiene toda la información necesaria para configurar la máquina virtual y prepararlas Aceptar las tareas.

Una tarea de inicio se define mediante la adición de una sección JSON para el cuerpo de solicitud para la operación de Agregar grupo. En el ejemplo siguiente se muestra una definición básica de una tarea de inicio:

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


#### <a name="jobmanagertask"></a>Tareas del Administrador de trabajos

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




## <a name="workflow"></a>Flujo de trabajo del servicio de proceso por lotes

Se necesita una cuenta de Lote para utilizar el servicio Lote, y se usan varios recursos del servicio para programar un cálculo. Utilice el siguiente flujo de trabajo básico al crear un escenario de cálculo distribuido con el servicio Lote:

1. cargue los archivos que desea utilizar en el escenario de cálculo distribuido a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio de Lote pueda tener acceso a ellos. El servicio Lote los carga en una TVM cuando se ejecuta la tarea.

2.en cargue los archivos binarios dependientes en la cuenta de almacenamiento. Los archivos binarios incluyen el programa que ejecutará la tarea y los ensamblados dependientes. También es necesario tener acceso a estos archivos desde el almacenamiento y se cargan en la TVM.

3 crear un grupo de TVMs. Puede asignar el tamaño de la máquina virtual de tareas que se utilizará cuando se crea el grupo. Cuando se ejecuta una tarea, se le asigna una TVM de este grupo.

4 crear un elemento de trabajo. Cuando se crea un elemento de trabajo, se crea automáticamente un trabajo. Un elemento de trabajo le permite administrar un trabajo de tareas.

5 agregar tareas al elemento de trabajo. Cada tarea usa el programa que ha cargado para procesar la información de un archivo cargado.

6 supervisar los resultados de la salida.

## <a name="files"></a>Archivos y directorios

Cada tarea tiene un directorio de trabajo en el que crea cero o más directorios y archivos para almacenar el programa que ejecuta una tarea, los datos procesados por una tarea y el resultado del procesamiento realizado por una tarea. Estos archivos y directorios a continuación se ponen a disposición de otras tareas para que los utilicen durante la ejecución de un trabajo. Todas las tareas, archivos y directorios de una TVM pertenecen a una cuenta de usuario único.

El servicio Lote expone una parte del sistema de archivos en una TVM como directorio raíz. El directorio raíz de la TVM está disponible para una tarea mediante la variable de entorno WATASK_TVM_ROOT_DIR. Para obtener más información acerca del uso de variables de entorno, consulte Configuración del entorno para las tareas.

El directorio raíz contiene los siguientes subdirectorios:

- **Tareas** – esta ubicación es donde todos los archivos se almacenan que pertenecen a las tareas que se ejecutan en el TVM. Para cada tarea, el servicio de proceso por lotes crea un directorio de trabajo con la ruta de acceso única en forma de % WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio, y este directorio se conserva en función de la restricción RetentionTime especificada para la tarea.

- **Shared** – esta ubicación es un directorio compartido para todas las tareas en la cuenta. Sobre el TVM el directorio compartido se encuentra en % WATASK_TVM_ROOT_DIR%/shared. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio.

- **Iniciar** – se utiliza esta ubicación mediante una tarea de inicio como directorio de trabajo. Todos los archivos descargados por el servicio Lote para iniciar la tarea de inicio también se almacenan en este directorio. Sobre el TVM, el directorio de inicio se encuentra en % WATASK_TVM_ROOT_DIR%/start. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio, y este directorio puede utilizarse por las tareas de inicio para configurar el sistema operativo.

Cuando se quita una TVM del grupo, se quitan todos los archivos que están almacenados en la TVM.

## <a name="lifetime"></a>Grupo de servidores y la duración de la máquina virtual

Una decisión de diseño fundamental es cuando se crean los grupos y cuánto tiempo se mantiene la máquina virtual a disposición.

En un extremo se pudo crear un grupo para cada trabajo cuando se envía el trabajo y se quita de la máquina virtual como tareas de fin la ejecución. Esto maximizará la utilización de la máquina virtual se asigna solo cuando sea absolutamente necesario y cierre tan pronto como estén inactivas. Esto significa que el trabajo debe esperar a la máquina virtual a asignarse, aunque es importante tener en cuenta que las tareas se programará para la máquina virtual en cuanto estén disponibles de forma individual, asignado y se ha completado la tarea de inicio; es decir, por lotes no espera hasta que estén disponible todos VM en un grupo de medida Esto conduciría a la utilización deficiente.

Si tiene trabajos iniciar ejecutar inmediatamente es la prioridad, se debe crear un grupo y máquina virtual disponible del antes de enviar el trabajo. Las tareas se pueden iniciar inmediatamente, pero la máquina virtual podría estar inactiva en espera de las tareas de trabajo, dependiendo de la carga.

Diseño de un común cuando hay una cantidad variable de carga constante es tener un grupo al que se envían varios trabajos, pero la escala hacia arriba o hacia abajo el número de la máquina virtual según la carga; Esto se puede realizar de forma proactiva o reactiva si puede predecir la carga.

## <a name="scaling"></a>Escalar aplicaciones

La aplicación se puede escalar o reducir verticalmente de forma automática para ajustarse al cálculo que necesite. Puede ajustar de forma dinámica el número de TVM de un grupo en función de la carga de trabajo actual y la estadística de uso de los recursos. También puede optimizar el costo general de ejecución de la aplicación configurándola para que se escale automáticamente. Puede especificar los valores de escalado para un grupo cuando se crea y puede actualizar la configuración en cualquier momento.

Para una disminución del número de la máquina virtual, puede haber tareas que se ejecutan en máquinas virtuales que hay que considerar. Se especifica una política de cancelación de la asignación que determina si las tareas en ejecución se detienen para quitar la máquina virtual inmediatamente o si las tareas pueden finalizar antes de quita la máquina virtual. Establecer el número de destino de la máquina virtual a cero al final de un trabajo, pero que permite la ejecución de tareas para finalizar, maximizar la utilización.

Puede especificar el escalado automático de una aplicación mediante un conjunto de fórmulas de escalado. Las fórmulas que pueden utilizarse para determinar el número de TVM que se encuentran en el grupo para el siguiente intervalo de escalado. Por ejemplo, necesita enviar un gran número de tareas para que se programen en un grupo. Puede asignar una fórmula de escalado al grupo que especifica su tamaño en función del número actual de tareas pendientes y la tasa de finalización de las tareas. El servicio Lote evalúa periódicamente la fórmula y cambia el tamaño del grupo en función de la carga de trabajo.

Una fórmula puede basarse en las métricas siguientes:

- **Las métricas de tiempo** – según las estadísticas recopiladas cada cinco minutos en el número especificado de horas.

- **Métricas de recursos** : en función del uso de CPU, uso de ancho de banda, el uso de memoria y número de TVMs.

- **Tareas métricas** : según el estado de las tareas, como por ejemplo activo, pendiente y completado.

Para obtener más información acerca de cómo escalar automáticamente una aplicación, vea Configuración del escalado automático de máquinas virtuales de tareas.

>Eliminar la máquina virtual
>
>A menudo no es necesario, pero es posible especificar VM individual para quitar de un grupo. Si hay una máquina virtual que se sospecha que es menos confiable se puede quitar, por ejemplo.

## <a name="cert"></a>Certificados para las aplicaciones

Normalmente, deberá utilizar certificados al cifrar la información secreta. Los certificados pueden instalarse en TVM. Los secretos cifrados se pasan a las tareas en los parámetros de línea de comandos o se insertan en uno de los recursos, y se pueden utilizar los certificados instalados para descifrarlos. Un ejemplo de información secreta es la clave de una cuenta de almacenamiento.

Utilice la operación Agregar certificado para agregar un certificado a una cuenta de Lote. Se puede asociar el certificado a un grupo nuevo o existente. Cuando un certificado está asociado a un grupo, el servicio Lote instala el certificado en cada TVM del grupo. El servicio Lote instala los certificados apropiados cuando se inicia la TVM, antes de que se inicie cualquier tarea, lo cual incluye tareas de inicio y tareas de administrador de trabajos.

## <a name="scheduling"></a>La prioridad de programación

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

###Control de errores de tarea
Errores de tareas se dividen en las siguientes categorías:

- Errores de programación:
	- Si se especifican los archivos de la tarea, podría producirse un error en la copia de uno o varios de los archivos. Podría deberse a que los archivos se han movido, la cuenta de almacenamiento ya no está disponible, etc..
	- "Error de programación" en este caso se establece para la tarea.
- Errores de aplicación:
	- También se producirá un error en el proceso de tarea especificado por la línea de comandos. El proceso se ha dado error cuando se devuelve un código de salida distinto de cero.
	- Error de aplicaciones es posible configurar por lotes para volver a intentar automáticamente la tarea hasta un número especificado de veces. 
- Errores de restricción:
	- Una restricción se puede especificar para la cantidad máxima de tiempo que se puede ejecutar un trabajo o tarea de. La puede ser útil para finalizar una tarea que se ha bloqueado.
	- Cuando se ha superado la cantidad máxima de tiempo, a continuación, la tarea se marca como completada, pero la salida no se realizará código marcado como `0xC000013A` y schedulingError campo se marcará como `{ category:“ServerError”, code=“TaskEnded”}`.

###Depuración de errores de aplicación

Una aplicación puede producir de diagnóstico que puede utilizarse para solucionar problemas. Aplicaciones a menudo escribirá información en stdout y stderr archivos o salida para los archivos personalizados. En estos casos se proporciona una API para obtener los archivos, mediante la especificación de la tarea o la máquina virtual.

También es posible iniciar sesión en el grupo máquina virtual. Una API devuelve el archivo RDP para una máquina virtual, que se puede usar para iniciar sesión en la máquina virtual.

###Los errores de tareas y problemas de cocina

Tareas pueden producir un error o se interrumpe por diversas razones. Puede producir un error en la propia aplicación de tarea, obtiene reinicia la máquina virtual en el que se ejecuta la tarea o la máquina virtual se quita por un cambio de tamaño de grupo con la directiva de eliminación de la asignación establecida para quitar la máquina virtual inmediatamente sin esperar a que finalice la tarea. En todos los casos, la tarea se puede automáticamente volver a poner en cola por lotes y ejecutar en otra máquina virtual.

También es posible que un problema intermitente hacer que una tarea deje de responder o tardan demasiado tiempo en ejecutarse. El tiempo de ejecución máximo puede ser para una tarea y si lote superado interrumpirá la aplicación de la tarea. Actualmente, automática volver a la cola no es posible que en este caso, pero el caso puede ser detectado por el cliente que puede enviar una nueva tarea.

###Restauración de la máquina virtual "Mala"

Cada máquina virtual en un grupo tiene un nombre único y la máquina virtual en el que se ejecuta una tarea incluidos en los metadatos de tarea. En caso de que hay una máquina virtual que por alguna razón está causando errores en las tareas, esto se puede determinar mediante el cliente y el sospechosa que VM se elimina del grupo. Si una tarea se ejecuta en la máquina virtual que se ha eliminado, a continuación, se automáticamente vuelve a en cola y ejecutar en otra máquina virtual.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[información general del proceso de Azure]: batch-technical-overview.md

<!---HONumber=GIT-SubDir-->
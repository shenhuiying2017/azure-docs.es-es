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
	ms.date="02/02/2015" 
	ms.author="yidingz, kabatta"/>


<!--The next line, with one pound sign at the beginning, is the page title--> 
# Aspectos básicos sobre la API de Lote de Azure

El servicio Lote de Azure proporciona un marco para la programación de trabajos diseñado para un cálculo escalable y distribuido. El servicio Lote mantiene un conjunto de máquinas virtuales que se encuentran en diferentes clústeres y centros de datos de Azure. El servicio Lote realiza el cálculo distribuido ejecutando uno o varios programas, ya sea a petición o programados para ejecutarse en un momento concreto en una colección específica de estas máquinas virtuales. El servicio Lote administra estas máquinas virtuales para ejecutar las tareas de cálculo según los requisitos de recursos, especificaciones y restricciones proporcionadas por el usuario.

Al utilizar el servicio Lote, puede eliminar la necesidad de escribir código para la puesta en cola, programación, asignación y administración de recursos de proceso. Esto le permite centrarse en la aplicación específica y no preocuparse por la complejidad de la administración de recursos y la programación de trabajos en la plataforma subyacente. Además, esto también permite al servicio Lote optimizar la ubicación de estos trabajos, así como el acceso a los datos que necesitan para el procesamiento.

Estos son algunos de los escenarios que se pueden habilitar mediante el servicio Lote:

- Procesamiento paralelo de cálculo intensivo

- Limpieza diaria de archivos

- Procesamiento por lotes

Puede obtener más información acerca del servicio Lote mediante la lectura de las secciones siguientes:

- [Recursos del servicio Lote](#resource)

- [Flujo de trabajo del servicio Lote](#workflow)

- [Archivos y directorios](#file)

- [Escalado de aplicaciones](#scaling)

- [Certificados para aplicaciones](#cert)

- [Prioridad de programación](#scheduling)

- [Configuración del entorno para las tareas](#environment)

## <a name="resource"></a> Recursos del servicio Lote

Cuando usa el servicio Lote, se beneficia de los siguientes recursos:

- [Cuenta](#account)

- [Máquina virtual de tareas](#taskvm)

- [Grupo](#pool)

- [Elemento de trabajo](#workitem)

- [Job](#job)

- [Tarea](#task)

### <a name="account"></a>Cuenta

Una cuenta de Lote es una entidad identificada de forma exclusiva en el servicio Lote. Todo el procesamiento se realiza a través de una cuenta de Lote. Al realizar operaciones con el servicio Lote, necesita el nombre y la clave de la cuenta. Actualmente, debe ponerse en contacto con el equipo de Lote de Azure para crear una nueva cuenta. Una vez creada la cuenta, se proporciona una clave.

### <a name="taskvm"></a>Máquina virtual de tareas

Una máquina virtual de tareas (TVM) es una máquina virtual de Azure dedicada a una carga de trabajo específica de la aplicación. El tamaño de una TVM determina el número de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asigna a la TVM. Una TVM puede ser una máquina virtual pequeña, grande o extragrande, como se describe en [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/dn197896.aspx).

Los tipos de programas que se puede ejecutar una TVM incluyen archivos ejecutables (.exe), archivos de comandos (.cmd), archivos por lotes (.bat) y archivos de script. Una TVM también tiene los siguientes atributos:

- Carpetas del sistema de archivos que son específicas de la tarea y se comparten

- Archivos stdout.txt y stderr.txt que se escriben en una carpeta específica de la tarea

- Variables de entorno para procesamiento

- Configuración de firewall que se configura para controlar el acceso

### <a name="pool"></a>Grupo

Un grupo es una colección de TVM en el que se ejecuta la aplicación. Puede ser el usuario quien cree el grupo, pero también es posible que el servicio Lote lo cree automáticamente cuando se especifica el trabajo que se debe realizar. Puede crear y administrar un grupo que satisfaga las necesidades de su aplicación. Solo puede utilizarse un grupo por parte de la cuenta de Lote en que se creó. Una cuenta de Lote puede tener más de un grupo.

A cada TVM que se agrega a un grupo se le asigna un nombre único y una dirección IP asociada. Cuando se quita una TVM de un grupo, esta pierde los cambios realizados en el sistema operativo, todos sus archivos locales, su nombre y su dirección IP. Cuando una TVM abandona un grupo, su vigencia finaliza.

Puede configurar un grupo para permitir la comunicación entre las TVM que contiene. Si se solicita la comunicación dentro del grupo para un grupo, el servicio Lote habilita puertos superiores a 1100 en cada TVM del grupo. Cada TVM del grupo está configurada para permitir o restringir las conexiones entrantes a solo este intervalo de puertos y solo desde otras TVM del grupo. Si la aplicación no requiere comunicación entre TVM, el servicio Lote puede asignar potencialmente un gran número de TVM en los diferentes centros de datos o clústeres al grupo para habilitar un mayor procesamiento paralelo.

Cuando se crea un grupo, puede especificar los siguientes atributos:

- El tamaño de las TVM del grupo.

- La familia del sistema operativo y la versión que se ejecuta en las TVM.

- El número de TVM que deben estar disponibles para el grupo.

- La directiva de escalado para el grupo.

- El estado de comunicación de las TVM en el grupo.

- La tarea de inicio de las TVM del grupo.

Cuando se crea un grupo, puede especificar la cuenta de almacenamiento que desea asociar al grupo. El servicio Lote asigna TVM desde los centros de datos con mejor conectividad de red y capacidad de ancho de banda a la cuenta de almacenamiento especificada. Esto permite a las cargas de trabajo tener acceso a datos de forma más eficaz.

### <a name="workitem"></a>Elemento de trabajo

Un elemento de trabajo especifica cómo se realiza el cálculo en las TVM de un grupo. Un elemento de trabajo incluye los siguientes elementos de configuración:

1.El programa que realiza el procesamiento en la TVM.

2.Los parámetros de línea de comandos para el programa.

3.La prioridad del cálculo.

4.La programación del cálculo, que incluye la definición de si el cálculo debe repetirse.

Un elemento de trabajo siempre se asigna a un grupo, y el grupo puede existir o bien se puede crear automáticamente cuando se crea el elemento de trabajo.

### <a name="job"></a>Job

Un trabajo es una instancia en ejecución de un elemento de trabajo y consta de una colección de tareas. El servicio Lote crea una instancia de un trabajo en función de la configuración del elemento de trabajo. El trabajo utiliza TVM desde el grupo que está asociado con el elemento de trabajo.

### <a name="task"></a>Tarea

Una tarea es una unidad de cálculo que está asociada con un trabajo y se ejecuta en una TVM. Una tarea usa los siguientes recursos:

- El programa que se especificó en el elemento de trabajo.

- Los archivos de recursos que contienen los datos que se procesan. Estos archivos se copian automáticamente en la TVM del almacenamiento de blobs. Para obtener más información, consulte Archivos y directorios.

- La configuración del entorno que necesita el programa. Para obtener más información, consulte Configuración del entorno para las tareas.

- Las restricciones en las que debe realizarse el cálculo. Por ejemplo, el tiempo máximo en el que la tarea se puede ejecutar, el número máximo de veces que una tarea debe intentarse si se produce un error y el tiempo máximo que se conservan los archivos del directorio de trabajo.

Además de las tareas que se pueden definir para realizar cálculos en una TVM, puede utilizar las siguientes tareas especiales proporcionadas por el servicio Lote:

- [Tarea de inicio](#starttask)

- [Tarea del administrador de trabajos](#jobmanagertask)

#### <a name="starttask"></a>Tarea de inicio

Puede configurar el sistema operativo de las TVM en un grupo mediante la asociación de una tarea de inicio con el grupo. Instalar software e iniciar procesos en segundo plano son algunas de las acciones que puede realizar una tarea de inicio. La tarea de inicio se ejecuta cada vez que se inicia una TVM mientras permanece en el grupo.

Una tarea de inicio se define mediante la adición de una sección de XML al cuerpo de solicitud para la operación de Agregar grupo. En el ejemplo siguiente se muestra una definición básica de una tarea de inicio:

	{
		"commandLine":"mypoolsetup.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"mypoolsetup.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"maxTaskRetryCount":0
	}


#### <a name="jobmanagertask"></a>Tarea del administrador de trabajos

Una tarea del administrador de trabajos se inicia antes que cualquier otra. La tarea del administrador de trabajos ofrece las siguientes ventajas:

- Se crea automáticamente por parte del servicio Lote cuando se crea el trabajo.

- Se programa antes que otras tareas en el trabajo.

- Su TVM asociada es la última que se quita de un grupo cuando se reduce el tamaño de dicho grupo.

- Se le concede la prioridad más alta cuando hay que reiniciarla. Si una TVM inactiva no está disponible, el servicio Lote puede finalizar una de las tareas en ejecución del grupo para dejarle espacio para su ejecución.

- Su finalización se puede vincular a la finalización de todas las tareas en el trabajo.

Una tarea del administrador de trabajos en un mismo trabajo no tiene prioridad sobre las tareas de otros trabajos. Entre trabajos, solo se tienen en cuenta las prioridades a nivel de trabajo. Una tarea del administrador de trabajos se define mediante la adición de una sección de XML al cuerpo de solicitud para la operación de Agregar elemento de trabajo. En el ejemplo siguiente se muestra una definición básica de una tarea del administrador de trabajos:

	{
		"name":"jmTask",
		"commandLine":"myapp1.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp1.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"taskConstraints":
		{
			"maxWallClockTime":"PT1H",
			"maxTaskRetryCount":0,
			"retentionTime":"PT1H"
		},
		"killJobOnCompletion":true,
		"runElevated":false,
		"runExclusive":true
	}




## <a name="workflow"></a>Flujo de trabajo del servicio Lote

Se necesita una cuenta de Lote para utilizar el servicio Lote, y se usan varios recursos del servicio para programar un cálculo. Utilice el siguiente flujo de trabajo básico al crear un escenario de cálculo distribuido con el servicio Lote:

1.Cargue los archivos que desea utilizar en el escenario de cálculo distribuido a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio Lote pueda tener acceso a ellos. El servicio Lote los carga en una TVM cuando se ejecuta la tarea.

2.Cargue los archivos binarios dependientes en la cuenta de almacenamiento. Los archivos binarios incluyen el programa que ejecutará la tarea y los ensamblados dependientes. También es necesario tener acceso a estos archivos desde el almacenamiento y se cargan en la TVM.

3.Cree un grupo de TVM. Puede asignar el tamaño de la máquina virtual de tareas que se utilizará cuando se crea el grupo. Cuando se ejecuta una tarea, se le asigna una TVM de este grupo.

4.Cree un elemento de trabajo. Cuando se crea un elemento de trabajo, se crea automáticamente un trabajo. Un elemento de trabajo le permite administrar un trabajo de tareas.

5.Agregue tareas al elemento de trabajo. Cada tarea usa el programa que ha cargado para procesar la información de un archivo cargado.

6.Supervise los resultados de la salida.

## <a name="files"></a>Archivos y directorios

Cada tarea tiene un directorio de trabajo en el que crea cero o más directorios y archivos para almacenar el programa que ejecuta una tarea, los datos procesados por una tarea y el resultado del procesamiento realizado por una tarea. Estos archivos y directorios a continuación se ponen a disposición de otras tareas para que los utilicen durante la ejecución de un trabajo. Todas las tareas, archivos y directorios de una TVM pertenecen a una cuenta de usuario único.

El servicio Lote expone una parte del sistema de archivos en una TVM como directorio raíz. El directorio raíz de la TVM está disponible para una tarea mediante la variable de entorno WATASK_TVM_ROOT_DIR. Para obtener más información acerca del uso de variables de entorno, consulte Configuración del entorno para las tareas.

El directorio raíz contiene los siguientes subdirectorios:

- **Tareas** : esta ubicación es donde se almacenan todos los archivos que pertenecen a las tareas que se ejecutan en la TVM. Para cada tarea, el servicio Lote crea un directorio de trabajo con la ruta de acceso única en forma de %WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio, y este directorio se conserva en función de la restricción RetentionTime especificada para la tarea.

- **Compartido**: esta ubicación es un directorio compartido para todas las tareas de la cuenta. En la TVM, el directorio compartido se encuentra en %WATASK_TVM_ROOT_DIR%/shared. Este directorio proporciona acceso de lectura y escritura a la tarea. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio.

- **Inicio**: una tarea de inicio utiliza esta ubicación como directorio de trabajo. Todos los archivos descargados por el servicio Lote para iniciar la tarea de inicio también se almacenan en este directorio. En la TVM, el directorio de inicio se encuentra en %WATASK_TVM_ROOT_DIR%/start. La tarea puede crear, leer, actualizar y eliminar archivos en este directorio, y este directorio puede utilizarse por las tareas de inicio para configurar el sistema operativo.

Cuando se quita una TVM del grupo, se quitan todos los archivos que están almacenados en la TVM.

## <a name="scaling"></a>Escalado de aplicaciones

La aplicación se puede escalar o reducir verticalmente de forma automática para ajustarse al cálculo que necesite. Puede ajustar de forma dinámica el número de TVM de un grupo en función de la carga de trabajo actual y la estadística de uso de los recursos. También puede optimizar el costo general de ejecución de la aplicación configurándola para que se escale automáticamente. Puede especificar los valores de escalado para un grupo cuando se crea y puede actualizar la configuración en cualquier momento.

Puede especificar el escalado automático de una aplicación mediante un conjunto de fórmulas de escalado. Las fórmulas que pueden utilizarse para determinar el número de TVM que se encuentran en el grupo para el siguiente intervalo de escalado. Por ejemplo, necesita enviar un gran número de tareas para que se programen en un grupo. Puede asignar una fórmula de escalado al grupo que especifica su tamaño en función del número actual de tareas pendientes y la tasa de finalización de las tareas. El servicio Lote evalúa periódicamente la fórmula y cambia el tamaño del grupo en función de la carga de trabajo.

Una fórmula puede basarse en las métricas siguientes:

- **Métricas de tiempo**: basadas en las estadísticas recopiladas cada cinco minutos en el número especificado de horas.

- **Métricas de recursos**: basadas en el uso de CPU, el uso de ancho de banda, el uso de memoria y el número de TVM.

- **Métricas de tareas**: basadas en el estado de las tareas, como Activo, Pendiente y Completado.

Para obtener más información acerca de cómo escalar automáticamente una aplicación, vea Configuración del escalado automático de máquinas virtuales de tareas.

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
  WATASK_PORTRANGE_LOW
  WATASK_PORTRANGE_HIGH
</td>
<td>
  El intervalo de puertos (incluidos ambos extremos) que puede utilizar la tarea para la comunicación cuando se habilita la comunicación dentro del grupo.
</td>
</table>


**Nota:** 

No se pueden sobrescribir estas variables definidas por el sistema.

Puede recuperar el valor de la configuración del entorno mediante una operación Obtener tarea.

<!--HONumber=46--> 

<!--HONumber=46--> 

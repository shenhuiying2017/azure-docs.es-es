<properties linkid="batch-technical-overview" urlDisplayName="" pageTitle="Información técnica de Lote de Azure" metaKeywords="" description="Obtenga información sobre los conceptos, los flujos de trabajo y los escenarios del servicio Azure Batch" metaCanonical="" services="batch" documentationCenter="" title="Azure Batch technical overview" authors="danlep" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="batch" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="danlep" />


#Información técnica de Lote de Azure
Lote de Azure le ayuda a ejecutar aplicaciones de informática de alto rendimiento (HPC) y paralelas a gran escala en la nube al brindar la programación de tareas y autoescala de recursos informáticos, como un servicio de plataforma. Mediante el uso el SDK de Lote y el servicio de Lote, se pueden configurar cargas de trabajo por lote para ejecutarlas a petición o en función de una programación en una recopilación administrada de máquinas virtuales y no tener que preocuparse de la complejidad que implica la programación de trabajos y la administración de recursos en la plataforma subyacente.
 
>[WACOM.NOTE]Lote se encuentra en su versión preliminar. Para usar Lote, necesita una cuenta de Azure y debe tener habilitada la versión preliminar de Lote en su suscripción. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure](http://www.windowsazure.com/es-es/develop/php/tutorials/create-a-windows-azure-account/). 


Este artículo le brinda información general de:
 
* [Casos de uso](#BKMK_Scenarios)
* [Escenarios de desarrollador](#BKMK_Approaches)
* [Conceptos de Lote](#BKMK_Entities)
* [Flujo de trabajo de los elementos de trabajo](#BKMK_Workflow_workitems)
* [Flujo de trabajo para publicar y ejecutar aplicaciones](#BKMK_Workflow_cloudappss)
* [Recursos adicionales](#BKMK_Resources)

<h2 id="BKMK_Scenarios">Casos de uso</h2>

Lote usa la elasticidad y la escala de la nube para ayudarle con el *procesamiento por lotes* o la *informática por lotes*, es decir, la ejecución de grandes volúmenes de tareas similares de forma programática. Un script o un programa de línea de comandos toma un conjunto de archivos de datos como entrada, procesa los datos en una serie de tareas y genera un conjunto de archivos de salida. Los archivos de salida pueden ser los resultados finales o un paso intermedio en un flujo de trabajo de mayor tamaño.       
 
La informática por lotes es un patrón común para las organizaciones que procesan, transforman y analizan grandes cantidades de datos, ya sea según un programa o a petición. Incluye un procesamiento de fin de ciclo, como el informe de riesgos diarios de un banco o una nómina que se debe realizar según una programación. También incluye aplicaciones empresariales, científicas y de ingeniería a gran escala que normalmente necesitan las herramientas y los recursos de una cuadrícula o clúster de cálculo. Las aplicaciones incluyen aplicaciones HPC tradicionales como simulaciones dinámicas fluidas, así como también cargas de trabajo especializadas en campos como el diseño automotriz, la exploración de petróleo y gas, la investigación de ciencias biológicas y la creación de contenido digital. 
 
Lote funciona bien con cargas de trabajo o aplicaciones intrínsicamente paralelas (a veces llamadas "lamentablemente paralelas"), las que se prestan para ejecutar como tareas paralelas en varios equipos, como las máquinas virtuales de cálculo que administra el servicio de Lote. Consulte la ilustración 1.  

![Parallel tasks][parallel]

**Ilustración 1: tareas paralelas que se ejecutan en varios equipos.**

Algunos ejemplos son:

* Modelado de riesgos financieros 
* Representación y procesamiento de imágenes
* Codificación y transcodificación multimedia
* Análisis de secuencia genética
* Pruebas de software

También puede usar Lote para realizar cálculos paralelos con un paso menos al final y otras cargas de trabajo paralelas no acíclicas más complicadas. 

>[WACOM.NOTE]La versión preliminar de Lote actualmente no es compatible con aplicaciones de interfaz de especificación de mensajes (MPI).

<h2 id="BKMK_Approaches">Escenarios de desarrollador</h2>

Las API de Lote basadas en REST son compatibles con dos escenarios de desarrollador para ayudarle a configurar y ejecutar sus cargas de trabajo en lote con el servicio Lote:
 
1. **Distribución de cálculos como elementos de trabajo**: use las API de *Lote* para crear y administrar un grupo flexible de máquinas virtuales de cálculo y especifique los elementos de trabajo que se ejecutan en ellas. Esto le brinda un control total sobre los recursos y requiere que el cliente administre la canalización de ejecuciones de tareas, por ejemplo, con un administrador de flujos de trabajo o un meta-programador, que se pueden implementar con las API de REST de Lote o, de manera opcional, con la característica de administrador de trabajos del elemento de trabajo. En lugar de tener que configurar un clúster de cálculo o de escribir código para agregar a la cola y programas los trabajos, puede automatizar la programación de trabajos de cálculo y escalar un grupo de máquinas virtuales de cálculo hacia arriba o hacia abajo para ejecutarlos. Como parte de la especificación de los elementos de trabajo, puede definir todas las dependencias y definir el movimiento de los archivos de entrada y de salida. 

2. **Publicación y ejecución de aplicaciones con el servicio de Lote**: las API de las *aplicaciones de Lote* brindan un nivel superior de abstracción y la canalización de ejecuciones de trabajos hospedada por el servicio de Lote. Con las aplicaciones de Lote puede crear una carga de trabajo por lotes como servicio en la nube desde una aplicación que se ejecuta hoy en estaciones de trabajo de cliente o en un clúster de cálculo. Las aplicaciones de Lote le ayudan a encapsular archivos binarios y archivos ejecutables y publicarlos para ejecutarse en máquinas virtuales agrupadas que el servicio de Lote crea y administra en segundo plano. El marco de las aplicaciones de Lote controla el movimiento de los archivos de entrada y salida, la ejecución de los trabajos, la administración de los trabajos y la persistencia de los datos. Las aplicaciones de Lote también le permiten modelar tareas sobre cómo se particionan los datos y para los diversos pasos en un trabajo. Se incluye una API basada en REST y el portal de aplicaciones de Lote, al que se puede tener acceso desde el Portal de administración de Azure y le ayuda a supervisar los trabajos que envió.


<h2 id="BKMK_Entities">Conceptos de Lote</h2>

Las siguientes secciones resumen los conceptos clave para trabajar con las API y el servicio de Lote. Para obtener más información, consulte [API basics for Azure Batch].(http://azure.microsoft.com/es-es/documentation/articles/batch-api-basics) 

* [Cuenta de Lote](#BKMK_Account)
* [Grupos y máquinas virtuales de tareas](#BKMK_TVM)
* [Elementos de trabajo, trabajos y tareas](#BKMK_Workitem)
* [Archivos y directorios](#BKMK_Files)

<h3 id="BKMK_Account">Cuenta de Lote</h3>
Necesita una **cuenta de Lote** única para trabajar con el servicio de Lote. Cuando realice operaciones de API con el servicio de Lote, autentique las solicitudes con la dirección URL de la cuenta y una clave de acceso asociada. También necesita una cuenta de Lote para tener acceso al portal de aplicaciones de Lote.
 
Puede crear una cuenta de Lote y administrar claves de acceso para la cuenta en el Portal de administración de Azure.

Para crear una cuenta de Lote:

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en **Nuevo**, **Cálculo**, luego en **Servicio de Lote** y, a continuación, en **Creación rápida**.
![Create a Batch account][account_portal]

3. Escriba la siguiente información:

	* En **Nombre de la cuenta**, escriba un nombre único para usarlo en la dirección URL de la cuenta de Lote.
	* En **Región**, seleccione una región en la que Lote está disponible.
	* Si tiene más de una suscripción, en **Suscripción**, seleccione una suscripción disponible en la que se facturará el uso de Lote.

 
<h3 id="BKMK_TVM">Grupos y máquinas virtuales de tareas</h3>

Una **máquina virtual de tareas** (TVM) es una máquina virtual de Azure que el servicio de Lote dedica para ejecutar una carga de trabajo (tarea) específica para su aplicación, como un archivo ejecutable (.exe) o, en el caso de las aplicaciones de Lote, uno o más programas desde la imagen de una aplicación. A diferencia de una máquina virtual de Azure típica, no aprovisiona o administra directamente una TVM; en lugar de eso, el servicio de Lote crea y administra las TVM como un **grupo** de recursos de proceso configurados de manera similar, tal como aparece en la ilustración 2. 

![Pool and TVMs][TVM_pool]

**Ilustración 2: un grupo de TVM.**

Si utiliza las API de Lote, puede crear directamente un grupo o configurar el servicio de Lote para crear automáticamente uno cuando especifique el trabajo que se debe realizar. Si utiliza las API de las aplicaciones de Lote, se crea automáticamente un grupo cuando ejecuta la aplicación de Lote habilitada para la nube.


Los atributos de un grupo incluyen:

* Un [tamaño](http://msdn.microsoft.com/library/azure/dn197896.aspx) para las TVM 
* El sistema operativo que se ejecuta en las TVM
* El número máximo de TVM
* Una directiva de escala para el grupo, una fórmula basada en el uso actual de cargas de trabajo y recursos que ajusta dinámicamente la cantidad de TVM que procesan tareas
* Si los puertos de firewall en las TVM están habilitados para permitir la comunicación entre grupos
* Un certificado que está instalado en las TVM, por ejemplo, para autenticar el acceso a una cuenta de almacenamiento
* Una tarea de inicio para las TMV, para operaciones únicas como la instalación de aplicaciones o la descarga de datos usados por las tareas

>[WACOM.NOTE]Actualmente una TVM solo puede ejecutar el sistema operativo Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.

Un grupo solo se puede usar con la cuenta de Lote en la que se creó. Una cuenta de Lote puede tener más de un grupo.

A cada TVM que se agrega a un grupo se le asigna un nombre único y una dirección IP asociada. Cuando se quita una TVM de un grupo, esta pierde los cambios realizados en el sistema operativo, todos sus archivos locales, su nombre y su dirección IP. Cuando una TVM abandona un grupo, su vigencia finaliza.


<h3 id="BKMK_Workitem">Elementos de trabajo, trabajos y tareas</h3>

Un **elemento de trabajo** es una plantilla que especifica cómo se ejecutará una aplicación en las TVM de un grupo. Un **trabajo** es una instancia programada de un elemento de trabajo y se puede producir una vez o puede repetirse. Un trabajo consta de una recopilación de **tareas**. La ilustración 3 muestra las relaciones básicas.    
 
![Work item, job, and tasks][job_task]

**Ilustración 3: un elemento de trabajo, trabajo y tareas.**

Dependiendo de las API que usa para desarrollar con Lote, deberá administrar más o menos detalles acerca de los elementos de trabajo, los trabajos y las tareas.

* Si desarrolla una aplicación con las API de Lote de nivel inferior, necesitará definir de manera programática todos los elementos de trabajos, los trabajos y las tareas que el servicio de Lote ejecuta y configurar los grupos de TVM que ejecutan las tareas.

* Si integra una aplicación cliente usando las herramientas y API de aplicaciones de Lote, puede usar componentes que dividan automáticamente un trabajo en tareas, procesen las tareas y fusionen los resultados de tareas individuales con los resultados del trabajo final. Cuando envíe la carga de trabajo al servicio de Lote, el marco de las aplicaciones de Lote administra los trabajos y ejecuta las tareas en los recursos de proceso subyacentes. 



<h3 id="BKMK_Files">Archivos y directorios</h3>

Un archivo es un fragmento de datos que se utiliza como entrada en una tarea de trabajo. Una tarea puede tener asociado uno, muchos o ningún archivo de entrada. El mismo archivo se puede usar también en varias tareas, por ejemplo, para las tareas de un trabajo de representación de películas, puede haber texturas y modelos comúnmente usados. Para las tareas de un trabajo de análisis de datos, los archivos pueden ser un conjunto de observaciones o medidas.

Cada tarea tiene un directorio de trabajo bajo el cual crea los directorios y archivos necesarios para almacenar el programa que una tarea ejecuta, los datos de entrada que procesa una tarea y la salida del procesamiento que una tarea ejecuta. Estos directorios y archivos están disponibles para que los usen otras tareas mientras se ejecuta un trabajo. Todas las tareas, archivos y directorios de una TVM pertenecen a una cuenta de usuario único.

Nuevamente, dependiendo de las API que use con Lote, deberá administrar más o menos detalles acerca de las ubicaciones y del movimiento de los archivos de entrada y salida para sus trabajos y tareas. Si está desarrollando con las API de Lote de nivel inferior, especifique estas dependencias y movimientos de archivos de manera explícita. Con las aplicaciones de Lote, el marco controla la mayoría de estos detalles por usted. 

<h2 id="BKMK_Workflow_workitems">Flujo de trabajo de elementos de trabajo</h2>
La ilustración 4 muestra un flujo de trabajo típico para distribuir cálculos a un grupo de TVM mediante la API de Lote.

![Work items workflow][work_item_workflow]

**Ilustración 4: flujo de trabajo para distribuir elementos de trabajo a las máquinas virtuales agrupadas.**

1.	Cargue los archivos de entrada (como imágenes o datos de origen) que se requieren para un trabajo a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio Lote pueda tener acceso a ellos. El servicio Lote los carga en una TVM cuando se ejecuta la tarea.
2.	Cargue los archivos binarios dependientes en la cuenta de almacenamiento. Los archivos binarios incluyen el programa que ejecutará la tarea y los ensamblados dependientes. También es necesario tener acceso a estos archivos desde el almacenamiento y se cargan en la TVM.
3.	Cree un conjunto de TVM especificando el tamaño de las TVM del grupo, el SO que ejecutan y otras propiedades. Cuando se ejecuta una tarea, se le asigna una TVM de este grupo.
4.	Cree un elemento de trabajo. Un trabajo se creará automáticamente cuando cree un elemento de trabajo. Un elemento de trabajo le permite administrar un trabajo de tareas.
5.	Agregue tareas al trabajo. Cada tarea usa el programa que cargó para procesar la información desde un archivo que cargó.
6.	Ejecute la aplicación y supervise los resultados de la salida.

<h2 id="BKMK_Workflow_cloudapps">Flujo de trabajo para publicar y ejecutar aplicaciones </h2>
La ilustración 5 muestra un flujo de trabajo básico para publicar una aplicación mediante la API de aplicaciones de Lote y luego envíe trabajos a la aplicación habilitada por Lote.

![Application publishing workflow][app_pub_workflow]

**Ilustración 5: flujo de trabajo para publicar y ejecutar una aplicación con las aplicaciones de Lote.**

1.	Prepare una **imagen de aplicación**, un archivo zip de los archivos ejecutables de aplicaciones existentes y cualquier archivo de compatibilidad que necesiten. Pueden ser los mismos archivos ejecutables que ejecuta en un clúster o una granja de servidores tradicional.
2.	Cree un archivo zip del **ensamblado de nubes** que invocarán y enviarán las cargas de trabajo al servicio de Lote. Este contiene dos componentes que están disponibles a través del SDK:

	a. **Divisor de trabajo**: divide un trabajo en tareas que se pueden procesar de manera independiente. Por ejemplo, en un escenario de animación, el divisor de trabajo podría tomar un trabajo de representación de película y dividirlo en tramas individuales.

	b. **Procesador de tareas**: invoca el archivo ejecutable de la aplicación para una tarea determinada. Por ejemplo, en un escenario de animación, el procesador de tareas podría invocar un programa de representación para representar la trama que especifica la tarea. 

3.	Use las API de las aplicaciones de Lote o las herramientas de desarrollador para cargar los archivos zip preparados en los dos pasos anteriores a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio Lote pueda tener acceso a ellos. Esto normalmente lo hace un administrador de servicios una vez por aplicación.
4.	Brinde una manera de ejecutar trabajos al servicio de aplicaciones habilitado en Azure. Puede ser un complemento en su UI de aplicación, un portal web o un servicio desatendido como parte de su sistema backend. En el SDK hay ejemplos disponibles para demostrar diversas opciones. 

	Para ejecutar un trabajo:

	a. Cargue los archivos de entrada (como imágenes o datos de origen) específicos para el trabajo del usuario. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio Lote pueda tener acceso a ellos.

	b. Envíe un trabajo con los parámetros requeridos y la lista de los archivos.
	
	c. Supervise el progreso del trabajo usando las API o el portal de aplicaciones de Lote.
	
	d. Descargue los archivos de salida.
	
<h2 id="BKMK_Resources">Recursos adicionales</h2>

* [Introducción a la biblioteca de proceso por lotes de Azure para .NET](http://azure.microsoft.com/es-es/documentation/articles/batch-dotnet-get-started/)
* [Herramientas y bibliotecas de desarollo por lotes de Azure](http://azure.microsoft.com/es-es/documentation/articles/batch-development-libraries-tools/)
* [Referencia de la API de REST por lotes de Azure](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Referencia de la API de REST de aplicaciones por lotes de Azure](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!--HONumber=35.2-->

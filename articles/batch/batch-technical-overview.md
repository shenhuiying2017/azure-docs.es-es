<properties
	pageTitle="Información técnica de Lote de Azure | Microsoft Azure"
	description="Obtener información acerca de los conceptos, los flujos de trabajo y los escenarios del servicio Batch de Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="danlep"/>


# Información técnica de Lote de Azure
Lote de Azure le ayudará a ejecutar aplicaciones a gran escala paralelas y de informática de alto rendimiento (HPC) de manera eficaz en la nube. Es un servicio de plataforma que proporciona la programación de trabajos y el ajuste automático de una colección administrada de máquinas virtuales (VM) para ejecutar los trabajos. Mediante el servicio de proceso por lotes, puede configurar las cargas de trabajo por lotes para ejecutarse en Azure a petición o según una programación y no preocuparse acerca de la complejidad de configurar y administrar un clúster HPC, máquinas virtuales o un programador de trabajos.

>[AZURE.NOTE]Para usar Lote, necesitará una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Crear una cuenta de Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Casos de uso

Lote usa la elasticidad y la escala de la nube para ayudarle con el *procesamiento por lotes* o la *informática por lotes*, es decir, la ejecución de grandes volúmenes de tareas similares de forma programática. Un script o un programa de línea de comandos toma un conjunto de archivos de datos como entrada, procesa los datos en una serie de tareas y genera un conjunto de archivos de salida. Los archivos de salida pueden ser los resultados finales o un paso intermedio en un flujo de trabajo de mayor tamaño.

La informática por lotes es un patrón común para las organizaciones que procesan, transforman y analizan grandes cantidades de datos, ya sea según un programa o a petición. Incluye un procesamiento de fin de ciclo, como el informe de riesgos diarios de un banco o una nómina que se debe realizar según una programación. También incluye aplicaciones empresariales, científicas y de ingeniería a gran escala que normalmente necesitan las herramientas y los recursos de una cuadrícula o clúster de cálculo. Entre las aplicaciones se incluyen aplicaciones HPC tradicionales, como las simulaciones de dinámica de fluidos, así como las cargas de trabajo especializadas en los campos que van desde la creación de contenidos digitales hasta servicios financieros y de investigaciones de ciencias biológicas.

Lote funciona bien con cargas de trabajo o aplicaciones intrínsicamente paralelas (a veces llamadas "lamentablemente paralelas"), las que se prestan para ejecutar como tareas paralelas en varios equipos, como las máquinas virtuales de cálculo que administra el servicio de Lote.

![Tareas paralelas][parallel]

**Ilustración 1: Tareas paralelas que se ejecutan en varios equipos**

Algunos ejemplos son:

* Modelado de riesgos financieros
* Representación y procesamiento de imágenes
* Codificación y transcodificación multimedia
* Análisis de secuencia genética
* Pruebas de software

También puede usar Lote para realizar cálculos paralelos con un paso menos al final y otras cargas de trabajo paralelas más complicadas.

>[AZURE.NOTE]En este momento solo se pueden ejecutar cargas de trabajo de Windows Server en Lote. Además, actualmente Lote no es compatible con aplicaciones de Interfaz de Paso de Mensajes (MPI).

## Escenarios de desarrollador

Lote admite escenarios de desarrollo diferentes para ayudarle a configurar y ejecutar las cargas de trabajo paralelas a gran escala con el servicio Lote. Estos escenarios usan las API para crear y administrar grupos de máquinas virtuales (nodos de ejecución) y programar los trabajos y tareas que se ejecutan en ellos. Consulte [Aspectos básicos de la API de Lote de Azure](batch-api-basics.md) para obtener más información acerca de los conceptos de Lote.

En las secciones siguientes se facilitan escenarios típicos para desarrolladores de Lote.

### Escalar horizontalmente una carga de trabajo paralela

Use la API de Lote para escalar horizontalmente trabajo paralelo intrínsecamente, como la representación de imágenes en un grupo de hasta miles de núcleos de cálculo. En lugar de configurar un clúster de proceso o de escribir código para agregar a la cola y programar los trabajos y mover los datos de entrada y salida necesarios, automatice la programación de grandes trabajos de proceso y escale un grupo de máquinas virtuales de proceso hacia arriba y abajo para ejecutarlas. Puede escribir aplicaciones cliente o front-end para ejecutar trabajos y tareas bajo demanda, según una programación, o como parte de un flujo de trabajo mayor administrado mediante herramientas como [Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/).

En la figura 2 se muestra un flujo de trabajo simplificado para enviar una aplicación a un grupo de lotes en los que se distribuye para su procesamiento.

![Flujo de trabajo de elementos de trabajo][work_item_workflow]

**Ilustración 2. Escalar horizontalmente una carga de trabajo paralela en Lote**

1.	Cargue los archivos de entrada (como imágenes o datos de origen) que se requieren para un trabajo a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio de Lote pueda tener acceso a ellos. El servicio Lote carga archivos en nodos de ejecución al ejecutar las tareas.
2.	Cargue los archivos binarios dependientes en la cuenta de almacenamiento. Los archivos binarios incluyen el programa que ejecutará la tarea y los ensamblados dependientes. También es necesario tener acceso a estos archivos desde el almacenamiento y se cargan en nodos de ejecución.
3.	Cree un grupo de nodos de ejecución, especificando propiedades como el tamaño de la máquina virtual y el sistema operativo que ejecutan. También puede definir cómo aumenta o disminuye el número de nodos del grupo en respuesta a la carga de trabajo. Cuando se ejecuta una tarea, se le asigna un nodo de este grupo.
4.	Defina la ejecución de un trabajo en el grupo.
5.	Agregue tareas al trabajo. Cada tarea usa el programa que cargó para procesar la información desde un archivo que cargó.
6.	Ejecute la aplicación y supervise los resultados de la salida.


### Habilite para la nube una aplicación de proceso intensivo

Puede usar la API de vista previa de aplicaciones de Lote para ajustar una aplicación existente para que se ejecute como un servicio en un grupo de nodos de ejecución que Lote administra en segundo plano. La aplicación puede ser una que se ejecute hoy en estaciones de trabajo cliente o en un clúster de proceso. Puede desarrollar el servicio para que los usuarios puedan descargar el trabajo de punta en la nube o ejecutar picos de trabajo completamente en la nube. El marco de trabajo de aplicaciones de Lote controla el movimiento de archivos de entrada y salida, la división de trabajos en tareas, el procesamiento de trabajos y tareas y la persistencia de los datos.

>[AZURE.IMPORTANT]Azure solo ofrece la API de aplicaciones de Lote en formato de vista previa. Solo deberá desarrollar con ella proyectos de prueba o de prueba de concepto. En futuras versiones del servicio se integrarán capacidades de aplicaciones de Lote clave en la API de Lote.

La ilustración 3 muestra un flujo de trabajo básico para publicar una aplicación mediante la API de aplicaciones de Lote y, a continuación, le permitirá a un usuario enviar trabajos a la aplicación.

![Flujo de trabajo de publicación de aplicaciones][app_pub_workflow]

**Ilustración 3. flujo de trabajo para publicar y ejecutar una aplicación con Aplicaciones de Lote**

1.	Prepare una **imagen de aplicación**, un archivo zip de los archivos ejecutables de aplicaciones existentes y cualquier archivo de compatibilidad que necesiten. Pueden ser los mismos archivos ejecutables que ejecuta en un clúster o una granja de servidores tradicional.
2.	Cree un archivo ZIP del **ensamblado de nubes** que invoca y envía las cargas de trabajo al servicio de Lote. Este contiene dos componentes:

	a. **Divisor de trabajo**: divide un trabajo en tareas que se pueden procesar de manera independiente. Por ejemplo, en un escenario de animación, el divisor de trabajo podría tomar un trabajo de representación de película y dividirlo en tramas individuales.

	b. **Procesador de tareas**: invoca el archivo ejecutable de la aplicación para una tarea determinada. Por ejemplo, en un escenario de animación, el procesador de tareas podría invocar un programa de representación para representar la trama que especifica la tarea.

3.	Use la API de las aplicaciones de Lote o las herramientas de desarrollador para cargar los archivos zip preparados en los dos pasos anteriores a una cuenta de almacenamiento de Azure. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio de Lote pueda tener acceso a ellos. Esto normalmente lo hace un administrador de servicios una vez por aplicación.
4.	Brinde una manera de ejecutar trabajos al servicio de aplicaciones habilitado en Azure. Puede ser un complemento en su UI de aplicación, un portal web o un servicio desatendido como parte de su sistema backend.

	Para ejecutar un trabajo:

	a. Cargue los archivos de entrada (como imágenes o datos de origen) específicos para el trabajo del usuario. Estos archivos deben estar en la cuenta de almacenamiento para que el servicio de Lote pueda tener acceso a ellos.

	b. Envíe un trabajo con los parámetros requeridos y la lista de los archivos.

	c. Supervise el progreso del trabajo usando las API o el portal de aplicaciones de Lote.



## <a id="BKMK_Account">Cuenta de Lote</a>
Tendrá que crear una o más **cuentas de Lote** únicas para usar y desarrollar con el servicio Lote. Todas las solicitudes que se realicen en el servicio de Lote deben autenticarse mediante el nombre de la cuenta y su clave de acceso.

Puede crear una cuenta de Lote y administrar claves de acceso para la cuenta en el Portal de vista previa de Azure o con los [cmdlets de PowerShell de Lote](batch-powershell-cmdlets-get-started.md).

Para crear una cuenta de Lote en el Portal:

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).

2. Haga clic en**Nuevo** > **Proceso** > **Marketplace** > **Todo** y, a continuación, escriba *Lote* en el cuadro de búsqueda.

	![Lote en Marketplace][marketplace_portal]

3. Haga clic en **Servicio Lote** en los resultados de búsqueda y, a continuación, haga clic en **Crear**.

4. En la hoja **Nueva cuenta de Lote**, escriba la siguiente información:

	a. En **Nombre de la cuenta**, escriba un nombre único para usarlo en la dirección URL de la cuenta de Lote.

	>[AZURE.NOTE]El nombre de la cuenta de proceso debe ser único para Azure, contener entre 3 y 24 caracteres y usar solo letras minúsculas y números.

	b. Haga clic en **Grupo de recursos** para seleccionar un grupo de recursos existente para la cuenta o cree una nueva.

	c. Si tiene más de una suscripción, haga clic en **Suscripción** para seleccionar una suscripción disponible donde se creará la cuenta.

	d. En **Ubicación**, seleccione una región de Azure en la que esté disponible Lote.

	![Crear una cuenta de lote][account_portal]

5. Haga clic en **Crear** para completar la creación de la cuenta.


Una vez creada la cuenta, puede encontrarla en el portal para administrar claves de acceso y otras opciones. Por ejemplo, haga clic en el icono de la clave para administrar las claves de acceso.

![Claves de la cuenta de Lote][account_keys]

## Recursos adicionales

* [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md)
* [Herramientas y bibliotecas de desarrollo de Lote de Azure](batch-development-libraries-tools.md)
* [Referencia de la API de REST de Lote de Azure](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Referencia de la API de REST de Aplicaciones de Lote de Azure](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[marketplace_portal]: ./media/batch-technical-overview/marketplace_batch.PNG
[account_portal]: ./media/batch-technical-overview/batch_acct_portal.png
[account_keys]: ./media/batch-technical-overview/account_keys.PNG
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=Oct15_HO3-->
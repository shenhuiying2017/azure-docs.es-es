<properties
	pageTitle="Datos básicos del servicio Lote de Azure | Microsoft Azure"
	description="Información acerca del servicio Lote de Azure para cargas de trabajo HPC y paralelas a gran escala"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/16/2016"
	ms.author="marsma"/>

# Datos básicos de Lote de Batch

Lote de Azure le permite ejecutar a gran escala aplicaciones paralelas y de informática de alto rendimiento (HPC) de manera eficaz en la nube. Se trata de un servicio de plataforma que programa el trabajo de proceso intensivo para que se ejecute en una colección administrada de máquinas virtuales, y que puede escalar automáticamente los recursos de proceso para satisfacer las necesidades de sus trabajos.

Con el servicio Lote, se definen los recursos de procesos de Azure para ejecutar las aplicaciones en paralelo y a escala. Puede ejecutar trabajos a petición o programados, y no tiene que crear, configurar ni administrar manualmente un clúster de HPC, máquinas virtuales individuales, redes virtuales ni una infraestructura compleja de programación de tareas y trabajos.

## Casos de uso de Lote

Lote es un servicio administrado de Azure que se usa para el *procesamiento por lotes* o la *computación por lotes*, es decir, la ejecución de grandes volúmenes de tareas similares para obtener el resultado deseado. La computación por lotes la utilizan normalmente organizaciones que procesan, transforman y analizan grandes volúmenes de datos con regularidad.

Lote funciona bien con cargas de trabajo y aplicaciones intrínsecamente paralelas (a veces llamadas "lamentablemente paralelas"). Las cargas de trabajo intrínsecamente paralelas se dividen fácilmente en varias tareas que trabajan simultáneamente en varios equipos.

![Tareas paralelas][1]<br/>

Algunos ejemplos de cargas de trabajo que normalmente se procesan mediante esta técnica son:

* Modelado de riesgos financieros
* Análisis de datos de clima e hidrología
* Representación, análisis y procesamiento de imágenes
* Codificación y transcodificación multimedia
* Análisis de secuencia genética
* Análisis de esfuerzos en ingeniería
* Pruebas de software

El servicio Lote también puede realizar cálculos paralelos con un paso de reducción al final, además de ejecutar cargas de trabajo HPC más complejas, como las aplicaciones de [interfaz de transferencia de mensajes (MPI)](batch-mpi.md).

Para obtener una comparación entre Lote y otras opciones de solución HPC en Azure, consulte [Soluciones de lote y HPC en la nube de Azure](batch-hpc-solutions.md).

## Desarrollo con Lote

Cuando compila soluciones que usan Lote de Azure para el procesamiento paralelo de la carga de trabajo, lo hace mediante programación con las [API de Lote](#batch-development-apis). Con las API de Lote, puede crear y administrar grupos de nodos de proceso (máquinas virtuales) y programar trabajos y tareas para que se ejecuten en esos nodos. Un servicio o una aplicación cliente que usted cree, usa las API de Lote para comunicarse con el servicio Lote.

Puede procesar de forma eficiente cargas de trabajo a gran escala para su organización, o bien proporcionar un servicio front-end a los clientes para que estos puedan ejecutar trabajos y tareas (a petición o dentro de una programación) en uno, cientos, o incluso, miles de nodos. También puede usar Lote como parte de un flujo de trabajo mayor, administrado mediante herramientas como [Data Factory de Azure](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Cuando esté listo para adentrarse en la API de Lote para una mayor comprensión de las características que proporciona, consulte [Información general de las características de Lote de Azure](batch-api-basics.md).

### Cuentas de Azure que necesita

Cuando se desarrollan soluciones de Lote, es necesario usar las siguientes cuentas en Microsoft Azure.

- **Cuenta de Azure y suscripción**: si aún no tiene una suscripción de Azure, puede activar su [crédito de Azure para suscriptores de MSDN][msdn_benefits] o bien registrarse para obtener una [cuenta gratuita de Azure][free_account]. Al crear una cuenta, se creará una suscripción predeterminada para usted.

- **Cuenta de Lote**: cuando las aplicaciones interactúan con el servicio Lote, el nombre de cuenta, la dirección URL de la cuenta y una clave de acceso se utilizarán como credenciales. Todos los recursos de Lote como grupos, nodos de proceso, trabajos y tareas están asociados a una cuenta de Lote. Puede [crear y administrar una cuenta de Lote](batch-account-create-portal.md) en el Portal de Azure.

- **Cuenta de Almacenamiento**: Lote incluye compatibilidad integrada para trabajar con archivos en [Almacenamiento de Azure][azure_storage]. Casi todos los escenarios de Lote usarán Almacenamiento de Azure: para el almacenamiento provisional de programas que ejecuten las tareas y de los datos que procesen; y para el almacenamiento de los datos de salida que generen. Para crear una cuenta de Almacenamiento, consulte [Acerca de las cuentas de Almacenamiento de Azure](./../storage/storage-create-storage-account.md).

### API de desarrollo de Lote

Las aplicaciones y los servicios pueden emitir llamadas de API de REST directas, usar una o varias de las bibliotecas de cliente siguientes, o bien una combinación de ambas, para administrar recursos de procesos y ejecutar cargas de trabajo paralelas a escala mediante el servicio Lote.

| API | Referencia de API | Descargar | Ejemplos de código |
| ----------------- | ------------- | -------- | ------------ |
| **REST de Lote** | [MSDN][batch_rest] | N/D | [MSDN][batch_rest] |
| **.NET de Lote** | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Batch Python** | [readthedocs.io][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** | [github.io][api_nodejs] | [npm][api_nodejs_npm] | - | | **Batch Java** (versión preliminar) | [github.io][api_java] | [Repositorio de instantáneas Maven][api_java_jar] |-|

### Administración de recursos de Lote

Además de las API de cliente, también puede utilizar lo siguiente para administrar los recursos en su cuenta de Lote.

- [Cmdlets de PowerShell de Lote][batch_ps]\: los cmdlets de Lote de Azure en el módulo [Azure PowerShell](../powershell-install-configure.md) permiten administrar los recursos de Lote con PowerShell.

- [CLI de Azure](../xplat-cli-install.md): la interfaz de la línea de comandos de Azure (CLI de Azure) es un conjunto de herramientas multiplataforma que proporciona comandos de shell para interactuar con muchos de los servicios de Azure, incluido Lote.

- Biblioteca de cliente [Batch Management .NET](batch-management-dotnet.md): también disponible a través de [NuGet][api_net_mgmt_nuget]; puede usar la biblioteca de cliente Batch Management .NET para administrar mediante programación las cuentas, las cuotas y los paquetes de aplicación de Lote. La referencia sobre la biblioteca de administración se encuentra en [MSDN][api_net_mgmt].

## Escenario: Escalar horizontalmente una carga de trabajo paralela

Una solución común que usa las API de Lote para interactuar con el servicio Lote implica escalar horizontalmente trabajo intrínsecamente paralelo, como la representación de imágenes para escenas 3D, en un grupo de nodos de proceso. Este grupo de nodos de proceso puede ser la "granja de representación" que le proporciona decenas, cientos o incluso miles de núcleos de su trabajo de representación, por ejemplo.

El siguiente diagrama muestra un flujo de trabajo común de Lote, con una aplicación cliente o un servicio hospedado usando Lote para ejecutar una carga de trabajo paralela.

![Flujo de trabajo de soluciones de Lote][2]

En este escenario común, la aplicación o servicio procesa una carga de trabajo de computación en Lote de Azure realizando los pasos siguientes:

1. Cargue los **archivos de entrada** y la **aplicación** que procesará esos archivos en su cuenta de Almacenamiento de Azure. Los archivos de entrada pueden ser cualquier dato que vaya a procesar la aplicación, como diseños de modelos financieros, o archivos de vídeo que se van a transcodificar. Los archivos de aplicación pueden ser cualquier aplicación que se use para procesar los datos, como una aplicación de representación de 3D o un transcodificador de medios.

2. Cree un **grupo** de nodos de proceso de Lote en su cuenta de Lote; son las máquinas virtuales que ejecutarán las tareas. Especifique propiedades como el [tamaño del nodo](./../cloud-services/cloud-services-sizes-specs.md), su sistema operativo y la ubicación en Almacenamiento de Azure de la aplicación que se instalará cuando los nodos se unan al grupo (la aplicación que ha cargado en el paso 1). También puede configurar el grupo para que se [escale automáticamente](batch-automatic-scaling.md) (ajuste de forma dinámica el número de nodos de proceso en el grupo), en respuesta a la carga de trabajo que generan las tareas.

3. Cree un **trabajo** de Lote para ejecutar la carga de trabajo en el grupo de nodos de proceso. Cuando crea un trabajo, lo asocia a un grupo de Lote.

4. Agregue **tareas** al trabajo. Al agregar tareas a un trabajo, el servicio Lote programa automáticamente las tareas para su ejecución en los nodos de proceso en el grupo. Cada tarea usa la aplicación que ha cargado para procesar los archivos de entrada.

    - 4a. Antes de que la tarea se ejecute, puede descargar los datos (los archivos de entrada) que va a procesar al nodo de proceso al que está asignada. Si la aplicación no se ha instalado aún en el nodo (consulte el paso 2), se puede descargar aquí. Cuando haya completado las descargas, las tareas se ejecutan en sus nodos asignados.

5. Mientras se ejecutan las tareas, puede solicitar a Lote que supervise el progreso del trabajo y sus tareas. El servicio o la aplicación cliente se comunica con el servicio Lote a través de HTTPS; además, como es posible que esté supervisando miles de tareas que se ejecutan en miles de nodos de proceso, asegúrese de realizar una [consulta eficaz del servicio Lote](batch-efficient-list-queries.md).

6. Cuando se completan las tareas, estas cargan los datos de sus resultados en Almacenamiento de Azure. También puede recuperar archivos directamente desde los nodos de proceso.

7. Cuando la supervisión detecta que se han completado las tareas en su trabajo, el servicio o la aplicación de cliente puede descargar los datos de salida para su posterior procesamiento o evaluación.

No olvide que esto es simplemente una forma de usar Lote, y que este escenario describe solo algunas de sus características. Por ejemplo, puede ejecutar [varias tareas en paralelo](batch-parallel-node-tasks.md) en cada nodo de proceso, y puede usar [tareas de preparación y finalización del trabajo](batch-job-prep-release.md) para preparar los nodos para los trabajos y limpiar después.

## Pasos siguientes

Ahora que ha visto información general de alto nivel del servicio Lote, es hora de profundizar en el servicio para aprender cómo puede utilizarlo para procesar las cargas de trabajo paralelas de proceso intensivo.

- Lea [Información general de las características de Lote de Azure](batch-api-basics.md) para ver información más detallada sobre las características de API proporcionadas por Lote para procesar las cargas de trabajo. Esta lectura es esencial para cualquier persona que se prepara para utilizar Lote.

- Consulte [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md) para más información sobre cómo usar C# y la biblioteca de .NET de Lote para ejecutar una carga de trabajo sencilla con un flujo de trabajo de Lote común. Esta debería ser una de sus primeras lecturas mientras está aprendiendo a utilizar el servicio Lote. También hay un [versión para Python](batch-python-tutorial.md) del tutorial.

- Descargue los [códigos de ejemplo en GitHub][github_samples] para ver cómo C# y Python pueden comunicarse con Lote para programar y procesar cargas de trabajo de ejemplo.

- Consulte la [ruta de aprendizaje de Lote][learning_path] para hacerse una idea de los recursos que tiene a su disposición a medida que va aprendiendo a trabajar con Lote.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://adxsnapshots.azurewebsites.net/?dir=com%5cmicrosoft%5cazure%5cazure-batch
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

<!---HONumber=AcomDC_0622_2016-->
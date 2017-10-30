---
title: Las API y las herramientas de Azure Batch para desarrolladores | Microsoft Docs
description: "Obtenga información acerca de las API y herramientas disponibles para desarrollar soluciones con el servicio Azure Batch."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: get-started-article
ms.date: 10/12/2017
ms.author: tamram
ms.openlocfilehash: a17856013c8db2e671b8f5201fbcc9223953ab6f
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/20/2017
---
# <a name="overview-of-batch-apis-and-tools"></a>Información general sobre las API y herramientas de Batch

El procesamiento de cargas de trabajo paralelas con Azure Batch se suele realizar mediante programación con una de las [API de Batch](#batch-development-apis). Un servicio o una aplicación cliente pueden usar las API de Batch para comunicarse con el servicio Batch. Con las API de Batch se pueden crear y administrar grupos de nodos de proceso, tanto máquinas virtuales como servicios en la nube. A continuación, se puede programar que se ejecuten trabajos y tareas en dichos nodos. 

Puede procesar de forma eficiente cargas de trabajo a gran escala para su organización, o bien proporcionar un front-end del servicio a los clientes para que puedan ejecutar trabajos y tareas (a petición o de forma programada) en uno, cientos, o incluso, miles de nodos. Azure Batch también se puede usar como parte de un flujo de trabajo mayor, administrado mediante herramientas como [Azure Data Factory](../data-factory/v1/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Cuando esté listo para adentrarse en la API de Lote para una mayor comprensión de las características que proporciona, consulte [Información general de las características de Lote de Azure](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Cuentas de Azure para desarrollo con Batch
Cuando se desarrollan soluciones de Batch, se usan las siguientes cuentas en Microsoft Azure.

* **Cuenta y suscripción de Azure**: si aún no tiene ninguna suscripción de Azure, puede activar su [ventaja como suscriptor de Visual Studio][msdn_benefits] o bien registrarse para obtener una [cuenta gratuita de Azure][free_account]. Al crear una cuenta, se crea automáticamente una suscripción predeterminada.
* **Cuenta de Batch**: los recursos de Azure Batch, entre los que se incluyen grupos, nodos de proceso, trabajos y tareas, están asociados a una cuenta de Azure Batch. Cuando la aplicación realiza una solicitud en el servicio Batch, autentica la solicitud mediante el nombre de la cuenta de Azure Batch, la dirección URL de la cuenta y una clave de acceso<o un token de Azure Active Directory. Puede [crear una cuenta de Batch](batch-account-create-portal.md) en Azure Portal o mediante programación.
* **Cuenta de Storage**: Batch incluye compatibilidad integrada para trabajar con archivos en [Azure Storage][azure_storage]. Casi todos los escenarios de Batch usan Azure Blob Storage tanto para el almacenamiento provisional de los programas que ejecutan las tareas como de los datos que procesan, así como para el almacenamiento de los datos de salida que generan. Para crear una cuenta de Storage, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).

## <a name="batch-service-apis"></a>API del servicio Batch

Las aplicaciones y servicios pueden emitir llamadas directas de la API de REST o usar una o varias de las siguientes bibliotecas de cliente para ejecutar y administrar las cargas de trabajo de Azure Batch.

| API | Referencia de API | Descargar | Tutorial | Ejemplos de código | Más información |
| --- | --- | --- | --- | --- | --- |
| **REST de Lote** |[docs.microsoft.com][batch_rest] |N/D |- |- | [Versiones compatibles](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET de Lote** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Tutorial](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [Notas de la versión](http://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Tutorial](batch-python-tutorial.md)|[GitHub][api_sample_python] | [Léame](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Tutorial](batch-nodejs-get-started.md) |- | [Léame](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[github.io][api_java] |[Maven][api_java_jar] |- |[Léame][api_sample_java] | [Léame](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>API de administración de Batch

Las API de Azure Resource Manager para Batch proporcionan acceso mediante programación a las cuentas de Batch. Con estas API, se pueden administrar mediante programación cuentas, cuotas y paquetes de aplicaciones de Batch.  

| API | Referencia de API | Descargar | Tutorial | Ejemplos de código |
| --- | --- | --- | --- | --- |
| **REST de Resource Manager de Batch** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET de Resource Manager de Batch** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Tutorial](batch-management-dotnet.md) |[GitHub][api_sample_net] |

## <a name="batch-command-line-tools"></a>Herramientas de línea de comandos de Batch

Estas herramientas de línea de comandos proporcionan la misma funcionalidad que el servicio Batch y las API de administración de Batch: 

* [Cmdlets de Batch en PowerShell][batch_ps]: los cmdlets de Azure Batch en el módulo [Azure PowerShell](/powershell/azure/overview) permiten administrar los recursos de Batch con PowerShell.
* [CLI de Azure 2.0](/cli/azure/overview): la interfaz de la línea de comandos de Azure (CLI de Azure) es un conjunto de herramientas multiplataforma que proporciona comandos de shell para interactuar con muchos de los servicios de Azure, incluidos el servicio Batch y el servicio Batch Management. Consulte [Administración de recursos de Batch con la CLI de Azure](batch-cli-get-started.md) para más información sobre el uso de la CLI de Azure con Batch.

## <a name="other-tools-for-application-development"></a>Otras herramientas de desarrollo de aplicaciones

Estas son algunas herramientas adicionales que pueden ser útiles para crear y depurar los servicios y aplicaciones de Batch:

* [Azure Portal][portal]: puede crear, supervisar y eliminar grupos, trabajos y tareas de Batch en Azure Portal. La información relativa al estado de estos y otros recursos se puede ver mientras se ejecutan trabajos, e incluso se pueden descargar archivos desde los nodos de proceso de los grupos. Por ejemplo, puede descargar el archivo `stderr.txt` de una tarea con errores mientras soluciona problemas. También puede descargar los archivos de Escritorio remoto (RDP) que puede usar para iniciar sesión en los nodos de proceso.
* [Azure BatchLabs][batch_labs] es una herramienta de cliente independiente, completa y gratuita que puede ayudarle a crear, depurar y supervisar las aplicaciones de Azure Batch. Descargue un [paquete de instalación](https://azure.github.io/BatchLabs/) para Mac, Linux o Windows.
* [Explorador de Microsoft Azure Storage][storage_explorer]: aunque no es estrictamente una herramienta de Azure Batch, el Explorador de Storage es otra herramienta muy útil para desarrollar y depurar soluciones de Batch.

## <a name="additional-resources"></a>Recursos adicionales

- Para más información acerca del registro de eventos de la aplicación de Batch, consulte [Registro de eventos para evaluación de diagnóstico y supervisión de soluciones de Batch](batch-diagnostics.md). Para ver una referencia de eventos generados por el servicio Batch, consulte [Análisis de Batch](batch-analytics.md).
- Para más información acerca de las variables de entorno para nodos de proceso, consulte [Variables de entorno de nodos de proceso de Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Información general de las características de Lote para desarrolladores](batch-api-basics.md), donde encontrará información esencial para cualquier persona que vaya a utilizar Lote. El artículo contiene información más detallada acerca de los recursos del servicio de Lote, como grupos, nodos, trabajos y tareas, así como las numerosas características de API que se pueden usar al compilar cualquier aplicación de Lote.
* [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md) . Este artículo debería ser una de sus primeras lecturas durante el periodo de aprendizaje de Lote. También hay disponible una [versión de Python](batch-python-tutorial.md) y de [Node.js](batch-nodejs-get-started.md) del tutorial.
* Descargue los [ejemplos de código en GitHub][github_samples] para ver la forma en que C# y Python pueden comunicarse con Batch para programar y procesar las cargas de trabajo de ejemplo.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/client
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /nodejs/api/overview/azure/batch
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/azurerm.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchLabs/
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

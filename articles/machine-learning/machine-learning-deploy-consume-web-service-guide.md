<properties
	pageTitle="Servicios web de Azure Machine Learning: Implementación y consumo | Microsoft Azure"
	description="Recursos para implementar y consumir servicios web."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="v-donglo"/>

# Servicios web de Azure Machine Learning: Implementación y consumo

Aprendizaje automático de Azure (Azure ML) le permite implementar flujos de trabajo de aprendizaje automático y modelos como servicios web. Estos servicios web pueden, posteriormente, utilizarse para llamar a los modelos de aprendizaje automático de las aplicaciones a través de Internet para que realicen predicciones en tiempo real o en modo por lotes. Mediante RESTFul, se puede llamar a los servicios web desde diversos lenguajes de programación y plataformas como .NET, Java, y aplicaciones como Excel.

En las secciones siguientes, se explican los pasos necesarios y se ofrecen vínculos a código y documentación para ayudarle a empezar.

## Implementación

### Uso de Estudio de aprendizaje automático de Azure

Estudio de aprendizaje automático y Servicios web Machine Learning de Microsoft Azure le permiten implementar y administrar un servicio web sin tener que escribir código.

Los vínculos siguientes proporcionan información general sobre el proceso de implementación de un nuevo servicio web:

* Para ver una introducción a la implementación de un nuevo servicio web basado en Azure Resource Manager, consulte [Implementación de servicios web nuevos](machine-learning-webservice-deploy-a-web-service.md).
* Para ver un tutorial de implementación de un servicio web, consulte [Implementar un servicio web Machine Learning de Azure](machine-learning-publish-a-machine-learning-web-service.md).

Para obtener información detallada acerca de cómo crear e implementar un servicio web, consulte [Paso 1 del tutorial: Creación de un área de trabajo de Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md).

Para obtener ejemplos específicos de la implementación de un servicio web, consulte:

* [Paso 5 del tutorial: Implementar el servicio web de Aprendizaje automático de Azure](machine-learning-walkthrough-5-publish-web-service.md)
* [Procedimiento para implementar un servicio web en varias regiones](machine-learning-how-to-deploy-to-multiple-regions.md)

### Uso de API de proveedor de recursos de servicios web (API de Azure Resource Manager)

El proveedor de recursos de Aprendizaje automático de Azure para servicios web permite la implementación y administración de servicios web mediante llamadas a las API de REST. Consulte los siguientes artículos y código de ejemplo para obtener más detalles.

* Referencia sobre [servicios web Machine Learning (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) en MSDN.


### Uso de cmdlets de PowerShell

El proveedor de recursos de Aprendizaje automático de Azure para servicios web permite la implementación y administración de servicios web mediante cmdlets de PowerShell.

Para usar los cmdlets, debe primero iniciar sesión en su cuenta de Azure desde el entorno de PowerShell mediante el cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx). Si no está familiarizado con la llamada a comandos de PowerShell basados en Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Para exportar el experimento predictivo, utilice este [código de ejemplo](https://github.com/ritwik20/AzureML-WebServices). Después de crear el archivo ejecutable desde el código, puede escribir:

	C:<folder>\GetWSD <experiment-url> <workspace-auth-token>

La ejecución de la aplicación permite crear una plantilla JSON del servicio web. Para usar la plantilla para implementar un servicio web, debe agregar la siguiente información:

* Nombre y clave de la cuenta de almacenamiento
	* Puede obtener el nombre y la clave de la cuenta de almacenamiento en el Portal de Azure clásico o en el nuevo.
* Identificador del plan de compromiso
	* Puede obtener este identificador de plan en el portal de [servicios web Machine Learning de Azure](https://services.azureml.net) si inicia sesión y hace clic en un nombre de plan.

Agréguelos a la plantilla JSON como elementos secundarios del nodo *Properties* en el mismo nivel que el nodo *MachineLearningWorkspace*.

Consulte el ejemplo siguiente:

	"StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
	},
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte los siguientes artículos y código de ejemplo para obtener más detalles.

* Referencia sobre [cmdlets de Aprendizaje automático de Azure](https://msdn.microsoft.com/library/azure/mt767952.aspx) en MSDN
* [Tutorial](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) de ejemplo en GitHub.

## Consumo de servicios web

### Desde la interfaz de usuario de servicios web Machine Learning de Azure (prueba)

Puede probar el servicio web desde el portal de servicios web Machine Learning de Azure. Esto incluye las pruebas de las interfaces RRS y BES.

* [Implementación de servicios web nuevos](machine-learning-webservice-deploy-a-web-service.md)
* [Implementar un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Paso 5 del tutorial: Implementar el servicio web de Aprendizaje automático de Azure](machine-learning-walkthrough-5-publish-web-service.md)

### Desde Excel

Puede descargar una plantilla de Excel que le permite consumir fácilmente el servicio web:

* [Consumo de un servicio web de Aprendizaje automático de Azure de Excel](machine-learning-consuming-from-excel.md)
* [Complemento de Excel para servicios web de Aprendizaje automático de Azure](machine-learning-excel-add-in-for-web-services.md)


### Uso de un cliente basado en REST

Los servicios web Machine Learning de Azure son API de RESTful. Puede consumir estas API desde varias plataformas como. NET, Python, R, Java, etc. La página de consumo del servicio web en el [portal de servicios web Machine Learning de Microsoft Azure](https://services.azureml.net) tiene el código de ejemplo disponible que le ayudará a empezar.

* [Cómo consumir un servicio web de Aprendizaje automático de Azure implementado en un experimento de Aprendizaje automático](machine-learning-consume-web-services.md)

<!---HONumber=AcomDC_0824_2016-->
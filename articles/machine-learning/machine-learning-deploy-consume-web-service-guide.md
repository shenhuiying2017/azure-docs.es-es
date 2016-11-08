---
title: 'Servicios web Azure Machine Learning: Implementación y consumo | Microsoft Docs'
description: Recursos para implementar y consumir servicios web.
services: machine-learning
documentationcenter: ''
author: vDonGlover
manager: raymondl
editor: ''

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: v-donglo

---
# Servicios web Azure Machine Learning: Implementación y consumo
Puede usar Azure Machine Learning para implementar flujos de trabajo y modelos de aprendizaje automático como servicios web. Estos servicios web pueden usarse después para llamar a los modelos de aprendizaje automático de las aplicaciones a través de Internet para que realicen predicciones en tiempo real o en modo por lotes. Como estos servicios web son RESTFul, se los puede llamar desde diversos lenguajes de programación y plataformas como .NET y Java, y aplicaciones como Excel.

Las siguientes secciones proporcionan vínculos a tutoriales, código y documentación para ayudarle a empezar.

## Implementación de un servicio web
### Con Azure Machine Learning Studio
Machine Learning Studio y el portal de Servicios web Microsoft Azure Machine Learning le permiten implementar y administrar un servicio web sin tener que escribir código.

Los vínculos siguientes proporcionan información general sobre cómo implementar un nuevo servicio web:

* Para ver una introducción a la implementación de un nuevo servicio web basado en Azure Resource Manager, consulte [Implementación de servicios web nuevos](machine-learning-webservice-deploy-a-web-service.md).
* Para ver un tutorial sobre cómo implementar un servicio web, consulte [Implementar un servicio web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).
* Para obtener información detallada acerca de cómo crear e implementar un servicio web, consulte [Paso 1 del tutorial: Creación de un área de trabajo de Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md).
* Para obtener ejemplos específicos de la implementación de un servicio web, consulte:
  
  * [Paso 5 del tutorial: Implementar el servicio web de Aprendizaje automático de Azure](machine-learning-walkthrough-5-publish-web-service.md)
  * [Procedimiento para implementar un servicio web en varias regiones](machine-learning-how-to-deploy-to-multiple-regions.md)

### Con API de proveedor de recursos de servicios web (API de Azure Resource Manager)
El proveedor de recursos de Azure Machine Learning para servicios web permite la implementación y administración de servicios web mediante llamadas a las API de REST. Para información más detallada, consulte la referencia [Servicio web Machine Learning (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) en MSDN.

### Con cmdlets de PowerShell
El proveedor de recursos de Azure Machine Learning para servicios web permite la implementación y administración de servicios web mediante los cmdlets de PowerShell.

Para usar los cmdlets, primero debe iniciar sesión en su cuenta de Azure desde el entorno de PowerShell mediante el cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx). Si no sabe cómo llamar a los comandos de PowerShell basados en Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Para exportar el experimento predictivo, use este [código de ejemplo](https://github.com/ritwik20/AzureML-WebServices). Después de crear el archivo .exe a partir del código, escriba:

    C:<folder>\GetWSD <experiment-url> <workspace-auth-token>

La ejecución de la aplicación permite crear una plantilla JSON del servicio web. Para usar la plantilla para implementar un servicio web, debe agregar la siguiente información:

* Nombre y clave de la cuenta de almacenamiento
  
    Puede obtener el nombre y la clave de la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com/) o en el [ortal de Azure clásico](http://manage.windowsazure.com/).
* Identificador del plan de compromiso
  
    Para obtener este identificador de plan en el portal de [Servicios web Azure Machine Learning](https://services.azureml.net), inicie sesión y haga clic en un nombre de plan.

Agréguelos a la plantilla JSON como elementos secundarios del nodo *Properties*, en el mismo nivel que el nodo *MachineLearningWorkspace*.

Este es un ejemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte los siguientes artículos y el código de ejemplo para obtener más detalles:

* Referencia sobre [cmdlets de Aprendizaje automático de Azure](https://msdn.microsoft.com/library/azure/mt767952.aspx) en MSDN
* [Tutorial](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) de ejemplo en GitHub

## Consumo de servicios web
### En la interfaz de Servicios web Azure Machine Learning (pruebas)
Puede probar el servicio web en el portal de Servicios web Azure Machine Learning. Esto incluye probar las interfaces del servicio de solicitud-respuesta (RRS) y del servicio de ejecución de lotes (BES).

* [Implementación de servicios web nuevos](machine-learning-webservice-deploy-a-web-service.md)
* [Implementar un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Paso 5 del tutorial: Implementar el servicio web de Aprendizaje automático de Azure](machine-learning-walkthrough-5-publish-web-service.md)

### Desde Excel
Puede descargar una plantilla de Excel para consumir el servicio web:

* [Consumo de un servicio web Azure Machine Learning desde Excel](machine-learning-consuming-from-excel.md)
* [Complemento de Excel para Servicios web Azure Machine Learning](machine-learning-excel-add-in-for-web-services.md)

### Desde un cliente basado en REST
Los servicios web Azure Machine Learning son API de RESTful. Puede consumir estas API desde varias plataformas como. NET, Python, R, Java, etc. La página [Consumo](https://services.azureml.net) del servicio web en el portal de **Servicios web Microsoft Azure Machine Learning** incluye código de ejemplo que le ayudará a empezar. Para más información, consulte [Cómo consumir un servicio web Azure Machine Learning implementado en un experimento de Machine Learning](machine-learning-consume-web-services.md).

<!---HONumber=AcomDC_0907_2016-->
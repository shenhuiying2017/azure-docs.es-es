---
title: "Servicios web Azure Machine Learning: Implementación y consumo | Microsoft Docs"
description: Recursos para implementar y consumir servicios web.
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 9841b61c21586976ee06ffc1fe4d01e3fc3428b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Servicios web Azure Machine Learning: Implementación y consumo
Puede usar Azure Machine Learning para implementar flujos de trabajo y modelos de aprendizaje automático como servicios web. Estos servicios web pueden usarse después para llamar a los modelos de aprendizaje automático de las aplicaciones a través de Internet para que realicen predicciones en tiempo real o en modo por lotes. Como estos servicios web son RESTFul, se los puede llamar desde diversos lenguajes de programación y plataformas como .NET y Java, y aplicaciones como Excel.

Las siguientes secciones proporcionan vínculos a tutoriales, código y documentación para ayudarle a empezar.

## <a name="deploy-a-web-service"></a>Implementación de un servicio web
### <a name="with-azure-machine-learning-studio"></a>Con Azure Machine Learning Studio
Machine Learning Studio y el portal de Servicios web Microsoft Azure Machine Learning le permiten implementar y administrar un servicio web sin tener que escribir código.

Los vínculos siguientes proporcionan información general sobre cómo implementar un nuevo servicio web:

* Para ver una introducción a la implementación de un nuevo servicio web basado en Azure Resource Manager, consulte [Implementación de servicios web nuevos](publish-a-machine-learning-web-service.md).
* Para ver un tutorial sobre cómo implementar un servicio web, consulte [Implementar un servicio web Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Para obtener información detallada acerca de cómo crear e implementar un servicio web, consulte [Paso 1 del tutorial: Creación de un área de trabajo de Machine Learning](walkthrough-1-create-ml-workspace.md).
* Para obtener ejemplos específicos de la implementación de un servicio web, consulte:

  * [Paso 5 del tutorial: Implementar el servicio web de Aprendizaje automático de Azure](walkthrough-5-publish-web-service.md)
  * [Procedimiento para implementar un servicio web en varias regiones](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Con API de proveedor de recursos de servicios web (API de Azure Resource Manager)
El proveedor de recursos de Azure Machine Learning para servicios web permite la implementación y administración de servicios web mediante llamadas a las API de REST. Para información más detallada, consulte la referencia [Servicio web Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>Con cmdlets de PowerShell
El proveedor de recursos de Azure Machine Learning para servicios web permite la implementación y administración de servicios web mediante los cmdlets de PowerShell.

Para usar los cmdlets, primero debe iniciar sesión en su cuenta de Azure desde el entorno de PowerShell mediante el cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) . Si no sabe cómo llamar a los comandos de PowerShell basados en Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md#log-in-to-your-azure-account).

Para exportar el experimento predictivo, use este [código de ejemplo](https://github.com/ritwik20/AzureML-WebServices). Después de crear el archivo .exe a partir del código, escriba:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

La ejecución de la aplicación permite crear una plantilla JSON del servicio web. Para usar la plantilla para implementar un servicio web, debe agregar la siguiente información:

* Nombre y clave de la cuenta de almacenamiento

    Puede obtener el nombre y la clave de la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com/) o en el [Portal de Azure clásico](http://manage.windowsazure.com/).
* Identificador del plan de compromiso

    Para obtener este identificador de plan en el portal de [Servicios web Azure Machine Learning](https://services.azureml.net) , inicie sesión y haga clic en un nombre de plan.

Agréguelos a la plantilla JSON como elementos secundarios del nodo *Properties* en el mismo nivel que el nodo *MachineLearningWorkspace*.

Este es un ejemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte los siguientes artículos y el código de ejemplo para obtener más detalles:

* [cmdlets de Aprendizaje automático de Azure](https://msdn.microsoft.com/library/azure/mt767952.aspx) en MSDN
* [Tutorial](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) de ejemplo en GitHub

## <a name="consume-the-web-services"></a>Consumo de servicios web
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>En la interfaz de Servicios web Azure Machine Learning (pruebas)
Puede probar el servicio web en el portal de Servicios web Azure Machine Learning. Esto incluye probar las interfaces del servicio de solicitud-respuesta (RRS) y del servicio de ejecución de lotes (BES).

* [Implementación de servicios web nuevos](publish-a-machine-learning-web-service.md)
* [Implementar un servicio web de Aprendizaje automático de Azure](publish-a-machine-learning-web-service.md)
* [Paso 5 del tutorial: Implementar el servicio web de Aprendizaje automático de Azure](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Desde Excel
Puede descargar una plantilla de Excel para consumir el servicio web:

* [Consumo de un servicio web Azure Machine Learning desde Excel](consuming-from-excel.md)
* [Complemento de Excel para Servicios web Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Desde un cliente basado en REST
Los servicios web Azure Machine Learning son API de RESTful. Puede consumir estas API desde varias plataformas como. NET, Python, R, Java, etc. La página **Consumo** del servicio web en el [portal de Servicios web Microsoft Azure Machine Learning](https://services.azureml.net) incluye código de ejemplo que le ayudará a empezar. Para más información, consulte [Consumo de servicios web de Azure Machine Learning](consume-web-services.md).

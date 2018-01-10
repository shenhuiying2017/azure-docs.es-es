---
title: "Implementación de un conjunto de escalado de máquinas virtuales mediante Visual Studio | Microsoft Docs"
description: "Implementación de un conjunto de escalado de máquinas virtuales mediante Visual Studio y una plantilla de Resource Manager"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73454abc11a832a1b7f4131bf13699bd0a94edea
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Procedimiento para crear un conjunto de escalado de máquinas virtuales con Visual Studio
En este artículo se muestra cómo implementar un conjunto de escalado de máquinas virtuales de Azure mediante la utilización de una implementación de grupo de recursos de Visual Studio.

Los [conjuntos de escalado de máquinas virtuales de Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) son un recurso de Azure Compute para implementar y administrar un conjunto de máquinas virtuales similares con equilibrio de carga y escalado automático. Puede aprovisionar e implementar conjuntos de escalado de máquinas virtuales mediante [plantillas de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Las plantillas de Azure Resource Manager se pueden implementar mediante la CLI de Azure, PowerShell, REST y también directamente desde Visual Studio. Visual Studio proporciona un conjunto de plantillas que se pueden implementar como parte de un proyecto de implementación de grupo de recursos de Azure.

Las implementaciones de grupo de recursos de Azure son una forma de agrupar y publicar un conjunto de recursos de Azure relacionados en una única operación de implementación. Puede obtener más información aquí: [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Requisitos previos
Para empezar a implementar conjuntos de escalado de máquinas virtuales en Visual Studio, necesita lo siguiente:

* Visual Studio 2013 o posterior.
* Azure SDK 2.7, 2.8 o 2.9

>[!NOTE]
>En estas instrucciones se asume que usa Visual Studio con el [SDK de Azure 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Creación de un proyecto
1. Cree un proyecto nuevo en Visual Studio; para ello, seleccione **Archivo | Nuevo | Proyecto**.
   
    ![Archivo nuevo][file_new]

2. En **Visual C# | Nube**, elija **Azure Resource Manager** para crear un proyecto para implementar una plantilla de Azure Resource Manager.
   
    ![Crear proyecto][create_project]

3. En la lista de plantillas, seleccione la plantilla del conjunto de escalado de máquinas virtuales de Linux o Windows.
   
   ![Seleccionar plantilla][select_Template]

4. Una vez creado el proyecto, verá los scripts de implementación de PowerShell, una plantilla de Azure Resource Manager y un archivo de parámetros para el conjunto de escalado de máquinas virtuales.
   
    ![Explorador de soluciones][solution_explorer]

## <a name="customize-your-project"></a>Personalización del proyecto
Ahora puede editar la plantilla para personalizarla en función de las necesidades de su aplicación, como agregar propiedades de extensión de máquinas virtuales o editar reglas de equilibrio de carga. De forma predeterminada, las plantillas de conjunto de escalado de máquinas virtuales se configuran para implementar la extensión AzureDiagnostics que facilita la operación de agregar reglas de escalado automático. También implementa un equilibrador de carga con una dirección IP pública, configurado con reglas NAT entrantes. 

El equilibrador de carga le permite conectarse a las instancias de VM con SSH (Linux) o RDP (Windows). El intervalo de puertos de front-end comienza en 50000. Para Linux, esto significa que si usa SSH en el puerto 50000, se le enrutará al puerto 22 de la primera VM en el conjunto de escalado. La conexión al puerto 50001 se enruta al puerto 22 de la segunda VM, y así sucesivamente.

 Una buena forma de editar las plantillas con Visual Studio es utilizar el esquema JSON para organizar los parámetros, las variables y los recursos. Para comprender el esquema, Visual Studio puede señalar errores en la plantilla antes de implementarla.

![Explorador de JSON][json_explorer]

## <a name="deploy-the-project"></a>Implementación del proyecto
1. Implemente la plantilla de Azure Resource Manager para crear el recurso de conjunto de escalado de máquinas virtuales. Haga clic con el botón derecho en el nodo de proyecto y elija **Implementar | Nueva implementación**.
   
    ![Implementar plantilla][5deploy_Template]
    
2. Seleccione la suscripción en el cuadro de diálogo "Implementar en grupo de recursos".
   
    ![Implementar plantilla][6deploy_Template]

3. Desde aquí, puede crear un grupo de recursos de Azure donde implementar la plantilla.
   
    ![Nuevo grupo de recursos][new_resource]

4. A continuación, haga clic en **Editar parámetros** para escribir los parámetros que se pasan a la plantilla. Proporcione el nombre de usuario y la contraseña del SO que se necesitan para crear la implementación. Si no tiene instaladas las herramientas de PowerShell para Visual Studio, se recomienda activar la casilla **Guardar contraseñas** para evitar que se oculte un símbolo de la línea de comandos de PowerShell, o bien usar la [compatibilidad con Key Vault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Editar parámetros][edit_parameters]

5. Ahora haga clic en **Implementar**. En la ventana **Salida** se muestra el progreso de la implementación. Tenga en cuenta que la acción ejecuta el script **Deploy-AzureResourceGroup.ps1**.
   
   ![Ventana de salida][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Exploración del conjunto de escalado de máquinas virtuales
Una vez finalizada la implementación, puede ver el nuevo conjunto de escalado de máquinas virtuales en Visual Studio **Cloud Explorer** (actualice la lista). Cloud Explorer le permite administrar recursos de Azure en Visual Studio mientras se desarrollan aplicaciones. También puede ver el conjunto de escalado de máquinas virtuales en [Azure Portal](https://portal.azure.com) y en el [Explorador de recursos de Azure](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 El portal es la mejor opción para administrar visualmente la infraestructura de Azure con un explorador web, mientras que Azure Resource Explorer ofrece una forma sencilla de explorar y depurar los recursos de Azure al proporcionar una ventana en la "vista de instancias" y mostrar también los comandos de PowerShell para los recursos que está mirando.

## <a name="next-steps"></a>pasos siguientes
Después de implementar satisfactoriamente los conjuntos de escalado de máquinas virtuales en Visual Studio, puede personalizar aún más el proyecto para satisfacer las necesidades de la aplicación. Por ejemplo, configurar el escalado automático agregando un recurso de **Insights**, agregar infraestructura a la plantilla (como máquinas virtuales independientes) o implementar aplicaciones con la extensión del script personalizado. Se puede encontrar una buena serie de plantillas de ejemplo en el repositorio de GitHub de [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates) (busque "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png

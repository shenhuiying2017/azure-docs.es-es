---
title: "Soluciones de administración de Azure | Microsoft Docs"
description: "Las soluciones de administración incluyen escenarios de administración empaquetados en Azure que los clientes pueden agregar a su área de trabajo de Log Analytics.  En este artículo se proporciona información sobre cómo los clientes y asociados pueden crear soluciones personalizadas."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d46b869815fef44a8137bb5121133a1c0140ca30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="working-with-management-solutions-in-azure-preview"></a>Trabajo con soluciones de administración de Azure (versión preliminar)
> [!NOTE]
> Esta es una documentación preliminar para las soluciones de administración de Azure que están actualmente en versión preliminar.    
> 
> 

Las soluciones de administración incluyen escenarios de administración empaquetados que los clientes pueden agregar a su entorno de Azure.  Además de las [soluciones que proporciona Microsoft](../log-analytics/log-analytics-add-solutions.md), los asociados y los clientes pueden crear soluciones de administración para utilizarlas en su propio entorno o ponerlas a disposición de los clientes a través de la comunidad.

## <a name="finding-and-installing-management-solutions"></a>Búsqueda e instalación de soluciones de administración
Hay varios métodos para buscar e instalar soluciones de administración, tal y como se describe en las secciones siguientes.

### <a name="azure-marketplace"></a>Azure Marketplace
Las soluciones de administración que proporciona Microsoft y los asociados de confianza pueden instalarse desde Azure Marketplace en Azure Portal.

1. Inicie sesión en el Portal de Azure.
2. Seleccione **Todos los servicios** en el panel izquierdo.
3. Desplácese hacia abajo hasta **Soluciones** o escriba *soluciones* en el cuadro de diálogo **Filtrar**.
4. Haga clic en el botón **+ Agregar**.
5. Busque las soluciones que le interesen mediante la característica de exploración, haciendo clic en el botón **Filtrar** o escribiendo en el cuadro de diálogo **Buscar todo**.
6. Haga clic en un elemento de Marketplace para ver la información detallada.
7. Haga clic en **Crear** para abrir el panel **Agregar solución**.
8. Se le pedirá una serie de datos obligatorios como el [área de trabajo de Log Analytics y la cuenta de Automation](#log-analytics-workspace-and-automation-account), además de los valores de los parámetros de la solución.
9. Haga clic en **Crear** para instalar la solución.

### <a name="oms-portal"></a>Portal de OMS
Las soluciones de administración de Microsoft pueden instalarse desde la Galería de soluciones del portal de OMS.

1. Inicie sesión en el portal de OMS.
2. Haga clic en el icono de **Galería de soluciones**.
3. En la página Galería de soluciones de OMS, obtenga información sobre cada solución disponible. Haga clic en el nombre de la solución que quiera agregar.
4. En la página de la solución que eligió, aparecerá información detallada sobre la solución. Haga clic en **Agregar**.
5. En la página de información general del portal aparece un icono nuevo para la solución que agregó y puede empezar a usarlo una vez que Log Analytics procese los datos.

### <a name="azure-quickstart-templates"></a>Plantillas de inicio rápido de Azure
Los miembros de la comunidad pueden enviar soluciones de administración a las plantillas de inicio rápido de Azure.  Puede descargar estas plantillas para instalarlas posteriormente o inspeccionarlas para saber cómo [crear sus propias soluciones](#creating-a-solution).

1. Siga el proceso descrito en [Área de trabajo de Log Analytics y cuenta de Automation](#log-analytics-workspace-and-automation-account) para vincular un área de trabajo y una cuenta.
2. Visite [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).  
3. Busque una solución que le interese.
4. Seleccione la solución en los resultados para ver su información.
5. Haga clic en botón **Deploy to Azure** (Implementar en Azure).
6. Se le pedirá que proporcione información como el grupo de recursos y la ubicación, además de los valores de los parámetros de la solución.
7. Haga clic en **Adquirir** para instalar la solución.

### <a name="deploy-azure-resource-manager-template"></a>Implementación de plantilla de Azure Resource Manager
Las soluciones que obtenga de la comunidad o que [cree usted mismo](#creating-a-solution) se implementan como una plantilla de Resource Manager. Puede utilizar cualquiera de los métodos estándares para [implementar plantillas](../azure-resource-manager/resource-group-template-deploy-portal.md).  Tenga en cuenta que antes de instalar la solución, debe crear y vincular el [área de trabajo de Log Analytics y la cuenta de Automation](#log-analytics-workspace-and-automation-account).

## <a name="log-analytics-workspace-and-automation-account"></a>Área de trabajo de Log Analytics y cuenta de Automation
La mayoría de las soluciones de administración requieren que un [área de trabajo de Log Analytics](../log-analytics/log-analytics-manage-access.md) contenga vistas y que una [cuenta de Automation](../automation/automation-security-overview.md#automation-account-overview) contenga runbooks y recursos relacionados. El área de trabajo y la cuenta deben cumplir los siguientes requisitos.

* Una solución solo puede utilizar un área de trabajo de Log Analytics y una cuenta de Automation.  
* El área de trabajo de Log Analytics y la cuenta de Automation que use una solución deben estar vinculados entre sí. Un área de trabajo de Log Analytics solo puede estar vinculada a una cuenta de Automation y viceversa.
* Para poder vincularse, el área de trabajo de Log Analytics y la cuenta de Automation deben encontrarse en el mismo grupo de recursos y la misma región.  La excepción es un área de trabajo que se encuentre en la región Este de EE. UU. y una cuenta de Automation del Este de EE. UU. 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Creación de un vínculo entre un área de trabajo de Log Analytics y una cuenta de Automation
La forma de especificar el área de trabajo de Log Analytics y una cuenta de Automation dependerá del método de instalación de la solución.

* La instalación de una solución de Microsoft a través del portal de OMS se hará en el área de trabajo actual y no se precisará ninguna cuenta de Automation.
* Al instalar una solución a través de Azure Marketplace, se le pedirá una cuenta de Automation y un área de trabajo; el vínculo entre ellos se crea automáticamente.  
* Para las soluciones externas a Azure Marketplace, debe vincular el área de trabajo de Log Analytics y la cuenta de Automation antes de instalar la solución.  Para ello, seleccione cualquier solución en Azure Marketplace y seleccione el área de trabajo de Log Analytics y la cuenta de Automation.  No tendrá que instalar la solución, ya que el vínculo se creará en cuanto se seleccionen el área de trabajo de Log Analytics y la cuenta de Automation.  Cuando se cree el vínculo, podrá usar esa área de trabajo de Log Analytics y la cuenta de Automation para cualquier solución. 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Comprobación de un vínculo entre un área de trabajo de Log Analytics y una cuenta de Automation
Puede comprobar el vínculo entre un área de trabajo de Log Analytics y una cuenta de Automation mediante el procedimiento siguiente.

1. Seleccione la cuenta de Automation en Azure Portal.
2. Si la configuración **Área de trabajo** de la sección **Recursos relacionados** del menú está habilitada, esta cuenta se adjunta a un área de trabajo de Log Analytics.  Puede hacer clic en **Área de trabajo** para ver sus detalles.

## <a name="listing-management-solutions"></a>Visualización de soluciones de administración
Utilice el siguiente procedimiento para ver las soluciones de administración en las áreas de trabajo vinculadas a la suscripción de Azure.

1. Inicie sesión en el Portal de Azure.
2. Seleccione **Todos los servicios** en el panel izquierdo.
3. Desplácese hacia abajo hasta **Soluciones** o escriba *soluciones* en el cuadro de diálogo **Filtrar**.
4. Se mostrarán las soluciones instaladas en todas las áreas de trabajo.

Tenga en cuenta que solo puede ver las soluciones de Microsoft instaladas en el área de trabajo actual mediante el portal de OMS.

## <a name="removing-a-management-solution"></a>Eliminación de una solución de administración
Cuando se quita una solución de administración, también se eliminan todos los recursos de la solución.  

1. Busque la solución en Azure Portal mediante el procedimiento de la sección [Visualización de soluciones de administración](#listing-solutions).
2. Seleccione la solución que quiera quitar.
3. Haga clic en el botón **Eliminar** .

## <a name="creating-a-management-solution"></a>Creación de una solución de administración
Hay disponible una guía completa sobre cómo crear soluciones de administración en el artículo sobre [creación de soluciones de Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>pasos siguientes
* Búsqueda de [plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates) para obtener ejemplos de diferentes plantillas de Resource Manager.
* Cree sus propias [soluciones de administración](operations-management-suite-solutions-creating.md).


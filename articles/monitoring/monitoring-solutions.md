---
title: Soluciones de administración en Azure | Microsoft Docs
description: Las soluciones de administración de Azure son una colección de reglas de lógica, visualización y adquisición de datos que proporcionan métricas que giran en torno a una determinada área de problemas.  Este artículo proporciona información sobre la instalación y el uso de soluciones de administración.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 0df54d1758693bce5fb5fd74c3be9c4cfd7dccb6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
---
# <a name="management-solutions-in-azure"></a>Soluciones de administración en Azure
Las soluciones de administración aprovechan los servicios de Azure para proporcionar información adicional sobre el funcionamiento de una aplicación o servicio determinados. Este artículo proporciona una breve descripción de las soluciones de administración en Azure y los detalles sobre su uso e instalación.

Las soluciones de administración suelen recopilar información en Log Analytics y proporcionar búsquedas de registros y vistas para analizar los datos recopilados. También pueden aprovechar otros servicios como Azure Automation para realizar acciones relacionadas con la aplicación o el servicio.

Puede agregar soluciones de administración a su suscripción de Azure para todas las aplicaciones y los servicios que use. Suelen estar disponibles sin costo, pero la recopilación de datos puede suponer cargos por uso. Además de las soluciones que proporciona Microsoft, los asociados y los clientes pueden [crear soluciones de administración](../monitoring/monitoring-solutions-creating.md) para utilizarlas en su propio entorno o ponerlas a disposición de los clientes en la comunidad.

## <a name="using-management-solutions"></a>Uso de soluciones de administración
La página **Introducción** de cada área de trabajo de Log Analytics muestra un icono para cada solución instalada en el área de trabajo. Haga clic en el icono de la solución abrir la vista que incluye un análisis más detallado de los datos recopilados.

![Información general](media/monitoring-solutions/overview.png)

Las soluciones de administración pueden contener varios tipos de recursos de Azure; puede ver todos los recursos que incluyen las soluciones igual que cualquier otro recurso. Por ejemplo, las búsquedas de registros incluidas en la solución se incluyen con **Búsquedas guardadas** en el área de trabajo. Puede usar las búsquedas al realizar análisis ad hoc en Log Analytics.

## <a name="list-installed-management-solutions"></a>Lista de soluciones de administración instaladas 
Utilice el procedimiento siguiente para enumerar las soluciones de administración instaladas en su suscripción.

1. Inicie sesión en el Portal de Azure.
2. Seleccione **Todos los servicios** en el panel izquierdo.
3. Desplácese hacia abajo hasta **Soluciones** o escriba *soluciones* en el cuadro de diálogo **Filtrar**.
4. Se mostrarán las soluciones instaladas en todas las áreas de trabajo. Al nombre de la solución le sigue el nombre del área de trabajo de Log Analytics donde está instalada.
1. Use los cuadros de lista desplegable de la parte superior de la pantalla para filtrar por suscripción o grupo de recursos.


![Lista de todas las soluciones](media/monitoring-solutions/list-solutions-all.png)

Haga clic en el nombre de una solución para abrir su página de resumen. Esta página muestra las vistas de Log Analytics incluidas en la solución y proporciona diferentes opciones para la solución y su área de trabajo. Para ver la página de resumen de una solución, siga uno de los procedimientos descritos anteriormente para mostrar las soluciones y haga clic en el nombre concreto.

![Propiedades de la solución](media/monitoring-solutions/solution-properties.png)


## <a name="find-management-solutions"></a>Búsqueda de soluciones de administración
Puede examinar e instalar las soluciones de administración disponibles de Microsoft y asociados desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace). Realice una [búsqueda de *soluciones de administración*](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions) para filtrarlas y haga clic en cualquier elemento para más detalles.

![Marketplace](media/monitoring-solutions/marketplace.png)

## <a name="install-a-management-solution"></a>Instalación de una solución de administración

### <a name="install-a-management-solution-from-the-azure-marketplace"></a>Instalación de una solución de administración desde Azure Marketplace
Puede utilizar cualquiera de los métodos siguientes para buscar e iniciar la instalación de una solución de administración.

- Haga clic en **Obtenerla ahora** en una solución de administración de [Azure Marketplace](#find-management-solutions).
- Desde la [lista de soluciones para la suscripción](#list-installed-management-solutions), haga clic en **Agregar**. A la derecha de **Soluciones de administración de**, haga clic en **Más**. Busque la solución de administración que desee y haga clic en **Crear**.
- En Azure Portal, seleccione **Crear un recurso** > **Supervisión y administración** > **Ver todos**. A la derecha de **Soluciones de administración de**, haga clic en **Más**. Busque la solución de administración que desee y haga clic en **Crear**.

Cuando se inicia el proceso de instalación, se le pedirá que proporcione la configuración necesaria, que varía para cada solución. Todas requieren que seleccione un área de trabajo de Log Analytics donde se instalará la solución y se recopilarán los datos. Puede que también necesite [especificar una cuenta de Automation](#log-analytics-workspace-and-automation-account), si la solución lo requiere.

### <a name="install-a-solution-from-the-community"></a>Instalación de una solución desde la comunidad
Los miembros de la comunidad pueden enviar soluciones de administración a las plantillas de inicio rápido de Azure. Puede instalar estas soluciones directamente o descargar plantillas para hacerlo más adelante.

1. Siga el proceso descrito en [Área de trabajo de Log Analytics y cuenta de Automation](#log-analytics-workspace-and-automation-account) para vincular un área de trabajo y una cuenta.
2. Visite [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/). 
3. Busque una solución que le interese.
4. Seleccione la solución en los resultados para ver su información.
5. Haga clic en botón **Deploy to Azure** (Implementar en Azure).
6. Se le pedirá que proporcione información como el grupo de recursos y la ubicación, además de los valores de los parámetros de la solución.
7. Haga clic en **Adquirir** para instalar la solución.


## <a name="log-analytics-workspace-and-automation-account"></a>Área de trabajo de Log Analytics y cuenta de Automation
Todas las soluciones de administración requieren un [área de trabajo de Log Analytics](../log-analytics/log-analytics-manage-access.md) para almacenar los datos recopilados por la solución y para hospedar sus vistas y búsquedas de registros. Algunas soluciones también requieren una [cuenta de Automation](../automation/automation-security-overview.md#automation-account-overview) que contenga runbooks y recursos relacionados. El área de trabajo y la cuenta deben cumplir los siguientes requisitos.

* Cada instalación de la solución solo puede utilizar un área de trabajo de Log Analytics y una cuenta de Automation. Puede instalar la solución por separado en varias áreas de trabajo.
* Si una solución requiere una cuenta de Automation, el área de trabajo de Log Analytics y la cuenta de Automation solución deben estar vinculadas. Un área de trabajo de Log Analytics solo puede estar vinculada a una cuenta de Automation y viceversa.
* Para poder vincularse, el área de trabajo de Log Analytics y la cuenta de Automation deben encontrarse en el mismo grupo de recursos y la misma región. La excepción es un área de trabajo que se encuentre en la región Este de EE. UU. y una cuenta de Automation del Este de EE. UU. 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Creación de un vínculo entre un área de trabajo de Log Analytics y una cuenta de Automation
La forma de especificar el área de trabajo de Log Analytics y una cuenta de Automation dependerá del método de instalación de la solución.

* Al instalar una solución desde Azure Marketplace, se le pedirá una cuenta de Automation y un área de trabajo. Se creará un vínculo entre ellas, si no están ya vinculadas.
* Para las soluciones externas a Azure Marketplace, debe vincular el área de trabajo de Log Analytics y la cuenta de Automation antes de instalar la solución. Para ello, seleccione cualquier solución en Azure Marketplace y seleccione el área de trabajo de Log Analytics y la cuenta de Automation. No tendrá que instalar la solución en sí, ya que el vínculo se crea en cuanto se seleccionan el área de trabajo de Log Analytics y la cuenta de Automation. Cuando se cree el vínculo, podrá usar esa área de trabajo de Log Analytics y la cuenta de Automation para cualquier solución.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Comprobación de un vínculo entre un área de trabajo de Log Analytics y una cuenta de Automation
Puede comprobar el vínculo entre un área de trabajo de Log Analytics y una cuenta de Automation mediante el procedimiento siguiente.

1. Seleccione la cuenta de Automation en Azure Portal.
1. Desplácese hasta la sección **Recursos relacionados** del menú.
1. Si el **Área de trabajo** está habilitada, esta cuenta se vincula a un área de trabajo de Log Analytics. Puede hacer clic en **Área de trabajo** para ver sus detalles.

## <a name="remove-a-management-solution"></a>Eliminación de una solución de administración
Para eliminar una solución instalada, busque en la [lista de soluciones instaladas](#list-installed-management-solutions). Haga clic en el nombre de la solución para abrir su página de resumen y haga clic en **Eliminar**.




## <a name="next-steps"></a>Pasos siguientes
* Obtenga un [lista de soluciones de administración de Microsoft](monitoring-solutions-inventory.md).
* Aprenda a [crear consultas](../log-analytics/log-analytics-log-searches.md) para analizar los datos recopilados por las soluciones de administración.


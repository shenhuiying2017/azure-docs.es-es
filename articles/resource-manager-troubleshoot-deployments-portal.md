---
title: Visualización de operaciones de implementación con el Portal | Microsoft Docs
description: Describe cómo usar el Portal de Azure para detectar los errores de la implementación de Resource Manager.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 06/15/2016
ms.author: tomfitz

---
# Visualización de operaciones de implementación con el Portal de Azure
> [!div class="op_single_selector"]
> * [Portal](resource-manager-troubleshoot-deployments-portal.md)
> * [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
> * [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
> * [API DE REST](resource-manager-troubleshoot-deployments-rest.md)
> 
> 

Puede ver las operaciones de una implementación mediante el Portal de Azure. Es posible que le resulte más interesante ver las operaciones si ha recibido un error durante la implementación; por ello, este artículo se centrará en la visualización de las operaciones en las que se han producido errores. El portal proporciona una interfaz que permite buscar los errores y determinar posibles correcciones con facilidad.

[!INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Uso de operaciones de implementación para solucionar problemas
Para ver las operaciones de implementación, siga estos pasos:

1. En el grupo de recursos implicado en la implementación, examine el estado de la última implementación. Puede seleccionar este estado para obtener más detalles.
   
    ![Estado de la implementación](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)
2. Verá el historial de implementación reciente. Seleccione la implementación en la que se produjo un error.
   
    ![Estado de la implementación](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)
3. Seleccione **Error. Haga clic aquí para más información** y para ver una descripción del motivo de error de la implementación. En la imagen siguiente, el registro de DNS no es único.
   
    ![ver la implementación con errores](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)
   
    Este mensaje de error debería ser suficiente para que comience la solución de problemas. Sin embargo, si necesita más detalles acerca de las tareas que se han completado, puede ver las operaciones como se muestra en los pasos siguientes.
4. Puede ver todas las operaciones de implementación en la hoja **Implementación**. Seleccione la operación que desea ver con mayor detalle.
   
    ![ver operaciones](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)
   
    En este caso, puede ver que la cuenta de almacenamiento, la red virtual y el conjunto de disponibilidad se crearon correctamente. Se produjo un error en la dirección IP pública y no se han intentado otros recursos.
5. Puede ver los eventos de la implementación seleccionando **Eventos**.
   
    ![ver eventos](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)
6. Puede ver todos los eventos de la implementación y seleccionar cualquiera de ellos para más información.
   
    ![consultar eventos](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## Uso de registros de auditoría para solucionar problemas
[!INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver los errores de una implementación, siga estos pasos:

1. Vea los registros de auditoría de un grupo de recursos seleccionando **Registros de auditoría**.
   
    ![seleccionar registros de auditoría](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)
2. En la hoja **Registros de auditoría**, verá un resumen de las operaciones recientes de todos los grupos de recursos de su suscripción. Incluye una representación gráfica del tiempo y el estado de las operaciones, así como una lista de las operaciones.
   
    ![mostrar acciones](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)
3. Puede filtrar la vista de los registros de auditoría para centrarse en condiciones concretas. Seleccione **Filtro** en la parte superior de la hoja **Registros de auditoría**.
   
    ![filtrar registros](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)
4. En la hoja **Filtro**, seleccione las condiciones para que la visualización de los registros de auditoría se restrinja únicamente a las operaciones que desea ver. Por ejemplo, puede filtrar las operaciones para mostrar únicamente los errores de un grupo de recursos.
   
    ![establecer opciones de filtro](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)
5. Puede filtrar aún más las operaciones definiendo un intervalo de tiempo. La imagen siguiente filtra la vista a un intervalo de 20 minutos determinado.
   
    ![establecer la hora](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)
6. Puede seleccionar cualquiera de las operaciones de la lista. Seleccione la operación que contenga el error que desea investigar.
   
    ![seleccionar la operación](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
7. Verá todos los eventos de esa operación. Observe los **Id. de correlación** en el resumen. El identificador se usa para realizar un seguimiento de los eventos relacionados. Puede ser útil al trabajar con el soporte técnico para solucionar un problema. Puede seleccionar cualquiera de los eventos para ver los detalles de dicho evento.
   
    ![seleccionar el evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)
8. Verá los detalles del evento. En concreto, preste atención a las **Propiedades** para obtener información sobre el error.
   
    ![mostrar detalles del registro de auditoría](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

El filtro aplicado al registro de auditoría se conservará la próxima vez que lo vea, por lo que podría tener que cambiar esos valores para ampliar la vista de las operaciones.

## Pasos siguientes
* Para obtener ayuda con la resolución de errores de implementación concretos, consulte [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para más información acerca de cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
* Para validar la implementación antes de ejecutarla, consulte [Implementación de recursos con plantillas de Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0622_2016-->
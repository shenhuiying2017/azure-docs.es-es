<properties
   pageTitle="Visualización de operaciones de implementación con el Portal | Microsoft Azure"
   description="Describe cómo usar el Portal de Azure para detectar los errores de la implementación de Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="05/19/2016"
   ms.author="tomfitz"/>

# Visualización de operaciones de implementación con el Portal de Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API DE REST](resource-manager-troubleshoot-deployments-rest.md)

Si recibe un error al implementar recursos en Azure, quizás desee conocer más detalles acerca de las operaciones de implementación ejecutadas. El Portal de Azure proporciona una interfaz que permite buscar los errores y determinar posibles correcciones con facilidad.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Uso de registros de auditoría para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver los errores de una implementación, siga estos pasos:

1. Vea los registros de auditoría en el portal seleccionando **Examinar** y **Registros de auditoría**.

    ![seleccionar registros de auditoría](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. En la hoja **Registros de auditoría**, verá un resumen de las operaciones recientes de todos los grupos de recursos de su suscripción. Incluye una representación gráfica del tiempo y el estado de las operaciones, así como una lista de las operaciones.

    ![mostrar acciones](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Puede seleccionar cualquiera de las operaciones de la lista. Seleccione la operación que contenga el error que desea investigar.

    ![seleccionar la operación](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. Verá todos los eventos de esa operación. Observe los **Id. de correlación** en el resumen. El identificador se usa para realizar un seguimiento de los eventos relacionados. Puede ser útil al trabajar con el soporte técnico para solucionar un problema. Puede seleccionar cualquiera de los eventos para ver los detalles de dicho evento.

    ![seleccionar el evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. Verá los detalles del evento. En concreto, preste atención a las **Propiedades** para obtener información sobre el error.

    ![mostrar detalles del registro de auditoría](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Puede filtrar la vista de los registros de auditoría para centrarse en condiciones concretas. Para personalizar la vista del registro de auditoría:

1. Seleccione **Filtro** en la parte superior de la hoja **Registros de auditoría**.

    ![filtrar registros](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. En la hoja **Filtro**, seleccione las condiciones para que la visualización de los registros de auditoría se restrinja únicamente a las operaciones que desea ver. Por ejemplo, puede filtrar las operaciones para mostrar únicamente los errores de un grupo de recursos determinado.

    ![establecer opciones de filtro](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. Puede filtrar aún más las operaciones definiendo un intervalo de tiempo. La imagen siguiente filtra la vista a un intervalo de 20 minutos.

    ![establecer la hora](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

Después de actualizar la vista del registro de auditoría, solo verá las operaciones que cumplan la condición especificada. Estas configuraciones se conservan la próxima vez que vea los registros de auditoría, por lo que podría tener que cambiar esos valores para ampliar la vista de las operaciones.

Esperamos que haya podido averiguar por qué se produjo un error en su implementación. También puede mirar las operaciones de implementación para obtener información sobre el estado, tal y como se muestra en la sección siguiente.

## Uso de operaciones de implementación para solucionar problemas

Para ver las operaciones de implementación, siga estos pasos:

1. En el grupo de recursos implicado en la implementación, examine el estado de la última implementación. Puede seleccionar este estado para obtener más detalles.

    ![Estado de la implementación](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Verá el historial de implementación reciente. Seleccione la implementación en la que se produjo un error.

    ![Estado de la implementación](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Consulte la información de la implementación y seleccione la operación en la que se produjo un error para ver los detalles del error.

    ![ver la implementación con errores](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. En la hoja **Detalles de las operaciones**, verá información sobre la operación con errores. En concreto, preste atención al mensaje de estado.

    ![ver el mensaje de estado](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## Pasos siguientes

- Para obtener ayuda con la resolución de errores de implementación concretos, consulte [Resolución de errores comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para más información acerca de cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
- Para validar la implementación antes de ejecutarla, consulte [Implementación de un grupo de recursos con la plantilla de Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0525_2016-->
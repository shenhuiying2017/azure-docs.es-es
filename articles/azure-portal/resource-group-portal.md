<properties 
	pageTitle="Uso del Portal de Azure para administrar los recursos de Azure | Microsoft Azure" 
	description="Uso del Portal de Azure y de Azure Resource Manager para implementar los recursos. Muestra cómo trabajar con paneles y mosaicos para supervisar los recursos." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2016" 
	ms.author="tomfitz"/>


# Administración de los recursos de Azure a través del Portal

> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md)
- [CLI de Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [.NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [Nodo](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

En este tema se muestra cómo utilizar el [Portal de Azure](https://portal.azure.com) con [Azure Resource Manager](../resource-group-overview.md) para administrar los recursos de Azure. Actualmente, no todos los servicios son compatibles con el portal o con el Administrador de recursos. Para esos servicios, deberá usar el [portal clásico](https://manage.windowsazure.com). Para obtener más información sobre el estado de cada servicio, consulte [Tabla de disponibilidad de los portales de Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## Administración de grupos de recursos

1. Para ver todos los grupos de recursos de su suscripción, seleccione **Grupos de recursos**.

    ![buscar grupos de recursos](./media/resource-group-portal/browse-groups.png)

1. Seleccione **Agregar** para crear un grupo de recursos vacío.

    ![agregar grupo de recursos](./media/resource-group-portal/add-resource-group.png)

1. Proporcione un nombre y una ubicación para el nuevo grupo de recursos. Seleccione **Crear**.

    ![crear grupo de recursos](./media/resource-group-portal/create-empty-group.png)

1. Debe seleccionar **Actualizar** para ver el grupo de recursos creado recientemente.

    ![actualizar grupo de recursos](./media/resource-group-portal/refresh-resource-groups.png)

1. Para personalizar la información que se muestra para los grupos de recursos, seleccione **Columnas**.

    ![personalizar columnas](./media/resource-group-portal/select-columns.png)

1. Seleccione las columnas que desea agregar y, después, seleccione **Actualizar**.

    ![agregar columnas](./media/resource-group-portal/add-columns.png)

1. Si quiere aprender cómo implementar recursos en su nuevo grupo de recursos, consulte [Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure](../resource-group-template-deploy-portal.md).

1. Para obtener un acceso rápido al grupo de recursos, puede anclar la hoja en el panel.

    ![anclar un grupo de recursos](./media/resource-group-portal/pin-group.png)

1. El panel muestra el grupo de recursos y sus recursos. Puede seleccionar los grupos de recursos o cualquiera de sus recursos para navegar hasta el elemento.

    ![anclar un grupo de recursos](./media/resource-group-portal/show-resource-group-dashboard.png)

## Etiquetado de recursos

Puede aplicar etiquetas a los recursos y grupos de recursos para organizar de manera lógica los recursos. Para obtener información sobre cómo trabajar con etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

[AZURE.INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## Supervisión de recursos

Cuando se selecciona un recurso, la hoja del recurso presenta los gráficos y las tablas predeterminados para supervisar ese tipo de recurso. Puede cambiar las tablas y los gráficos que se muestran para personalizar la supervisión del recurso.

1. En la hoja de un recurso, puede agregar más gráficos y tablas; para ello, seleccione **Agregar una sección** debajo del resumen.

    ![agregar sección](./media/resource-group-portal/add-section.png)

1. En la galería de iconos, seleccione la información que desea incluir en la hoja. El editor filtra los iconos por el tipo de recurso. Si se selecciona un recurso diferente, cambiarán los iconos disponibles.

    ![agregar sección](./media/resource-group-portal/tile-gallery.png)

1. Arrastre el icono que se necesita en los espacios disponibles.

    ![arrastrar icono](./media/resource-group-portal/drag-tile.png)

1. Después de seleccionar **Listo** en la parte superior del Portal, la nueva vista forma parte de la hoja.

    ![mostrar icono](./media/resource-group-portal/show-lens.png)

1. Para anclar una sección de la hoja al panel, seleccione el botón de puntos suspensivos (...) que se encuentra encima de la sección. Asimismo puede personalizar el tamaño de la sección en la hoja o quitarlo. La siguiente imagen muestra cómo anclar, personalizar o quitar la sección CPU y memoria.

    ![anclar sección](./media/resource-group-portal/pin-cpu-section.png)

1. Después de anclar la sección en el panel, verá el resumen en el panel. Y, si lo selecciona inmediatamente, obtendrá más detalles de los datos.

    ![ver panel](./media/resource-group-portal/view-startboard.png)

1. También puede crear varios paneles para supervisar y administrar los recursos, y compartir esos paneles con otros usuarios de su organización. Seleccione **Nuevo panel**.

    ![dashboard](./media/resource-group-portal/dashboard.png)

     Para aprender a trabajar con paneles, vea el vídeo [Build Custom Dashboards in the Microsoft Azure Portal](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards) (Creación de paneles personalizados en el Portal de Microsoft Azure). Para ver cómo compartir el acceso a un panel publicado, consulte [Sharing Azure dashboards](azure-portal-dashboard-share-access.md) (Uso compartido de paneles de Azure).

## Traslado de recursos

Si necesita trasladar un recurso a otro grupo de recursos u otra suscripción, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../resource-group-move-resources.md).

## Bloqueo de recursos

Puede bloquear una suscripción, un grupo de recursos o un recurso para impedir que otros usuarios de su organización eliminen o modifiquen por error recursos esenciales. Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](../resource-group-lock-resources.md).

[AZURE.INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## Consulta de sus datos de suscripción y costos

Puede ver información acerca de la suscripción y los costos acumulados de todos los recursos. Seleccione **Suscripciones** y la suscripción que desea ver. Es posible que solo tenga una suscripción para seleccionar.

![subscription](./media/resource-group-portal/select-subscription.png)

En la hoja de suscripciones, verá una tasa de evolución.

![tasa de evolución](./media/resource-group-portal/burn-rate.png)

Y un desglose de costos por tipo de recurso.

![costo de recursos](./media/resource-group-portal/cost-by-resource.png)

## Exportación de la plantilla

Después de configurar el grupo de recursos, quizás quiera ver la plantilla de Resource Manager para el grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque la plantilla contiene la infraestructura completa.

2. Para familiarizarse con la sintaxis de la plantilla, consulte la notación de objetos JavaScript (JSON) que representa la solución.

Para obtener instrucciones detalladas, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](../resource-manager-export-template.md).

## Eliminación de los recursos o grupos de recursos

Al eliminar un grupo de recursos se eliminan todos los recursos contenidos en el mismo. También puede eliminar recursos individuales de un grupo de recursos. Debe prestar atención cuando elimine un grupo de recursos porque podría haber recursos vinculados a él en otros grupos de recursos. Resource Manager no elimina los recursos vinculados, pero podrían no funcionar correctamente sin los recursos esperados.

![eliminar grupo](./media/resource-group-portal/delete-group.png)

## Pasos siguientes

- Para ver los registros de auditoría, consulte [Operaciones de auditoría con Resource Manager](../resource-group-audit.md).
- Para solucionar problemas de implementación, consulte [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Para implementar recursos mediante el Portal, consulte [Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure](../resource-group-template-deploy-portal.md).
- Para administrar el acceso a los recursos, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0803_2016-->
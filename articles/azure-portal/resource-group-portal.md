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
	ms.date="06/10/2016" 
	ms.author="tomfitz"/>


# Administración de los recursos de Azure a través del Portal

> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md)
- [CLI de Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [Nodo](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

En este tema se muestra cómo utilizar el [Portal de Azure](https://portal.azure.com) con [Azure Resource Manager](../resource-group-overview.md) para administrar los recursos de Azure. Si quiere obtener más información cómo implementar recursos a través del Portal, consulte [Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure](../resource-group-template-deploy-portal.md).

Actualmente, no todos los servicios son compatibles con el portal o con el Administrador de recursos. Para esos servicios, deberá usar el [portal clásico](https://manage.windowsazure.com). Para obtener más información sobre el estado de cada servicio, consulte [Tabla de disponibilidad de los portales de Azure](https://azure.microsoft.com/features/azure-portal/availability/).

<a id="access-control-for-azure-dashboards" />
## Personalización del panel para supervisar los recursos

El Portal proporciona un panel que puede utilizar para supervisar y administrar los recursos. El panel se puede personalizar en su totalidad y puede crear varios paneles para proporcionar fácilmente diferentes vistas de una suscripción.

![dashboard](./media/resource-group-portal/dashboard.png)

> [AZURE.TIP] Es la mejor manera de aprender a trabajar con los paneles ver el vídeo [Build Custom Dashboards in the Microsoft Azure Portal](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards) (Compilación de paneles personalizados en el Portal de Microsoft Azure).

### Uso compartido de los paneles y el control de acceso de Azure
Después de configurar un panel, puede publicarlo y compartirlo con otros usuarios de su organización. El [control de acceso basado en roles](../active-directory/role-based-access-control-configure.md) de Azure rige el acceso a la información que se muestra en los iconos del Portal. Todos los paneles publicados se implementan como recursos de Azure. Desde una perspectiva de control de acceso, los paneles no son distintos de una máquina virtual o de una cuenta de almacenamiento.

Aquí tiene un ejemplo. Supongamos que tiene una suscripción de Azure y que a varios miembros del equipo se les han asignado los roles de **propietario**, **colaborador** o **lector** de la suscripción. Los usuarios que son propietarios o colaboradores pueden enumerar, ver, crear, modificar o eliminar paneles dentro de la suscripción. Los usuarios que sean lectores pueden enumerar y ver los paneles, pero no pueden modificarlos ni eliminarlos. Los usuarios con acceso de lectura pueden realizar modificaciones locales en un panel publicado (por ejemplo, para solucionar un problema), pero no tienen la opción de publicar esos cambios de vuelta en el servidor. Podrán realizar una copia privada del panel para ellos mismos.

Los iconos individuales del panel tendrán sus propios requisitos de control de acceso en función de los recursos que muestran. Esto significa que puede diseñar un panel que se comparta ampliamente mientras sigue protegiendo los datos de iconos individuales.

## Administración de grupos de recursos

1. Para ver todos los grupos de recursos en su suscripción, seleccione **Grupos de recursos**.

    ![buscar grupos de recursos](./media/resource-group-portal/browse-groups.png)

2. Seleccione el grupo de recursos específico que desea administrar. Verá una hoja del grupo de recursos que ofrece información sobre el mismo, incluida una lista de todos los recursos del grupo.

    ![resumen del grupo de recursos](./media/resource-group-portal/group-summary.png)

    Al seleccionar cualquiera de los recursos, se muestra información sobre ese recurso.

3. Dentro de la hoja del grupo de recursos, puede agregar más gráficos y tablas seleccionando **Agregar una sección** debajo del resumen.

    ![agregar sección](./media/resource-group-portal/add-section.png)

4. Se le presentará una galería de iconos para seleccionar la información que desea incluir en la hoja. Los tipos de iconos que se muestran se filtran por el tipo de recurso. Si se selecciona un recurso diferente, se cambiarán los iconos disponibles.

    ![agregar sección](./media/resource-group-portal/tile-gallery.png)

5. Arrastre el icono que se necesita en los espacios disponibles.

    ![arrastrar icono](./media/resource-group-portal/drag-tile.png)

6. Después de seleccionar **Listo** en la parte superior del Portal, la nueva vista forma parte de la hoja.

    ![mostrar icono](./media/resource-group-portal/show-lens.png)

7. Para obtener un acceso rápido al grupo de recursos, puede anclar la hoja en el panel.

    ![anclar un grupo de recursos](./media/resource-group-portal/pin-group.png)

    O bien, puede anclar una sección de la hoja en el panel seleccionando el botón de puntos suspensivos (...) que se encuentra sobre la sección. Asimismo puede personalizar el tamaño de la sección en la hoja o quitarlo. La siguiente imagen muestra cómo anclar, personalizar o quitar la sección CPU y memoria.

    ![anclar sección](./media/resource-group-portal/pin-cpu-section.png)

8. Después de anclar la sección en el panel, verá el resumen en el panel.

    ![ver panel](./media/resource-group-portal/view-startboard.png)

Y, si lo selecciona inmediatamente, obtendrá más detalles de los datos.

## Etiquetado de recursos

Puede aplicar etiquetas a los recursos y grupos de recursos para organizar de manera lógica los recursos. Para obtener información sobre cómo trabajar con etiquetas a través del portal, consulte [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

## Consulta de sus datos de suscripción y costos

Puede ver información acerca de la suscripción y de los costos acumulados de todos los recursos. Seleccione **Suscripciones** y la suscripción que desea ver. Es posible que solo tenga una suscripción para seleccionar.

![subscription](./media/resource-group-portal/select-subscription.png)

En la hoja de suscripciones, verá una tasa de evolución.

![tasa de evolución](./media/resource-group-portal/burn-rate.png)

Y un desglose de costos por tipo de recurso.

![costo de recursos](./media/resource-group-portal/cost-by-resource.png)

## Exportación de la plantilla

Después de configurar el grupo de recursos, quizás quiera ver la plantilla de Resource Manager para el grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque toda la infraestructura está definida en la plantilla.

2. Para familiarizarse con la sintaxis de la plantilla, consulte la notación de objetos JavaScript (JSON) que representa la solución.

Para consultar instrucciones detalladas, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](../resource-manager-export-template.md).

## Eliminación de los recursos o grupos de recursos

Al eliminar un grupo de recursos se eliminan todos los recursos contenidos en el mismo. También puede eliminar recursos individuales de un grupo de recursos. Debe prestar atención cuando elimine un grupo de recursos porque podría haber otros recursos en otros grupos de recursos vinculados a él. No se eliminarán los recursos vinculados aunque no funcionen según lo esperado.

![eliminar grupo](./media/resource-group-portal/delete-group.png)


## Pasos siguientes

- Para ver los registros de auditoría, consulte [Operaciones de auditoría con Resource Manager](../resource-group-audit.md).
- Para solucionar problemas de implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Para implementar recursos a través del Portal, consulte [Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure](../resource-group-template-deploy-portal.md).

<!---HONumber=AcomDC_0706_2016-->
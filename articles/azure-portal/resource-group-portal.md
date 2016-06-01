<properties 
	pageTitle="Uso del Portal de Azure para administrar los recursos de Azure | Microsoft Azure" 
	description="Utilice el Portal de Azure y Azure Resource Manager para implementar y administrar los recursos. Muestra cómo etiquetar los recursos y ver los registros de auditoría." 
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
	ms.date="05/16/2016" 
	ms.author="tomfitz"/>


# Uso del Portal de Azure para implementar y administrar los recursos de Azure

## Introducción

En este tema se muestra cómo utilizar el [Portal de Azure](https://portal.azure.com) con [Azure Resource Manager](../resource-group-overview.md) para implementar y administrar los recursos de Azure.

Actualmente, no todos los servicios son compatibles con el portal o con el Administrador de recursos. Para esos servicios, deberá usar el [portal clásico](https://manage.windowsazure.com). Para más información sobre el estado de cada servicio, consulte [Tabla de disponibilidad de los portales de Azure](https://azure.microsoft.com/features/azure-portal/availability/).

También puede administrar los recursos mediante la CLI de Azure y Azure PowerShell. Para más información acerca del uso de estas interfaces, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md) y [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](../xplat-cli-azure-resource-manager.md). Para más información sobre la implementación de soluciones con Visual Studio, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Creación de un grupo de recursos

Para crear un grupo de recursos vacío, seleccione **Nuevo**, **Administración** y **Grupo de recursos**.

![crear un grupo de recursos vacío](./media/resource-group-portal/create-empty-group.png)

Asígnele un nombre y una ubicación y, si es necesario, seleccione una suscripción.

![establecer valores de grupo](./media/resource-group-portal/set-group-properties.png)

## Implementación de recursos

Una vez creado el grupo de recursos, puede implementar recursos en él. Para iniciar la implementación, simplemente seleccione **Nuevo** y el tipo de recurso que le gustaría implementar.

![implementar recursos](./media/resource-group-portal/deploy-resource.png)

Si no ve el tipo de recurso que le gustaría implementar, búsquelo en Marketplace.

![buscar en Marketplace](./media/resource-group-portal/search-resource.png)

Según el tipo de recurso que haya seleccionado, tendrá una colección de propiedades pertinentes que hay que establecer antes de la implementación. Estas opciones no se muestran aquí, dado que varía según el tipo de recurso. Para todos los tipos, debe seleccionar un grupo de recursos de destino. La imagen siguiente muestra cómo crear una nueva aplicación web e implementarla en el grupo de recursos que acaba de crear.

![crear grupo de recursos](./media/resource-group-portal/select-existing-group.png)

De forma alternativa, puede decidir crear un nuevo grupo de recursos al implementar sus recursos. En lugar de seleccionar uno de los grupos de recursos existente en su suscripción, seleccione **Nuevo** y asígnele un nombre al grupo de recursos.

![crear nuevo grupo de recursos.](./media/resource-group-portal/select-new-group.png)

Se iniciará la implementación. Esto puede tardar unos minutos. Cuando haya terminado la implementación, verá una notificación.

![ver notificación](./media/resource-group-portal/view-notification.png)

Después de implementar los recursos, puede que necesite agregar más recursos al grupo. Puede agregar recursos a un grupo de recursos con el comando **Add** en la hoja del grupo de recursos.

![agregar recurso](./media/resource-group-portal/add-resource.png)

## Exportación de la plantilla

Después de configurar el grupo de recursos, quizás quiera ver la plantilla de Resource Manager para el grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque toda la infraestructura está definida en la plantilla.

2. Para familiarizarse con la sintaxis de la plantilla, consulte la notación de objetos JavaScript (JSON) que representa la solución.

> [AZURE.NOTE] La característica de exportación de la plantilla es una versión preliminar y no todos los tipos de recursos admiten actualmente la exportación de una plantilla. Al intentar exportar una plantilla, verá un error que indica que algunos recursos no se han exportado. Si es necesario, puede definir manualmente estos recursos en la plantilla después de descargarla.

Para instrucciones detalladas, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](../resource-manager-export-template/)

## Administración de un grupo de recursos

Para examinar todos los grupos de recursos, haga clic en **Grupos de recursos**.

![buscar grupos de recursos](./media/resource-group-portal/browse-groups.png)

Al seleccionar un grupo de recursos determinado, verá una hoja del grupo de recursos que ofrece información acerca de dicho grupo de recursos, incluida una lista de todos los recursos del grupo.

![resumen del grupo de recursos](./media/resource-group-portal/group-summary.png)

Para agregar más gráficos y tablas a la hoja del grupo de recursos, seleccione **Agregar una sección** debajo del resumen.

![agregar sección](./media/resource-group-portal/add-section.png)

Se le presentará una galería de iconos para seleccionar la información que desea incluir en la hoja. Los tipos de iconos que se muestran se filtran por el tipo de recurso. Si se selecciona un recurso diferente, se cambiarán los iconos disponibles.

![agregar sección](./media/resource-group-portal/tile-gallery.png)

Arrastre el icono que se necesita en los espacios disponibles.

![arrastrar icono](./media/resource-group-portal/drag-tile.png)

Después de seleccionar **Hecho** en la parte superior del portal, la nueva vista forma parte de la hoja.

![mostrar icono](./media/resource-group-portal/show-lens.png)

Para obtener un acceso rápido al grupo de recursos, puede anclar la hoja en el panel.

![anclar un grupo de recursos](./media/resource-group-portal/pin-group.png)

O bien, puede anclar una sección de la hoja en el panel seleccionando el botón de puntos suspensivos (...) que se encuentra sobre la sección. Asimismo puede personalizar el tamaño de la sección en la hoja o quitarlo. La siguiente imagen muestra cómo anclar, personalizar o quitar la sección CPU y memoria.

![anclar sección](./media/resource-group-portal/pin-cpu-section.png)

Después de anclar la sección en el panel, verá el resumen en el panel.

![ver panel](./media/resource-group-portal/view-startboard.png)

Y, si lo selecciona inmediatamente, obtendrá más detalles de los datos.

Puesto que los grupos de recursos le permiten administrar el ciclo de vida de todos los recursos contenidos, la eliminación de un grupo de recursos provocará que se eliminen todos los recursos que alberga. También puede eliminar recursos individuales de un grupo de recursos. Debe prestar atención cuando elimine un grupo de recursos porque podría haber otros recursos en otros grupos de recursos vinculados a él. No se eliminarán los recursos vinculados aunque no funcionen según lo esperado.

![eliminar grupo](./media/resource-group-portal/delete-group.png)

## Etiquetado de recursos

Puede aplicar etiquetas a los recursos y grupos de recursos para organizar de manera lógica los recursos. Para obtener información sobre cómo trabajar con etiquetas a través del portal, consulte [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

## Implementación de una plantilla guardada

Si guardó una plantilla en su cuenta y desea verla posteriormente, seleccione **Examinar** y **Plantillas**.

![examinar plantillas](./media/resource-group-portal/browse-templates.png)

Verá su propia colección de plantillas.

![mostrar colección de plantillas](./media/resource-group-portal/show-template-collection.png)


## Implementación de una plantilla personalizada

Si desea ejecutar una implementación sin usar las plantillas de Marketplace, puede crear una plantilla personalizada que defina la infraestructura para la solución. Para obtener más información sobre las plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para implementar una plantilla personalizada a través del portal, seleccione **Nuevo** y busque **Implementación de plantillas** hasta que pueda seleccionarla entre las opciones.

![buscar implementación de plantilla](./media/resource-group-portal/search-template.png)

Seleccione **Implementación de plantillas** en los recursos disponibles.

![seleccionar implementación de plantillas](./media/resource-group-portal/select-template.png)

Después de iniciar la implementación de la plantilla, puede crear la plantilla personalizada y establecer los valores para la implementación.

![crear plantilla](./media/resource-group-portal/show-custom-template.png)

O bien puede seleccionar una plantilla existente en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/). Estas plantillas son aportaciones de la comunidad. Abarcan muchos escenarios comunes y puede que alguien haya agregado una plantilla que sea similar a lo que desea implementar. Puede buscar las plantillas para encontrar alguna que coincida con su escenario.

![seleccionar plantilla de inicio rápido](./media/resource-group-portal/select-quickstart-template.png)

Después de seleccionar una plantilla, se carga en el editor.

## Consulta de sus datos de suscripción y costos

Puede ver información acerca de la suscripción y de los costos acumulados de todos los recursos. Seleccione **Suscripciones** y la suscripción que desea ver. Es posible que solo tenga una suscripción para seleccionar.

![subscription](./media/resource-group-portal/select-subscription.png)

En la hoja de suscripciones, verá una tasa de evolución.

![tasa de evolución](./media/resource-group-portal/burn-rate.png)

Y un desglose de costos por tipo de recurso.

![costo de recursos](./media/resource-group-portal/cost-by-resource.png)

## Control de acceso para los paneles de Azure

El acceso a la información que muestra la mayoría de los iconos del portal se rige mediante el [Control de acceso basado en roles](../active-directory/role-based-access-control-configure.md) de Azure. Con el fin de integrar perfectamente los paneles en el ecosistema, todos los paneles publicados se implementan como recursos de Azure. Desde una perspectiva de control de acceso, los paneles no son distintos de una máquina virtual o de una cuenta de almacenamiento.

Aquí tiene un ejemplo. Supongamos que tiene una suscripción de Azure y que a varios miembros del equipo se les han asignado los roles de **propietario**, **colaborador** o **lector** de la suscripción. Los usuarios que son propietarios o colaboradores podrán enumerar, ver, crear, modificar o eliminar paneles dentro de la suscripción. Los usuarios que sean lectores podrán enumerar y ver los paneles, pero no podrán modificarlos ni eliminarlos. Los usuarios con acceso de lectura podrán realizar modificaciones locales en un panel publicado (por ejemplo, para solucionar un problema), pero no tendrán la opción de volver a publicar esos cambios en el servidor. Podrán realizar una copia privada del panel para ellos mismos.

Tenga en cuenta que los iconos individuales del panel tendrán sus propios requisitos de control de acceso en función de los recursos para los que están mostrando los datos. Esto significa que puede diseñar un panel que se puede compartir más ampliamente mientras sigue protegiendo los datos de iconos individuales.

## Pasos siguientes

- Para ver los registros de auditoría, consulte [Operaciones de auditoría con Resource Manager](../resource-group-audit.md).
- Para solucionar problemas de implementación, consulte [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0518_2016-->
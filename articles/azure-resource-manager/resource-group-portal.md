---
title: Uso de Azure Portal para administrar los recursos de Azure | Microsoft Docs
description: "Uso del Portal de Azure y de Azure Resource Manager para implementar los recursos. Muestra cómo trabajar con paneles para supervisar recursos."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
ms.openlocfilehash: 7a94fd5065de93384460e851627a9813d439956b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="manage-azure-resources-through-portal"></a>Administración de los recursos de Azure a través del Portal
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [CLI de Azure](xplat-cli-azure-resource-manager.md)
> * [Portal](resource-group-portal.md) 
> * [API DE REST](resource-manager-rest-api.md)
> 
> 

En este tema se muestra cómo utilizar el [Azure Portal](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) para administrar los recursos de Azure. Para aprender sobre la implementación de recursos a través del Portal, consulte [Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure](resource-group-template-deploy-portal.md).

Actualmente, no todos los servicios son compatibles con el portal o con el Administrador de recursos. Para esos servicios, deberá usar el [portal clásico](https://manage.windowsazure.com). Para más información sobre el estado de cada servicio, consulte [Tabla de disponibilidad de los portales de Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="manage-resource-groups"></a>Administración de grupos de recursos

Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. El grupo de recursos puede incluir todos los recursos de la solución o solo aquellos que se desean administrar como grupo. Para decidir cómo asignar los recursos a los grupos de recursos, tenga en cuenta lo que más conviene a su organización. Por lo general, se recomienda agregar recursos que compartan el mismo ciclo de vida al mismo grupo de recursos para que los pueda implementar, actualizar y eliminar con facilidad como un grupo. 

Los grupos de recursos almacenan metadatos acerca de los recursos. Por consiguiente, al especificar la ubicación del grupo de recursos, se especifica el lugar en que se almacenan dichos metadatos. Por motivos de compatibilidad, es posible que sea preciso asegurarse de que los datos se almacenan en una región concreta.

1. Para ver todos los grupos de recursos de su suscripción, seleccione **Grupos de recursos**.
   
    ![buscar grupos de recursos](./media/resource-group-portal/browse-groups.png)
2. Seleccione **Agregar**para crear un grupo de recursos vacío.
   
    ![agregar grupo de recursos](./media/resource-group-portal/add-resource-group.png)
3. Proporcione un nombre y una ubicación para el nuevo grupo de recursos. Seleccione **Crear**.
   
    ![crear grupo de recursos](./media/resource-group-portal/create-empty-group.png)
4. Debe seleccionar **Actualizar** para ver el grupo de recursos creado recientemente.
   
    ![actualizar grupo de recursos](./media/resource-group-portal/refresh-resource-groups.png)
5. Para personalizar la información que se muestra para los grupos de recursos, seleccione **Columnas**.
   
    ![personalizar columnas](./media/resource-group-portal/select-columns.png)
6. Seleccione las columnas que desea agregar y, después, seleccione **Actualizar**.
   
    ![agregar columnas](./media/resource-group-portal/add-columns.png)
7. Si quiere aprender cómo implementar recursos en su nuevo grupo de recursos, consulte [Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure](resource-group-template-deploy-portal.md).
8. Para obtener un acceso rápido al grupo de recursos, puede anclar la hoja en el panel.
   
    ![anclar un grupo de recursos](./media/resource-group-portal/pin-group.png)
9. El panel muestra el grupo de recursos y sus recursos. Puede seleccionar los grupos de recursos o cualquiera de sus recursos para navegar hasta el elemento.
   
    ![anclar un grupo de recursos](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Etiquetado de recursos
Puede aplicar etiquetas a los recursos y grupos de recursos para organizar de manera lógica los recursos. Para más información sobre cómo trabajar con etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Supervisión de recursos
Cuando se selecciona un recurso, la hoja del recurso presenta los gráficos y las tablas predeterminados para supervisar ese tipo de recurso.

1. Seleccione un recurso y observe la sección **Supervisión** . Incluye gráficos que son pertinentes para el tipo de recurso. En la imagen siguiente se muestran los datos de supervisión predeterminados para una cuenta de almacenamiento.
   
    ![mostrar supervisión](./media/resource-group-portal/show-monitoring.png)
2. Para anclar una sección de la hoja al panel, seleccione el botón de puntos suspensivos (...) que se encuentra encima de la sección. Asimismo puede personalizar el tamaño de la sección en la hoja o quitarlo. La siguiente imagen muestra cómo anclar, personalizar o quitar la sección CPU y memoria.
   
    ![anclar sección](./media/resource-group-portal/pin-cpu-section.png)
3. Después de anclar la sección en el panel, verá el resumen en el panel. Y, si lo selecciona inmediatamente, obtendrá más detalles de los datos.
   
    ![ver panel](./media/resource-group-portal/view-startboard.png)
4. Para personalizar completamente los datos que supervisa mediante el portal, vaya al panel predeterminado y seleccione **Nuevo panel**.
   
    ![dashboard](./media/resource-group-portal/dashboard.png)
5. Asigne un nombre al nuevo panel y arrastre hasta ahí los iconos. Los iconos se filtran por distintas opciones.
   
    ![dashboard](./media/resource-group-portal/create-dashboard.png)
   
     Para aprender a trabajar con paneles, vea el vídeo [Creación y uso compartido de paneles en Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Administración de recursos
En la hoja de un recurso, verá las opciones para administrar el recurso. El portal presenta opciones de administración para ese tipo de recurso en particular. Verá los comandos de administración en la parte superior de la hoja de recursos y en el lado izquierdo.

![Administración de recursos](./media/resource-group-portal/manage-resources.png)

Desde estas opciones, puede realizar operaciones como iniciar y detener una máquina virtual o volver a configurar las propiedades de la máquina virtual.

## <a name="move-resources"></a>Traslado de recursos
Si necesita trasladar un recurso a otro grupo de recursos u otra suscripción, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloqueo de recursos
Puede bloquear una suscripción, un grupo de recursos o un recurso para impedir que otros usuarios de su organización eliminen o modifiquen por error recursos esenciales. Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Consulta de sus datos de suscripción y costos
Puede ver información acerca de la suscripción y los costos acumulados de todos los recursos. Seleccione **Suscripciones** y la suscripción que quiere ver. Es posible que solo tenga una suscripción para seleccionar.

![suscripción](./media/resource-group-portal/select-subscription.png)

En la hoja de suscripciones, verá una tasa de evolución.

![tasa de evolución](./media/resource-group-portal/burn-rate.png)

Y un desglose de costos por tipo de recurso.

![costo de recursos](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Exportación de la plantilla
Después de configurar el grupo de recursos, quizás quiera ver la plantilla de Resource Manager para el grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque la plantilla contiene la infraestructura completa.
2. Para familiarizarse con la sintaxis de la plantilla, consulte la notación de objetos JavaScript (JSON) que representa la solución.

Para obtener instrucciones detalladas, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Eliminación de los recursos o grupos de recursos
Al eliminar un grupo de recursos se eliminan todos los recursos contenidos en el mismo. También puede eliminar recursos individuales de un grupo de recursos. Debe prestar atención cuando elimine un grupo de recursos porque podría haber recursos vinculados a él en otros grupos de recursos. Resource Manager no elimina los recursos vinculados, pero podrían no funcionar correctamente sin los recursos esperados.

![eliminar grupo](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Pasos siguientes
* Para ver los registros de auditoría, consulte el artículo sobre [operaciones de auditoría con Resource Manager](resource-group-audit.md).
* Para ver detalles acerca de una implementación, consulte [View deployment operations](resource-manager-deployment-operations.md) (Ver operaciones de implementación).
* Para implementar recursos mediante el Portal, consulte [Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure](resource-group-template-deploy-portal.md).
* Para administrar el acceso a los recursos, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).


---
title: Uso de Azure Portal para implementar los recursos de Azure | Microsoft Docs
description: Utilice el Portal de Azure y Azure Resource Manager para implementar los recursos.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: ea91fdd58dd3b5c118fe390afe1eb355e3c26570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure

En este tema se muestra cómo utilizar [Azure Portal](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) para implementar los recursos de Azure. Para obtener más información sobre cómo administrar los recursos, consulte [Administración de los recursos de Azure a través del Portal](resource-group-portal.md).

## <a name="create-resource-group"></a>Creación de un grupo de recursos

1. Para crear un grupo de recursos vacío, seleccione **Grupos de recursos**.

   ![Seleccionar Grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. En Grupo de recursos, seleccione **Agregar**.

   ![Agregar un grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Asígnele un nombre y una ubicación y, si es necesario, seleccione una suscripción. Debe proporcionar una ubicación para el grupo de recursos porque este almacena metadatos sobre los recursos. Por motivos de cumplimiento, debería especificar dónde se almacenan esos metadatos. Por lo general, se recomienda especificar una ubicación en la que vayan a residir la mayoría de los recursos. Si usa la misma ubicación, puede simplificar la plantilla.

   ![Establecer valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Cuando haya terminado de establecer las propiedades, haga clic en **Crear**.

1. Para ver el nuevo grupo de recursos, haga clic en **Actualizar**.

   ![Actualizar los grupos de recursos](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Implementación de recursos desde Marketplace

Una vez creado el grupo de recursos, puede implementar recursos en él desde Marketplace. Marketplace proporciona soluciones predefinidas para escenarios habituales.

1. Para iniciar una implementación, haga clic en **Nuevo**.

   ![Nuevo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Busque el tipo de recurso que quiera implementar.

   ![Selección del tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Si no ve la solución específica que gustaría implementar, búsquela en Marketplace. Por ejemplo, para buscar una solución de Wordpress, empiece a escribir **Wordpress** y seleccione la opción que quiera.

   ![Buscar en Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. Según el tipo del recurso seleccionado, tiene una colección de propiedades pertinentes que debe establecer antes de la implementación. Para todos los tipos, debe seleccionar un grupo de recursos de destino. En la imagen siguiente se muestra cómo crear una aplicación web e implementarla en el grupo de recursos que ha creado.

   ![Creación de un grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Como alternativa, puede decidir crear un nuevo grupo de recursos al implementar estos últimos. Seleccione **Crear nuevo** y asígnele un nombre al grupo de recursos.

   ![Crear un grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Comenzará la implementación. Esta puede tardar unos minutos. Cuando haya terminado la implementación, verá una notificación.

   ![Ver la notificación](./media/resource-group-template-deploy-portal/view-notification.png)

1. Después de implementar los recursos, puede agregar más recursos al grupo de recursos si hace clic en **Agregar**.

   ![Agregar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementación de recursos desde plantilla personalizada

Si desea ejecutar una implementación sin usar las plantillas de Marketplace, puede crear una plantilla personalizada que defina la infraestructura para la solución. Para más información sobre cómo crear plantillas, vea [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](resource-group-authoring-templates.md).

1. Para implementar una plantilla personalizada a través del portal, haga clic en **Nuevo** y busque **Implementación de plantillas** hasta que pueda seleccionarla de entre las opciones.

   ![Buscar la implementación de plantilla](./media/resource-group-template-deploy-portal/search-template.png)

1. Seleccione **Crear**.

   ![Haga clic en Crear](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Tiene varias opciones para crear una plantilla. Seleccione **Cree su propia plantilla en el editor**.

   ![Ver las opciones](./media/resource-group-template-deploy-portal/see-options.png)

1. Verá una plantilla en blanco que está disponible para que la personalice.

   ![Creación de una plantilla](./media/resource-group-template-deploy-portal/blank-template.png)

1. Puede editar la sintaxis JSON manualmente o seleccionar una plantilla pregenerada de la [Galería Plantilla de inicio rápido](https://azure.microsoft.com/resources/templates/). Pero en este artículo usaremos la opción **Agregar recurso**.

   ![Editar plantilla](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Seleccione **Cuenta de almacenamiento** e indique un nombre. Cuando termine de proporcionar los valores pertinentes, haga clic en **Aceptar**.

   ![Selección de la cuenta de almacenamiento](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. El editor agrega automáticamente JSON para el tipo de recurso. Observe que se incluye un parámetro para definir el tipo de cuenta de almacenamiento. Seleccione **Guardar**.

   ![Mostrar la plantilla](./media/resource-group-template-deploy-portal/show-json.png)

1. Ahora, tiene la posibilidad de implementar los recursos definidos en la plantilla. Para hacerlo, acepte los términos y condiciones y haga clic en **Comprar**.

   ![Implementar plantilla](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implementación de los recursos desde una plantilla guardada en la cuenta

El portal permite guardar una plantilla en su cuenta de Azure y volver a implementarla más adelante. Para obtener más información sobre cómo trabajar con estas plantillas guardadas, consulte [Introducción a las plantillas privadas del Portal de Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Para buscar las plantillas guardadas, haga clic en **Más servicios**.

   ![Más servicios](./media/resource-group-template-deploy-portal/more-services.png)

1. Busque **plantillas** y seleccione esa opción.

   ![Buscar plantillas](./media/resource-group-template-deploy-portal/find-templates.png)

1. En la lista de plantillas guardadas en su cuenta, seleccione aquella en la que desea trabajar.

   ![Plantillas guardadas](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Seleccione **Implementar** para volver a implementar esta plantilla guardada.

   ![Implementar una plantilla guardada](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Pasos siguientes
* Para ver los registros de auditoría, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
* Para solucionar errores de implementación, vea [View deployment operations](resource-manager-deployment-operations.md) (Ver operaciones de implementación).
* Para recuperar una plantilla de una implementación o un grupo de recursos, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

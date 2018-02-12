---
title: "Introducción a las plantillas privadas | Microsoft Docs"
description: Agregue, administre y comparta plantillas privadas con el Portal de Azure, con la CLI de Azure o con PowerShell.
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
ms.openlocfilehash: c890339ba7677b23717a6e0437b5e936fdf8ab03
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Introducción a las plantillas privadas de Azure Portal
Las plantillas de [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) son plantillas declarativas que se utilizan para definir la implementación. Puede definir los recursos que se van a implementar en una solución y especificar parámetros y variables que le permitan especificar valores de entrada para diferentes entornos. La plantilla consta de código JSON y expresiones que puede usar para generar valores para su implementación.

Puede usar la nueva funcionalidad **Plantillas** de [Azure Portal](https://portal.azure.com) junto con el proveedor de recursos **Microsoft.Gallery** como una extensión de [Azure Marketplace](https://azure.microsoft.com/marketplace/) para que los usuarios puedan crear, administrar e implementar plantillas privadas procedentes de una biblioteca personal.

> [!NOTE]
> En lugar de usar plantillas privadas en el portal, Microsoft recomienda crear una aplicación de catálogo de servicios mediante [aplicaciones administradas](../managed-applications/overview.md). Puede poner la aplicación de catálogo de servicios a disposición de los usuarios de la organización.

En este documento, se explica paso a paso cómo agregar, administrar y compartir una **plantilla** privada mediante Azure Portal.

## <a name="guidance"></a>Guía
Las sugerencias siguientes le ayudan a sacar el máximo provecho de las **plantillas** cuando trabaje con sus soluciones:

* Una **plantilla** es un recurso de encapsulamiento que contiene una plantilla de Resource Manager y otros metadatos. Su comportamiento es similar al de los elementos de Marketplace. La principal diferencia es que se trata de un elemento privado, mientras que los elementos de Marketplace son públicos.
* La biblioteca **Plantillas** resulta muy útil para los usuarios que necesitan personalizar sus implementaciones.
* **plantillas** son apropiadas para los usuarios que necesitan disponer de un repositorio sencillo dentro de Azure.
* Para empezar, utilice una plantilla de Resource Manager existente. Para buscar plantillas, utilice [github](https://github.com/Azure/azure-quickstart-templates) o [Exportar plantilla](../azure-resource-manager/resource-manager-export-template.md) desde un grupo de recursos existente.
* **plantillas** están vinculadas al usuario que las publica. Cualquier persona que tenga acceso podrá ver el nombre del publicador.
* **plantillas** son recursos de Resource Manager y, una vez publicadas, su nombre no se puede modificar.

## <a name="add-a-template-resource"></a>Adición de un recurso de plantilla
Existen dos formas de crear un recurso de **plantilla** en el Portal de Azure.

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>Método 1: Creación de un nuevo recurso de plantilla a partir de un grupo de recursos en funcionamiento
1. Acceda a un grupo de recursos existente en Azure Portal. En **Configuración**, seleccione **Exportar plantilla**.
2. Cuando la plantilla de Resource Manager se haya exportado, utilice el botón **Guardar plantilla** para guardarla en el repositorio **Plantillas**. Para más detalles sobre la exportación de plantillas, haga clic [aquí](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Exportación de grupos de recursos](media/rg-export-portal1.PNG)
3. Seleccione el botón de comando **Guardar plantilla** .
   <br /><br />
4. Escriba la siguiente información:
   
   * Nombre: nombre del objeto de plantilla (NOTA: Este campo debe ajustarse a las especificaciones de Azure Resource Manager, por lo que se aplicarán todas las restricciones de nomenclatura, y no se puede cambiar una vez creado).
   * Descripción: resumen rápido de la plantilla.
     
     ![Guardar plantilla](media/save-template-portal1.PNG)
5. Haga clic en **Save**(Guardar).
   
   > [!NOTE]
   > El portal muestra notificaciones cuando la plantilla de Resource Manager exportada contiene errores; sin embargo, a pesar de los errores, podrá guardarla en Plantillas. No olvide comprobar y corregir los problemas antes de volver a implementar la plantilla de Resource Manager exportada.
   > 
   > 

### <a name="method-2-add-a-new-template-resource-from-browse"></a>Método 2: Adición de un nuevo recurso de plantilla desde Examinar
También puede agregar una nueva **plantilla** desde el principio utilizando el botón de comando +Agregar de **Examinar > Plantillas**. Proporcione un nombre, una descripción y el archivo JSON de la plantilla de Resource Manager.

![Agregar plantilla](media/add-template-portal1.PNG)

> [!NOTE]
> Microsoft.Gallery es un proveedor de recursos de Azure basado en un inquilino. El recurso de plantilla está vinculado al usuario que lo creó. No está asociado a ninguna suscripción específica. Elija una suscripción al implementar una plantilla.
> 
> 

## <a name="view-template-resources"></a>Consulta de los recursos de plantilla
Puede ver todas las **plantillas** disponibles en **Examinar > Plantillas**. Las plantillas disponibles son las que ha creado, así como las plantillas que se han compartido con usted y que tienen distintos niveles de permisos. Para más información, consulte [Control de acceso](#access-control-for-a-tenant-resource-provider).

![Ver plantilla](media/view-template-portal1.PNG)

Para ver los detalles de una **plantilla** , haga clic en un elemento de la lista.

![Ver plantilla](media/view-template-portal2c.png)

## <a name="edit-a-template-resource"></a>Edición de un recurso de plantilla
Para iniciar el flujo de edición de una **plantilla**, haga clic con el botón derecho en el elemento de la lista Examinar o seleccione el botón de comando Editar.

![Editar plantilla](media/edit-template-portal1a.PNG)

Puede editar la descripción o el texto de la plantilla de Resource Manager. No obstante, el nombre no se puede modificar, ya que se trata de un nombre de recurso de Resource Manager. Cuando edite el archivo JSON de la plantilla de Resource Manager, realizaremos las comprobaciones pertinentes para garantizar que se trata de un archivo JSON válido. Elija **Aceptar** y **Guardar** para guardar la plantilla actualizada.

![Editar plantilla](media/edit-template-portal2a.PNG)

Cuando haya guardado la plantilla, aparecerá una notificación de confirmación.

![Editar plantilla](media/edit-template-portal3b.png)

## <a name="deploy-a-template-resource"></a>Implementación de un recurso de plantilla
Puede implementar cualquier **plantilla** en la que tenga permisos de **lectura**. Rellene los valores de los parámetros de la plantilla de Resource Manager para continuar con la implementación.

![Implementar plantilla](media/deploy-template-portal1b.png)

## <a name="share-a-template-resource"></a>Uso compartido de un recurso de plantilla
Puede compartir con sus compañeros los recursos de **plantilla** . El proceso de uso compartido es similar a la [asignación de roles en recursos de Azure](../active-directory/role-based-access-control-configure.md). El propietario de la **plantilla** proporciona permisos a otros usuarios para que puedan interactuar con el recurso de plantilla. La persona o el grupo de personas con los que se comparte la **plantilla** pueden ver la plantilla de Resource Manager y las propiedades de la galería.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Control de acceso en los recursos de Microsoft.Gallery
| Rol | Permisos |
| --- | --- |
| Propietario |Tiene todo el control sobre el recurso de plantilla, por lo que puede compartirlo. |
| Lector |Tiene permiso de lectura y ejecución (implementación) en el recurso de plantilla. |
| Colaborador |Tiene permiso de edición y eliminación en el recurso de plantilla. El usuario no puede compartir la plantilla con otras personas. |

Seleccione **Compartir** haciendo clic con el botón derecho en el elemento de la lista o cuando esté viendo un elemento específico.

![Compartir plantilla](media/share-template-portal1a.png)

 Ahora, puede elegir un rol y un usuario o grupo para concederles acceso a una determinada **plantilla**. Los roles disponibles son Propietario, Lector y Colaborador.

![Compartir plantilla](media/share-template-portal2b.png)

![Compartir plantilla](media/share-template-portal3b.png)

Haga clic en **Seleccionar** y en **Aceptar**. Ahora, puede ver los usuarios o grupos que ha agregado al recurso.

![Compartir plantilla](media/share-template-portal4b.png)

> [!NOTE]
> Las plantillas solo se pueden compartir con usuarios y grupos del mismo inquilino de Azure Active Directory. Si comparte una plantilla con una dirección de correo electrónico que no se encuentra en el inquilino, se envía una invitación al usuario para que se una a este inquilino como invitado.
> 
> 

## <a name="next-steps"></a>pasos siguientes
* Para más información sobre la creación de plantillas de Resource Manager, consulte [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* Para conocer las funciones que puede usar en la plantilla de Resource Manager, consulte [Funciones de plantilla](../azure-resource-manager/resource-group-template-functions.md).


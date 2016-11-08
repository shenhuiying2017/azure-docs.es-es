---
title: Introducción a las plantillas privadas | Microsoft Docs
description: Agregue, administre y comparta plantillas privadas con el Portal de Azure, con la CLI de Azure o con PowerShell.
services: marketplace-customer
documentationcenter: ''
author: VybavaRamadoss
manager: asimm
editor: ''
tags: marketplace, azure-resource-manager
keywords: ''

ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar

---
# Introducción a las plantillas privadas del Portal de Azure
Las plantillas de [Azure Resource Manager](../resource-group-authoring-templates.md) son plantillas declarativas que se utilizan para definir la implementación. Puede definir los recursos que se van a implementar en una solución y especificar parámetros y variables que le permitan especificar valores de entrada para diferentes entornos. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación.

Puede usar la nueva funcionalidad **Plantillas** del [Portal de Azure](https://portal.azure.com) junto con el proveedor de recursos **Microsoft.Gallery** como una extensión de [Azure Marketplace](https://azure.microsoft.com/marketplace/) para que los usuarios puedan crear, administrar e implementar plantillas privadas procedentes de una biblioteca personal.

En este documento, se explica paso a paso cómo agregar, administrar y compartir una **plantilla** privada mediante el Portal de Azure.

## Guía
Las sugerencias siguientes le ayudarán a sacar el máximo provecho de las **plantillas** cuando trabaje en sus soluciones:

* Una **plantilla** en un recurso de encapsulamiento que contiene una plantilla de Resource Manager y otros metadatos. Su comportamiento es muy similar al de los elementos de Marketplace. La principal diferencia es que se trata de un elemento privado, mientras que los elementos de Marketplace son públicos.
* La biblioteca **Plantillas** resulta muy útil para los usuarios que necesitan personalizar sus implementaciones.
* Las **plantillas** son apropiadas para los usuarios que necesitan disponer de un repositorio sencillo dentro de Azure.
* Para empezar, utilice una plantilla de Resource Manager existente. Para buscar plantillas, utilice [github](https://github.com/Azure/azure-quickstart-templates) o [Exportar plantilla](../resource-manager-export-template.md) desde un grupo de recursos existente.
* Las **plantillas** están vinculadas al usuario que las publica. Cualquier persona que tenga acceso podrá ver el nombre del publicador.
* Las **plantillas** son recursos de Resource Manager y, una vez publicadas, su nombre no se puede modificar.

## Adición de un recurso de plantilla
Existen dos formas de crear un recurso de **plantilla** en el Portal de Azure.

### Método 1: Creación de un nuevo recurso de plantilla a partir de un grupo de recursos en funcionamiento
1. Acceda a un grupo de recursos existente del Portal de Azure. En **Configuración**, seleccione **Exportar plantilla**.
2. Cuando la plantilla de Resource Manager se haya exportado, utilice el botón **Guardar plantilla** para guardarla en el repositorio **Plantillas**. Para más detalles sobre la exportación de plantillas, haga clic [aquí](../resource-manager-export-template.md). <br /><br /> ![Exportación de grupos de recursos](media/rg-export-portal1.PNG) <br />
3. Seleccione el botón de comando **Guardar plantilla**. <br /><br />
4. Escriba la siguiente información:
   
   * Nombre: nombre del objeto de plantilla (NOTA: El nombre debe ajustarse a las especificaciones de Azure Resource Manager, por lo que se aplicarán todas las restricciones de nomenclatura, y no se puede cambiar una vez creado).
   * Descripción: resumen rápido de la plantilla.
     
     ![Guardar plantilla](media/save-template-portal1.PNG) <br />
5. Haga clic en **Guardar**.
   
   > [!NOTE]
   > Si la plantilla de Resource Manager exportada contiene errores, aparecerán notificaciones en la hoja Exportar plantilla; sin embargo, a pesar de los errores, podrá guardarla en Plantillas. No olvide comprobar y corregir los problemas antes de volver a implementar la plantilla de Resource Manager exportada.
   > 
   > 

### B. Método 2 : Agregar un nuevo recurso de plantilla desde Examinar
También puede agregar una nueva **plantilla** desde el principio utilizando el botón de comando +Agregar de **Examinar > Plantillas**. Tendrá que especificar un nombre, una descripción y el JSON de la plantilla de Resource Manager.

![Agregar plantilla](media/add-template-portal1.PNG) <br />

> [!NOTE]
> Microsoft.Gallery es un proveedor de recursos de Azure basado en un inquilino. El recurso de plantilla está vinculado al usuario que lo creó. No está asociado a ninguna suscripción específica. Al implementar una plantilla, es necesario elegir una suscripción.
> 
> 

## Consulta de los recursos de plantilla
Puede ver todas las **plantillas** disponibles en **Examinar > Plantillas**. Aquí encontrará las **plantillas** que ha creado, así como las plantillas que han compartido con usted y que tienen distintos niveles de permisos. Para más detalles, consulte más adelante la sección [Control de acceso](#access-control-for-a-tenant-resource-provider).

![Ver plantilla](media/view-template-portal1.PNG) <br />

Para ver los detalles de una **plantilla**, haga clic en un elemento de la lista.

![Ver plantilla](media/view-template-portal2c.png) <br />

## Edición de un recurso de plantilla
Para iniciar el flujo de edición de una **plantilla**, haga clic con el botón derecho en el elemento de la lista o seleccione el botón de comando Editar.

![Editar plantilla](media/edit-template-portal1a.PNG) <br />

Puede editar la descripción o el texto de la plantilla de Resource Manager. No obstante, el nombre no se puede modificar, ya que se trata de un nombre de recurso de Resource Manager. Cuando edite el JSON de la plantilla de Resource Manager, realizaremos las comprobaciones pertinentes para garantizar que se trata de un JSON válido. Elija **Aceptar** y **Guardar** para guardar la plantilla actualizada.

![Editar plantilla](media/edit-template-portal2a.PNG) <br />

Cuando haya guardado la **plantilla**, aparecerá una notificación de confirmación.

![Editar plantilla](media/edit-template-portal3b.png) <br />

## Implementación de un recurso de plantilla
Puede implementar cualquier **plantilla** en la que tenga permisos de **lectura**. El flujo de implementación abre la hoja de implementación de plantillas estándar de Azure. Rellene los valores de los parámetros de la plantilla de Resource Manager para continuar con la implementación.

![Implementar plantilla](media/deploy-template-portal1b.png) <br />

## Uso compartido de un recurso de plantilla
Puede compartir con sus compañeros los recursos de **plantilla**. El proceso de uso compartido es similar a la [asignación de roles en recursos de Azure](../active-directory/role-based-access-control-configure.md). El propietario de la **plantilla** proporciona permisos a otros usuarios para que puedan interactuar con el recurso de plantilla. La persona o el grupo de personas con los que se comparte la **plantilla** pueden ver la plantilla de Resource Manager y las propiedades de la galería.

### Control de acceso en los recursos de Microsoft.Gallery
| Rol | Permisos |
| --- | --- |
| Propietario |Tiene todo el control sobre el recurso de plantilla, por lo que puede compartirlo. |
| Lector |Tiene permiso de lectura y ejecución (implementación) en el recurso de plantilla. |
| Colaborador |Tiene permiso de edición y eliminación en el recurso de plantilla. El usuario no puede compartir la plantilla con otras personas. |

Seleccione **Compartir** haciendo clic con el botón derecho en el elemento de la lista o en la hoja de vista de un elemento específico. De este modo, se iniciará un proceso que le permitirá compartir el recurso de plantilla.

![Compartir plantilla](media/share-template-portal1a.png) <br />

 Ahora, puede elegir un rol y un usuario o grupo para concederles acceso a una determinada **plantilla**. Los roles disponibles son Propietario, Lector y Colaborador. Para más detalles, consulte la sección [Control de acceso](#access-control-for-a-tenant-resource-provider) que apareció anteriormente.

![Compartir plantilla](media/share-template-portal2b.png) <br />

![Compartir plantilla](media/share-template-portal3b.png) <br />

Haga clic en **Seleccionar** y en **Aceptar**. Ahora, puede ver los usuarios o grupos que ha agregado al recurso.

![Compartir plantilla](media/share-template-portal4b.png) <br />

> [!NOTE]
> Las plantillas solo se pueden compartir con usuarios y grupos del mismo inquilino de Azure Active Directory. Si comparte una plantilla con una dirección de correo electrónico que no se encuentra en el inquilino, se enviará una invitación al usuario para que se una a este inquilino como invitado.
> 
> 

## Pasos siguientes
* Para más información sobre la creación de plantillas de Resource Manager, consulte [Creación de plantillas de Azure Resource Manager](../resource-group-authoring-templates.md)
* Para conocer las funciones que puede usar en la plantilla de Resource Manager, consulte [Funciones de plantilla de Azure Resource Manager](../resource-group-template-functions.md).
* Para obtener instrucciones sobre cómo diseñar las plantillas, consulte [Prácticas recomendadas para diseñar plantillas del Administrador de recursos de Azure](../best-practices-resource-manager-design-templates.md).

<!---HONumber=AcomDC_0629_2016-->
---
title: "Guía de implementación de aplicaciones web de Azure con plantillas | Microsoft Docs"
description: "Recomendaciones de creación de plantillas de Azure Resource Manager para implementar aplicaciones web."
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: eff0b0e6258217fa8fbf7f15606f98d70fecd7c5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="guidance-on-deploying-web-apps-with-azure-resource-manager-templates"></a>Guía de implementación de aplicaciones web con plantillas de Azure Resource Manager

En este artículo se ofrecen recomendaciones de creación de plantillas de Azure Resource Manager para implementar soluciones de App Service. Estas recomendaciones pueden ayudarle a evitar problemas comunes.

## <a name="define-dependencies"></a>Definición de las dependencias

La definición de las dependencias de Web Apps requiere entender la interacción de los recursos dentro de una instancia de Web Apps. Si especifica las dependencias en un orden incorrecto, puede provocar errores de implementación o crear una condición de carrera que detenga la implementación.

> [!WARNING]
> Si incluye una extensión de sitio de MSDeploy en la plantilla, debe establecer los recursos de configuración de manera que dependan del recurso de MSDeploy. Los cambios de configuración provocan el reinicio asincrónico del sitio. Al hacer que los recursos de configuración dependan de MSDeploy, se garantiza que MSDeploy finaliza antes de que se reinicie el sitio. Sin estas dependencias, el sitio podría reiniciarse durante el proceso de implementación de MSDeploy. Para una plantilla de ejemplo, consulte la [plantilla de Wordpress con dependencia de Web Deploy](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

En la siguiente imagen se muestra el orden de dependencia de distintos recursos de App Service.

![Dependencias de la aplicación web](media/web-sites-rm-template-guidance/web-dependencies.png)

Los recursos se implementan en el orden siguiente:

**Nivel 1**
* Plan de servicio de aplicación
* Otros recursos relacionados, como las bases de datos o las cuentas de almacenamiento

**Nivel 2**
* Aplicación web: depende de plan de App Service
* Application Insights dirigido a la granja de servidores: depende del plan de App Service

**Nivel 3**
* Control de código fuente: depende de la aplicación web
* Extensión del sitio MSDeploy: depende de la aplicación web
* Application Insights dirigido a la granja de servidores: depende de la aplicación web

**Nivel 4**
* App Service Certificate: depende de control de código fuente o de MSDeploy (si alguno está presente); de lo contrario, depende de la aplicación web
* Configuración (cadenas de conexión, valores de configuración web, configuración de la aplicación): depende del control de código fuente o de MSDeploy (si alguno está presente); de lo contrario, depende de la aplicación web

**Nivel 5**
* Enlaces de nombre de host: dependen del certificado (si está presente); en caso contrario, de un recurso de nivel superior
* Extensiones de sitio: dependen de los valores de configuración (si están presentes); en caso contrario, de un recurso de nivel superior

Normalmente, la solución incluye solo algunos de estos recursos y niveles. Para los niveles que faltan, asigne los recursos inferiores al siguiente nivel superior.

En el ejemplo siguiente se muestra parte de una plantilla. El valor de configuración de la cadena de conexión depende de la extensión de MSDeploy. La extensión de MSDeploy depende de la aplicación web y la base de datos.

```json
{
    "name": "[parameters('name')]",
    "type": "Microsoft.Web/sites",
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'))]",
            "[concat('SuccessBricks.ClearDB/databases/', parameters('databaseName'))]"
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

## <a name="find-information-about-msdeploy-errors"></a>Búsqueda de información sobre los errores de MSDeploy

Si la plantilla de Resource Manager usa MSDeploy, los mensajes de error de implementación pueden ser difíciles de comprender. Para más información después de un error de implementación, pruebe los pasos siguientes:

1. Vaya a la [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) del sitio.
2. Vaya a la carpeta `D:\home\LogFiles\SiteExtensions\MSDeploy`.
3. Busque los archivos *appManagerStatus.xml* y *appManagerLog.xml*. El primer archivo registra el estado. El segundo, información sobre el error. Si el error no está claro, puede incluirlo al pedir ayuda en el foro.

## <a name="unique-web-app-name"></a>Nombre de aplicación web único

El nombre de la aplicación web debe ser globalmente único. Puede utilizar una convención de nomenclatura probablemente única o la [función uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring), que le ayudará a generar un nombre único.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>pasos siguientes

* Para un tutorial sobre la implementación de aplicaciones web con una plantilla, consulte [Aprovisionamiento e implementación predecibles de microservicios en Azure](app-service-deploy-complex-application-predictably.md).
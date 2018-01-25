---
title: 'CLI de Azure Service Fabric: sfctl is | Microsoft Docs'
description: Describe los comandos de sfctl is de la CLI Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: b611a447dd6669a09ca16c816de74acd7f3e8c7e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-is"></a>sfctl is
Consulte y envíe comandos al servicio de infraestructura.

## <a name="commands"></a>Comandos:

|Get-Help|DESCRIPCIÓN|
| --- | --- |
|    command| Invoca un comando administrativo en la instancia del servicio de infraestructura determinada.|
|    query  | Invoca una consulta de solo lectura en la instancia del servicio de infraestructura determinada.|


## <a name="sfctl-is-command"></a>sfctl is command
Invoca un comando administrativo en la instancia del servicio de infraestructura determinada.

Para los clústeres que tienen una o varias instancias del servicio de infraestructura configurado, esta API proporciona una manera de enviar comandos específicos de la infraestructura a una instancia determinada del servicio de infraestructura. Los comandos disponibles y sus formatos de respuesta correspondientes variarán en función de la infraestructura en la que se ejecute el clúster. Esta API es compatible con la plataforma Service Fabric; no está diseñada para utilizarse directamente desde el código. .

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --command [Obligatorio]| El texto del comando que se va a invocar. El contenido del comando es específico de la infraestructura.  Valor predeterminado: is command.|
| --service-id     | La identidad del servicio de infraestructura. Este es el nombre completo del servicio de infraestructura sin el esquema de URI 'fabric:'. Este parámetro solo es obligatorio para los clústeres que tienen en ejecución más de una instancia del servicio de infraestructura.|
| --timeout -t     | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug          | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h        | Muestra este mensaje de ayuda y sale.|
| --output -o      | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query          | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose        | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-is-query"></a>sfctl is query
Invoca una consulta de solo lectura en la instancia del servicio de infraestructura determinada.

Para los clústeres que tienen una o varias instancias del servicio de infraestructura configurado, esta API proporciona una manera de enviar consultas específicas de la infraestructura a una instancia determinada del servicio de infraestructura. Los comandos disponibles y sus formatos de respuesta correspondientes variarán en función de la infraestructura en la que se ejecute el clúster. Esta API es compatible con la plataforma Service Fabric; no está diseñada para utilizarse directamente desde el código.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --command [Obligatorio]| El texto del comando que se va a invocar. El contenido del comando es específico de la infraestructura.  Valor predeterminado: is query.|
| --service-id     | La identidad del servicio de infraestructura. Este es el nombre completo del servicio de infraestructura sin el esquema de URI 'fabric:'. Este parámetro solo es obligatorio para los clústeres que tienen en ejecución más de una instancia del servicio de infraestructura.|
| --timeout -t     | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug          | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h        | Muestra este mensaje de ayuda y sale.|
| --output -o      | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query          | Cadena de consulta de JMESPath. Para obtener más información, consulte http://jmespath.org/.|
| --verbose        | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
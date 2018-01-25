---
title: 'CLI de Azure Service Fabric: sfctl compose | Microsoft Docs'
description: Describe los comandos de sfctl compose de la CLI Service Fabric.
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
ms.openlocfilehash: 0e35ac70125bc640114a4492498b12ea96800d42
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-compose"></a>sfctl compose
Cree, elimine y administre implementaciones de Docker Compose.

## <a name="commands"></a>Comandos:

|Get-Help|DESCRIPCIÓN|
| --- | --- |
|    create| Implemente una aplicación de Service Fabric desde un archivo de Compose.|
|    list  | Obtiene la lista de implementaciones de Compose creadas en el clúster de Service Fabric.|
|   remove| Elimina una implementación existente de Compose de Service Fabric del clúster.|
|   status| Obtiene información sobre una implementación de Compose de Service Fabric.|
|upgrade       | Inicia la actualización de una implementación de Compose en el clúster de Service Fabric.|
|    upgrade-status| Obtiene detalles de la actualización más reciente realizada en esta implementación de Compose de Service Fabric.|


## <a name="sfctl-compose-create"></a>sfctl compose create
Crea una implementación de Compose de Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --file-path [Obligatorio]| Ruta de acceso al archivo de destino de Docker Compose.|
 |   --deployment-name [Obligatorio]| Nombre de la implementación.|
|    --encrypted-pass             | En lugar de solicitar una contraseña de registro de contenedor, utilice una frase de contraseña ya cifrada.|
|    --has-pass                   | Solicita una contraseña para el registro de contenedor.|
|    --timeout -t                 | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|
 |   --user                       | Nombre de usuario para conectarse al registro de contenedor.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                 | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h               | Muestra este mensaje de ayuda y sale.|
| --output -o             | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                 | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose               | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-compose-list"></a>sfctl compose list
Obtiene la lista de implementaciones de Compose creadas en el clúster de Service Fabric.

Obtiene el estado sobre las implementaciones de Compose que se crearon o están en proceso de creación en el clúster de Service Fabric. La respuesta incluye el nombre, el estado y otros detalles acerca de la implementación de Compose. Si las implementaciones no caben en una página, se devuelve una página de resultados, así como un token de continuación que puede usarse para obtener la página siguiente.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --continuation-token| El parámetro continuation token se utiliza para obtener el siguiente conjunto de resultados. Un continuation token con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta.      Cuando este valor se pasa a la siguiente llamada de la API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el continuation token no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --max-results    | El número máximo de resultados que se devuelven como parte de las consultas paginadas.      Este parámetro define el límite superior en el número de resultados devueltos.      Si no caben en el mensaje según las restricciones del tamaño máximo del mensaje definidas en la configuración, los resultados devueltos pueden ser menos que el número máximo de resultados especificado. Si este parámetro es cero o no se especifica, las consultas paginadas incluyen tantos resultados como quepan en el mensaje devuelto.|
| --timeout -t     | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug          | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h        | Muestra este mensaje de ayuda y sale.|
| --output -o      | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query          | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose        | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-compose-remove"></a>sfctl compose remove
Elimina una implementación existente de Compose de Service Fabric del clúster.

Elimina una implementación existente de Compose de Service Fabric. 

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --deployment-name [Obligatorio]| La identidad de la implementación. Este suele ser el nombre completo de la aplicación sin el esquema de URI 'fabric:'.|
| --timeout -t            | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                 | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h               | Muestra este mensaje de ayuda y sale.|
| --output -o             | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                 | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose               | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-compose-status"></a>sfctl compose status
Obtiene información sobre una implementación de Compose de Service Fabric.

Devuelve el estado de la implementación de Compose que se creó o está en proceso de creación en el clúster de Service Fabric y cuyo nombre coincide con el especificado como parámetro. La respuesta incluye el nombre, el estado y otros detalles acerca de la aplicación.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --deployment-name [Obligatorio]| La identidad de la implementación. |
| --timeout -t            | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                 | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h               | Muestra este mensaje de ayuda y sale.|
| --output -o             | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                 | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose               | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
Inicia la actualización de una implementación de Compose en el clúster de Service Fabric.

Valida los parámetros de actualización proporcionados e inicia la actualización de la implementación.

### <a name="arguments"></a>Argumentos
|Argumento|DESCRIPCIÓN|
| --- | --- |
|    --file-path [Obligatorio]| Ruta de acceso al archivo de destino de Docker Compose.|
|    --deployment-name [Obligatorio]| Nombre de la implementación.|
|    --default-svc-type-health-map| Diccionario JSON codificado que describe la directiva de estado que se usa para evaluar el estado de los servicios.|
|    --encrypted-pass             | En lugar de solicitar una contraseña de registro de contenedor, utilice una frase de contraseña ya cifrada.|
 |   --failure-action             | Los valores posibles son: 'Invalid', 'Rollback', 'Manual'.|
|    --force-restart              | Fuerza el reinicio.|
 |   --has-pass                   | Solicita una contraseña para el registro de contenedor.|
|    --health-check-retry         | Tiempo de espera de reintentos de comprobación de mantenimiento medido en milisegundos.|
|    --health-check-stable        | Duración estable de comprobación de mantenimiento medida en milisegundos.|
|    --health-check-wait          | Duración de espera de comprobación de mantenimiento medida en milisegundos.|
|    --replica-set-check          | Tiempo de espera de comprobación del conjunto de réplicas de actualización medido en segundos.|
|    --svc-type-health-map        | Lista JSON codificada lista de objetos que describen las directivas de estado que se usan para evaluar el estado de los distintos tipos de servicio.|
|    --timeout -t                 | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|
|    --unhealthy-app              | El porcentaje máximo permitido de aplicaciones en mal estado antes de informar de un error.        Por ejemplo, para permitir el 10 % de las aplicaciones en mal estado, este valor sería 10. El valor representa el porcentaje máximo tolerado de aplicaciones que pueden ser incorrectas antes de que el clúster se considere erróneo. Si se respeta el porcentaje, pero hay al menos una aplicación en mal estado, el estado se evalúa como Warning. El porcentaje se calcula dividiendo el número de aplicaciones en mal estado sobre el número total de instancias de aplicación en el clúster.|
|    --upgrade-domain-timeout     | Tiempo de espera del dominio de actualización medido en milisegundos.|
|    --upgrade-kind               | Valor predeterminado: Rolling.|
|    --upgrade-mode               | Los valores posibles son: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Valor predeterminado: UnmonitoredAuto.|
|    --upgrade-timeout            | Tiempo de espera de actualización medido en milisegundos.|
|    --user                       | Nombre de usuario para conectarse al registro de contenedor.|
|    --warning-as-error           | Las advertencias se tratan con el mismo nivel de gravedad que los errores.|

### <a name="global-arguments"></a>Argumentos globales
 |Argumento|DESCRIPCIÓN|
| --- | --- |
|   --debug                      | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
|    --help -h                    | Muestra este mensaje de ayuda y sale.|
 |   --output -o                  | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.
                                   Valor predeterminado: json.|
 |   --query                      | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
 |   --verbose                    | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
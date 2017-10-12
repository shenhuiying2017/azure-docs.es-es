---
title: "CLI de Azure Service Fabric: almacén de sfctl | Microsoft Docs"
description: "Se describen los comandos del almacén de sfctl de la CLI de Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2af6dff4ffcdf295731f2d61b5f9e35af40615e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-store"></a>almacén de sfctl
Realice operaciones básicas a nivel de archivo en el almacén de imágenes del clúster.

## <a name="commands"></a>Comandos:

|Comando|Descripción|
| --- | --- |
|    delete| Elimina el contenido existente en el almacén de imágenes.|
|    root-info| Obtiene la información de contenido en la raíz del almacén de imágenes.|
|    stat  | Obtiene la información de contenido del almacén de imágenes.|


## <a name="sfctl-store-delete"></a>sfctl store delete
Elimina el contenido existente en el almacén de imágenes.

Elimina el contenido existente del almacén de imágenes que se encuentra en la ruta de acceso relativa especificada. Este comando se puede usar para eliminar los paquetes de aplicación cargados una vez aprovisionados.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --content-path [Obligatorio]| Ruta de acceso relativa al archivo o la carpeta del almacén de imágenes desde su raíz.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-store-stat"></a>sfctl store stat
Obtiene la información de contenido del almacén de imágenes.

Devuelve la información sobre el contenido del almacén de imágenes que se encuentra en la ruta de acceso relativa contentPath especificada a la raíz de dicho almacén.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --content-path [Obligatorio]| Ruta de acceso relativa al archivo o la carpeta del almacén de imágenes desde su raíz.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
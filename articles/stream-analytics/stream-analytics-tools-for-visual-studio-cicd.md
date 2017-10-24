---
title: "Uso de las herramientas de Stream Analytics para Visual Studio para configurar la integración continua y el proceso de implementación | Microsoft Docs"
description: "Tutorial sobre el uso de las herramientas de Stream Analytics para Visual Studio para configurar la integración continua y el proceso de implementación"
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 947266dc94babab21556da26d8cc8c917cf81c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-the-continuous-integration-and-deployment-process"></a>Uso de las herramientas de Stream Analytics para Visual Studio para configurar la integración continua y el proceso de implementación
En este tutorial, aprenderá a usar las herramientas de Stream Analytics para Visual Studio para configurar la integración continua y el proceso de implementación.

La versión más reciente (2.3.0000.0 o superior) de las [herramientas de Stream Analytics para Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio) agrega la compatibilidad con **MSBuild**. 

También hay un paquete NuGet [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) recientemente publicado. Proporciona las herramientas de implementación, ejecución local y MSBuild que admite la integración continua y el proceso de implementación de los proyectos Stream Analytics para Visual Studio. 
> [!NOTE] 
El paquete NuGet solo se puede usar con la versión 2.3.0000.0 o superior de las herramientas de Stream Analytics para Visual Studio. Si tiene proyectos creados en versiones anteriores de las herramientas de Visual Studio, ábralo con la versión 2.3.0000.0 o superior y guárdelo. Así tendrá habilitadas las funcionalidades nuevas. 

[Sepa cómo usar las herramientas de Stream Analytics para Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="msbuild"></a>MSBuild
Al igual que la experiencia de MSBuild de Visual Studio estándar, para compilar un proyecto puede hacer clic con el botón derecho en el proyecto y luego elegir **Compilar**, o bien **MSBuild** en el paquete NuGet desde la línea de comandos.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Cuando un proyecto de Stream Analytics para Visual Studio se compila correctamente, genera los dos archivos siguientes de plantilla de Azure Resource Manager en la carpeta **bin/[Debug/Retail]/Deploy**: 

Archivo de plantilla de Azure Resource Manager.
*       [NombreDeProyecto].PlantillaDeTrabajo.json 

Archivo de parámetros de Azure Resource Manager.
*       [NombreDeProyecto].PlantillaDeTrabajo.parameters.json   

Los parámetros predeterminados en el archivo parameters.json provienen de la configuración del proyecto de Visual Studio. Si desea implementar en otro entorno, simplemente reemplace los parámetros según corresponda. 
> [!NOTE] 
En todas las credenciales, los valores predeterminados están establecidos en null. Es **IMPERATIVO** establecerlos antes de la implementación en la nube.
```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Obtenga más información sobre [cómo implementar con el archivo de plantilla de Azure Resource Manager y Azure PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy) y [cómo usar un objeto como parámetro en una plantilla de Azure Resource Manager](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Herramienta de línea de comandos

### <a name="build-the-project"></a>Compilación del proyecto
En el paquete NuGet, hay una herramienta de línea de comandos llamada **SA.exe**. Admite la compilación del proyecto, la prueba local en una máquina arbitraria, que puede usar en el proceso de integración continua y entrega continua. 

De manera predeterminada, los archivos de implementación se colocan en el directorio actual. Puede especificar la ruta de acceso de salida con el parámetro -OutputPath.

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Prueba local del script

Si el proyecto tiene archivos de entrada locales especificados en Visual Studio, puede ejecutar una prueba de script automatizada con el comando *localrun*. El resultado de salida se coloca en el directorio actual.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-job-definition-file-to-use-with-stream-analytics-power-shell"></a>Genere el archivo de definición del trabajo para usarlo con Stream Analytics PowerShell.

El comando *arm* toma la plantilla de trabajo y los archivos de parámetro de la plantilla de trabajo que se generaron como entrada en la compilación. Luego los combina en un archivo JSON de definición de trabajo que se puede usar con la API PowerShell de Stream Analytics.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```

```
Example
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



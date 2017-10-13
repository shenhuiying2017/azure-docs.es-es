---
title: "Compilación de línea de comandos de Azure | Microsoft Docs"
description: "Compilación de línea de comandos de Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2017
ms.author: kraigb
ms.openlocfilehash: 5fe910e2757dd5ec783538e23e7f52e2f5725b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="building-azure-projects-from-the-command-line"></a>Compilación de proyectos de Azure desde la línea de comandos
Con Microsoft Build Engine (MSBuild) puede compilar productos en entornos de laboratorio de compilación en los que Visual Studio no está instalado. MSBuild utiliza un formato XML para archivos de proyecto que es ampliable y totalmente compatible con Microsoft. En este formato de archivo, puede describir los elementos que deben crearse en una o más plataformas y configuraciones.

También puede ejecutar MSBuild en una línea de comandos, enfoque que se describe en este tema. Estableciendo propiedades en un símbolo del sistema, puede crear configuraciones específicas de un proyecto. De forma similar, también puede definir los destinos que creará MSBuild. Para obtener más información sobre los parámetros de línea de comandos y MSBuild, consulte [Referencia de la línea de comandos de MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>Parámetros de MSBuild
La manera más sencilla de crear un paquete consiste en ejecutar MSBuild con la opción `/t:Publish` . De manera predeterminada, este comando crea un directorio en relación con la carpeta raíz para el proyecto, por ejemplo, `<ProjectDirectory>\bin\Configuration\app.publish\`. Al compilar un proyecto de Azure, se generan dos archivos: el archivo del paquete y el archivo de configuración que lo acompaña:

* Archivo de paquete (`project.cspkg`)
* Archivo de configuración (`ServiceConfiguration.TargetProfile.cscfg`)

De manera predeterminada, cada proyecto de Azure incluye un archivo de configuración del servicio para las compilaciones locales (depuración) y otro para las compilaciones en la nube (ensayo o producción). Sin embargo, puede agregar o quitar archivos de configuración del servicio según sea necesario. Cuando compila un paquete dentro de Visual Studio, se le preguntará cuál archivo de configuración del servicio se debe incluir junto con el paquete. Cuando compila un paquete con MSBuild, el archivo de configuración del servicio local se incluye de manera predeterminada. Para incluir un archivo de configuración del servicio diferente, establezca la propiedad `TargetProfile` del comando de MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Si desea utilizar un directorio alternativo para el paquete y los archivos de configuración almacenados, establezca la ruta de acceso mediante la opción `/p:PublishDir=Directory\`, incluido el separador de barra diagonal inversa final.

## <a name="next-steps"></a>Pasos siguientes
Una vez compilado el paquete, puede implementarlo en Azure. Para ver un tutorial que demuestra cómo automatizar el proceso, consulte [Entrega continua para Cloud Services de Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).


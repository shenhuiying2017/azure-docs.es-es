---
title: Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics | Microsoft Docs
description: 'Aprenda a desarrollar ensamblados para usarse y reutilizarse en trabajos de Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics
Aprenda convertir código subyacente en ensamblados para usarse y reutilizarse en trabajos de Data Lake Analytics. 

Gracias a U-SQL, resulta sencillo agregar código personalizado en lenguajes .NET, como C#, Visual Basic.NET o F #. Además, puede implementar su propio entorno en tiempo de ejecución para admitir otros lenguajes.

La manera más fácil de usar el código personalizado es utilizar las funcionalidades de código subyacente de Data Lake Tools para Visual Studio. Para obtener más información, consulte [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Sin embargo, usar código subyacente tiene algunas desventajas:

- El código fuente se carga con cada envío de script.
- El código subyacente no se puede compartir con otros trabajos.

Para resolver estos inconvenientes, puede convertir el código subyacente en ensamblados y registrarlos en el catálogo de Data Lake Analytics.

## <a name="prerequisites"></a>Requisitos previos
* Visual Studio 2017, Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012 con Visual C ++ Instalado
* SDK de Microsoft Azure para .NET versión 2.5 o posterior.  Instálelo mediante el instalador de la plataforma web o el instalador de Visual Studio.
* Una cuenta de Análisis de Data Lake.  [Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md).
* Realice el tutorial [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
* Conéctese a Azure.
* Descargue los datos de origen y consulte [Introducción al lenguaje U-SQL de Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md). 

## <a name="develop-assemblies-for-u-sql"></a>Desarrollo de ensamblados para U-SQL

**Para crear y enviar un trabajo de U-SQL**

1. En el menú **Archivo**, haga clic en **Nuevo** y en **Proyecto**.
2. Expanda **Installed** (Instalado), **Templates** (Plantillas), **Azure Data Lake**, **U-SQL(ADLA)**, seleccione la plantilla **Class Library (For U-SQL Application)** (Biblioteca de clases [para aplicaciones U-SQL]) y, luego, haga clic en **OK** (Aceptar).
3. Escriba el código en Class1.cs.  A continuación, se muestra un ejemplo de código.

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. Haga clic en el menú **Build** (Compilar) y, luego, haga clic en **Build Solution** para (Compilar solución) para crear el archivo .dll.

## <a name="register-assemblies"></a>Registro de ensamblados

Consulte [Uso del catálogo de Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md).


## <a name="use-the-assemblies"></a>Uso de los ensamblados

Consulte [Uso de Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

## <a name="see-also"></a>Consulte también
* [Introducción a Análisis de Data Lake mediante PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
* [Uso de Data Lake Tools for Visual Studio para desarrollar aplicaciones de U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso del catálogo de Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
* [Uso de Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
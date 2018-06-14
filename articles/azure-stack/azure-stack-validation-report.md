---
title: Informe de validación para Azure Stack | Microsoft Docs
description: Use el informe de Azure Stack Readiness Checker para revisar los resultados de validación.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937557"
---
# <a name="azure-stack-validation-report"></a>Informe de validación de Azure Stack
La herramienta Azure Stack Readiness Checker ejecuta validaciones que admiten la implementación y el mantenimiento de un entorno de Azure Stack. La herramienta escribe los resultados de la validación en un archivo de informe .json. El informe muestra los datos resumidos y detallados sobre el estado de los requisitos previos de implementación de Azure Stack y sobre la rotación de secretos en implementaciones de Azure Stack existentes.  

 ## <a name="where-to-find-the-report"></a>Lugar donde se puede encontrar el informe
Cuando se ejecuta la herramienta, los resultados se registran en **AzsReadinessCheckerReport.json**. La herramienta crea también un registro llamado **AzsReadinessChecker.log**. La ubicación de estos archivos se muestra con los resultados de validación de PowerShell.

![ejecución de la validación](./media/azure-stack-validation-report/validation.png)

Ambos archivos conservan los resultados de comprobaciones de validación posterior que se ejecutan en el mismo equipo.  Por ejemplo, se puede ejecutar la herramienta para validar los certificados, volver a ejecutarla para validar la identidad de Azure y validarla y luego, una tercera vez para validar el registro. Los resultados de las tres validaciones están disponibles en el informe .json resultante.  

De forma predeterminada, los dos archivos se escriben en *C:\Users\<nombre de usuario>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Use el parámetro **-OutputPath** ***&lt;ruta de acceso&gt;***  al final de la línea de comandos de ejecución para especificar otra ubicación para el informe.   
- Use el parámetro **-CleanReport** al final del comando de ejecución para borrar la información de *AzsReadinessCheckerReport.json* sobre ejecuciones anteriores de la herramienta.

## <a name="view-the-report"></a>Visualización del informe
Para ver el informe en PowerShell, indique la ruta de acceso al informe como valor de **-ReportPath**. Este comando muestra el contenido del informe y también identifica las validaciones que aún no tienen resultados.

Por ejemplo, para ver el informe desde un símbolo del sistema de PowerShell que está abierto en la ubicación donde se encuentra el informe, ejecute: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

El resultado tiene un aspecto similar al de la siguiente imagen:

![vista del informe](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Visualización del resumen del informe
Para ver un resumen del informe, puede agregar el modificador **-Summary** al final de la línea de comandos de PowerShell. Por ejemplo:  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

El resumen muestra las validaciones que no tienen resultados e indica si las validaciones que están completas son correctas o no. El resultado tiene un aspecto similar al de la siguiente imagen:

![resumen del informe](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Vista de un informe filtrado
Para ver un informe filtrado por un solo tipo de validación, utilice el parámetro **-ReportSections** y especifique uno de los siguientes valores que se corresponda con el tipo de validación que quiere ver:
- Certificate
- AzureRegistration
- AzureIdentity
- Trabajos   
- Todo  

Por ejemplo, para ver solo el informe resumen de certificados, utilice la siguiente línea de comandos de PowerShell: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Otras referencias
[Referencia del cmdlet Start-AzsReadinessChecker](azure-stack-azsreadiness-cmdlet.md)

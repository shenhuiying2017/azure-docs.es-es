---
title: 'Microsoft Genomics: Guía de solución de problemas | Microsoft Docs'
titleSuffix: Azure
description: Más información sobre estrategias de solución de problemas
keywords: solución de problemas, error, depuración
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/13/2018
ms.openlocfilehash: 18761c02cc423affe7b1050700e560b1f0b0594d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-guide"></a>Guía de solución de problemas
Esta información general describe las estrategias para solucionar problemas comunes al usar el servicio Microsoft Genomics. Para ver las preguntas más frecuentes, consulte [Preguntas frecuentes](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>¿Cómo se puede comprobar el estado del trabajo?
Puede comprobar el estado del flujo de trabajo mediante una llamada a `msgen status` desde la línea de comandos, tal como se muestra. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Hay tres argumentos obligatorios:
* URL: el URI base de la API.
* KEY: la clave de acceso de la cuenta de Genomics. 
* ID: el identificador del flujo de trabajo.

Vaya a Azure Portal y abra la página de la cuenta de Genomics para buscar la URL. En el encabezado **Administración**, elija **Claves de acceso**. Allí encontrará la URL de la API y las claves de acceso.

Como alternativa, puede incluir la ruta de acceso al archivo de configuración en lugar de escribir directamente la URL y la clave. Tenga en cuenta que si incluye estos argumentos en la línea de comandos, así como el archivo de configuración, los argumentos de la línea de comandos tendrán prioridad. 

Después de llamar a `msgen status`, se mostrará un mensaje descriptivo que describe si el flujo de trabajo se realizó correctamente o que proporciona un motivo del error del trabajo. 


## <a name="get-more-information-about-my-workflow-status"></a>Más información sobre el estado del flujo de trabajo

Para obtener más información sobre por qué un trabajo podría no haberse realizado correctamente, puede explorar los archivos de registro generados durante el flujo de trabajo. En el contenedor de salida, debería ver una carpeta `[youroutputfilename].logs.zip`.  Al descomprimir esta carpeta, verá los siguientes elementos:

* outputFileList.txt: una lista de los archivos de salida generados durante el flujo de trabajo
* StandardError.txt: este archivo está en blanco.
* StandardOutput.txt: contiene el registro de nivel superior del flujo de trabajo. 
* Archivos de registro GATK: el resto de los archivos de la carpeta `logs`.

El archivo `standardoutput.txt` es un buen recurso para empezar a determinar por qué no se realizó correctamente el flujo de trabajo, ya que incluye más información de nivel inferior del flujo de trabajo. 

## <a name="common-issues-and-how-to-resolve-them"></a>Problemas comunes y cómo resolverlos
Esta sección resalta brevemente los problemas comunes y cómo resolverlos.

### <a name="fastq-files-are-unmatched"></a>Los archivos Fastq no coinciden
Los archivos Fastq deben diferenciarse únicamente por los /1 o /2 finales en el identificador de ejemplo. Si ha enviado accidentalmente los archivos FASTQ no coincidentes, verá los siguientes mensajes de error al llamar a `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Para resolver este problema, revise si los archivos FASTQ enviados al flujo de trabajo son realmente un conjunto coincidente. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Error al cargar el archivo .bam. El blob de salida ya existe y la opción de sobrescritura estaba establecida como False.
Si ve el siguiente mensaje de error `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, la carpeta de salida ya contiene un archivo de salida con el mismo nombre.  Elimine el archivo de salida existente o active la opción de sobrescritura en el archivo de configuración. A continuación, vuelva a enviar el flujo de trabajo.

### <a name="when-to-contact-microsoft-genomics-support"></a>Cuándo ponerse en contacto con el equipo de soporte técnico de Microsoft Genomics
Si ve los mensajes de error siguientes, se produjo un error interno. 

* `Error locating input files on worker machine`
* `Process management failure`

Trate de volver a enviar el flujo de trabajo. Si sigue obteniendo errores en el trabajo, o si tiene alguna otra pregunta, póngase en contacto con el equipo de soporte técnico de Microsoft Genomics desde Azure Portal.

![Póngase en contacto con el equipo de soporte técnico en Azure Portal](./media/troubleshooting-guide/genomics-contact-support.png "Póngase en contacto con el equipo de soporte técnico en Azure Portal")

## <a name="next-steps"></a>Pasos siguientes
En este artículo, aprendió a solucionar errores y problemas comunes con el servicio Microsoft Genomics. Para obtener más información y preguntas frecuentes, vea [Preguntas frecuentes](frequently-asked-questions-genomics.md). 
---
title: "Notas de la versión de Azure Machine Learning Workbench sprint 2 de diciembre de 2017"
description: "En este documento se detallan las actualizaciones de la versión de sprint 2 de Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: 6fede727a36b0f3243975bf23df2ef53baf1b15c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="sprint-2---december-2017"></a>Sprint 2: diciembre de 2017 

#### <a name="version-number-01171115263"></a>Número de la versión: 0.1.1711.15263

>Le mostramos cómo puede [buscar el número de versión](https://docs.microsoft.com/azure/machine-learning/preview/known-issues-and-troubleshooting-guide).

Esta es la tercera actualización de Azure Machine Learning Workbench. Esta actualización incluye mejoras en la aplicación de workbench, la interfaz de línea de comandos (CLI) y los servicios back-end. Le agradecemos enormemente sus comentarios, tanto positivos como negativos. Muchas de las actualizaciones siguientes son consecuencia directa de esos comentarios. 

## <a name="notable-new-features"></a>Nuevas características destacables
- [Compatibilidad con SQL Server y Azure SQL Database como un origen de datos](https://docs.microsoft.com/azure/machine-learning/preview/data-prep-appendix2-supported-data-sources#types) 
- [Aprendizaje profundo sobre Spark con compatibilidad con GPU mediante MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Todos los contenedores de AML son compatibles con dispositivos de Azure IoT Edge cuando se implementan (no se requieren pasos adicionales)](http://aka.ms/aml-iot-edge-blog)
- Lista de modelos disponibles y vistas de detalle disponibles en Azure Portal
- Acceso a los destinos de proceso mediante la autenticación basada en claves SSH además del acceso basado en nombre de usuario y contraseña. 
- Nuevo inspector de frecuencia de patrones en la experiencia de preparación de datos. 

## <a name="detailed-updates"></a>Actualizaciones detalladas
A continuación figura una lista de actualizaciones detalladas de cada área de componente de Azure Machine Learning en esta versión de sprint.

### <a name="installer"></a>Instalador
- El instalador puede actualizarse automáticamente de modo que las correcciones de errores y las nuevas características pueden admitirse sin que el usuario tenga que volver a instalarlas.

### <a name="workbench-authentication"></a>Autenticación de Workbench
- Varias correcciones para el sistema de autenticación. Háganos saber si sigue experimentando problemas de inicio de sesión.
- Cambios en la IU que facilitan la búsqueda de la configuración del administrador del proxy.

### <a name="workbench"></a>Workbench
- La vista de archivo de solo lectura ahora tiene fondo azul claro.
- El botón Editar se ha movido a la derecha para que sea más reconocible.
- Los formatos de archivo "dsource", "dprep" y "ipynb" ahora se pueden representar en formato de texto sin formato.
- El workbench ahora tiene una nueva experiencia de edición que guía a los usuarios hacia el uso de IDE externo para editar scripts y usar Workbench solo para editar los tipos de archivos que tienen una experiencia de edición enriquecida (por ejemplo, Notebook, orígenes de datos y paquetes de preparación de datos).
- La carga de la lista de áreas de trabajo y proyectos a los que el usuario tiene acceso es significativamente más rápida ahora.

### <a name="data-preparation"></a>Preparación de los datos 
- Un inspector de frecuencia de patrones para ver los patrones de cadena en una columna. También puede filtrar los datos mediante estos patrones. Esto muestra una vista similar al inspector de recuentos de valores. La diferencia es que la frecuencia de patrones muestra los recuentos de los patrones únicos de los datos, en lugar de los recuentos de datos únicos. También puede filtrar o no todas las filas que se ajusten a un determinado patrón.

![Imagen del inspector de frecuencia de patrones con en el número de producto](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- Mejoras de rendimiento al recomendar casos extremos para revisar en la transformación 'derivar columna por ejemplo'.

- [Compatibilidad con SQL Server y Azure SQL Database como un origen de datos](https://docs.microsoft.com/azure/machine-learning/preview/data-prep-appendix2-supported-data-sources#types) 

![Imagen de creación de un nuevo origen de datos de SQL Server](media/release-notes-sprint-2/sql-server-data-source.png)

- Vista "rápida" de recuentos de filas y columnas habilitada.

![Imagen rápida del número de columnas de fila](media/release-notes-sprint-2/row-col-count.png)

- La preparación de datos está habilitada en todos los contextos de proceso.
- Los orígenes de datos que utilizan una base de datos SQL Server están habilitados en todos los contextos de proceso
- Las columnas de cuadrícula de preparación de datos se pueden filtrar por tipo de datos.
- Problema corregido con la conversión de varias columnas a fecha.
- Se ha corregido el problema por el que el usuario podía seleccionar la columna de salida como un origen en Derivación de columnas por ejemplos si el usuario cambiaba el nombre de la columna de salida en el modo avanzado.

### <a name="job-execution"></a>Ejecución de trabajos
Ahora puede crear un destino de proceso de tipo de clúster o docker remoto y tener acceso a él mediante la autenticación basada en claves SSH siguiendo estos pasos:
- Adjunte un destino de proceso mediante el siguiente comando de la CLI.

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] La opción -k (o --use-azureml-ssh-key) del comando especifica generar y utilizar la clave SSH.

- Azure ML Workbench generará una clave pública y la transmitirá en la consola. Inicie sesión en el destino de proceso usando el mismo nombre de usuario y anexe el archivo ~/.ssh/authorized_keys con esta clave pública.

- Puede preparar este destino de proceso y usarlo para la ejecución y el área de trabajo de Azure ML Workbench usará esta clave para la autenticación.  

Para más información sobre la creación de destinos de proceso, consulte [Configuración del servicio Experimentación de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/experimentation-service-configuration).

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools para AI
- Compatibilidad agregada para [Visual Studio Tools para AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interfaz de línea de comandos (CLI)
- El comando `az ml datasource create` agregado permite la creación de un archivo de origen de datos desde la línea de comandos.

### <a name="model-management-and-operationalization"></a>Operacionalización y administración de modelos
- [Todos los contenedores de AML son compatibles con dispositivos de Azure IoT Edge cuando se operacionalizan (no se requieren pasos adicionales)](http://aka.ms/aml-iot-edge-blog) 
- Mejoras de mensajes de error en la CLI o16n
- Correcciones de errores en UX del portal de administración de modelos  
- Uso coherente de mayúsculas y minúsculas de letras para los atributos de administración de modelos en la página de detalles
- Tiempo de espera de llamadas de puntuación en tiempo real establecido en 60 segundos
- Lista de modelos disponibles y vistas de detalle disponibles en Azure Portal

![detalles del modelo en el portal](media/release-notes-sprint-2/model-list.jpg)

![introducción al modelo en el portal](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Aprendizaje profundo sobre Spark con [compatibilidad con GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Compatibilidad con plantillas de Resource Manager para la implementación sencilla de recursos
- Compatibilidad con el ecosistema de SparklyR
- [Integración de AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Proyectos de ejemplo
- Ejemplos de [Iris](https://github.com/Azure/MachineLearningSamples-Iris) y [MMLSpark](https://github.com/Azure/mmlspark) actualizado con la nueva versión del SDK de Azure ML

## <a name="breaking-changes"></a>CAMBIOS IMPORTANTES
- Se ha promovido el modificador `--type` de `az ml computetarget attach` a un subcomando. 

    - `az ml computetarget attach --type remotedocker` ahora es `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` ahora es `az ml computetarget attach cluster`

---
title: "Ejecute trabajos de Azure Batch de un extremo a otro sin escribir código (Versión preliminar) | Microsoft Docs"
description: Cree archivos de plantilla para la CLI de Azure CLI a fin de crear grupos, trabajos y tareas de Batch.
services: batch
author: mscurrell
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: markscu
ms.openlocfilehash: 6b91466da46d1f4ca9f25bf1718be783603efc58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Uso de plantillas y transferencia de archivos de la CLI de Azure Batch (versión preliminar)

Se puede usar la CLI de Azure para ejecutar trabajos de Batch sin escribir código.

Se pueden crear y usar los archivos de plantilla con la CLI de Azure que permiten que se creen grupos, trabajos y tareas de Batch. Los archivos de entrada de trabajo se pueden cargar fácilmente a la cuenta de almacenamiento asociada con la cuenta de Batch. También se pueden descargar los archivos de salida de trabajos.

## <a name="overview"></a>Información general

Una extensión de la CLI de Azure permite que los usuarios que no son desarrolladores usen Batch de un extremo a otro. Se puede crear un grupo, cargar datos de entrada, crear trabajos y tareas asociadas, y descargar los datos de salida resultantes. No es necesario ningún código, ya que se usa directamente la CLI o se integra en los scripts.

Las plantillas de Batch se basan en el [soporte de Batch existente en la CLI de Azure](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation) que permite que los archivos JSON especifiquen los valores de propiedad para la creación de grupos, trabajos, tareas y otros elementos. Con las plantillas de Batch, se agregan las siguientes funcionalidades sobre lo que es posible con los archivos JSON:

-   Se pueden definir los parámetros. Cuando se usa la plantilla, solo los valores de parámetro se especifican para crear el elemento, con otros valores de propiedad del elemento especificados en el cuerpo de la plantilla. Un usuario que entiende Batch y las aplicaciones que Batch ejecuta puede crear plantillas, especificar grupos, trabajos y valores de propiedad de la tarea. Un usuario menos familiarizado con Batch y/o las aplicaciones solo tiene que especificar los valores para los parámetros definidos.

-   Los generadores de tareas de trabajo crean una o varias tareas asociadas a un trabajo, evitando la necesidad de crear muchas definiciones de tareas y simplificando de manera significativa el envío de trabajos.


Los archivos de datos de entrada deben proporcionarse para trabajos y a menudo se generan archivos de datos de salida. De forma predeterminada, se asocia una cuenta de almacenamiento con cada cuenta de Batch y los archivos pueden transferirse fácilmente hacia y desde esta cuenta de almacenamiento mediante la CLI, sin necesidad de programación, ni credenciales de almacenamiento.

Por ejemplo, [ffmpeg](http://ffmpeg.org/) es una aplicación popular que procesa archivos de audio y video. La CLI de Azure Batch puede usarse para invocar ffmpeg para transcodificar los archivos de video de origen para diferentes resoluciones.

-   Se crea una plantilla de grupo. El usuario que crea la plantilla sabe cómo llamar a la aplicación ffmpeg y sus requisitos. Especifica el sistema operativo adecuado, el tamaño de la máquina virtual, cómo está instalado ffmpeg (por ejemplo, de un paquete de aplicación o mediante un administrador de paquetes) y otros valores de propiedad del grupo. Los parámetros se crean para que cuando se use la plantilla, solo deban especificarse el ID del grupo y el número de máquinas virtuales.

-   Se crea una plantilla de trabajo. El usuario que crea la plantilla sabe cómo debe invocarse ffmpeg para transcodificar el vídeo de origen a una resolución diferente y especifica la línea de comandos de la tarea. También sabe que hay una carpeta que contiene los archivos de vídeo de origen, con una tarea obligatoria por cada archivo de entrada.

-   Si un usuario final necesita transcodificar un conjunto de archivos de vídeo, primero crea un grupo con la plantilla de grupo, donde solo se especifica el identificador del grupo y el número de máquinas virtuales necesarias. A continuación, puede cargar los archivos de origen para transcodificarlos. Luego, se puede enviar un trabajo mediante la plantilla de trabajo, especificando solo el ID del grupo y la ubicación de los archivos de origen cargados. Se crea el trabajo de Batch, con una tarea por cada archivo de entrada que se esté generando. Por último, se pueden descargar los archivos de salida transcodificados.

## <a name="installation"></a>Instalación

Las funcionalidades de transferencia de plantillas y archivos requieren una extensión para instalarse.

Para obtener instrucciones sobre cómo instalar la CLI de Azure, vea [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Una vez que se ha instalado la CLI de Azure, se puede instalar la extensión de Batch mediante el siguiente comando de la CLI:

```azurecli
az component update --add batch-extensions --allow-third-party
```

Para más información sobre la extensión de Batch, vea [Microsoft Azure Batch CLI Extensions for Windows, Mac and Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux) (Extensiones de la CLI de Microsoft Azure Batch para Windows, Mac y Linux).

## <a name="templates"></a>Plantillas

La CLI de Azure Batch permite que se creen elementos como grupos, trabajos y tareas mediante la especificación de un archivo JSON que contenga los valores y nombres de la propiedad. Por ejemplo:

```azurecli
az batch pool create –-json-file AppPool.json
```

Las plantillas de Azure Batch son similares a las plantillas de Azure Resource Manager, en la funcionalidad y la sintaxis. Son archivos JSON que contienen los nombres y valores de la propiedad del elemento, pero agregan los siguientes conceptos principales:

-   **Parámetros**

    -   Permiten que se especifiquen valores de propiedad en una sección del cuerpo, donde solo es necesario proporcionar los valores de parámetro cuando se usa la plantilla. Por ejemplo, se podría colocar la definición completa de un grupo en el cuerpo y solo un parámetro definido para el ID del grupo. Por lo tanto, solo tiene que proporcionarse un string del ID del grupo para crearlo.
        
    -   El cuerpo de la plantilla lo puede crear alguien con conocimientos de Batch y las aplicaciones que Batch ejecuta. Solo se deben proporcionar los valores para los parámetros definidos por el autor cuando se use la plantilla. Un usuario sin conocimientos detallados de las aplicaciones y/o Batch, por tanto, puede usar las plantillas.

-   **Variables**

    -   Permiten que se especifiquen valores de parámetro complejos o simples en un solo lugar y se usen en uno o más lugares en el cuerpo de la plantilla. Las variables pueden simplificar y reducir el tamaño de la plantilla, así como también facilitar el mantenimiento al tener una ubicación para cambiar las propiedades cuyo valor puede cambiar.

-   **Construcciones de nivel superiores**

    -   Algunas construcciones de niveles superiores están disponibles en la plantilla que todavía no están disponibles en las API de Batch. Por ejemplo, un generador de tareas se puede definir en una plantilla de trabajo que crea varias tareas para el trabajo mediante una definición de tarea común. Estas construcciones evitan la necesidad de programar para crear de forma dinámica varios archivos JSON, como un archivo por cada tarea, así como crear archivos de script para instalar aplicaciones a través de un administrador de paquetes, por ejemplo.

    -   En algún momento, si corresponde, estas construcciones pueden agregarse al servicio de Batch y estar disponibles en las API, las interfaces de usuario y otros elementos de Batch.

### <a name="pool-templates"></a>Plantillas de grupo

Además de las funcionalidades de plantillas estándares de parámetros y variables, la plantilla de grupo admite las siguientes construcciones de niveles superiores:

-   **Referencias de paquetes**

    -   Opcionalmente, permite que el software se copie en nodos de grupo mediante el uso de administradores de paquetes. Se especifican el administrador y el identificador de paquete. Al poder declararse uno o varios paquetes, se evita la necesidad de crear un script que obtenga los paquetes necesarios, instalarlo y ejecutarlo en cada nodo de grupo.

A continuación, se muestra un ejemplo de una plantilla que crea un grupo de máquinas virtuales de Linux con ffmpeg instalada y solo requiere que se proporcionen el string del ID de grupo y el número de máquinas virtuales para usarse:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool id "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Si el archivo de plantilla se denominara _pool-ffmpeg.json_, entonces la plantilla se invocaría de la siguiente manera:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Plantillas de trabajo

Además de las funcionalidades de plantillas estándares de parámetros y variables, la plantilla de trabajo admite las siguientes construcciones de niveles superiores:

-   **Fábrica de tareas**

    -   Crea varias tareas para un trabajo a partir de una definición de tarea. Se admiten tres tipos de fábricas de tareas: barrido paramétrico, tarea por archivo y colección de tareas.

El siguiente es un ejemplo de una plantilla que crea un trabajo que utiliza ffmpeg para transcodificar archivos de video MP4 a una de las dos resoluciones más bajas, con una tarea creada por cada archivo de video de origen:

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Si el archivo de plantilla se denominara _job-ffmpeg.json_, entonces la plantilla se invocaría de la siguiente manera:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Grupos de archivos y transferencia de archivos

La mayoría de los trabajos y tareas requieren archivos de entrada y generan archivos de salida. Tanto los archivos de entrada como los de salida normalmente tienen que transferirse, desde el cliente al nodo, o bien desde el nodo al cliente. La extensión de la CLI de Azure Batch abstrae la transferencia de archivos y utiliza la cuenta de almacenamiento que se crea de forma predeterminada para cada cuenta de Batch.

Un grupo de archivos equivale a un contenedor que se crea en la cuenta de Azure Storage. Dicho grupo puede tener subcarpetas.

La extensión de la CLI de Batch proporciona comandos para la carga de archivos desde el cliente a un grupo de archivos especificado y la descarga de archivos desde el grupo de archivos especificado a un cliente.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Las plantillas de grupo y de trabajo permiten que se especifiquen los archivos almacenados en grupos de archivos para la copia en nodos de grupo o para su extracción de los nodos de grupo a un grupo de archivos. Por ejemplo, en la plantilla de trabajo que se especificó anteriormente, el archivo de grupo "ffmpeg-input" se especifica para la fábrica de tareas como la ubicación de los archivos de video de origen copiados en el nodo para su transcodificación. El grupo de archivos "ffmpeg-output" se usa como la ubicación donde se copian los archivos de salida transcodificados desde el nodo que ejecuta cada tarea.

## <a name="summary"></a>Resumen

Actualmente, solo se ha agregado la compatibilidad de transferencia de plantillas y archivos a la CLI de Azure. El objetivo es expandir la audiencia que pueda usar Batch a los usuarios que no necesitan desarrollar código usando las API de Batch, como los investigadores, los usuarios de TI y muchos más. Sin programación, los usuarios con conocimientos de Azure, Batch y las aplicaciones que ejecuta Batch, pueden crear plantillas para la creación de grupos y trabajos. Con los parámetros de plantilla, los usuarios sin un conocimiento detallado de Batch ni de las aplicaciones pueden usar las plantillas.

Pruebe la extensión Batch para la CLI de Azure y proporcione sus comentarios y sugerencias, ya sea en los comentarios de este artículo o a través del [foro de Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Pasos siguientes

- Consulte el blog de plantillas de Batch: [Running Azure Batch jobs using the Azure CLI – no code required](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/) (Ejecución de trabajos de Azure Batch mediante la CLI de Azure: sin necesidad de código).
- La documentación detallada sobre la instalación y el uso, ejemplos y el código fuente está disponible en el [repositorio de GitHub de Azure](https://github.com/Azure/azure-batch-cli-extensions).

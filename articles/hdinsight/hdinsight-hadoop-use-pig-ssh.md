---
title: "Uso de Pig en Hadoop con SSH en un clúster de HDInsight (Azure) | Microsoft Docs"
description: "Aprenda a conectarse a un clúster de Hadoop basado en Linux con SSH y use luego el comando Pig para ejecutar instrucciones de Pig Latin de forma interactiva o como un trabajo por lotes."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e4c893ef4bfa573dd9fbc9c9b0ae296720769842
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Ejecución de trabajos de Pig en un clúster basado en Linux con el comando Pig (SSH)

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Aprenda a ejecutar trabajos de Pig de forma interactiva desde una conexión SSH a su clúster de HDInsight. El lenguaje de programación de Pig Latin le permite describir las transformaciones que se aplican a los datos de entrada para generar el resultado deseado.

> [!IMPORTANT]
> Para realizar los pasos que se describen en este documento se requiere un clúster de HDInsight basado en Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Conexión con SSH

Use SSH para conectarse a su clúster de HDInsight. En el siguiente ejemplo, se conecta con un clúster denominado **myhdinsight** como la cuenta denominada **sshuser**:

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net

**Si proporcionó una clave de certificado para la autenticación de SSH** , al crear el clúster de HDInsight, es posible que tenga que especificar la ubicación de la clave privada en el sistema cliente.

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si proporcionó una contraseña para la autenticación de SSH**, al crear el clúster de HDInsight, indique la contraseña cuando se le solicite.

Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Uso del comando Pig

1. Una vez conectado, inicie la interfaz de la línea de comandos (CLI) de Pig mediante el comando siguiente:

        pig

    Después de un momento, debiera ver un símbolo de sistema de `grunt>` .

2. Introduzca la siguiente instrucción:

        LOGS = LOAD '/example/data/sample.log';

    Este comando carga el contenido del archivo sample.log en LOGS. Puede ver el contenido del archivo mediante la siguiente instrucción:

        DUMP LOGS;

3. A continuación, transforme los datos aplicando una expresión regular para extraer solo el nivel de registro en cada registro mediante la instrucción siguiente:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Puede usar **DUMP** para ver los datos después de la transformación. En este caso, utilice `DUMP LEVELS;`.

4. Continúe aplicando transformaciones mediante las instrucciones de la tabla siguiente:

    | Instrucción de Pig Latin | Qué hace la instrucción |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Quita las filas que contienen un valor nulo para el nivel de registro y almacena los resultados en `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Agrupa las filas por nivel de registro y almacena los resultados en `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Crea un conjunto de datos que contiene cada valor de registro único y cuántas veces se produce. El conjunto de datos se almacena en `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Ordena los niveles de registro por número (descendente) y los almacena en `RESULT`. |

    > [!TIP]
    > Utilice `DUMP` para ver el resultado de la transformación después de cada paso.

5. También puede guardar los resultados de una transformación mediante la instrucción `STORE` . Por ejemplo, la siguiente instrucción guarda el valor de `RESULT` en el directorio `/example/data/pigout` en el almacenamiento predeterminado para el clúster:

        STORE RESULT into '/example/data/pigout';

   > [!NOTE]
   > Los datos se almacenan en el directorio especificado en los archivos denominados `part-nnnnn`. Si el directorio ya existe, recibe un error.

6. Para salir del aviso de grunt, escriba la siguiente instrucción:

        QUIT;

### <a name="pig-latin-batch-files"></a>Archivos por lotes de Pig Latin

También puede usar el comando de Pig para ejecutar Pig Latin contenido en un archivo.

1. Después de salir del símbolo de sistema de Grunt, use el comando siguiente para canalizar STDIN en un archivo denominado `pigbatch.pig`. Este archivo se crea en el directorio particular para la cuenta de usuario de SSH.

        cat > ~/pigbatch.pig

2. Escriba o pegue las siguientes líneas y, a continuación, utilice Ctrl+D cuando finalice.

        LOGS = LOAD '/example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

3. Utilice el comando siguiente para ejecutar el archivo `pigbatch.pig` mediante el comando de Pig.

        pig ~/pigbatch.pig

    Una vez que finalice el trabajo por lotes, ve el siguiente resultado:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre el uso de Pig en HDInsight, consulte los documentos siguientes:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener más información sobre otras maneras de trabajar con Hadoop en HDInsight, consulte los documentos siguientes:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)


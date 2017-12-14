---
title: Funciones definidas por el usuario (UDF) de Python con Apache Hive y Pig - Azure HDInsight | Microsoft Docs
description: "Vea cómo usar funciones definidas por el usuario (UDF) de Python desde Hive y Pig en HDInsight, la pila de tecnología de Hadoop en Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/05/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 002072c8eac37ffb1548b44627ec08e941c96a1d
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Uso de funciones definidas por el usuario (UDF) de Python con Hive y Pig en HDInsight

Aprenda a usar funciones definidas por el usuario (UDF) de Python con Apache Hive y Pig en Hadoop en Azure HDInsight.

## <a name="python"></a>Python en HDInsight

Python 2.7 se instala de forma predeterminada en HDInsight 3.0 y posteriores. Apache Hive puede usarse con esta versión de Python para el procesamiento de streaming. El procesamiento de streaming utiliza STDOUT y STDIN para pasar datos entre Hive y UDF.

HDInsight incluye también Jython, que es una implementación de Python escrita en Java. Jython se ejecuta directamente en Máquina virtual Java y no utiliza streaming. Jython es el intérprete de Python recomendado cuando se usa Python con Pig.

> [!WARNING]
> En los pasos de este documento se realizan las hipótesis siguientes: 
>
> * Se crean los scripts de Python en el entorno de desarrollo local.
> * Se cargan los scripts en HDInsight con el comando `scp` desde una sesión de Bash local o el script de PowerShell proporcionado.
>
> Si desea utilizar la versión preliminar de [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) para trabajar con HDInsight, debe:
>
> * Crear los scripts dentro del entorno de Cloud Shell.
> * Usar `scp` para cargar los archivos de Cloud Shell a HDInsight.
> * Usar `ssh` de Cloud Shell para conectarse a HDInsight y ejecutar los ejemplos.

## <a name="hivepython"></a>UDF de Hive

Python se puede usar como UDF desde Hive a través de la instrucción `TRANSFORM` de HiveQL. Por ejemplo, el siguiente archivo HiveQL invoca el archivo `hiveudf.py` almacenado en la cuenta de Azure Storage predeterminada para el clúster.

**HDInsight basado en Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight basado en Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> En los clústeres de HDInsight basados en Windows, la cláusula `USING` debe especificar la ruta completa a python.exe.

A continuación se muestra lo que hace este ejemplo:

1. La instrucción `add file` al comienzo del archivo agrega el archivo `hiveudf.py` a la memoria caché distribuida, de modo que todos los nodos del clúster puedan acceder a él.
2. La instrucción `SELECT TRANSFORM ... USING` selecciona datos desde `hivesampletable`. También pasa los valores clientid, devicemake y devicemodel al script `hiveudf.py`.
3. La cláusula `AS` describe los campos devueltos por `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Creación del archivo hiveudf.py


En el entorno de desarrollo, cree un archivo de texto denominado `hiveudf.py`. Use el siguiente código como contenido del archivo:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Este script realiza las acciones siguientes:

1. Leer una línea de datos de STDIN.
2. El carácter de nueva línea final se quita mediante `string.strip(line, "\n ")`.
3. Al realizar el procesamiento por secuencias, una sola línea contiene todos los valores con un carácter de tabulación entre cada uno. Por tanto se puede usar `string.split(line, "\t")` para dividir la entrada en cada tabulación y que solo se devuelvan los campos.
4. Cuando el procesamiento haya finalizado, la salida se debe escribir en STDOUT como una sola línea, con una tabulación entre cada campo. Por ejemplo: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. El bucle `while` se repite hasta que no se lee ningún `line`.

La salida del script es una concatenación de los valores de entrada de `devicemake` y `devicemodel`, y un hash del valor concatenado.

Consulte [Ejecución de los ejemplos](#running) para obtener información sobre cómo ejecutar este ejemplo en su clúster de HDInsight.

## <a name="pigpython"></a>UDF de Pig

Un script de Python se puede usar como UDF desde Pig a través de la instrucción `GENERATE`. Puede ejecutar el script mediante Jython o C Python.

* Jython se ejecuta en JVM y se puede llamar de forma nativa desde Pig.
* C Python es un proceso externo, por lo que los datos de Pig en JVM se envían al script que se ejecuta en un proceso de Python. La salida del script de Python se devuelve a Pig.

Para especificar el intérprete de Python, use `register` al hacer referencia al script de Python. Los ejemplos siguientes registran scripts con Pig como `myfuncs`:

* **Para usar Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Para usar C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Al usar Jython, la ruta de acceso al archivo pig_jython puede ser una ruta de acceso local o una ruta de acceso WASB://. Sin embargo, cuando se usa C Python, se debe hacer referencia a un archivo local en el sistema de archivos local del nodo que se usa para enviar el trabajo de Pig.

Pasado el registro, el Pig Latin en este ejemplo es el mismo para ambos:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

A continuación se muestra lo que hace este ejemplo:

1. La primera línea carga el archivo de datos de ejemplo `sample.log` en `LOGS`. También define cada registro como `chararray`.
2. La siguiente línea filtra los valores nulos y almacena el resultado de la operación en `LOG`.
3. A continuación, recorre en iteración los registros de `LOG` y usa `GENERATE` para invocar el método `create_structure` en el script de Python/Jython cargado como `myfuncs`. `LINE` se usa para pasar el registro actual a la función.
4. Por último, los resultados se vuelcan en STDOUT con el comando `DUMP`. Este comando muestra los resultados una vez finalizada la operación.

### <a name="create-the-pigudfpy-file"></a>Creación del archivo pigudf.py

En el entorno de desarrollo, cree un archivo de texto denominado `pigudf.py`. Use el siguiente código como contenido del archivo:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

En el ejemplo de Pig Latin, la entrada `LINE` se define como chararray porque no había un esquema coherente para la entrada. El script de Python transforma los datos en un esquema coherente para la salida.

1. La instrucción `@outputSchema` define el formato de los datos que se devuelven a Pig. En este caso es un **contenedor de datos**, que es un tipo de datos de Pig. El contenedor contiene los siguientes campos, los cuales son todos chararray (cadenas):

   * date: la fecha en la que se creó la entrada del registro
   * time: la hora a la que se creó la entrada del registro
   * classname: el nombre de la clase para la que se creó la entrada
   * level: el nivel de registro
   * detail: los detalles de la entrada del registro

2. A continuación, `def create_structure(input)` define la función a la que Pig pasa elementos de línea.

3. En los datos de ejemplo, `sample.log`, se ajusta principalmente al esquema de date, time, classname, level y detail. Sin embargo, contiene unas pocas líneas que comienzan por `*java.lang.Exception*`. Estas líneas deben modificarse para que coincidan con el esquema. La instrucción `if` las busca y luego modifica los datos de entrada para mover la cadena `*java.lang.Exception*` al final, de forma que pone los datos en línea con el esquema de salida esperado.

4. A continuación, se usa el comando `split` para dividir los datos en los primeros cuatro caracteres de espacio. La salida se asigna a `date`, `time`, `classname`, `level` y `detail`.

5. Finalmente, los valores se devuelven a Pig.

Entonces tendremos un esquema coherente tal y como se define en la instrucción `@outputSchema`.

## <a name="running"></a>Carga y ejecución de los ejemplos

> [!IMPORTANT]
> Los pasos de **SSH** solo funcionan con un clúster de HDInsight basado en Linux. Los pasos de **PowerShell** funcionan con un clúster de HDInsight basado tanto en Linux como en Windows pero es necesario tener un cliente Windows.

### <a name="ssh"></a>SSH

Para obtener más información sobre cómo usar SSH, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Use `scp` para copiar los archivos en el clúster de HDInsight. Por ejemplo, el siguiente comando copia los archivos a un clúster denominado **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Use SSH para conectarse al clúster.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Para más información, vea el documento [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. En la sesión de SSH, agregue los archivos de python cargados anteriormente en el almacenamiento de WASB para el clúster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Después de cargar los archivos, siga los pasos siguientes para ejecutar los trabajos de Hive y Pig.

#### <a name="use-the-hive-udf"></a>Uso del UDF de Hive

1. Para conectarse a Hive, use el comando siguiente:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Este comando inicia al cliente de Beeline.

2. En el símbolo del sistema `0: jdbc:hive2://headnodehost:10001/>`, escriba la siguiente consulta:

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Después de escribir la última línea, debe iniciarse el trabajo. Cuando el trabajo se completa, devuelve una salida similar al siguiente ejemplo:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Para salir de Beeline, use el comando siguiente:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Uso del UDF de Pig

1. Para conectarse a Pig, use el comando siguiente:

    ```bash
    pig
    ```

2. En el símbolo del sistema `grunt>`, escriba las siguientes instrucciones:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Después de escribir la siguiente línea, debe iniciarse el trabajo. Cuando el trabajo se completa, devuelve una salida similar a los datos siguientes:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Use `quit` para salir del shell de Grunt y use lo siguiente para editar el archivo pigudf.py en el sistema de archivos local:

    ```bash
    nano pigudf.py
    ```

5. Una vez en el editor, quite la siguiente línea de comentario eliminando el carácter `#` del principio de la línea:

    ```bash
    #from pig_util import outputSchema
    ```

    Esta línea modifica el script de Python para trabajar con Python C, en lugar de con Jython. Cuando se haya realizado el cambio, use **Ctrl + X** para salir del editor. Seleccione **Y** y, a continuación, **Intro** para guardar los cambios.

6. Use el comando `pig` para iniciar de nuevo el shell. Cuando esté en el símbolo del sistema `grunt>` , use lo siguiente para ejecutar el script de Python con el intérprete de C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Una vez completado este trabajo, verá la misma salida que cuando anteriormente ejecutó el script mediante Jython.

### <a name="powershell-upload-the-files"></a>PowerShell: carga de los archivos

Puede usar PowerShell para cargar los archivos en el servidor de HDInsight. Use el siguiente script de para cargar los archivos de Python:

> [!IMPORTANT] 
> En los pasos de esta sección se usa Azure PowerShell. Para más información sobre cómo usar Azure PowerShell, consulte [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalación y configuración de Azure PowerShell).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Cambiar el valor `C:\path\to` a la ruta de acceso a los archivos del entorno de desarrollo.

Este script recupera información del clúster de HDInsight, luego extrae la cuenta y la clave de la cuenta de almacenamiento predeterminada y seguidamente carga los archivos en la raíz del contenedor.

> [!NOTE]
> Para más información sobre la carga de archivos, consulte el documento [Carga de datos para trabajos de Hadoop en HDInsight](../hdinsight-upload-data.md).

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: uso del UDF de Hive

PowerShell también puede utilizarse para ejecutar consultas de Hive de forma remota. Use el siguiente script de PowerShell para ejecutar una consulta de Hive que use el script **hiveudf.py**:

> [!IMPORTANT]
> Antes de ejecutarse, el script solicita la información de la cuenta de administrador/HTTPs para el clúster de HDInsight.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

La salida del trabajo de **Hive** debe parecerse al siguiente ejemplo:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell también puede utilizarse para ejecutar trabajos de Pig Latin. Para ejecutar un trabajo de Pig Latin que utilice el script **pigudf.py**, use el siguiente script de PowerShell:

> [!NOTE]
> Al enviar de forma remota un trabajo mediante PowerShell, no es posible usar Python C como intérprete.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

La salida del trabajo de **Pig** debe parecerse a los datos siguientes:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Solución de problemas

### <a name="errors-when-running-jobs"></a>Errores en la ejecución de trabajos

Al ejecutar el trabajo de Hive, es posible que se produzca un error similar al texto siguiente:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Este problema puede deberse a los finales de línea del archivo de Python. De forma predeterminada, muchos editores de Windows usan CRLF como final de línea, pero las aplicaciones Linux normalmente esperan caracteres LF.

Puede usar las siguientes instrucciones de PowerShell para quitar los caracteres CR antes de cargar el archivo en HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts de PowerShell

Ambos scripts de ejemplo de PowerShell usados para ejecutar los ejemplos contienen una línea comentada que muestra una salida con error para el trabajo. Si no ve la salida esperada del trabajo, quite los comentarios de la siguiente línea y observe si la información de error indica un problema.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

La información de error (STDERR) y el resultado del trabajo (STDOUT) también se registran en el almacenamiento para HDInsight.

| Para este trabajo... | Examine estos archivos en el contenedor de blobs. |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Pasos siguientes

Si necesita cargar módulos de Python que no se proporcionan de forma predeterminada, consulte [How to deploy a module to Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (Implementación de un módulo en HDInsight de Azure).

Para conocer otras formas de usar Pig y Hive, y para obtener información sobre el uso de MapReduce, consulte los siguientes documentos:

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

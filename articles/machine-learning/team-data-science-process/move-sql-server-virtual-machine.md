---
title: "Mover datos a un servidor SQL Server en una máquina virtual de Azure| Microsoft Docs"
description: "Mover datos desde archivos planos o desde un servidor SQL Server local a un servidor SQL Server en una máquina virtual de Azure"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: bd9289ea528c3a80f63380daf52161d2477a6771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover datos a un servidor SQL Server en una máquina virtual de Azure
En este tema se describen las opciones para mover datos desde archivos planos (formatos CSV o TSV) o desde un servidor SQL Server local hasta un servidor SQL Server en una máquina virtual de Azure. Estas tareas para mover datos a la nube forman parte del proceso de ciencia de datos en equipos.

Para ver un tema que describa las opciones para mover datos a una base de datos SQL de Azure para Aprendizaje automático, vea [Mover datos a una base de datos SQL de Azure para Aprendizaje automático de Azure](move-sql-azure.md).

El **menú** siguiente redirige a temas en los que se describe cómo introducir datos en otros entornos de destino en que se pueden almacenar y procesar datos durante el proceso de ciencia de datos en equipos (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

En la tabla siguiente se resumen las opciones para mover datos a un servidor SQL Server en una máquina virtual de Azure.

| <b>ORIGEN</b> | <b>DESTINO: servidor SQL Server en una máquina virtual de Azure</b> |
| --- | --- |
| <b>Archivo plano</b> |1. <a href="#insert-tables-bcp">Utilidad de copia masiva (BCP) de la línea de comandos</a><br> 2. <a href="#insert-tables-bulkquery">Consulta SQL de inserción masiva</a><br> 3. <a href="#sql-builtin-utilities">Utilidades integradas gráficas de SQL Server</a> |
| <b>SQL Server local</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportación a un archivo plano </a><br> 3. <a href="#sql-migration">SQL Database Migration Wizard </a> <br> 4. <a href="#sql-backup">Copia de seguridad y restauración de una base de datos </a><br> |

Tenga en cuenta que en este documento se da por supuesto que los comandos SQL se ejecutan desde SQL Server Management Studio o el Explorador de bases de datos de Visual Studio.

> [!TIP]
> Como alternativa, puede usar [factoría de datos de Azure](https://azure.microsoft.com/services/data-factory/) para crear y programar una canalización de datos se moverá a una máquina virtual de SQL Server en Azure. Para obtener más información, consulte [Copia de datos con Factoría de datos de Azure (actividad de copia)](../../data-factory/v1/data-factory-data-movement-activities.md)
>
>

## <a name="prereqs"></a>Requisitos previos
En este tutorial se asume que dispone de:

* Una **suscripción de Azure**. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**. En este tutorial, usará una cuenta de almacenamiento de Azure para almacenar los datos. Si no dispone de una cuenta de almacenamiento de Azure, vea el artículo [Creación de una cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) . Tras crear la cuenta de almacenamiento, tendrá que obtener la clave de cuenta que se usa para tener acceso al almacenamiento. Consulte [Administración de las claves de acceso de almacenamiento](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* **Servidor SQL Server aprovisionado en una máquina virtual de Azure**. Para obtener instrucciones, vea [Configuración de una máquina virtual de Azure SQL Server como servidor del Notebook de IPython para realizar análisis avanzados](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* **Azure PowerShell** instalado y configurado de forma local. Para obtener instrucciones, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Mover datos desde un origen de archivo plano a un servidor SQL Server en una máquina virtual de Azure
Si los datos se encuentran en un archivo plano (organizado en un formato de fila y columna), se pueden mover a la VM de SQL Server en Azure mediante los métodos siguientes:

1. [Utilidad de copia masiva (BCP) de la línea de comandos](#insert-tables-bcp)
2. [Consulta SQL de inserción masiva ](#insert-tables-bulkquery)
3. [Utilidades integradas gráficas de SQL Server (importación/exportación, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Utilidad de copia masiva (BCP) de la línea de comandos
BCP es una utilidad de línea de comandos instalada con SQL Server y es una de las maneras más rápidas de mover datos. Funciona en las tres variantes de SQL Server (SQL Server local, SQL Azure y máquina virtual SQL Server en Azure).

> [!NOTE]
> **¿Dónde deberían estar mis datos para BCP?**  
> Aunque no es necesario, tener archivos que contienen datos de origen que se encuentran en la misma máquina que el SQL Server de destino permite transferencias más rápidas (velocidad de red frente a velocidad de E/S de disco local). Puede mover los archivos planos que contienen los datos en la máquina en la que está instalado SQL Server mediante las diversas herramientas de copia de archivos como [AZCopy](../../storage/common/storage-use-azcopy.md), el [Explorador de Azure Storage](http://storageexplorer.com/) o copiar y pegar de Windows mediante el Protocolo de escritorio remoto (RDP).
>
>

1. Asegúrese de que la base de datos y las tablas se crean en la base de datos de SQL Server de destino. Este es un ejemplo de cómo realizar esa tarea con los comandos `Create Database` y `Create Table`:

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Genere el archivo de formato que describe el esquema de la tabla emitiendo el siguiente comando desde la línea de comandos de la máquina donde está instalado bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Inserte los datos en la base de datos usando el comando bcp de la siguiente manera. Esto debería funcionar desde la línea de comandos suponiendo que SQL Server esté instalado en la misma máquina:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimización de inserciones de BCP** Consulte el siguiente artículo ['Directrices para optimizar la importación masiva'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) para optimizar este tipo de inserciones.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Poner en paralelo inserciones para el movimiento de datos más rápido
Si los datos que está moviendo son grandes, puede acelerar las cosas ejecutando simultáneamente varios comandos BCP en paralelo en un script de PowerShell.

> [!NOTE]
> **Ingesta de Big Data** Para optimizar la carga de datos para conjuntos de datos grandes y muy grandes, cree particiones de sus tablas de bases de datos lógicas y físicas con varios grupos de archivos y tablas de particiones. Para obtener más información acerca de cómo crear y cargar datos en las tablas de partición, consulte [Tablas de particiones de SQL de carga paralela](parallel-load-sql-partitioned-tables.md).
>
>

El script de PowerShell de ejemplo siguiente muestra inserciones paralelas con bcp:

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
      }


    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }


    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }

    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Consulta SQL de inserción masiva
La [Consulta SQL de inserción masiva](https://msdn.microsoft.com/library/ms188365) se puede usar para importar datos en la base de datos desde archivos basados en fila o columnas [los tipos admitidos se tratan en el tema [Preparar los datos para exportar o importar de forma masiva (SQL Server)](https://msdn.microsoft.com/library/ms188609)].

Estos son algunos ejemplos de comandos de inserción masiva:  

1. Analice sus datos y establezca las opciones personalizadas antes de importar para asegurarse de que la base de datos de SQL Server asume el mismo formato para todos los campos especiales, como las fechas. Este es un ejemplo de cómo establecer el formato de fecha como año-mes-día (si los datos contienen la fecha en formato año-mes-día):

        SET DATEFORMAT ymd;    
2. Importar datos mediante instrucciones de importación en bloque:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Utilidades integradas de SQL Server
Puede utilizar los servicios de integraciones de SQL Server (SSIS) para importar datos en la VM de SQL Server de Azure desde un archivo plano.
SSIS está disponible en dos entornos de estudio. Para obtener más detalles, consulte [Entornos de Studio e Integration Services (SSIS)](https://technet.microsoft.com/library/ms140028.aspx):

* Para obtener más información acerca de las herramientas de datos de SQL Server, consulte [Herramientas de datos de Microsoft SQL Server](https://msdn.microsoft.com/data/tools.aspx)  
* Para obtener más información acerca del Asistente para importación y exportación, consulte [Asistente para importación y exportación de SQL Server](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Mover datos desde un servidor SQL Server local a un servidor SQL Server en una máquina virtual de Azure
También puede usar las siguientes estrategias de migración:

1. [Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportación a un archivo plano](#export-flat-file)
3. [Asistente para migración de Base de datos SQL](#sql-migration)
4. [Copia de seguridad y restauración de una base de datos](#sql-backup)

Describimos cada una de estas opciones:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure
El **Asistente para implementación de una base de datos de SQL Server en una máquina virtual de Microsoft Azure** es una manera sencilla y recomendada de mover datos de una instancia local de SQL Server a SQL Server en una máquina virtual de Azure. Para obtener pasos detallados así como un debate sobre otras alternativas, vea [Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportación a un archivo plano
Se pueden usar diversos métodos para la exportación masiva de datos desde un servidor SQL Server local, como se documenta en el tema [Importación y exportación masiva de datos (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Este documento tratará el programa de copia masiva (BCP) como ejemplo. Una vez que los datos se exportan a un archivo plano, se puede importar a otro servidor SQL Server mediante la importación masiva.

1. Exporte los datos del SQL Server local a un archivo mediante la utilidad bcp como se indica a continuación.

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Cree la base de datos y la tabla en la máquina virtual de SQL Server en Azure mediante `create database` y `create table` para el esquema de tablas que se exportó en el paso 1.
3. Cree un archivo de formato para describir el esquema de tabla de los datos que se están exportando e importando. En [Crear un archivo de formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx)se describen detalles del archivo de formato.

    Generación de archivos de formato cuando se ejecuta BCP desde la máquina de SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Generación de archivos de formato cuando se ejecuta BCP de forma remota contra un SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Utilice cualquiera de los métodos descritos en la sección [Mover datos desde el origen de archivo](#filesource_to_sqlonazurevm) para mover los datos de los archivos planos a un servidor SQL Server.

### <a name="sql-migration"></a>Asistente para migración de Base de datos SQL
[Asistente para migración de Base de datos SQL](http://sqlazuremw.codeplex.com/) proporciona una manera fácil de mover datos entre dos instancias de SQL server. Permite al usuario asignar el esquema de datos entre orígenes y tablas de destino, elegir los tipos de columna y otras funcionalidades. Utiliza la copia masiva (BCP) en segundo plano. A continuación se muestra una captura de la pantalla de bienvenida del Asistente para migración de base de datos de SQL.  

![Asistente para migración de SQL Server][2]

### <a name="sql-backup"></a>Copia de seguridad y restauración de una base de datos
SQL Server es compatible con:

1. La [funcionalidad de copia de seguridad y restauración de la base de datos](https://msdn.microsoft.com/library/ms187048.aspx) (tanto a un archivo local como la exportación de bacpac a blob) y [Aplicaciones de capa de datos](https://msdn.microsoft.com/library/ee210546.aspx) (con bacpac).
2. Capacidad para crear directamente las máquinas virtuales de SQL Server en Azure con una copia o una base de datos copiada en una base de datos existente de SQL Azure. Para obtener más detalles, consulte [Usar el Asistente para copiar bases de datos](https://msdn.microsoft.com/library/ms188664.aspx).

A continuación se muestra una captura de pantalla de las opciones de copia de seguridad y restauración de base de datos desde SQL Server Management Studio.

![Herramienta de importación SQL Server][1]

## <a name="resources"></a>Recursos
[Migración de una Base de datos SQL Server a SQL Server en una máquina virtual de Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Información general de SQL Server en Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png

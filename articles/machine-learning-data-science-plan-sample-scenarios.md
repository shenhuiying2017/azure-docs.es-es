<properties
	pageTitle="Tutoriales del Proceso de ciencia de datos en la nube | Azure" 
	description="Tutoriales del Proceso de ciencia de datos en la nube" 
	metaKeywords="" 
	services="data-science-process" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="" />

<tags 
	ms.service="data-science-process" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="msolhab" />


# Proceso de ciencia de datos en la nube para Aprendizaje automático de Azure

Este artículo le guía a través del mapa del Proceso de ciencia de datos en la nube y algunos escenarios de origen y destino de datos de ejemplo. Muestra algunas de las opciones y secuencias de procesamiento en función de las características de datos, ubicación de origen y repositorios destino de Azure. El flujo del Proceso de ciencia de datos en la nube para los escenarios de ejemplo se resume en la última sección.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)]

**Para todos los escenarios, necesita:**

   [Crear una cuenta de almacenamiento](storage-whatis-account.md)

  [Crear un área de trabajo de Aprendizaje automático de Azure](machine-learning/machine-learning-create-workspace.md)

**En las secciones siguientes se presentan algunos escenarios de ejemplo. Para cada escenario, se enumeran un posible flujo de ciencia de datos y recursos de compatibilidad de Azure.**

- [Escenario nº1: Conjunto de datos tabular de tamaño pequeño a medio de archivos locales](#smalllocal)
- [Escenario nº 2: Conjunto de datos de tamaño pequeño a medio de archivos locales que requieren procesamiento](#smalllocalprocess)
- [Escenario nº 3: Conjunto de datos grande de archivos locales, con blobs de Azure como destino.](#largelocal)
- [Escenario nº 4: Conjunto de datos de tamaño pequeño a medio de archivos locales, con SQL Server en una máquina virtual de Azure como destino.](#smalllocaltodb)
- [Escenario nº 5: Conjunto de datos grande de archivos locales, con SQL Server en una máquina virtual de Azure como destino.](#largelocaltodb)
- [Escenario nº 6: Conjunto de datos grande de archivos locales, con SQL Server en una máquina virtual de Azure como destino.](#largedbtodb)
- [Escenario nº 7: Big Data de archivos locales, con base de datos de Hive como destino en clústeres de Hadoop de HDInsight de Azure.](#largedbtohive)

## <a name="smalllocal"></a>Escenario nº 1: Conjunto de datos tabular de tamaño pequeño a medio de archivos locales

![Archivos locales de tamaño pequeño a medio][1]

#### Recursos adicionales de Azure: ninguno

1.  Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/).

2.  Cargue un conjunto de datos.

3.  Cree un flujo de experimento de Aprendizaje automático de Azure comenzando con conjuntos de datos cargados.

## <a name="smalllocalprocess"></a>Escenario nº 2: Conjunto de datos de tamaño pequeño a medio de archivos locales que requieren procesamiento

![Archivos locales de tamaño pequeño a medio con procesamiento][2]

#### Recursos adicionales de Azure: Máquina virtual de Azure (servidor IPython Notebook)

1.  Cree una máquina virtual de Azure que ejecute IPython Notebook.

2.  Cargue datos en un contenedor de almacenamiento de Azure.

3.  Preprocese y limpie datos en IPython Notebook obteniendo acceso desde el contenedor de almacenamiento de Azure.

4.  Transforme datos para un formulario limpio y tabular.

5.  Guarde los datos transformados en blobs de Azure.

6.  Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/).

7.  Lea los datos de blobs de Azure mediante el módulo [Lector][reader].

8. Cree un flujo de experimento de Aprendizaje automático de Azure comenzando con conjuntos de datos introducidos.

## <a name="largelocal"></a>Escenario nº 3: Conjunto de datos grande de archivos locales, con blobs de Azure como destino.

![Archivos locales grandes][3]

#### Recursos adicionales de Azure: Máquina virtual de Azure (servidor IPython Notebook)

1.  Cree una máquina virtual de Azure que ejecute IPython Notebook.

2.  Cargue datos en un contenedor de almacenamiento de Azure.

3.  Preprocese y limpie datos en IPython Notebook obteniendo acceso desde blobs de Azure.

4.  Transforme datos en un formulario limpio y tabular si es necesario.

5.  Explore datos y cree características según sea necesario.

6.  Extraiga una muestra de datos de tamaño pequeño a medio.

7.  Guarde los datos de muestra en blobs de Azure.

8. Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/).

9. Lea los datos de blobs de Azure mediante el módulo [Lector][reader].

10. Cree un flujo de experimento de Aprendizaje automático de Azure comenzando con conjuntos de datos introducidos.


## <a name="smalllocaltodb"></a>Escenario nº 4: Conjunto de datos de tamaño pequeño a medio de archivos locales, con SQL Server en una máquina virtual de Azure como destino.

![Archivos locales de tamaño pequeño a medio a Base de datos SQL de Azure][4]

#### Recursos adicionales de Azure: Máquina virtual de Azure (AQL Server/servidor IPython Notebook)

1.  Cree una máquina virtual de Azure que ejecute SQL Server + IPython Notebook.

2.  Cargue datos en un contenedor de almacenamiento de Azure.

3.  Preprocese y limpie datos en el contenedor de almacenamiento de Azure usando IPython Notebook.

4.  Transforme datos en un formulario limpio y tabular si es necesario.

5.  Guarde datos en archivos locales de máquina virtual (IPython Notebook se ejecuta en una máquina virtual; las unidades locales hacen referencia a unidades de máquina virtual).

6.  Cargue datos en la base de datos de SQL Server que se ejecuta en una máquina virtual de Azure.

    a. Opción nº 1: Mediante SQL Server Management Studio.

		i.  Inicie sesión en la máquina virtual de SQL Server.
        ii. Ejecute SQL Server Management Studio.
        iii. Cree tablas de base de datos y de destino.
        iv. Use one of the bulk import methods to load the data from VM-local files.

    b. Opción nº 2: Mediante IPython Notebook (no se recomienda para conjuntos de datos de tamaño medio o más grandes)

        i.  Use ODBC connection string to access SQL Server on VM.
        ii. Cree tablas de base de datos y de destino.
        iii. Use one of the bulk import methods to load the data from VM-local files.

7.  Explore datos y cree características según sea necesario. Tenga en cuenta que las características no necesitan materializarse en las tablas de base de datos. Solo tenga en cuenta la consulta necesaria para crearlas.

8. Elija un tamaño de muestra de datos, si lo necesita y/o desea.

9. Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/).

10. Lea los datos directamente desde SQL Server mediante el módulo [Lector][reader]. Pegue la consulta necesaria que extrae los campos, crea características y toma muestras de datos si es necesario directamente en la consulta [Lector][reader].

11. Cree un flujo de experimento de Aprendizaje automático de Azure comenzando con conjuntos de datos introducidos.

## <a name="largelocaltodb"></a>Escenario nº 5: Conjunto de datos grande de archivos locales, con SQL Server en una máquina virtual de Azure como destino.

![Archivos locales grandes a Base de datos SQL de Azure][5]

#### Recursos adicionales de Azure: Máquina virtual de Azure (AQL Server/servidor IPython Notebook)

1.  Cree una máquina virtual de Azure que ejecute SQL Server y el servidor IPython Notebook.

2.  Cargue datos en un contenedor de almacenamiento de Azure.

3.  (Opcional) Preprocese y limpie los datos.

    a. Preprocese y limpie datos en IPython Notebook obteniendo acceso desde blobs de Azure.

    b. Transforme datos en un formulario limpio y tabular si es necesario.

    c. Guarde datos en archivos locales de máquina virtual (IPython Notebook se ejecuta en una máquina virtual; las unidades locales hacen referencia a unidades de máquina virtual).

4.  Cargue datos en la base de datos de SQL Server que se ejecuta en una máquina virtual de Azure.

    a. Inicie sesión en la máquina virtual de SQL Server.

    b. Si los datos todavía no están guardados, descargue los archivos de datos desde el contenedor de almacenamiento de Azure a la carpeta de la máquina virtual local.

    c. Ejecute SQL Server Management Studio.

    d. Cree tablas de base de datos y de destino.

	e. Use uno de los métodos de importación en masa para cargar los datos.

    f. Si se requieren combinaciones de tablas, cree índices para acelerar dichas combinaciones.

 > [AZURE.NOTE]Para acelerar la carga de tamaños de datos de gran tamaño, es recomendable crear tablas con particiones e importar en masa los datos en paralelo. Para obtener más información, vea [Importación de datos en paralelo a tablas con particiones de SQL](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explore datos y cree características según sea necesario. Tenga en cuenta que las características no necesitan materializarse en las tablas de base de datos. Solo tenga en cuenta la consulta necesaria para crearlas.

6.  Elija un tamaño de muestra de datos, si lo necesita y/o desea.

7.  Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/).

8. Lea los datos directamente desde SQL Server mediante el módulo [Lector][reader]. Pegue la consulta necesaria que extrae los campos, crea características y toma muestras de datos si es necesario directamente en la consulta [Lector][reader].

9. Flujo de experimento de Aprendizaje automático de Azure sencillo comenzando con conjuntos de datos cargados.

## <a name="largedbtodb"></a>Escenario nº 6: Conjunto de datos grande de archivos locales, con SQL Server en una máquina virtual de Azure como destino.

![Base de datos SQL local a Base be datos SQL de Azure][6]

#### Recursos adicionales de Azure: Máquina virtual de Azure (AQL Server/servidor IPython Notebook)

1.  Cree una máquina virtual de Azure que ejecute SQL Server y el servidor IPython Notebook.

2.  Use uno de los métodos de exportación de datos para exportar los datos desde SQL Server a archivos de volcado de memoria.

    a. Nota: si decide mover todos los datos de la base de datos local, un método alternativo (más rápido) para mover la base de datos completa a la instancia de SQL Server en Azure. Omita los pasos para exportar datos, crear la base de datos y cargar e importar datos a la base de datos de destino y siga el método alternativo.

3.  Cargue archivos de volcado de memoria en el contenedor de almacenamiento de Azure.

4.  Cargue los datos en una base de datos de SQL Server que se ejecute en una máquina virtual de Azure.

    a. Inicie sesión en la máquina virtual de SQL Server.

    b. Descargue los archivos de datos desde un contenedor de almacenamiento de Azure a la carpeta de la máquina virtual local.

    c. Ejecute SQL Server Management Studio.

    d. Cree tablas de base de datos y de destino.

	e. Use uno de los métodos de importación en masa para cargar los datos.

	f. Si se requieren combinaciones de tablas, cree índices para acelerar dichas combinaciones.

> [AZURE.NOTE]Para acelerar la carga de tamaños de datos de gran tamaño, cree tablas con particiones e importe en masa los datos en paralelo. Para obtener más información, vea [Importación de datos en paralelo a tablas con particiones de SQL](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explore datos y cree características según sea necesario. Tenga en cuenta que las características no necesitan materializarse en las tablas de base de datos. Solo tenga en cuenta la consulta necesaria para crearlas.

6.  Elija un tamaño de muestra de datos, si lo necesita y/o desea.

7.  Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/).

8. Lea los datos directamente desde SQL Server mediante el módulo [Lector][reader]. Pegue la consulta necesaria que extrae los campos, crea características y toma muestras de datos si es necesario directamente en la consulta [Lector][reader].

9. Flujo de experimento de Aprendizaje automático de Azure sencillo comenzando con conjuntos de datos cargados.

### Método alternativo para copiar una base de datos completa desde un servidor SQL Server local a Base de datos SQL de Azure

![Desasociación de base de datos local y asociación a Base de datos SQL de Azure][7]

#### Recursos adicionales de Azure: Máquina virtual de Azure (AQL Server/servidor IPython Notebook)

Para replicar toda la base de datos de SQL Server en la máquina virtual de SQL Server, debe copiar una base de datos desde una ubicación o  un servidor a otro, suponiendo que la base de datos se puede desconectar temporalmente. Esto se realiza en la GUI del Explorador de objetos de SQL Server Management Studio o mediante los comandos de Transact-SQL equivalentes.

1. Desasocie la base de datos en la ubicación de origen. Para obtener más información, vea [Desasociar una base de datos](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. En el Explorador de Windows o en la ventana del símbolo del sistema de Windows, copie los archivos de base de datos desasociados o los archivos de registro a la ubicación de destino en la máquina virtual de SQL Server en Azure.
3. Asocie los archivos copiados a la instancia de SQL Server de destino. Para obtener más información, vea [Asociar una base de datos](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx). 

[Mover una base de datos mediante las opciones desasociación y asociación (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Escenario nº 7: Big Data de archivos locales, con base de datos de Hive como destino en clústeres de Hadoop de HDInsight de Azure.

![Datos de gran tamaño en Hive de destino local][9]

#### Recursos adicionales de Azure: Clúster de Hadoop de HDInsight de Azure y máquina virtual de Azure (servidor IPython Notebook)

1.  Cree una máquina virtual de Azure que ejecute el servidor IPython Notebook.

2.  Cree un clúster de Hadoop de HDInsight de Azure.

3.  (Opcional) Preprocese y limpie los datos.

    a. Preprocese y limpie datos en IPython Notebook obteniendo acceso desde blobs de Azure.

    b. Transforme datos en un formulario limpio y tabular si es necesario.

    c. Guarde datos en archivos locales de máquina virtual (IPython Notebook se ejecuta en una máquina virtual; las unidades locales hacen referencia a unidades de máquina virtual).

4.  Cargue datos en el contenedor predeterminado del clúster de Hadoop que seleccionó en el paso 2.

5.  Cargue datos en la base de datos de Hive en el clúster de Hadoop de HDInsight de Azure.

    a. Inicie sesión en el nodo principal del clúster de Hadoop.

    b. Abra la línea de comandos de Hadoop.

    c. Especifique el directorio raíz de Hive mediante el comando `cd %hive_home%\bin` en la línea de comandos de Hadoop.

    d. Ejecute las consultas de Hive para crear bases de datos y tablas, y cargar datos desde el almacenamiento de blobs en tablas de Hive.

 	> [AZURE.NOTE]Si los datos son grandes, los usuarios pueden crear la tabla de Hive con particiones. A continuación, los usuarios pueden usar un bucle `for` en la línea de comandos de Hadoop en el nodo principal para cargar datos en la tabla de Hive con particiones por partición.

6.  Explore datos y cree características según sea necesario en la línea de comandos de Hadoop. Tenga en cuenta que las características no necesitan materializarse en las tablas de base de datos. Solo tenga en cuenta la consulta necesaria para crearlas.

	a. Inicie sesión en el nodo principal del clúster de Hadoop.

    b. Abra la línea de comandos de Hadoop.

    c. Especifique el directorio raíz de Hive mediante el comando `cd %hive_home%\bin` en la línea de comandos de Hadoop.

	d. Ejecute las consultas de Hive en línea de comandos de Hadoop en el nodo principal del clúster de Hadoop para explorar los datos y crear características según sea necesario.

7.  Si lo necesita o desea, muestree los datos para ajustarlos en Estudio de aprendizaje automático de Azure.

8.  Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net/).

9. Lea los datos directamente desde `Hive Queries` mediante el módulo [Lector][reader]. Pegue la consulta necesaria que extrae los campos, crea características y toma muestras de datos si es necesario directamente en la consulta [Lector][reader].

10. Flujo de experimento de Aprendizaje automático de Azure sencillo comenzando con conjuntos de datos cargados.

Resumen de los escenarios de ejemplo
------------------------

El diagrama siguiente resume los escenarios descritos anteriormente y las elecciones del Proceso de ciencia de datos en la nube en cada escenario. Tenga en cuenta que el procesamiento de datos, la exploración, la ingeniería de características y el muestreo pueden tener lugar en uno o varios métodos o entornos (en el entorno de origen, intermedio o de destino) y pueden continuar de forma iterativa según sea necesario. El diagrama sirve para ilustrar solamente algunos de los posibles flujos.

![Escenarios de tutoriales de proceso de ciencia de datos de ejemplo][8]

### Ejemplo de ciencia de datos de Azure en acción

Para obtener un ejemplo de tutorial completo del Proceso de ciencia de datos de Azure mediante un conjunto de datos público, consulte [Proceso de ciencia de datos de Azure en acción](machine-learning/machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!--HONumber=54-->
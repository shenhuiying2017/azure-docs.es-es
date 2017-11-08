
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Azure Data Factory es un servicio de integración de datos. Permite crear flujos de trabajo de datos en la nube. Un flujo de trabajo se implementa como una o más *canalizaciones*. Las canalizaciones orquestan y automatizan el movimiento y la transformación de los datos. Las canalizaciones pueden realizar la siguiente secuencia con los datos:

1. Ingerir datos de almacenes de datos dispares.
2. Transformar o procesar los datos mediante servicios de proceso como el siguiente:
    - Azure HDInsight Hadoop
    - Spark
    - Análisis con Azure Data Lake
    - Aprendizaje automático de Azure
3. Publique los datos de salida en los almacenes de datos.
    - El destino de publicación podría ser una instancia de Azure SQL Data Warehouse para que la consuman las aplicaciones de inteligencia empresarial (BI). 

Puede programar canalizaciones mediante Data Factory.


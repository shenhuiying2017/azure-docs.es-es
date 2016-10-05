<properties
pageTitle="Uso de Microsoft Azure Data Lake Tools para Visual Studio con Sandbox de Hortonworks | Microsoft Azure"
description="Aprenda a usar Azure Data Lake Tools para Visual Studio con Sandbox de Hortonworks (en ejecución en una máquina virtual local). Con estas herramientas, puede crear y ejecutar trabajos de Hive y Pig en el espacio aislado, así como ver la salida de los trabajos y el historial."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/26/2016"
ms.author="larryfr"/>

# Uso de Azure Data Lake Tools para Visual Studio con Sandbox de Hortonworks

Las herramientas de Azure Data Lake para Visual Studio incluyen herramientas para trabajar con clústeres genéricos de Hadoop, además de herramientas para trabajar con Azure Data Lake y HDInsight. Este documento proporciona los pasos necesarios para utilizar las herramientas de Azure Data Lake con Sandbox de Hortonworks en una máquina virtual local.

Mediante Sandbox de Hortonworks puede trabajar con Hadoop localmente en su entorno de desarrollo. Una vez que haya desarrollado una solución y desee implementarla a escala, puede desplazarse a un clúster de HDInsight.

## Requisitos previos

* Sandbox de Hortonworks que se esté ejecutando en una máquina virtual en el entorno de desarrollo. Este documento se ha escrito y probado con el espacio aislado que se ejecuta en Oracle VirtualBox, una vez configurado este con la información del documento [Introducción al ecosistema de Hadoop](hdinsight-hadoop-emulator-get-started.md).

* Cualquier edición de Visual Studio 2013 o 2015.

* [Azure SDK para .NET](https://azure.microsoft.com/downloads/) 2.7.1 o superior

* [Azure Data Lake Tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)

## Configuración de contraseñas para el espacio aislado

Asegúrese de que se esté ejecutando Sandbox de Hortonworks y luego siga los pasos de [Introducción al ecosistema de Hadoop](hdinsight-hadoop-emulator-get-started.md#set-passwords) para configurar la contraseña de la cuenta `root` de SSH y de la cuenta `admin` de Ambari. Estas contraseñas se usarán al conectarse al espacio aislado desde Visual Studio.

## Conexión de las herramientas al espacio aislado

1. Abra Visual Studio, seleccione __Ver__ y luego __Explorador de servidores__.

2. En el __Explorador de servidores__, haga clic con el botón derecho en la entrada __HDInsight__ y seleccione __Conectar con el emulador de HDInsight__.

    ![Conectarse con el emulador de HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. En el cuadro de diálogo __Conectar con el emulador de HDInsight__, escriba la contraseña que ha configurado para Ambari.

    ![Escribir la contraseña de Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Seleccione __Siguiente__ para continuar.

4. Utilice el campo __Contraseña__ para escribir la contraseña configurada para la cuenta `root`. Deje los demás campos con los valores predeterminados.

    ![Escribir la contraseña raíz](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Seleccione __Siguiente__ para continuar.

5. Espere a que finalice la validación de los servicios. En algunos casos, se puede producir un error de validación y se le pedirá que actualice la configuración. Cuando esto ocurre, seleccione el botón __Actualizar__, y espere a que finalicen la configuración y la comprobación del servicio.

    ![Errores y botón de actualización](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [AZURE.NOTE] El proceso de actualización utiliza Ambari para modificar la configuración de Sandbox de Hortonworks en función de lo que esperan las herramientas de Azure Data Lake para Visual Studio.

    Una vez finalizada la validación, seleccione __Finalizar__ para completar la configuración.

    ![Finalizar la conexión](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [AZURE.NOTE] Según la velocidad del entorno de desarrollo y de la cantidad de memoria asignada a la máquina virtual, la configuración y validación de los servicios puede tardar varios minutos.

Después de seguir estos pasos, tendrá una entrada "Clúster local de HDInsight" en la sección HDInsight del Explorador de servidores.

## Escritura de una consulta de Hive

Hive proporciona un lenguaje de consultas de tipo SQL (HiveQL) para trabajar con datos estructurados. Utilice los pasos siguientes para obtener información sobre cómo ejecutar consultas ad hoc en el clúster local.

1. En el __Explorador de servidores__, haga clic con el botón derecho en la entrada del clúster local agregado anteriormente y, después, seleccione __Escribir una consulta de Hive__.

    ![Escribir una consulta de Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Esto abre una nueva ventana de consulta que le permite escribir y enviar rápidamente una consulta al clúster local.

2. En la nueva ventana de consulta, escriba lo siguiente:

        select count(*) from sample_08;
    
    En la parte superior de la ventana de consulta, asegúrese de que esté seleccionada la configuración para el clúster local y seleccione __Enviar__. Deje las demás opciones (__Lote__ y el nombre del servidor) en los valores predeterminados.

    ![ventana de consulta y botón de envío](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Tenga en cuenta que también puede usar el menú desplegable junto a __Enviar__ para seleccionar __Avanzadas__. Se abrirá un cuadro de diálogo que le permite proporcionar opciones adicionales al enviar el trabajo.

    ![envío avanzado](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Una vez enviada la consulta, aparecerá el estado del trabajo. Esto proporciona información sobre el trabajo cuando lo procesa Hadoop. La entrada __Estado de trabajo__ proporciona el estado actual del trabajo. El estado se actualizará periódicamente o puede usar el icono de actualización para actualizarlo manualmente.

    ![Estado del trabajo](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Cuando el __Estado de trabajo__ cambia a __Finalizado__, se muestra un gráfico acíclico dirigido (DAG). Este describe la ruta de acceso de ejecución determinada mediante Tez (el motor de ejecución predeterminado para Hive en el clúster local.)
    
    > [AZURE.NOTE] Tez es también el valor predeterminado cuando se utilizan clústeres de HDInsight basado en Linux. No es el valor predeterminado para HDInsight basado en Windows; para poder usarlo en este sistema operativo, debe agregar la línea `set hive.execution.engine = tez;` al principio de la consulta de Hive.

    Utilice el vínculo __Salida de trabajo__ para ver la salida. En este caso, es __823__; el número de filas de la tabla sample\_08. Puede ver información de diagnóstico sobre el trabajo mediante los vínculos __Registro de trabajo__ y __Descargar registro YARN__.

4. También puede ejecutar trabajos de Hive de forma interactiva cambiando el campo __Lote__ a __Interactivo__ y seleccionando luego __Ejecutar__.

    ![Consulta interactiva](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    El registro de salida generado se transmite durante el procesamiento a la ventana __Salida de HiveServer2__.
    
    > [AZURE.NOTE] Se trata de la misma información que está disponible en el vínculo __Registro de trabajo__ una vez finalizado un trabajo.

    ![Salida de HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## Creación de un proyecto de Hive

También puede crear un proyecto que incluya varios scripts de Hive. Esto resulta útil si tiene scripts relacionados que necesita conservar juntos o mantener con sistemas de control de versiones.

1. Abra Visual Studio, seleccione __Archivo__ > __Nuevo__ y, después, \_\_Proyecto\_\_.

2. En la lista de proyectos, expanda __Plantillas__, __Azure Data Lake__ y seleccione __HIVE (HDInsight)__. En la lista de plantillas, seleccione __Hive Sample__. Escriba un nombre y una ubicación, y seleccione __Aceptar__.

    ![Plantilla de HIVE (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

El proyecto __Hive Sample__ contiene dos scripts, __WebLogAnalysis.hql__ y __SensorDataAnalysis.hql__. Puede mandarlos con el mismo botón __Enviar__ situado en la parte superior de la ventana.

## Creación de un proyecto de Pig

Aunque Hive proporciona un lenguaje similar a SQL para trabajar con datos estructurados, Pig facilita un lenguaje (Pig Latin) que le permite desarrollar una canalización de transformaciones que se aplican a los datos. Utilice los pasos siguientes para usar Pig con el clúster local.

1. Abra Visual Studio, seleccione __Archivo__, __Nuevo__ y, después, __Proyecto__. En la lista de proyectos, expanda __Plantillas__, __Azure Data Lake__ y seleccione __Pig (HDInsight)__. En la lista de plantillas, seleccione __Pig Application__. Escriba un nombre y una ubicación, y seleccione __Aceptar__.

    ![Proyecto de Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Escriba lo siguiente como contenido del archivo __script.pig__ que se creó con este proyecto.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int, 
            ip_address:chararray, 
            date:chararray, 
            time:chararray, 
            landing_page:chararray, 
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Aunque Pig utiliza un lenguaje diferente que Hive, la forma de ejecutar los trabajos es la misma en ambos lenguajes mediante el botón __Enviar__. La selección de la lista desplegable situada junto a __Enviar__ muestra un cuadro de diálogo de envío avanzado para Pig.

    ![envío avanzado de Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)
    
3. El estado y la salida de trabajo también se muestran del mismo modo que en una consulta de Hive.

    ![imagen de un trabajo de Pig finalizado](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## Vista de trabajos

Azure Data Lake Tools también permite ver fácilmente la información sobre los trabajos ejecutados en Hadoop. Utilice los pasos siguientes para ver los trabajos que se han ejecutado en el clúster local.

1. En el __Explorador de servidores__, haga clic con el botón derecho en el clúster local y seleccione __Ver trabajos__. Esto mostrará una lista de trabajos que se han enviado al clúster.

    ![Vista de trabajos](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. En la lista de trabajos, seleccione uno para ver los detalles del trabajo.

    ![seleccionar un trabajo](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    La información que aparece es similar a lo que se ve después de ejecutar una consulta de Hive o Pig, junto con vínculos a información de registro y de salida.

3. También puede modificar y volver a enviar el trabajo desde aquí.

## Vista de bases de datos de Hive

1. En el __Explorador de servidores__, expanda la entrada __Clúster local de HDInsight__ y luego __Bases de datos de Hive__. Aparecen las bases de datos __predeterminada__ y __xademo__ en el clúster local. La expansión de una base de datos muestra las tablas dentro de la base de datos.

    ![bases de datos expandidas](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. La expansión de una tabla muestra las columnas de esa tabla. Puede hacer clic con el botón derecho en una tabla y seleccionar __Ver las primeras 100 filas__ para ver rápidamente los datos.

    ![vista de bases de datos de Hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### Propiedades de una base de datos y una tabla

Probablemente ha observado que puede seleccionar ver las __propiedades__ de una base de datos o una tabla. Esto mostrará los detalles del elemento seleccionado en la ventana de propiedades.

![Propiedades](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### Creación de una tabla

Para crear una tabla, haga clic con el botón derecho en una base de datos y seleccione __Crear tabla__.

![Crear tabla](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Ya puede crear la tabla usando un formulario. Puede ver el script HiveQL sin formato que se utilizará para crear la tabla en la parte inferior de esta página.

![crear formulario de tabla](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## Pasos siguientes

* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/) (Aprendizaje del funcionamiento de Sandbox de Hortonworks)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/) (Tutorial de Hadoop: introducción a HDP)

<!---HONumber=AcomDC_0921_2016-->
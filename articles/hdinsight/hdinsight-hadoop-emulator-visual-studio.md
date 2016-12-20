---
title: Uso de Microsoft Azure Data Lake Tools para Visual Studio con Sandbox de Hortonworks | Microsoft Docs
description: "Aprenda a usar Azure Data Lake Tools para Visual Studio con Sandbox de Hortonworks (en ejecución en una máquina virtual local). Con estas herramientas, puede crear y ejecutar trabajos de Hive y Pig en el espacio aislado, así como ver la salida de los trabajos y el historial."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: b72443c0ca60196535ac093a6ac03df456f776ea
ms.openlocfilehash: 58b05844242b5d880c7c32b25889ee43f95d92a6


---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Uso de Azure Data Lake Tools para Visual Studio con Sandbox de Hortonworks

Las herramientas de Azure Data Lake para Visual Studio incluyen herramientas para trabajar con clústeres genéricos de Hadoop, además de herramientas para trabajar con Azure Data Lake y HDInsight. Este documento proporciona los pasos necesarios para utilizar las herramientas de Azure Data Lake con Sandbox de Hortonworks en una máquina virtual local.

Mediante Sandbox de Hortonworks puede trabajar con Hadoop localmente en su entorno de desarrollo. Una vez que haya desarrollado una solución y desee implementarla a escala, puede desplazarse a un clúster de HDInsight.

## <a name="prerequisites"></a>Requisitos previos

* Sandbox de Hortonworks que se esté ejecutando en una máquina virtual en el entorno de desarrollo. Este documento se ha escrito y probado con el espacio aislado que se ejecuta en Oracle VirtualBox, una vez configurado este con la información del documento [Introducción al ecosistema de Hadoop](hdinsight-hadoop-emulator-get-started.md) .

* Cualquier edición de Visual Studio 2013 o 2015.

* [SDK de Azure para .NET](https://azure.microsoft.com/downloads/) 2.7.1 o versiones posteriores.

* [Azure Data Lake Tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configuración de contraseñas para el espacio aislado

Asegúrese de que se esté ejecutando Sandbox de Hortonworks y luego siga los pasos de [Introducción al ecosistema de Hadoop](hdinsight-hadoop-emulator-get-started.md#set-passwords) para configurar la contraseña de la cuenta `root` de SSH y de la cuenta `admin` de Ambari. Estas contraseñas se usarán al conectarse al espacio aislado desde Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Conexión de las herramientas al espacio aislado

1. Abra Visual Studio, seleccione **Ver** y, luego, **Explorador de servidores**.
2. En el **Explorador de servidores**, haga clic con el botón derecho en la entrada **HDInsight** y seleccione **Conectar con el emulador de HDInsight**.
    
    ![Conectar con el emulador de HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. En el cuadro de diálogo **Conectar con el emulador de HDInsight**, escriba la contraseña que ha configurado para Ambari.
   
    ![Escribir la contraseña de Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)
    
    Seleccione **Siguiente** para continuar.

4. Use el campo **Contraseña** para escribir la contraseña configurada para la cuenta `root`. Deje los demás campos con los valores predeterminados.
   
    ![Escribir la contraseña raíz](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)
    
    Seleccione **Siguiente** para continuar.

5. Espere a que finalice la validación de los servicios. En algunos casos, se puede producir un error de validación y se le pedirá que actualice la configuración. Cuando esto ocurra, seleccione el botón **Actualizar** y espere a que finalicen la configuración y la comprobación del servicio.
    
    ![Errores y botón de actualización](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)
    
    > [!NOTE]
    > El proceso de actualización utiliza Ambari para modificar la configuración de Sandbox de Hortonworks en función de lo que esperan las herramientas de Azure Data Lake para Visual Studio.
   
    Una vez finalizada la validación, seleccione **Finalizar** para completar la configuración.
   
    ![Finalizar la conexión](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)
   
    > [!NOTE]
    > Según la velocidad del entorno de desarrollo y de la cantidad de memoria asignada a la máquina virtual, la configuración y validación de los servicios puede tardar varios minutos.

Después de seguir estos pasos, tendrá una entrada "Clúster local de HDInsight" en la sección HDInsight del Explorador de servidores.

## <a name="write-a-hive-query"></a>Escritura de una consulta de Hive

Hive proporciona un lenguaje de consultas de tipo SQL (HiveQL) para trabajar con datos estructurados. Utilice los pasos siguientes para obtener información sobre cómo ejecutar consultas ad hoc en el clúster local.

1. En el **Explorador de servidores**, haga clic con el botón derecho en la entrada del clúster local agregado anteriormente y, después, seleccione **Escribir una consulta de Hive**.
   
    ![Escritura de una consulta de Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)
   
    Esto abre una nueva ventana de consulta que le permite escribir y enviar rápidamente una consulta al clúster local.

2. En la nueva ventana de consulta, escriba lo siguiente:
   
        select count(*) from sample_08;
   
    En la parte superior de la ventana de consulta, asegúrese de que esté seleccionada la configuración para el clúster local, y seleccione **Enviar**. Deje las demás opciones (**Lote** y el nombre del servidor) en los valores predeterminados.
   
    ![ventana de consulta y botón de envío](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)
   
    Tenga en cuenta que también puede usar el menú desplegable junto a **Enviar** para seleccionar **Avanzadas**. Se abrirá un cuadro de diálogo que le permite proporcionar opciones adicionales al enviar el trabajo.
   
    ![envío avanzado](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Una vez enviada la consulta, aparecerá el estado del trabajo. Esto proporciona información sobre el trabajo cuando lo procesa Hadoop. La entrada **Estado de trabajo** proporciona el estado actual del trabajo. El estado se actualizará periódicamente o puede usar el icono de actualización para actualizarlo manualmente.
    
    ![Estado de trabajo](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)
    
    Cuando el **Estado de trabajo** cambia a **Finalizado**, se muestra un gráfico acíclico dirigido (DAG). Este describe la ruta de acceso de ejecución determinada mediante Tez (el motor de ejecución predeterminado para Hive en el clúster local.) 
   
    > [!NOTE]
    > Tez es también el valor predeterminado cuando se utilizan clústeres de HDInsight basado en Linux. No es el valor predeterminado para HDInsight basado en Windows; para poder usarlo en este sistema operativo, debe agregar la línea `set hive.execution.engine = tez;` al principio de la consulta de Hive. 

   
    Use el vínculo **Salida de trabajo** para ver la salida. En este caso, es **823**; el número de filas de la tabla sample_08. Puede ver información de diagnóstico sobre el trabajo mediante los vínculos **Registro de trabajo** y **Descargar registro YARN**.

4. También puede ejecutar trabajos de Hive de forma interactiva cambiando el campo **Lote** a **Interactivo** y seleccionando luego **Ejecutar**. 
    
    ![Consulta interactiva](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)
   
    El registro de salida generado se transmite durante el procesamiento a la ventana **Salida de HiveServer2**.
    
    > [!NOTE]
    > Se trata de la misma información que está disponible en el vínculo **Registro de trabajo** una vez finalizado un trabajo.
   
    ![Salida de HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Creación de un proyecto de Hive

También puede crear un proyecto que incluya varios scripts de Hive. Esto resulta útil si tiene scripts relacionados que necesita conservar juntos o mantener con sistemas de control de versiones.

1. En Visual Studio, seleccione **Archivo**, **Nuevo** y, después, __Proyecto__.

2. En la lista de proyectos, expanda **Plantillas**, **Azure Data Lake** y seleccione **HIVE (HDInsight)**. En la lista de plantillas, seleccione **Hive Sample**. Escriba un nombre y una ubicación, y seleccione **Aceptar**.
   
    ![Plantilla de HIVE (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

El proyecto **Hive Sample** contiene dos scripts, **WebLogAnalysis.hql** y **SensorDataAnalysis.hql**. Puede enviarlos con el mismo botón **Enviar** situado en la parte superior de la ventana.

## <a name="create-a-pig-project"></a>Creación de un proyecto de Pig

Aunque Hive proporciona un lenguaje similar a SQL para trabajar con datos estructurados, Pig facilita un lenguaje (Pig Latin) que le permite desarrollar una canalización de transformaciones que se aplican a los datos. Utilice los pasos siguientes para usar Pig con el clúster local.

1. Abra Visual Studio y seleccione **Archivo**, **Nuevo** y **Proyecto**. En la lista de proyectos, expanda **Plantillas**, **Azure Data Lake** y seleccione **Pig (HDInsight)**. En la lista de plantillas, seleccione **Pig Application**. Escriba un nombre y una ubicación, y seleccione **Aceptar**.
   
    ![Proyecto de Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Escriba lo siguiente como contenido del archivo **script.pig** que se creó con este proyecto.
   
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
   
    Aunque Pig utiliza un lenguaje diferente que Hive, la forma de ejecutar los trabajos es la misma en ambos lenguajes mediante el botón **Enviar**. La selección de la lista desplegable situada junto a **Enviar** muestra un cuadro de diálogo de envío avanzado para Pig.
   
    ![envío avanzado de Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. El estado y la salida de trabajo también se muestran del mismo modo que en una consulta de Hive.
   
    ![imagen de un trabajo de Pig finalizado](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Vista de trabajos

Azure Data Lake Tools también permite ver fácilmente la información sobre los trabajos ejecutados en Hadoop. Utilice los pasos siguientes para ver los trabajos que se han ejecutado en el clúster local.

1. En el **Explorador de servidores**, haga clic con el botón derecho en el clúster local y seleccione **Ver trabajos**. Esto mostrará una lista de trabajos que se han enviado al clúster.
   
    ![Vista de trabajos](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. En la lista de trabajos, seleccione uno para ver los detalles del trabajo.
   
    ![seleccionar un trabajo](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)
   
    La información que aparece es similar a lo que se ve después de ejecutar una consulta de Hive o Pig, junto con vínculos a información de registro y de salida.

3. También puede modificar y volver a enviar el trabajo desde aquí.

## <a name="view-hive-databases"></a>Vista de bases de datos de Hive

1. En el **Explorador de servidores**, expanda la entrada **Clúster local de HDInsight** y, luego, **Bases de datos de Hive**. Aparecen las bases de datos **Predeterminada** y **xademo** en el clúster local. La expansión de una base de datos muestra las tablas dentro de la base de datos.
   
    ![bases de datos expandidas](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. La expansión de una tabla muestra las columnas de esa tabla. Puede hacer clic con el botón derecho en una tabla y seleccionar **Ver las primeras 100 filas** para ver rápidamente los datos.
   
    ![vista de bases de datos de Hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Propiedades de una base de datos y una tabla

Probablemente ha observado que puede seleccionar ver las **propiedades** de una base de datos o una tabla. Esto mostrará los detalles del elemento seleccionado en la ventana de propiedades.

![propiedades](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Creación de una tabla

Para crear una tabla, haga clic con el botón derecho en una base de datos y seleccione **Crear tabla**.

![Crear tabla](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Ya puede crear la tabla usando un formulario. Puede ver el script HiveQL sin formato que se utilizará para crear la tabla en la parte inferior de esta página.

![crear formulario de tabla](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Pasos siguientes

* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)




<!--HONumber=Nov16_HO3-->



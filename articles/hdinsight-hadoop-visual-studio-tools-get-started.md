<properties 
	pageTitle="Introducción al uso de herramientas de HDInsight para Visual Studio | Azure" 
	description="Aprenda a instalar y utilizar herramientas de HDInsight para Visual Studio para conectarse a HDInsight y ejecutar consultas de Hive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="11/12/2014" 
	ms.author="jgao" 
	editor="cgronlun"/>

# Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio

Aprenda a instalar y utilizar herramientas de HDInsight para Visual Studio para conectarse a HDInsight y ejecutar consultas de Hive. Para obtener más información sobre el uso de HDInsight, consulte [Introducción a HDInsight][hdinsight.introduction] y [Primeros pasos con HDInsight][hdinsight.get.started].

+ [Instalación] 
+ [Conexión a su suscripción de Azure]
+ [Navegación por los recursos vinculados]
+ [Ejecución de consultas de Hive]
+ [Pasos siguientes]


##Requisitos previos

- Estación de trabajo con lo siguiente

	- Windows 7 o Windows 8.
	- Visual Studio 2012 con la [actualización 4](http://www.microsoft.com/es-es/download/details.aspx?id=39305) o Visual Studio 2013 con la [actualización 3](http://www.microsoft.com/es-es/download/details.aspx?id=43721), o bien [Visual Studio Express 2013](http://www.microsoft.com/es-es/download/details.aspx?id=43722).

	>[AZURE.NOTE] Actualmente las herramientas solo se suministran con la versión en inglés. 


## Instalación

Las herramientas de HDInsight para Visual Studio se suministran con el SDK de Microsoft Azure. Puede usar [Instalador de plataforma web](http://go.microsoft.com/fwlink/?LinkId=255386) para la instalación.

![HDinsight Tools for Visual Studio Web Platform installer][1]


## Conexión a su suscripción de Azure
Las herramientas de HDInsight para Visual Studio permiten conectarse a los clústeres de HDInsight, realizar algunas operaciones básicas de administración y ejecutar consultas de Hive.

**Para conectarse a la suscripción de Azure**

1.	Ejecute Visual Studio.
2.	Desde el menú **Ver** menú, haga clic en **Explorador de servidores** para abrir la ventana del explorador de servidores.
3.	Expanda **Azure** y, a continuación, haga clic en los clústeres de **HDInsight**. 

	>[AZURE.NOTE]Se abrirá la ventana **Lista de tareas de HDInsight**. Si no puede ver esta ventana, puede abrirla haciendo clic en **Otras ventanas** desde el menú **VER** y, a continuación, haga clic en la **Ventana de lista de tareas de HDInsight**.  
4.	Escriba sus credenciales de suscripción de Azure y, a continuación, haga clic en **Iniciar sesión**. Esto solo es necesario si nunca se ha conectado a la suscripción de Azure desde Visual Studio en esta estación de trabajo.
5.	En el Explorador de servidores, se mostrará una lista de los clústeres de HDInsight existentes. Si no tiene ningún clúster, puede aprovisionar uno mediante el portal de administración, con el PowerShell de Azure o el SDK de HDInsight.  Para obtener más información, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Expanda un clúster de HDInsight. Se mostrarán las **bases de datos de Hive**, la cuenta de almacenamiento predeterminada y las cuentas de almacenamiento vinculadas. Puede expandir las entidades. 

Una vez conectado a su suscripción de Azure, podrá realizar lo siguiente:

**Para conectarse al portal de administración desde Visual Studio**

- Desde el Explorador de servidores, expanda **Azure**, **Clústeres de HDInsight**, haga clic en un clúster de HDInsight y, a continuación, haga clic en **Administrar clústeres en el Portal de Azure**.

**Para hacer preguntas y proporcionar comentarios desde Visual Studio**

- Desde el menú **HERRAMIENTAS**, haga clic en **HDInsight** y, a continuación, haga clic en **Foro de MSDN** para realizar preguntas o **Enviar comentarios**.

## Navegación por los recursos vinculados 

Desde el Explorador de servidores, podrá ver la cuenta de almacenamiento predeterminada y las cuentas de almacenamiento vinculadas. Expanda la cuenta de almacenamiento predeterminada para ver los contenedores en la cuenta de almacenamiento. Se marcarán la cuenta de almacenamiento predeterminada y el contenedor predeterminado. También puede hacer clic con el botón secundario en cualquiera de los contenedores para ver el contenedor.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Ejecución de consultas de Hive
[Apache Hive][hdinsight.hive] es una infraestructura de almacenamiento de datos basada en Hadoop que proporciona resumen, consultas y análisis de datos. Las herramientas de HDInsight para Visual Studio admiten la ejecución de consultas de Hive desde Visual Studio. Para obtener más información acerca de Hive, consulte [Uso de Hive con HDInsight][hdinsight.hive].

###Ver la tabla hivesampletable Hive
Todos los clústeres de HDInsight incluyen una tabla Hive de ejemplo denominada *hivesampletable*. Utilizaremos esta tabla para mostrar cómo mostrar las tablas de Hive, ver los esquemas de tabla y mostrar las filas de la tabla de Hive.



**Para mostrar las tablas de Hive y ver el esquema de la tabla de Hive**

1.	Desde el **Explorador de servidores**, expanda **Azure**, **Clúster de HDInsight**, expanda el clúster, expanda **Bases de datos de Hive**, **Predeterminada** y, a continuación, expanda **hivesampletable** para ver el esquema de la tabla.
4.	Haga clic con el botón secundario en **hivesampletable** y, a continuación, haga clic en **Ver las primeras 100 filas** para mostrar las filas. Esta operación equivale a ejecutar la siguiente consulta de Hive usando el controlador ODBC de Hive:

		SELECT * FROM hivesampletable LIMIT 100

	Puede personalizar el recuento de filas. 
 
	![HDinsight Hive Visual Studio schema query][6]

###Crear tablas de Hive

Puede usar la interfaz gráfica de usuario para crear una tabla de Hive o usar las consultas de Hive. Para usar las consultas de Hive, consulte [Ejecutar consultas de Hive](#run.queries).

**Para crear una tabla de Hive**

1. Desde el **Explorador de servidores**, expanda **Azure**, **Clústeres de HDInsight**, expanda un clúster de HDInsight, expanda **Bases de datos de Hive**, haga clic con el botón secundario en **Predeterminada** y, a continuación, haga clic en **Crear tabla**.
2. Configure la tabla.
3. Haga clic en **Crear tabla** para enviar el trabajo de creación de la nueva tabla de Hive.

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>Ejecución de consultas de Hive
Existen dos formas de crear y ejecutar consultas de Hive:

- Crear consultas Ad hoc
- Crear una aplicación de Hive

**Para crear y ejecutar consultas ad hoc**

1. Desde el **Explorador de servicios**, expanda **Azure** y, a continuación, expanda **Clústeres de HDInsight**.
2. Haga clic en el clúster donde desea ejecutar la consulta y, a continuación, haga clic en **Escribir una consulta de Hive**. 
3. Especificación de consultas de Hive.
4. Haga clic en **Enviar** o en **Enviar (avanzado)**. Con el envío avanzado, configurará el **Nombre del trabajo**, los **Argumentos**, las **Configuraciones adicionales** y el **Estado de directorio** para la secuencia de comandos:

	![hdinsight hadoop hive query][9]

	Una vez enviado el trabajo, se mostrará la ventana **Resumen del trabajo de Hive**.

	![hdinsight hadoop hive query][8]
5. Utilice el botón **Actualizar** para actualizar el estado hasta que cambie a **Completado**.
6. Haga clic en los vínculos de la parte inferior para ver la **Consulta de trabajo**, el **Resultado del trabajo** y el **Registro de trabajo**.



**Para crear o ejecutar una solución de Hive**

1. En el menú **ARCHIVO**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.
2. Seleccione **HDInsight** en el panel izquierdo, seleccione **Aplicación Hive** en el panel central, especifique las propiedades y, a continuación, haga clic en **Aceptar**.
3. Desde el **Explorador de soluciones**, haga doble clic en **Script.hql** para abrirlo.

 
###Ver trabajos de Hive
Puede ver la consulta de trabajo, el resultado del trabajo y el registro de trabajo de todos los trabajos de Hive.

**Para ver los trabajos de Hive**

1. Desde el **Explorador de servidores**, expanda **Azure** y, a continuación, expanda **HDInsight**. 
2. Haga clic con el botón secundario del mouse en un clúster de HDInsight y, a continuación, haga clic en **Ver trabajos de Hive**. Se mostrará una lista de los trabajos de Hive que se ejecutaron en el clúster. 
3. Haga clic en un trabajo de la lista de trabajos para seleccionarlo y, a continuación, utilice la ventana **Resumen del trabajo de Hive** para abrir la **Consulta de trabajo**, el **Resultado del trabajo** o el **Registro de trabajo**.

## Pasos siguientes
En este artículo, ha aprendido a conectarse a clústeres de HDInsight desde Visual Studio y a ejecutar consultas de Hive. Para obtener más información, consulte:

- [Uso de Hive de Hadoop en HDInsight][hdinsight.hive]
- [Introducción al uso de Hadoop en HDInsight][hdinsight.get.started]
- [Envío de trabajos de Hadoop en HDInsight][hdinsight.submit.jobs]
- [Análisis de datos de Twitter con Hadoop en HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Instalación]: #installation
[Conexión a su suscripción de Azure]: #connect-to-your-azure-subscription
[Navegación por los recursos vinculados]: #navigate-the-linked-resources
[Ejecución de consultas de Hive]: #run-hive-queries
[Pasos siguientes]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/


[apache.hive]: http://hive.apache.org<!--HONumber=42-->

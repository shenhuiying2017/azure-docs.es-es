<properties
   pageTitle="Conector de HDInsight"
   description="Uso del conector de HDInsight"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Conector de Microsoft HDInsight #

Los conectores pueden utilizarse en aplicaciones lógicas para capturar, procesar o insertar datos como parte de un flujo. El conector de HDInsight le permite crear clústeres de Hadoop en Azure y enviar trabajos de Hadoop diferentes como Hive, Pig, MapReduce y MapReduce de streaming. El servicio de Azure HDInsight implementa y aprovisiona clústeres de Apache Hadoop en la nube con el fin de proporcionar un marco de software diseñado para realizar tareas de administración, análisis y generación de informes en relación con grandes volúmenes de datos. El núcleo de Hadoop proporciona almacenamiento de datos confiable con el sistema de archivos distribuido Hadoop (HDFS, Hadoop Distributed File System) y un sencillo modelo de programación MapReduce para procesar y analizar, en paralelo, los datos almacenados en este sistema distribuido. Mediante el conector de HDInsight puede crear o eliminar un clúster, enviar un trabajo y esperar a que el trabajo finalice.

###Acciones básicas

- Crear clúster
- Esperar a que se cree el clúster
- Enviar trabajo de Pig
- Enviar trabajo de Hive
- Enviar trabajo de MapReduce
- Esperar la finalización del trabajo
- Eliminar clúster


## Creación de una instancia de la aplicación de API del conector de HDInsight ##

Para usar el conector de HDInsight, deberá crear primero una instancia de la aplicación de API del conector de HDInsight. Se puede realizar del modo siguiente:

1. Abra Azure Marketplace mediante la opción “+NUEVO” en la parte izquierda del Portal de Azure y seleccione “Marketplace”.
2. Vaya a “Aplicaciones de API” y busque “Conector de HDInsight”, selecciónelo y haga clic en “Crear”.
3. Proporcione los detalles comunes, entre otros, el nombre y el plan del Servicio de aplicaciones, en la primera hoja.
4. Como parte de la configuración del paquete, proporcione el nombre de usuario y la contraseña del clúster de HDInsight y, a continuación, haga clic en “Aceptar”.
5. Haga clic en “Crear”.


 ![][1]

## Configuración del certificado (opcional) ##

Nota: Este paso solo se necesita si desea realizar operaciones de administración (creación y eliminación de clústeres) en la aplicación lógica.

Examine la aplicación de API del conector de HDInsight que acaba de crear y observará que en el componente “Seguridad” aparece 0, lo que significa que no hay ningún certificado de administración cargado.

![][2]

Para cargar el certificado de administración en la aplicación de API, debe hacer lo siguiente 1. Haga clic en el componente “Seguridad”. 2. En la hoja “Seguridad”, haga clic en "CARGAR CERTIFICADO". 3. Busque y seleccione el archivo de certificado en la hoja siguiente. 4. Una vez seleccionado el certificado, haga clic en Aceptar.

Cuando el certificado se ha cargado correctamente, se muestran los detalles del certificado.

![][3]

Nota: Si desea cambiar el certificado, puede cargar otro certificado que reemplazará al existente.

## Uso en una aplicación lógica ##

El conector de HDInsight puede usarse solo como acción de la aplicación lógica. Empecemos por una aplicación lógica sencilla que crea un clúster, ejecuta un trabajo “Hive” y elimina el clúster al final de la finalización del trabajo.


- En la ficha "Iniciar lógica", haga clic en "Ejecutar esta lógica manualmente".
- Seleccione la aplicación de API del conector de HDInsight creada desde la galería y, a continuación, se muestran todas las acciones disponibles.

![][5]


- Seleccione “Crear clúster”, proporcione todos los parámetros del clúster necesarios y haga clic en el signo ✓.

![][6]



- La acción aparecerá ahora como configurado en la aplicación lógica. Se mostrarán las salidas de la acción y se podrán utilizar como entradas en un paso posterior.

![][7]



- Seleccione el mismo conector de HDInsight de la Galería como una acción. Seleccione la acción “Esperar la creación de clústeres”, proporcione todos los parámetros necesarios y haga clic en el signo ✓.

![][8]



- Seleccione el mismo conector de HDInsight de la Galería como una acción. Seleccione la acción “Esperar trabajo de Hive”, proporcione todos los parámetros necesarios y haga clic en el signo ✓.

![][9]



- Seleccione el mismo conector de HDInsight de la Galería como una acción. Seleccione la acción “Esperar la finalización del trabajo”, proporcione todos los parámetros necesarios y haga clic en el signo ✓.

![][10]



- Seleccione el mismo conector de HDInsight de la Galería como una acción. Seleccione la acción “Eliminar clúster”, proporcione todos los parámetros necesarios y haga clic en el signo ✓.

![][11]

- Guarde la aplicación lógica con el comando habilitado para ello en la parte superior del diseñador.

Puede hacer clic en “Ejecutar ahora” para iniciar manualmente la aplicación lógica para probar el escenario.

<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg

<!---HONumber=July15_HO4-->
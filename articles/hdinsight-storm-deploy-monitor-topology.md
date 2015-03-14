<properties 
   pageTitle="Implementación y administración de topologías de Storm en HDInsight | Azure" 
   description="Aprenda a implementar, supervisar y administrar topologías de Storm mediante el panel de Storm incluido con Apache Storm en HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Implementación y administración de topologías de Apache Storm en HDInsight

El panel de Storm permite implementar y ejecutar fácilmente topologías de Apache Storm en el clúster de HDInsight mediante el explorador web. También puede utilizar el panel para supervisar y administrar topologías de ejecución. Si utiliza Visual Studio, las herramientas de HDInsight para Visual Studio proporcionan características similares en Visual Studio.

Tanto el panel de Storm como las características de Storm de las herramientas de HDInsight dependen de la API de REST de Storm, que se puede usar para crear sus propias soluciones de supervisión y administración.

## Requisitos previos

* **Apache Storm en HDInsight**: consulte <a href="../hdinsight-storm-getting-started/" target="_blank">Introducción a Apache Storm en HDInsight</a> para obtener información acerca de la creación de un clúster

* Para el **panel de Storm**: un explorador web moderno compatible con HTML5

* Para **Visual Studio**: Azure SDK 2.5.1 o versiones más recientes y las herramientas de HDInsight para Visual Studio. Consulte <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Introducción al uso de herramientas de HDInsight para Visual Studio</a> para instalar y configurar las herramientas de HDInsight para Visual Studio.

	Una de las siguientes versiones de Visual Studio:

	* Visual Studio 2012 con <a href="http://www.microsoft.com/es-es/download/details.aspx?id=39305" target="_blank">Actualización 4</a>

	* Visual Studio 2013 con <a href="http://www.microsoft.com/es-es/download/details.aspx?id=44921" target="_blank">Actualización 4</a> o <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Comunidad de Visual Studio 2013</a>

	* <a href="http://visualstudio.com/es-es/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] Actualmente las herramientas de HDInsight para Visual Studio solo admiten Storm en el clúster de HDInsight versión 3.2.

## Panel de Storm

El panel de Storm está disponible en el clúster de Storm. La dirección URL es **https://&lt;clustername>.azurehdinsight.net/**, donde **clustername** es el nombre del clúster de Storm en HDInsight. También puede tener acceso al panel mediante el vínculo al **panel de Storm** desde el panel del clúster en el Portal de Azure.

![the portal with storm dashboard highlighted][hdinsight-dashboard]

En la parte superior del panel de Storm, seleccione **Enviar topología**. Siga las instrucciones de la página para ejecutar una topología de muestra o cargar y ejecutar una topología que haya creado.

![the submit topology page][storm-dashboard-submit]

### UI de Storm

En el panel de Storm, seleccione el vínculo **UI de Storm**. Se mostrará información sobre el clúster, así como las topologías de ejecución.

![the storm ui][storm-dashboard-ui] 

#### Página principal

La página principal de la interfaz de usuario de Storm ofrece la siguiente información.

* **Cluster Summary** (Resumen del clúster): información básica sobre el clúster de Storm

* **Topology summary** (Resumen de la topología): una lista de las topologías en ejecución Use los vínculos de esta sección para obtener más información acerca de las topologías específicas

* **Supervisor summary** (Resumen de supervisor): información sobre el supervisor de Storm

* **Nimbus Configuration** (Configuración de Nimbus): configuración de Nimbus para el clúster

#### Resumen de la topología

Si selecciona un vínculo desde la sección **Topology summary** (Resumen de la topología), se mostrará la siguiente información acerca de la topología.

* **Topology summary** (Resumen de la topología): información básica acerca de la topología

* **Topology actions** (Acciones de topología): acciones de administración que puede realizar para la topología

	* **Activate** (Activar): reanuda el procesamiento de una topología desactivada
	
	* **Deactivate** (Desactivar): pausa una topología en ejecución
	
	* **Rebalance** (Reequilibrar): ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de los nodos del clúster
	
		Para obtener más información, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Descripción del paralelismo de una topología de Storm</a>
	
	* **Kill** (Eliminar): finaliza una topología de Storm tras el tiempo de espera especificado

* **Topology stats** (Estadísticas de topología): estadísticas acerca de la topología Use los vínculos de la columna **Window** (Ventana) para establecer el plazo de tiempo para las entradas restantes en la página.

* **Spouts**: los spouts utilizados por la topología. Utilice los vínculos de esta sección para obtener más información acerca de spouts específicos

* **Bolts**: los bolts usados por la topología. Utilice los vínculos de esta sección para obtener más información acerca de bolts específicos

* **Topology configuration** (Configuración de la topología): la configuración de la topología seleccionada

#### Resumen de spouts y bolts

Si se selecciona un spout en la sección **Spouts** o **Bolts**, se mostrará la siguiente información acerca del elemento seleccionado.

* **Component summary** (Resumen de componentes): información básica acerca del spout o bolt

* **Spout/Bolt stats** (Estadísticas de spouts/bolts): las estadísticas sobre el spout o bolt. Utilice los vínculos de la columna **Window** (Ventana) para establecer el plazo de tiempo para las entradas restantes de la página

* **Input stats** (Estadísticas de entrada) (solo bolt): información sobre las secuencias de entrada consumidas por el bolt

* **Output stats** (Estadísticas de salida): información sobre las secuencias emitidas por este spout o bolt

* **Executors** (Ejecutores): información sobre las instancias del spout o bolt. Seleccione la entrada **Port** (Puerto) para un ejecutor específico para ver un registro de información de diagnóstico generado por esta instancia

* **Errors** (Errores): cualquier información de error para este spout o bolt.

## Herramientas de HDInsight para Visual Studio

Las herramientas de HDInsight puede utilizarse para enviar las topologías de C# o híbridas al clúster de Storm. Los pasos siguientes usan una aplicación de muestra. Para obtener información acerca de cómo crear sus propias topologías mediante las herramientas de HDInsight, consulte [Desarrollo de las topologías de C# mediante las herramientas de HDInsight para Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/).

Utilice los siguientes pasos para implementar una muestra en el clúster de Storm en HDInsight y, a continuación, ver y administrar la topología.

1. Si no ha instalado la versión más reciente de las herramientas de HDInsight para Visual Studio, consulte <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Introducción al uso de herramientas de HDInsight para Visual Studio</a>.

2. Abra Visual Studio, seleccione **Archivo**, **Nuevo** y, a continuación, **Proyecto**.

3. Desde el cuadro de diálogo **Nuevo proyecto**, expanda **Instalado**, **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Muestra de Storm**. En la parte inferior del cuadro de diálogo, escriba un nombre para la aplicación.

	![image](./media/hdinsight-storm-deploy-monitor/sample.png)

1. En el **Explorador de soluciones**, haga doble clic en el proyecto y seleccione **Enviar a Storm en HDInsight**.

	> [AZURE.NOTE] Si se le pide, introduzca las credenciales de inicio de sesión de la suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la que contiene el clúster de Storm en HDInsight.

2. Seleccione el clúster de Storm en HDInsight desde el menú desplegable **Clúster de Storm** y, a continuación, seleccione **Enviar**. Puede supervisar si la presentación es correcta o no mediante la ventana **Salida**.

3. Una vez que se ha enviado correctamente la topología, debe aparecer **Topologías de Storm** para el clúster. Seleccione la topología de la lista para ver información acerca de la topología de ejecución.

	![visual studio monitor](./media/hdinsight-storm-deploy-monitor/vsmonitor.png)

	> [AZURE.NOTE] También puede ver las **topologías de Storm** del **Explorador de servidores** expandiendo **Azure**, **HDInsight** y, a continuación, haga clic con el botón derecho en un clúster de Storm en HDInsight y seleccione **Ver topologías de Storm**.

	Utilice los vínculos de los spouts o bolts para ver información acerca de estos componentes. Se abrirá una ventana nueva para cada elemento seleccionado.

4. Desde la vista **Topology Summary** (Resumen de la topología), seleccione **Kill** (Eliminar) para detener la topología.

	> [AZURE.NOTE] Topologías de Storm continúan ejecutándose hasta que se eliminen o se elimine el clúster.

## API de REST

La interfaz de usuario de Storm se basa en la API de REST, lo que permite realizar una funcionalidad similar de administración y supervisión mediante la API. Mediante la API de REST, puede crear herramientas personalizadas para administrar y supervisar las topologías de Storm.

La API de REST de Storm se documenta en <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md</a>. La siguiente información es específica para usar la API de REST con Apache Storm en HDInsight.

### URI base

El URI base para la API de REST en los clústeres de HDInsight es **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**, donde **clustername** es el nombre del clúster de Storm en HDInsight.

### Autenticación

Las solicitudes a la API de REST deben usar la **autenticación básica** con el nombre y la contraseña de administrador del clúster de HDInsight. 

> [AZURE.NOTE] Puesto que la autenticación básica se envía con texto no cifrado, **siempre** debe usar HTTPS para proteger las comunicaciones con el clúster.

### Valores devueltos

La información devuelta por la API de REST solo se puede utilizar desde dentro del clúster o de equipos en la misma red virtual de Azure que el clúster. Por ejemplo, no se podrá obtener el acceso desde Internet al nombre de dominio completo (FQDN) devuelto para servidores de Zookeeper.

## Pasos siguientes

Ahora que ha aprendido a implementar y supervisar topologías mediante el panel de Storm, aprenda a [desarrollar las topologías de C# mediante las herramientas de HDInsight para Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/), o cómo [desarrollar topologías basadas en Java con Maven](../hdinsight-storm-develop-java-topology/).


[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor/storm-ui-summary.png

<!--HONumber=45--> 

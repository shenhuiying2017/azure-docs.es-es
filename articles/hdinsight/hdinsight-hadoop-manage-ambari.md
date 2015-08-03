<properties
   pageTitle="Administración de clústeres de HDInsight con Ambari | Microsoft Azure"
   description="Aprenda a usar Ambari para supervisar y administrar clústeres de HDInsight basado en Linux."
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
   ms.date="07/01/2015"
   ms.author="larryfr"/>

#Administración de clústeres de HDInsight con Ambari (vista previa)

Aprenda a usar Ambari para administrar y supervisar clústeres de HDInsight de Azure basado en Linux.

> [AZURE.NOTE]Gran parte de la información de este artículo solo se aplica a clústeres de HDInsight basado en Linux. En el caso de los clústeres de HDInsight basado en Windows, solo se encuentra disponible la supervisión a través de la API de REST de Ambari. Consulte [Supervisión de Hadoop en HDInsight basado en Windows con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

##<a id="whatis"></a> ¿Qué es Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> simplifica la administración de Hadoop al proporcionar una interfaz de usuario web fácil de usar que se puede utilizar para aprovisionar, administrar y supervisar clústeres de Hadoop. Los desarrolladores pueden integrar estas funcionalidades en sus aplicaciones mediante el uso de las <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API de REST de Ambari</a>.

De manera predeterminada, Ambari viene con los clústeres de HDInsight basado en Linux. Los clústeres de HDInsight basado en Windows proporcionan la funcionalidad de supervisión a través de las API de REST de Ambari.

##Proxy SSH

> [AZURE.NOTE]A pesar de que es posible tener acceso directamente a través de Internet a Ambari para su clúster, algunos vínculos de la interfaz de usuario web de Ambari (como JobTracker) no estarán expuestos en Internet. Por lo tanto, recibirá errores de "servidor no encontrado" al intentar tener acceso a estas características, a menos que utilice un túnel de Shell seguro (SSH) para canalizar el tráfico web para el nodo principal del clúster.

Use los siguientes artículos para crear un túnel SSH desde un puerto en la máquina local al clúster:

* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Utilización de SSH en HDInsight basado en Linux desde Linux, Unix u OS X</a>: pasos para crear un túnel SSH con el comando `ssh`.

* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows</a>: pasos para usar PuTTY con el fin de crear un túnel SSH.

##Interfaz de usuario web de Ambari

La interfaz de usuario web de Ambari está disponible en cada clúster de HDInsight basado en Linux que crea en **https://&lt;clustername>.azurehdinsight.net**. También puede llegar a esta página si usa el botón **Ambari Web** que se encuentra en la parte inferior del panel del clúster en el portal de Azure.

![icono de ambari web](./media/hdinsight-hadoop-manage-ambari/ambari-web.png)

Se le pedirá autenticarse en la página dos veces. La primera solicitud es para autenticarse en el clúster de HDInsight, mientras que la segunda es para autenticarse en Ambari.

* **Autenticación de clúster**: use el nombre de usuario del administrador del clúster (el valor predeterminado es **admin**) y la contraseña.

* **Autenticación de Ambari**: el valor predeterminado del nombre de usuario y la contraseña es **admin**.

	> [AZURE.NOTE]Si cambió la contraseña del usuario **admin**, debe escribir la contraseña nueva.

Cuando se abra la página, observe la barra que se encuentra en la parte superior. Contiene la siguiente información y controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logotipo de Ambari**: abre el panel, que puede usarse para supervisar el clúster.

* **Cluster name # ops**: muestra el número de operaciones de Ambari en curso. Seleccione el nombre del clúster o **# ops** para ver una lista de las operaciones en segundo plano.

* **# alerts**: alertas de advertencia o alertas críticas del clúster, si las hay. Seleccione para ver una lista de las alertas.

* **Dashboard**: muestra el panel.

* **Services**: información y ajustes de configuración de los servicios en el clúster.

* **Hosts**: información y ajustes de configuración de los nodos del clúster.

* **Alerts**: un registro de información, advertencias y alertas críticas.

* **Admin**: servicios/pila de software que están instalados o que se pueden agregar al clúster, información de la cuenta de servicio y seguridad Kerberos.

* **Botón Admin**: administración de Ambari, configuración del usuario y cierre de sesión.

##Supervisión

###Alertas

Ambari proporciona muchas alertas, las que tendrán uno de los siguientes estados:

* **OK** (CORRECTO)

* **Warning** (ADVERTENCIA)

* **CRITICAL** (CRÍTICA)

* **UNKNOWN** (DESCONOCIDO)

Las alertas, con la excepción de **OK**, harán que la entrada **# alerts** en la parte superior de la página muestre el número de alertas. Seleccione esta entrada para ver las alertas y sus estados.

Las alertas se organizan en varios grupos predeterminados, que se pueden ver desde la página **Alertas**.

![página de alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Puede administrar los grupos mediante el menú **Actions** (Acciones) y seleccionando **Manage Alert Groups** (Administración de grupos de alertas). Esta acción le permite modificar los grupos existentes o crear grupos nuevos.

![administrar cuadro de diálogo de grupos de alertas](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

También puede crear notificaciones de alerta en el menú **Actions**. Esto le permite crear desencadenadores que envían notificaciones a través de **CORREO ELECTRÓNICO** o **SNMP** cuando se produce una combinación específica de alerta/gravedad. Por ejemplo, puede enviar una alerta cuando alguna de las alertas del grupo **YARN Default** (Valor predeterminado de YARN) está definida en **Critical** (Crítica).

![cuadro de diálogo Crear alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###Clúster

La pestaña **Metrics** (Métricas) del panel contiene una serie de widgets que facilitan la supervisión del estado del clúster de un solo vistazo. Varios widgets, como **CPU Usage** (Uso de CPU), proporcionan información adicional al hacer clic en ellos.

![panel con métricas](./media/hdinsight-hadoop-manage-ambari/metrics.png)

La pestaña **Heatmaps** (Mapas térmicos) muestran las métricas como mapas térmicos coloreados, desde el verde hasta el rojo.

![panel con mapas térmicos](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obtener información más detallada sobre los nodos dentro del clúster, seleccione **Hosts** y, a continuación, seleccione el nodo específico que le interesa.

![detalles del host](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###Servicios

La barra lateral **Services** (Servicios) del panel proporciona información rápida del estado de los servicios que se ejecutan en el clúster. Se usan varios iconos para indicar el estado o las acciones que se debieran realizar, como un símbolo de reciclaje de color amarillo si es necesario reciclar un servicio.

![barra lateral de servicios](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Seleccione un servicio para ver información más detallada sobre el servicio.

![información de resumen de servicio](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####Vínculos rápidos

Algunos servicios muestran un vínculo **Quick Links** (Vínculos rápidos) en la parte superior de la página. Este vínculo puede usarse para tener acceso a interfaces de usuario web específicas del servicio, como:

* **Job History**: el historial de trabajos de MapReduce.

* **Resource Manager**: la interfaz de usuario del administrador de recursos de YARN.

* **NameNode**: la interfaz de usuario de NameNode del Sistema de archivos distribuido de Hadoop (HDFS).

* **Oozie Web UI**: interfaz de usuario de Oozie.

Seleccione cualquiera de estos vínculos para abrir una pestaña nueva del explorador, donde aparecerá la página seleccionada.

> [AZURE.NOTE]Al seleccionar un vínculo **Quick Links** para cualquier servicio se generará un error de "servidor no encontrado", a menos que use un túnel de Capa de sockets seguros (SSL) para autorizar el tráfico web al clúster. Esto es porque las aplicaciones web que se usan para mostrar esta información no se exponen en Internet.
>
> Para obtener información sobre el uso de un túnel SSL con HDInsight, consulte uno de los siguientes elementos:
>
> * <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Utilización de SSH en HDInsight basado en Linux desde Linux, Unix u OS X</a>: pasos para crear un túnel SSH con el comando `ssh`.
>
>* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows</a>: pasos para usar PuTTY con el fin de crear un túnel SSH.

##Administración

###Usuarios, grupos y permisos de Ambari

La administración de usuarios, grupos y permisos no se debe usar durante la vista previa de HDInsight basado en Linux.

###Hosts

La página **Hosts** muestra todos los hosts existentes en el clúster. Siga estos pasos para administrar los hosts.

![página de hosts](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE]No se debe agregar, retirar o volver a programar un host con los clústeres de HDInsight.

1. Seleccione los hosts que desee administrar.

2. Use el menú **Actions** para seleccionar la acción que desea realizar:

	* **Start all components**: inicie todos los componentes en el host.

	* **Stop all components**: detenga todos los componentes en el host.

	* **Restart all components**: detenga e inicie todo los componentes en el host.

	* **Turn on maintenance mode**: suprime las alertas del host. Esta opción debe habilitarse si realiza acciones que generarán alertas, como reiniciar un servicio del que deben servicios en ejecución.

	* **Turn off maintenance mode**: devuelve el host a su estado de alerta normal.

	* **Stop**: detiene DataNode o NodeManagers en el host.

	* **Start**: inicia DataNode o NodeManagers en el host.

	* **Restart**: detiene e inicia DataNode o NodeManagers en el host.

	* **Decommission**: quita un host del clúster.

		> [AZURE.NOTE]No use esta acción en clústeres de HDInsight.

	* **Recommission**: agrega un host anteriormente retirado al clúster.

		> [AZURE.NOTE]No use esta acción en clústeres de HDInsight.

###<a id="service"></a>Servicios

Desde la página **Dashboard** (Panel) o **Services** (Servicios), use el botón **Actions** que se encuentra en la parte inferior de la lista de servicios para agregar servicios nuevos o para detener e iniciar todos los servicios.

![acciones de servicio](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

Los siguientes son los pasos generales para agregar un servicio:

1. En la página **Dashboard** o **Services**, use el botón **Actions** y seleccione **Add Service** (Agregar servicio).

2. En **Add Service Wizard** (Asistente para agregar un servicio), seleccione el servicio que se va a agregar y, a continuación, haga clic en **Next** (Siguiente).

	![agregar servicio](./media/hdinsight-hadoop-manage-ambari/add-service.png)

3. Siga con el asistente y brinda la información de configuración del servicio. Consulte la documentación sobre el servicio específico que está agregando para obtener más información sobre los requisitos de configuración.

4. En la página **Review** (Revisión), puede usar la opción **Print** (Imprimir) para imprimir la información de configuración o la opción **Deploy** (Implementar) para implementar el servicio en el clúster.

5. Una vez que se ha implementado el servicio, la página **Install, Start and Test** (Instalar, iniciar y probar) mostrará la información de progreso a medida que se instala y prueba el servicio. Una vez que el indicador **Status** (Estado) esté de color verde, seleccione **Next** (Siguiente).

	![imagen de página instalar, iniciar y probar](./media/hdinsight-hadoop-manage-ambari/install-start-test.png)

6. La página **Summary** (Resumen) muestra un resumen del proceso de instalación, además de cualquier acción posible que deba realizar; por ejemplo, el reinicio de otros servicios. Seleccione **Complete** (Completar) para salir del asistente.

A pesar de que el botón **Actions** puede reiniciar todos los servicios, con frecuencia se desea iniciar, detener o reiniciar un servicio específico. Use los siguientes pasos para realizar acciones sobre un servicio individual:

1. En la página **Dashboard** o **Services**, seleccione un servicio.

2. En la parte superior de la pestaña **Summary** (Resumen), use el botón **Service Actions** (Acciones de servicio) y seleccione la acción que se realizará. Con esto se reiniciará el servicio en todos los nodos.

	![acción de servicio](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE]Reiniciar algunos servicios mientras el clúster está en ejecución puede generar alertas. Para evitarlo, puede usar el botón **Service Actions** (Acciones de servicio) para habilitar el **modo de mantenimiento** del servicio antes de realizar el reinicio.

3. Una vez que se selecciona una acción, la entrada **# op** que aparece en la parte superior de la página aumentará para mostrar que se está produciendo una operación en segundo plano. La lista de operaciones en segundo plano aparecerá si está configurada de ese modo.

	> [AZURE.NOTE]Si habilitó el **modo de mantenimiento** para el servicio, recuerde deshabilitarlo con el botón **Service Actions** una vez finalizada la operación.

Para configurar un servicio, use los siguientes pasos:

1. En la página **Dashboard** o **Services**, seleccione un servicio.

2. Seleccione la pestaña **Configs** (Configuraciones). Aparecerá la configuración actual. También aparecerá una lista de las configuraciones anteriores.

	![configuraciones](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use los campos que aparecen para modificar la configuración y, a continuación, seleccione **Save** (Guardar). O bien, seleccione una configuración anterior y, a continuación, seleccione **Make current** (Convertir en actual) para volver a la configuración anterior.

##API de REST

Ambari Web se basa en una API de REST subyacente, que puede aprovechar para crear sus propias herramientas de administración y supervisión. A pesar de que la API es relativamente simple de usar, debe tener en cuenta algunos puntos específicos de Azure:

* **Autenticación**: el nombre de usuario del administrador del clúster (valor predeterminado **admin**) y la contraseña se deben usar para autenticarse en el servicio.

* **Seguridad**: Ambari usa autenticación básica, por lo que debe siempre usar HTTP seguro (HTTPS) cuando se comunique con la API.

* **Direcciones IP**: no es posible tener acceso a la dirección devuelta para los hosts dentro de un clúster desde afuera del clúster, a menos que este sea miembro de una Red virtual de Azure. De este modo, otros miembros de la red virtual podrán tener acceso a la dirección IP, pero no desde fuera de la red.

* **Alguna funcionalidad no está habilitada**: alguna funcionalidad de Ambari está deshabilitada, puesto que está administrada por el servicio en la nube de HDInsight; por ejemplo, agregar o quitar hosts desde el clúster. Es posible que no se implemente completamente otra funcionalidad durante la vista previa de HDInsight basado en Linux.

Para obtener una referencia completa de la API de REST, consulte [Referencia de API de Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

<!---HONumber=July15_HO4-->
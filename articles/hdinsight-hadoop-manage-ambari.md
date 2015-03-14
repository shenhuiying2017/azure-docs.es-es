<properties
   pageTitle="Administración de clústeres de HDInsight con Ambari | Aure"
   description="Aprenda a usar Ambari para supervisar y administrar clústeres de HDInsight basado en Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Administración de clústeres de HDInsight con Ambari (vista previa)

Aprenda a usar Ambari para administrar y supervisar clústeres de HDInsight basado en Linux.

> [AZURE.NOTE] Gran parte de la información de este artículo solo se aplica a clústeres de HDInsight basado en Linux. En el caso de los clústeres de HDInsight basado en Windows, solo se encuentra disponible la supervisión a través de la API de REST de Ambari. Consulte [Supervisión de Hadoop en HDInsight basado en Windows con la API de Ambari](../hdinsight-monitor-use-ambari-api/).

## <a id="whatis"></a>¿Qué es Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> simplifica la administración de Hadoop al proporcionar una interfaz de usuario web fácil de usar y que se puede utilizar para aprovisionar, administrar y supervisar clústeres de Hadoop. Los desarrolladores pueden integrar estas funcionalidades en sus aplicaciones mediante el uso de las <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API de REST de Ambari</a>. 

De manera predeterminada, Ambari viene con los clústeres de HDInsight basado en Linux. Los clústeres de HDInsight basado en Windows proporcionan la funcionalidad de supervisión a través de las API de REST de Ambari.

## Proxy SSH

> [AZURE.NOTE] A pesar de que es posible tener acceso directamente a través de Internet a Ambari para su clúster, cierta funcionalidad se basa en tener acceso a nodos a través del nombre de dominio interno que usa el clúster. Debido a que se trata de un nombre de dominio interno y no público, recibirá errores de servidor no encontrado al intentar tener acceso a algunas características a través de Internet.

Para evitar este problema, utilice un túnel SSH para canalizar el tráfico web para el nodo principal del clúster, que se puede resolver correctamente los nombres de dominio interno. Use los siguientes artículos para crear un túnel SSH desde un puerto en la máquina local al clúster.

* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X</a> : pasos para crear un túnel SSH con el comando `ssh`.

* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows</a> : pasos para usar PuTTY para crear un túnel SSH.

## Interfaz de usuario web de Ambari

La interfaz de usuario web de Ambari está disponible en cada clúster de HDInsight basado en Linux que crea en **https://&lt;nombre del clúster>.azurehdinsight.net**. También puede llegar a esta página si usa el botón **Ambari Web** que se encuentra en la parte inferior del panel del clúster en el portal de Azure.

![ambari web icon](./media/hdinsight-hadoop-manage-ambari/ambari-web.png)

Se le pedirá que se autentique dos veces en la página: la primera solicitud es para que se autentique en el clúster de HDInsight, mientras que la segunda solicitud, para que se autentique en Ambari.

* **Autenticación de clúster**: use el nombre de usuario del administrador del clúster (el valor predeterminado es **admin**) y la contraseña.

* **Autenticación de Ambari**: el valor predeterminado del nombre de usuario y la contraseña es **admin**.

	> [AZURE.NOTE] Si cambió la contraseña del usuario **admin**, debe escribir la contraseña nueva.

Cuando se abra la página, observe la barra que se encuentra en la parte superior; esta barra contiene la siguiente información y los siguientes controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logotipo de Ambari**: abre el **panel**.

* **Nombre del clúster y # ops**: muestra el número de operaciones de Ambari en curso. Seleccione el nombre del clúster o el **número de operaciones** para ver una lista de las operaciones en segundo plano.

* **# alerts**: alertas de advertencia o alertas críticas del clúster, si las hay. Seleccione para ver una lista de las alertas.

* **Panel**: muestra el panel, que se puede usar para supervisar el clúster.

* **Servicios**: información y ajustes de configuración de los servicios en el clúster.

* **Hosts**: información y ajustes de configuración de los nodos del clúster.

* **Alertas**: lista de alertas de información, de advertencia y críticas.

* **Admin**: servicios/pila de software que están instalados o que se pueden agregar al clúster, información de la cuenta de servicio y seguridad Kerberos.

* **Botón Admin**: administración de Ambari, configuración del usuario y cierre de sesión.

### Supervisión

#### Alertas

Ambari proporciona muchas alertas, las que tendrán uno de los siguientes estados:

* **OK** (Correcto)

* **Warning** (Advertencia)

* **CRITICAL** (CRÍTICA)

* **UNKNOWN** (DESCONOCIDO)

Las alertas, con la excepción de **OK**, harán que la entrada **# alerts** en la parte superior de la página muestre el número de alertas. Seleccione esta entrada para ver las alertas y sus estados.

Las alertas se organizan en varios grupos predeterminados, que se pueden ver desde la página **Alertas**. 

![alerts page](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Puede administrar los grupos mediante el menú **Actions** (Acciones) y seleccionando **Manage Alert Groups** (Administración de grupos de alertas). Esta acción le permite modificar los grupos existentes o crear grupos nuevos.

![manage alert groups dialog](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

También puede crear **notificaciones de alerta** en el menú **Actions**. Esto le permite crear desencadenadores que envían notificaciones a través de **correo electrónico** o **SNMP** cuando se produce una combinación específica de alerta/gravedad. Por ejemplo, puede enviar una alerta cuando alguna de las alertas del grupo **YARN Default** (Valor predeterminado de YARN) está definida en **Crítica**.

![Create alert dialog](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

#### Clúster

La pestaña **Métricas** del **panel** contiene una serie de widgets que facilitar la supervisión del estado del clúster de un solo vistazo. Varios widgets, como **Uso de CPU**, proporcionan información adicional al hacer clic en ellos.

![dashboard with metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

La pestaña **Mapas térmicos** muestran las métricas como mapas térmicos coloreados, desde el verde hasta el rojo.

![dashboard with heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obtener información más detallada sobre los nodos dentro del clúster, seleccione **Hosts** y, a continuación, seleccione el nodo específico que le interesa.

![host details](./media/hdinsight-hadoop-manage-ambari/host-details.png)

#### Servicio

La barra lateral **Servicios** del panel proporciona información rápida del estado de los servicios que se ejecutan en el clúster. Se usan varios iconos para indicar el estado o las acciones que se debieran realizar, como un símbolo de reciclaje de color amarillo si es necesario reciclar un servicio.

![services side-bar](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Seleccione un servicio para ver información más detallada sobre el servicio.

![service summary information](./media/hdinsight-hadoop-manage-ambari/service-details.png)

##### Vínculos rápidos

Algunos servicios muestran un vínculo de **Vínculos rápidos** en la parte superior de la página. Este vínculo puede usarse para tener acceso a una interfaz de usuario web específica del servicio, como:

* **Historial de servicios**: historial de los trabajos de MapReduce.

* **Administrador de recursos**: interfaz de usuario del administrador de recursos de YARN.

* **NameNode**: interfaz de usuario de HDFS NameNode.

* **Interfaz de usuario web de Oozie**: interfaz de usuario de Oozie.

Seleccione cualquiera de estos vínculos para abrir una pestaña nueva del explorador, donde aparecerá la página seleccionada.

> [AZURE.NOTE] Al seleccionar un vínculo de **Vínculos rápidos** para cualquier servicio generará un error de servidor no encontrado, a menos que use un túnel SSL para autorizar el tráfico web al clúster. Esto se produce porque Ambari usa el nombre de dominio interno para estos vínculos.
> 
> Para obtener información sobre cómo usar el túnel SSL con HDInsight, consulte uno de los siguientes elementos.
> 
> * <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X</a> : pasos para crear un túnel SSH con el comando `ssh`.
>
>* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows</a> : pasos para usar PuTTY para crear un túnel SSH.

### Administración

#### Usuarios, grupos y permisos de Ambari

La administración de usuarios, grupos y permisos no se debe usar durante la vista previa de HDInsight basado en Linux.

#### Host

La página **Hosts** muestra todos los hosts existentes en el clúster. Siga estos pasos para administrar los hosts.

![hosts page](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] No se debe agregar, retirar o volver a programar un host con los clústeres de HDInsight.

1. Seleccione los hosts que desee administrar.

2. Use el menú **Actions** para seleccionar la acción que desea realizar.

	* **Start all components** (Iniciar todos los componentes): inicie todos los componentes en el host.

	* **Stop all components** (Detener todos los componentes): detenga todos los componentes en el host.

	* **Restart all components** (Reiniciar todos los componentes): detenga y vuelva a iniciar todos los componentes en el host.

	* **Turn on maintenance mode** (Activar el modo de mantenimiento): suprime las alertas del host. Esta opción debiera estar habilitada si realiza acciones que generarán alertas, como el reinicio de un servicio que ejecuta servicios que se basan en este.

	* **Turn off maintenance mode** (Desactivar el modo de mantenimiento): devuelve el host a su estado de alerta normal.

	* **Stop** (Detener): detiene DataNode o NodeManagers en el host.

	* **Start** (Iniciar): inicia DataNode o NodeManagers en el host.

	* **Restart** (Reiniciar): detiene e inicia DataNode o NodeManagers en el host.

	* **Decommission** (Retirar): quita un host del clúster.

		> [AZURE.NOTE] No use esta acción en clústeres de HDInsight.

	* **Recommission** (Volver a programar): agrega un host anteriormente retirado al clúster.

		> [AZURE.NOTE] No use esta acción en clústeres de HDInsight.

#### <a id="service"></a>Servicio

Desde la página **Panel** o **Servicios**, use el botón **Acciones** que se encuentra en la parte inferior de la lista de servicios para **agregar** servicios nuevos o **detener** e **iniciar** todos los servicios.

![service actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

Los siguientes son los pasos generales para **agregar un servicio**:

1. En la página **Panel** o **Servicios**, use el botón **Acciones** y seleccione **Agregar servicio**.

2. En el **asistente para agregar un servicio**, seleccione el servicio que se va a agregar y, a continuación, haga clic en **Siguiente**.

	![add service](./media/hdinsight-hadoop-manage-ambari/add-service.png)

3. Siga con el asistente y brinda la información de configuración del servicio. Consulte la documentación sobre el servicio específico que está agregando para obtener más información sobre los requisitos de configuración.

4. En la página **Review** (Revisión) puede **imprimir** la información de configuración o **implementar** el servicio en el clúster.

5. Una vez que se ha implementado el servicio, la página **Instalar, iniciar y probar** mostrará la información de progreso a medida que se instala y prueba el servicio. Una vez que el **estado** esté de color verde, seleccione **Siguiente**.

	![image of install, start, and test page](./media/hdinsight-hadoop-manage-ambari/install-start-test.png)

6. La página **Summary** (Resumen) muestra un resumen del proceso de instalación, además de cualquier acción posible que deba realizar. Por ejemplo, reiniciar otros servicios. Seleccione **Complete** (Completar) para salir del asistente.

A pesar de que el botón **Acciones** puede reiniciar todos los servicios, con frecuencia se desea iniciar, detener o reiniciar un servicio específico. Use los siguientes pasos para **realizar acciones sobre un servicio individual**:

1. En la página **Panel** o **Servicios**, seleccione un servicio.

2. En la parte superior de la pestaña **Resumen**, use el botón **Acciones de servicio** y seleccione la acción que se realizará. Con esto se reiniciará el servicio en todos los nodos.

	![service action](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE] Reiniciar algunos servicios mientras el clúster está en ejecución puede generar alertas. Para evitarlo, puede usar el botón **Acciones de servicio** para habilitar el **modo de mantenimiento** del servicio antes de realizar el reinicio.

3. Una vez que se selecciona una acción, la entrada **# op** que aparece en la parte superior de la página aumentará para mostrar que se está produciendo una operación en segundo plano. La lista de operaciones en segundo plano aparecer´si está configurada de ese modo.

	> [AZURE.NOTE] Si habilitó el **modo de mantenimiento** para el servicio, recuerde deshabilitarlo con el botón **Acciones de servicio** una vez finalizada la operación.

Para **configurar un servicio**, use los siguientes pasos:

1. En la página **Panel** o **Servicios**, seleccione un servicio.

2. Seleccione la pestaña **Configs** (Configuraciones). Aparecerá la configuración actual. También aparecerá una lista de las configuraciones anteriores.

	![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use los campos que aparecen para modificar la configuración y, a continuación, seleccione **Guardar**. O bien, seleccione una configuración anterior y luego **Make current** (Convertir en actual) para volver a la configuración anterior.

## API de REST

Ambari Web se basa en una API de REST subyacente, que puede aprovechar para crear sus propias herramientas de administración y supervisión. A pesar de que la API es relativamente simple de usar, debe tener en cuenta algunos puntos específicos de Azure.

* **Autenticación**: el nombre de usuario del administración del clúster (valor predeterminado: **admin**)
*  y la contraseña se deben usar para autenticarse con el servicio.

* **Seguridad**: Ambari usa una autenticación básica, por lo que siempre debe usar HTTPS cuando se comunique con la API.

* **Direcciones IP**: no es posible tener acceso a la dirección devuelta para los hosts dentro de un clúster desde afuera del clúster, a menos que este sea miembro de una Red virtual de Azure. De este modo, otros miembros de la red virtual podrán tener acceso a la dirección IP, pero no desde fuera de la red.

* **Cierta funcionalidad no se encuentra habilitada**: cierta funcionalidad de Ambari se encuentra deshabilitada y es administrada por el servicio en la nube de HDInsight. Por ejemplo, agregar o quitar hosts desde el clúster. Es posible que no se implemente completamente otra funcionalidad durante la vista previa de HDInsight basado en Linux.

Para obtener una referencia completa de la API de REST, consulte [Referencia de API de Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).


<!--HONumber=45--> 

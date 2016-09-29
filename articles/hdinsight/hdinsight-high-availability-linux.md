<properties
	pageTitle="Características de alta disponibilidad de HDInsight (Hadoop) basado en Linux | Microsoft Azure"
	description="Obtenga información acerca de cómo los clústeres de HDInsight basados en Linux mejoran la confiabilidad y la disponibilidad mediante el uso de un nodo principal adicional. Aprenderá cómo esto afecta a los servicios de Hadoop como Ambari y Hive, y cómo conectarse individualmente a cada nodo mediante SSH."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="larryfr"/>

#Disponibilidad y fiabilidad de clústeres de Hadoop en HDInsight

Hadoop logra una alta disponibilidad y confiabilidad mediante la distribución de copias redundantes de los datos y servicios en los nodos de un clúster. Sin embargo, las distribuciones estándar de Hadoop suelen tener un único nodo principal. Cualquier interrupción de ese nodo principal puede causar que el clúster deje de funcionar.

Para solucionar este posible problema, los clústeres de HDInsight basados en Linux en Azure proporcionan dos nodos principales para aumentar la disponibilidad y la confiabilidad de los servicios y trabajos de Hadoop en curso.

> [AZURE.NOTE] Los pasos descritos en este documento son específicos de los clústeres de HDInsight basados en Linux. Si usa un clúster basado en Windows, consulte [Disponibilidad y confiabilidad de clústeres Hadoop basados en Windows en HDInsight](hdinsight-high-availability.md) para obtener información específica de Windows.

##Descripción de los nodos

Los nodos de un clúster de HDInsight se implementan mediante Máquinas virtuales de Azure. Si se produce un error en un nodo, se desconecta y se crea un nuevo nodo para reemplazar el nodo con error. Mientras el nodo está sin conexión, se utilizará otro nodo del mismo tipo hasta que se vuelva a conectar el nuevo nodo.

> [AZURE.NOTE] Si el nodo está analizando los datos cuando se produce un error, se pierde su progreso en el trabajo. El trabajo en el que estaba trabajando el nodo con el error se enviará a otro nodo.

En las secciones siguientes se describen los tipos de nodo individuales usados con HDInsight. No todos los tipos de nodo se utilizan para un tipo de clúster. Por ejemplo, un tipo de clúster de Hadoop no tendrá ningún nodo Nimbus. Para más información sobre los nodos usados por los tipos de clúster de HDInsight, consulte la sección Tipos de clúster en [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

###Nodos principales

Algunas implementaciones de Hadoop tienen un único nodo principal que hospeda servicios y componentes que administran el error de los nodos de trabajo sin problemas. Pero las posibles interrupciones de servicios principales que se ejecutan en el nodo principal provocarían que el clúster dejase de funcionar.

Los clústeres de HDInsight proporcionan un nodo principal secundario, lo que permite a los servicios y componentes principales continuar ejecutándose en el nodo secundario si se produce un error en el principal.

> [AZURE.IMPORTANT] Ambos nodos principales están activos y en ejecución dentro del clúster al mismo tiempo. Algunos servicios, como HDFS o YARN solo están “activos” en un nodo principal en un determinado momento (y “en espera” en el otro nodo principal). Otros servicios como HiveServer2 o MetaStore de Hive están activos en ambos nodos principales al mismo tiempo.

###Nodos Nimbus

Para los clústeres de Storm, los nodos Nimbus proporcionan una funcionalidad similar a la de JobTracker de Hadoop al distribuir y supervisar el procesamiento a través de nodos de trabajo. HDInsight proporciona dos nodos Nimbus para el tipo de clúster de Storm.

###Nodos Zookeeper

Los nodos [ZooKeeper](http://zookeeper.apache.org/) (ZK) se usan para la elección del líder de los servicios principales en los nodos principales y para asegurarse de que los servicios, los nodos de datos (trabajo) y las puertas de enlace saben en qué nodo principal está activo un servicio principal. De forma predeterminada, HDInsight proporciona tres nodos ZooKeeper.

###Nodos de trabajo

Los nodos de trabajo realizan el análisis de los datos reales cuando se envía un trabajo al clúster. Si se produce un error en un nodo de trabajo, la tarea que estaba realizando se envía a otro nodo de trabajo. De forma predeterminada, HDInsight creará cuatro nodos de trabajo; sin embargo, puede cambiar este número para adaptarlo a sus necesidades durante y después de la creación del clúster.

###Nodo perimetral

El nodo perimetral no participa activamente en el análisis de datos dentro del clúster, pero se usa por desarrolladores o científicos de datos al trabajar con Hadoop. El nodo perimetral se encuentra en la misma Red virtual de Azure como los demás nodos del clúster y puede acceder directamente a todos los demás nodos. Como no participa en el análisis de datos para el clúster, se puede utilizar sin ninguna preocupación de llevar los recursos fuera los trabajos de análisis o servicios críticos de Hadoop.

Actualmente, el servidor de R en HDInsight es el único tipo de clúster que proporciona un nodo perimetral de forma predeterminada. Para el servidor de R en HDInsight, se usa el nodo perimetral para probar el código de R localmente en el nodo antes de enviarlo al clúster para su procesamiento distribuido.

[Create a Linux-based HDInsight cluster with Hue on an Edge Node](https://azure.microsoft.com/documentation/templates/hdinsight-linux-with-hue-on-edge-node/) (Creación de un clúster de HDInsight basado en Linux con Hue en un nodo perimetral) es una plantilla de ejemplo que puede utilizarse para crear un tipo de clúster de Hadoop que tiene un nodo perimetral.


## Acceso a los nodos

Se proporciona acceso al clúster a través de Internet a través de una puerta de enlace pública y está limitado a la conexión con los nodos principales y (si se trata de un servidor de R en un clúster de HDInsight) con el nodo perimetral. El acceso a los servicios que se ejecutan en los nodos principales no se ve afectado por tener varios nodos principales, ya que la puerta de enlace pública enruta las solicitudes al nodo principal que hospeda el servicio solicitado. Por ejemplo, si Ambari está hospedado en el nodo principal 1, la puerta de enlace enrutará las solicitudes entrantes de Ambari a ese nodo.

Al tener acceso al clúster mediante SSH, efectuar la conexión a través del puerto 22 (el valor predeterminado de SSH) permitirá conectarse al nodo principal 0; la conexión a través del puerto 23 provocará la conexión al nodo principal 1. Por ejemplo, `ssh username@mycluster-ssh.azurehdinsight.net` se conectará al nodo principal 0 del clúster denominado __mycluster__.

> [AZURE.NOTE] Esto también se aplica a los protocolos basados en SSH, como el SSH File Transfer Protocol (SFTP).

También se puede acceder al nodo perimetral proporcionado con el servidor de R en clústeres de HDInsight directamente mediante SSH a través del puerto 22. Por ejemplo, `ssh username@RServer.mycluster.ssh.azurehdinsight.net` se conectará al nodo perimetral para un servidor de R en un clúster de HDInsight denominado __mycluster__.

### Nombres completos de dominio (FQDN) internos

Los nodos de un clúster de HDInsight tienen una dirección IP interna y el FQDN al que solo se puede acceder desde el clúster (por ejemplo, una sesión de SSH en el nodo principal o un trabajo que se ejecuta en el clúster). Al obtener acceso a servicios en el clúster mediante la dirección IP o FQDN interna, debe usar Ambari para comprobar la dirección IP o FQDN que se usará al obtener acceso al servicio.

Por ejemplo, el servicio de Oozie solo puede ejecutarse en un nodo principal y el uso del comando `oozie` desde una sesión de SSH requiere la dirección URL del servicio. Esto puede conseguirse en Ambari mediante el comando siguiente:

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Esto devolverá un valor similar al siguiente, que contiene la dirección URL interna para usar con el comando `oozie`:

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

### Acceso a otros tipos de nodos

Puede conectarse a los nodos que no son accesibles directamente a través de Internet mediante los métodos siguientes.

* __SSH__: una vez conectado a un nodo principal mediante SSH, puede usar SSH desde el nodo principal para conectarse a otros nodos del clúster.
* __Túnel SSH__: si tiene que acceder a un sitio web del servicio hospedado en uno de los nodos que no está expuesto a Internet, debe [usar un túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).
* __Red virtual de Azure__: si el clúster de HDInsight forma parte de una Red virtual de Azure, cualquier recurso en la misma Red virtual puede acceder directamente a todos los nodos del clúster.

## Cómo comprobar el estado del servicio

La interfaz de usuario de la web de Ambari o la API de REST de Ambari pueden usarse para comprobar el estado de los servicios que se ejecutan en el nodo principal.

###Interfaz de usuario web de Ambari

La interfaz de usuario web de Ambari se puede visualizar en https://CLUSTERNAME.azurehdinsight.net. Reemplace **CLUSTERNAME** por el nombre del clúster. Si se le solicita, introduzca las credenciales de usuario HTTP de su clúster. El nombre de usuario HTTP predeterminado es **admin** y la contraseña es la contraseña que especificó al crear el clúster.

Cuando llegue a la página de Ambari, se enumerarán los servicios instalados a la izquierda de la página.

![Servicios instalados](./media/hdinsight-high-availability-linux/services.png)

Hay una serie de iconos que pueden aparecer junto a un servicio para indicar el estado. Las alertas relacionadas con un servicio se pueden ver mediante el vínculo **Alertas** que se encuentra en la parte superior de la página. Puede seleccionar cada servicio para ver más información sobre él.

Mientras que la página del servicio proporciona información sobre el estado y la configuración de cada servicio, no proporciona información sobre en qué nodo principal se está ejecutando el servicio. Para ver esta información, use el vínculo **Hosts** de la parte superior de la página. Esto mostrará los hosts del clúster, incluidos los nodos principales.

![lista de hosts](./media/hdinsight-high-availability-linux/hosts.png)

Al seleccionar el vínculo de uno de los nodos principales se mostrarán los servicios y componentes que se ejecutan en ese nodo.

![Estado del componente](./media/hdinsight-high-availability-linux/nodeservices.png)

###API de REST de Ambari

La API de REST de Ambari está disponible en Internet, y la puerta de enlace pública controla las solicitudes de enrutamiento para el nodo principal que hospeda actualmente la API de REST.

Puede usar el siguiente comando para comprobar el estado de un servicio a través de la API de REST de Ambari:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Reemplace **CONTRASEÑA** por el usuario HTTP (admin.), la contraseña de la cuenta

* Reemplace **CLUSTERNAME** por el nombre del clúster.

* Reemplace **SERVICENAME** por el nombre del servicio para comprobar el estado de

Por ejemplo, para comprobar el estado del servicio **HDFS** en un clúster denominado **mycluster**, con la contraseña **contraseña**, deberá usar lo siguiente:

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

La respuesta será similar a la siguiente:

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

La dirección URL nos indica que el servicio se está ejecutando en el **nodo principal 0**.

El estado nos indica que el servicio se está ejecutando o se ha **INICIADO**.

Si no sabe qué servicios están instalados en el clúster, puede usar lo siguiente para recuperar una lista:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Componentes de servicio

Los servicios pueden contener componentes cuyo estado desea comprobar de forma individual. Por ejemplo, HDFS contiene el componente NameNode. Para ver información sobre un componente, el comando sería:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Si no sabe qué componentes son proporcionados por un servicio, puede usar lo siguiente para recuperar una lista:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
    
## Acceso a los archivos de registro en los nodos principales

###SSH

Mientras está conectado a un nodo principal a través de SSH, los archivos de registro pueden encontrarse en **/var/log**. Por ejemplo, **/var/log/hadoop-yarn/yarn** contiene registros de YARN.

Cada nodo principal puede tener entradas de registro único, por lo que debe comprobar los registros en ambos.

###SFTP

También se puede conectar con el nodo principal mediante el protocolo SSH File Transfer Protocol o el protocolo seguro de transferencia de archivos (SFTP) y descargar los archivos de registro directamente.

De igual forma a utilizar un cliente SSH, al conectarse al clúster debe proporcionar el nombre de cuenta de usuario SSH y la dirección SSH del clúster. Por ejemplo: `sftp username@mycluster-ssh.azurehdinsight.net`. También debe proporcionar la contraseña de la cuenta cuando se le solicite, o proporcionar una clave pública mediante el parámetro `-i`.

Una vez conectado, se le presentará un símbolo del sistema `sftp>`. Desde este símbolo del sistema, puede cambiar los directorios, cargar y descargar archivos. Por ejemplo, los siguientes comandos cambian los directorios al directorio **/var/log/hadoop/hdfs** y después descargan todos los archivos en el directorio.

    cd /var/log/hadoop/hdfs
    get *

Para ver una lista de comandos disponibles, escriba `help` en el símbolo del sistema `sftp>`.

> [AZURE.NOTE] También hay interfaces gráficas que le permiten visualizar el sistema de archivos cuando se conecta mediante SFTP. Por ejemplo, [MobaXTerm](http://mobaxterm.mobatek.net/) le permite examinar el sistema de archivos mediante una interfaz similar al Explorador de Windows.


###Ambari

> [AZURE.NOTE] Para acceder a los archivos de registro a través de Ambari se requiere un túnel SSH, ya que los sitios web de los servicios individuales no se exponen públicamente en Internet. Para obtener información sobre cómo usar un túnel SSH, vea [Uso de la tunelización SSH para acceder a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).

En la interfaz de usuario web de Ambari, seleccione el servicio cuyos registros quiere ver (por ejemplo, YARN) y luego use **Vínculos rápidos** para seleccionar el nodo principal en el que quiere ver los registros.

![Uso de vínculos rápidos para ver los registros](./media/hdinsight-high-availability-linux/viewlogs.png)

## Configuración del tamaño del nodo ##

El tamaño de un nodo solo se puede seleccionar durante la creación del clúster. Puede encontrar una lista de los diferentes tamaños de máquina virtual disponibles para HDInsight, incluido el núcleo, la memoria y el almacenamiento local para cada uno, en la [página de precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Al crear un nuevo clúster, puede especificar el tamaño de los nodos. A continuación se ofrece información sobre cómo especificar el tamaño mediante el [Portal de Azure][preview-portal], [Azure PowerShell][azure-powershell] y la [CLI de Azure][azure-cli]\:

* **Portal de Azure**: al crear un clúster, tiene la opción de establecer el tamaño (plan de tarifas) de los nodos principal, de trabajo y (si se usa el tipo de clúster) ZooKeeper para el clúster:

	![Imagen del asistente para creación de clústeres con selección del tamaño del nodo](./media/hdinsight-high-availability-linux/headnodesize.png)

* **CLI de Azure**: cuando se usa el comando `azure hdinsight cluster create`, puede establecer el tamaño de los nodos principal, de trabajo y ZooKeeper mediante los parámetros `--headNodeSize`, `--workerNodeSize` y `--zookeeperNodeSize`.

* **Azure PowerShell**: cuando se usa el cmdlet `New-AzureRmHDInsightCluster`, puede establecer el tamaño de los nodos principal, de trabajo y ZooKeeper mediante los parámetros `-HeadNodeVMSize`, `-WorkerNodeSize` y `-ZookeeperNodeSize`.

##Pasos siguientes

En este documento ha aprendido cómo proporciona HDInsight de Azure alta disponibilidad para Hadoop. Use lo siguiente para obtener más información acerca de las cosas que se mencionan en este documento.

- [Referencia de REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli-install.md)

- [Instale y configure Azure PowerShell.](../powershell-install-configure.md)

- [Administración de HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md)

- [Aprovisionamiento de clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0914_2016-->
<properties
   pageTitle="Utilización de claves SSH con Hadoop en HDInsight basado en Linux desde Windows | Azure"
   description="Aprenda a crear y usar claves SSH para autenticarse en clústeres de HDInsight basado en Linux."
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

## Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows (vista previa)

Los clústeres de HDInsight basado en Linux proporcionan la opción de proteger el acceso SSH a través de una contraseña o una clave de SSH. Este documento proporciona información sobre cómo conectarse a HDInsight desde clientes Windows usando el cliente SSH PuTTy.

> [AZURE.NOTE] Los pasos que aparecen en este artículo suponen que está usando un cliente Windows. Si usa un cliente Linux, Unix u OS X, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

## Requisitos previos

* **PuTTY** y **PuTTYGen** para clientes Windows. Estas utilidades se encuentran disponibles en <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

* Un explorador web moderno que sea compatible con HTML5

O bien

* Azure PowerShell

O bien

* Herramientas de la línea de comandos multiplataforma de Azure

## ¿Qué es SSH?

SSH es una utilidad para iniciar sesión y ejecutar de manera remota comandos en un servidor remoto. Con HDInsight basado en Linux, SSH establece una conexión segura al nodo principal del clúster y proporciona una línea de comandos que usa para escribir los comandos. Luego esos comandos se ejecutan directamente en el servidor.

## Creación de una clave SSH (opcional)

Cuando cree un clúster de HDInsight basado en Linux, tiene la opción de usar una contraseña o una clave SSH para autenticar el servidor cuando se usa SSH. Las claves SSH se consideran más seguras, puesto que están basadas en certificados. Use la siguiente información si planea usar claves SSH con el clúster.

1. Abra **PuTTYGen**.

2. En **Type of key to generate** (Tipo de clave a generar), seleccione **SSH-2 RSA** y, a continuación, haga clic en **Generate** (Generar).

	![PuTTYGen interface](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Mueva el mouse en el área debajo de la barra de progreso hasta que la barra se complete. Mover el mouse genera datos aleatorios que se usan para generar la clave.

	![moving the mouse around](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	Una vez que se genere la clave, aparecerá la clave pública.

4. Para mayor seguridad, puede escribir una frase de contraseña en el campo **Key passphrase** (Frase de contraseña de clave) y, a continuación, escribir el mismo valor en el campo **Confirm passphrase** (Confirmar frase de contraseña).

	![passphrase](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)
	
	> [AZURE.NOTE] Recomendamos encarecidamente que use una frase de contraseña segura para la clave. Sin embargo, si se le olvida la frase de contraseña, no hay forma de recuperarla.

5. Haga clic en **Save private key** (Guardar clave privada) para guardar la clave en un archivo **.ppk**. Esta clave se usará para autenticarse en el clúster de HDInsight basado en Linux.

	> [AZURE.NOTE] Debe almacenar esta clave en una ubicación segura, puesto que se puede usar para tener acceso a su clúster de HDInsight basado en Linux.

6. Haga clic en **Save public key** (Guardar clave pública) para guardar la clave como archivo **.txt**. Esto le permitirá volver a usar la clave pública cuando cree clústeres adicionales de HDInsight basado en Linux.

	> [AZURE.NOTE] La clave pública también aparece en la parte superior de **PuTTYGen**. Puede hacer clic con el botón secundario en este campo, copiar el valor y luego pegarlo en un formulario, como el asistente de HDInsight en el Portal de administración de Azure.

## Creación de un clúster de HDInsight basado en Linux

Cuando cree un clúster de HDInsight basado en Linux, deberá proporcionar la **clave pública** anteriormente creada. Desde clientes Windows, hay dos maneras de crear un clúster de HDInsight basado en Linux:

* **Portal de administración de Azure**: usa un portal basado en Web para crear el clúster.

* **Interfaz de la línea de comandos multiplataforma de Azure (xplat-cli)**: usa comandos de la línea de comandos para crear el clúster.

Cada uno de estos métodos requerirá la **clave pública**. Para obtener información completa sobre la creación de un clúster de HDInsight basado en Linux, consulte <a href="./hdinsight-hadoop-provision-linux-clusters/" target="_blank">Aprovisionamiento de clústeres de HDInsight basados en Linux</a>.

### Portal de administración de Azure

Cuando use el portal para crear un clúster de HDInsight basado en Linux, debe escribir un nombre de usuario y contraseña o una clave pública en el siguiente formulario.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Esta acción crea un inicio de sesión para el usuario especificado y le permite una autenticación por contraseña o por clave SSH.

### Interfaz de la línea de comandos multiplataforma de Azure

Puede usar la <a href="../xplat-cli/" target="_brad">interfaz de la línea de comandos multiplataforma de Azure</a> para crear un nuevo clúster mediante el comando `azure hdinsight cluzter create`.

Para obtener más información acerca del uso de este comando, consulte <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Aprovisionamiento de clústeres de Hadoop Linux en HDInsight con opciones personalizadas</a>

## <a id="connect"></a>Conexión a un clúster de HDInsight basado en Linux

1. Abra PuTTY.

	![putty interface](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Si proporcionó una **clave SSH** cuando creó la cuenta de usuario, debe realizar el siguiente paso para seleccionar la **clave privada** que se usará al autenticarse en el clúster.

	En **Category** (Categoría), expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Auth** (Autenticar). Finalmente, haga clic en **Browse** (Examinar) y seleccione el archivo **.ppk** que contiene su **clave privada**.

	![putty interface, select private key](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. En **Category** (Categoría), seleccione **Session** (Sesión). En la pantalla **Basic options for your PuTTY session** (Opciones básicas de la sesión de PuTTY), escriba la dirección SSH del servidor de HDInsight en el campo **Host name (or IP address)** (Nombre del host (o dirección IP)). La dirección SSH es el nombre de su clúster, seguido de **-ssh.azurehdinsight.net**. Por ejemplo, **mycluster-ssh.azurehdinsight.net**.

	![putty interface with ssh address entered](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Para guardar la información de conexión para un uso posterior, escriba un nombre para esta conexión en **Saved Sessions** (Sesiones guardadas) y, a continuación, haga clic en **Save** (Guardar). La conexión se agregará a la lista de sesiones guardadas.

5. Haga clic en **Open** (Abrir) para conectarse al clúster.

	> [AZURE.NOTE] Si es la primera vez que se conecta al clúster, recibirá una alerta de seguridad. Esto es normal: seleccione **Yes** (Sí) para almacenar en caché la clave RSA2 del servidor para continuar.

6. Cuando se le solicite, escriba el usuario que ingresó cuando creó el clúster. Si proporcionó una **contraseña** para el usuario, también se le pedirá escribirla.

## Incorporación de cuentas adicionales

2. Genere una **clave pública** nueva y una **clave privada** nueva para la cuenta de usuario nueva, tal como se describió anteriormente.

1. Desde una sesión de SSH al clúster, agregue el usuario nuevo con el siguiente comando.

		sudo adduser --disabled-password <username> 

	Con esto se creará una nueva cuenta de usuario, pero se deshabilitará la autenticación de contraseña.

2. Cree el directorio y los archivos para que contengan la clave con los siguientes comandos.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Cuando se abra el editor nano, copie y pegue el contenido de la **clave pública** para la cuenta de usuario nueva. Finalmente, use **Ctrl-X** para guardar el archivo y salga del editor.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

4. Use el comando siguiente para cambiar la propiedad de la carpeta .ssh y el contenido a la cuenta de usuario nueva.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Ahora debiera poder autenticarse con el servidor con la cuenta de usuario nueva y la **clave privada**.

## <a id="tunnel"></a>Tunelización de SSH

SSH también se puede usar para tunelizar las solicitudes locales, como solicitudes web, al clúster de HDInsight. La solicitud se enrutará al recurso solicitado como si se hubiese originado en el nodo principal del clúster de HDInsight.

Esto resulta muy útil cuando se obtiene acceso a servicios basados en Web en el clúster de HDInsight que usan nombres de dominio interno para el nodo principal o el nodo de trabajo en el clúster. Por ejemplo, algunas secciones de la página web de Ambari usan nombres de dominio interno, como **headnode0.mycluster.d1.internal.cloudapp.net**. Estos nombres no se pueden resolver desde el exterior del clúster; sin embargo, las solicitudes tunelizadas sobre SSH se original dentro del clúster y se resolverán correctamente.

Use los siguientes pasos para crear un túnel SSH y configure el explorador para que lo use para conectarse al clúster.

1. Abra PuTTY y escriba la información de conexión como aparece en [Connect] (Conectar)(#connect) .

2. En la sección **Category** (Categoría) que se encuentra a la izquierda del cuadro de diálogo, expanda **Connection** (Conexión), **SSH** y, finalmente, seleccione **Tunnels** (Túneles).

2. Proporcione la siguiente información en el formulario **Options controlling SSH port forwarding** (Opciones que controlan el desvío de puerto SSH).

	* **Source port**: el puerto en el cliente que desea desviar. Por ejemplo, **9876**

	* **Destination**: la dirección SSH del clúster de HDInsight basado en Linux. Por ejemplo, **mycluster-ssh.azurehdinsight.net**

	* **Dynamic**: habilita el enrutamiento dinámico del proxy SOCKS.

	![image of tunneling options](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

3. Finalmente, seleccione **Add** (Agregar) para agregar la configuración y, a continuación, seleccione **Open** (Abrir) para abrir una conexión SSH.

4. Cuando se le solicite, inicie sesión en el servidor. Esto establecerá una sesión SSH y habilitará el túnel.

2. Configure el programa cliente, como Firefox, para que use **localhost:9876** como el proxy **SOCKS v5**. La configuración de Firefox se verá de la siguiente manera.

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE] Seleccionar **DNS remoto** resolverá las solicitudes de DNS mediante el uso del clúster de HDInsight. Si no se selecciona, el DNS se resolverá de manera local.

	Puede comprobar que el tráfico se enruta a través del túnel mediante la visita a un sitio como <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> con la configuración del proxy habilitada y deshabilitada en Firefox. Cuando esté habilitada, la dirección IP será la de un equipo en el centro de datos de Microsoft Azure.

### Extensiones del explorador

A pesar de que la configuración del explorador para que use el túnel funciona, normalmente no desearía enrutar todo el tráfico a través del túnel. Las extensiones del explorador como <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a> son compatibles con la coincidencia de patrones para las solicitudes de dirección URL (FoxyProxy Standard o Plus solamente), de manera tal que solo las solicitudes para direcciones URL específicas se enviarán a través del túnel.

Si instaló **FoxyProxy Standard**, use los siguientes pasos para configurarlo para que solo desvíe tráfico para HDInsight a través del túnel.

1. Abra la extensión FoxyProxy en el explorador. Por ejemplo, en Firefox, seleccione el icono de FoxyProxy que se encuentra junto al campo de dirección.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. Seleccione **Agregar proxy nuevo**, la pestaña **General** y, a continuación, escriba un nombre de proxy de **HDInsight**.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. Seleccione la pestaña **Detalles de proxy** y rellene los campos siguientes.

	* **Host o dirección IP**: localhost, debido a que usamos un túnel SSH en el equipo local.

	* **Puerto**: el puerto que usó para el túnel SSH.

	* **Proxy SOCKS**: seleccione para permitir que el explorador use el túnel como proxy.

	* **SOCKS v5**: seleccione para definir la versión requerida del proxy.

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. Seleccione la pestaña **Patrones de dirección URL** y, a continuación, seleccione **Agregar patrón nuevo**. Use lo siguiente para definir el patrón y, a continuación, haga clic en **Aceptar**.

	* **Nombre de patrón** - **headnode**: es solo un nombre fácil de usar para el patrón.

	* **Patrón de dirección URL** - **\*headnode\***: define un patrón que coincide con cualquier dirección URL que contenga la palabra **headnode**.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. Seleccione **Aceptar** para agregar el proxy y cierre la **Configuración del proxy**

5. En la parte superior del cuadro de diálogo FoxyProxy, cambie **Seleccionar modo** a **Uso de servidores proxies según sus patrones y prioridades predefinidos** y, a continuación, seleccione **Cerrar**.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

Después de seguir estos pasos, solo las solicitudes de direcciones URL que contienen la cadena **headnode** se enrutarán a través del túnel SSL.

## Pasos siguientes

Ahora que sabe cómo autenticarse con una clave SSH, aprenda a usar MapReduce con Hadoop en HDInsight.

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con HDInsight](hdinsight-use-pig.md)

* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)
 
<!--HONumber=47-->

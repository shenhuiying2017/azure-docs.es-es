<properties
   pageTitle="Utilización de claves SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X | Azure"
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

## Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)

Los clústeres de HDInsight basado en Linux proporcionan la opción de proteger el acceso SSH a través de una contraseña o una clave de SSH. Este documento proporciona información sobre el uso de SSH con HDInsight desde clientes Linux, Unix u OS X.

> [AZURE.NOTE] Los pasos que aparecen en este artículo suponen que está usando un cliente Linux, Unix u OS X. A pesar de que estos pasos se podrían realizar en un cliente Windows si tiene instalado un paquete que proporciona `ssh` y `ssh-keygen` (como Git para Windows), recomendamos que los clientes Windows sigan los pasos que aparecen en [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Windows](/es-es/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/).

## Requisitos previos

* **ssh-keygen** y **ssh** para clientes Linux, Unix y OS X. Esta utilidad normalmente viene con el sistema operativo o se encuentra disponible a través del sistema de administración de paquetes.

* Un explorador web moderno que sea compatible con HTML5

O bien

* Herramientas de la línea de comandos multiplataforma de Azure

## ¿Qué es SSH?

SSH es una utilidad para iniciar sesión y ejecutar de manera remota comandos en un servidor remoto. Con HDInsight basado en Linux, SSH establece una conexión segura al nodo principal del clúster y proporciona una línea de comandos que usa para escribir los comandos. Luego esos comandos se ejecutan directamente en el servidor.

## Creación de una clave SSH (opcional)

Cuando cree un clúster de HDInsight basado en Linux, tiene la opción de usar una contraseña o una clave SSH para autenticar el servidor cuando se usa SSH. Las claves SSH se consideran más seguras, puesto que están basadas en certificados. Use la siguiente información si planea usar claves SSH con el clúster.

1. Abra una sesión de terminal y use el comando siguiente para ver si cuenta con claves SSH existentes.

		ls -al ~/.ssh

	Busque los siguientes archivos en el listado de directorios. Los siguientes son nombres comunes para claves SSH públicas.

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. Si no desea usar un archivo existente o no tiene claves SSH existentes, use lo siguiente para generar un archivo nuevo.

		ssh-keygen -t rsa

	Se le solicitará la información siguiente:

	* La ubicación del archivo, definida de manera predeterminada en ~/.ssh/id\_rsa.
	* Una frase de contraseña: se le pedirá que vuelva a escribirla.
	
		> [AZURE.NOTE] Recomendamos encarecidamente que use una frase de contraseña segura para la clave. Sin embargo, si se le olvida la frase de contraseña, no hay forma de recuperarla.

	Una vez que se complete el comando, tendrá dos archivos nuevos: la clave privada (por ejemplo, **id\_rsa**) y la clave pública (por ejemplo, **id\_rsa.pub**).

## Creación de un clúster de HDInsight basado en Linux

Cuando cree un clúster de HDInsight basado en Linux, deberá proporcionar la **clave pública** anteriormente creada. Desde clientes Linux, Unix u OS X, hay dos formas de crear un clúster de HDInsight:

* **Portal de administración de Azure**: usa un portal basado en Web para crear el clúster.

* **Interfaz de la línea de comandos multiplataforma de Azure (xplat-cli)**: usa comandos de la línea de comandos para crear el clúster.

Cada uno de estos métodos requerirá una **contraseña** o una **clave pública**. Para obtener información completa sobre la creación de un clúster de HDInsight basado en Linux, consulte <a href="/es-es/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Aprovisionamiento de clústeres de HDInsight basado en Linux</a>.

### Portal de administración de Azure

Al usar el portal para crear un clúster de HDInsight basado en Linux, debe escribir un **nombre de usuario de SSH** y seleccionar escribir una **contraseña** o una **clave pública SSH**. Si selecciona **Clave pública SSH**, debe pegar la clave pública (contenida en el archivo con la extensión **.pub**) en el siguiente formulario.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] El archivo de la clave es simplemente un texto de archivo. El contenido debe ser similar al siguiente.
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Con esto se crea un inicio de sesión para el usuario especificado, mediante la contraseña o la clave pública que proporciona.

### Interfaz de la línea de comandos multiplataforma de Azure

Puede usar la <a href="../xplat-cli/" target="_brad">Interfaz de la línea de comandos multiplataforma de Azure</a>para crear un clúster nuevo mediante el comando `azure hdinsight cluster create`.

Para obtener más información sobre el uso de este comando, consulte <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Aprovisionamiento de clústeres de Hadoop Linux en HDInsight usando opciones personalizadas.</a>

## Conexión a un clúster de HDInsight basado en Linux

Desde una sesión de terminal, use el comando SSH para conectarse al clúster proporcionando el nombre de usuario y la dirección del clúster.

* **Dirección del clúster**: el nombre del clúster, seguido de **-ssh.azurehdinsight.net**. Por ejemplo, **mycluster-ssh.azurehdinsight.net**.

* **Nombre de usuario**: el nombre de usuario SSH que proporcionó al crear el clúster.

En el siguiente ejemplo se conectará al clúster **mycluster** como el usuario **me**.

	ssh me@mycluster-ssh.azurehdinsight.net

Si usó una **contraseña** para la cuenta del usuario, se le solicitará escribir la contraseña.

Si usó una **clave SSH** protegida con una frase de contraseña, se le pedirá que escriba la frase de contraseña; de lo contrario, SSH intentará autenticarse automáticamente con una de las claves privadas locales existentes en el cliente.

> [AZURE.NOTE] Si SSH no se autentica automáticamente con la **clave privada** correcta, use el parámetro **-i** y especifique la ruta de acceso a la clave privada. El siguiente ejemplo cargará la **clave privada** desde `~/.ssh/id_rsa`.
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

## Incorporación de cuentas adicionales

2. Genere una **clave pública** nueva y una **clave privada** nueva para la cuenta de usuario nueva, tal como se describe en [Creación de una clave SSH](#create) .

	> [AZURE.NOTE] La clave privada se puede generar en un cliente que el usuario usará para conectarse al clúster, o bien, se puede transferir de manera protegida a dicho cliente después de la creación.

1. Desde una sesión de SSH al clúster, agregue el usuario nuevo con el siguiente comando.

		sudo adduser --disabled-password <username> 

	Con esto se creará una nueva cuenta de usuario, pero se deshabilitará la autenticación de contraseña.

2. Cree el directorio y los archivos para que contengan la clave con los siguientes comandos.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Cuando se abra el editor nano, copie y pegue el contenido de la **clave pública** para la cuenta de usuario nueva. Finalmente, use **Ctrl-X** para guardar el archivo y salga del editor.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Use el comando siguiente para cambiar la propiedad de la carpeta .ssh y el contenido a la cuenta de usuario nueva.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Ahora debiera poder autenticarse con el servidor con la cuenta de usuario nueva y la **clave privada**.

## <a id="tunnel"></a>Tunelización de SSH

SSH también se puede usar para tunelizar las solicitudes locales, como solicitudes web, al clúster de HDInsight. La solicitud se enrutará al recurso solicitado como si se hubiese originado en el nodo principal del clúster de HDInsight.

Esto resulta muy útil cuando se obtiene acceso a servicios basados en Web en el clúster de HDInsight que usan nombres de dominio interno para el nodo principal o el nodo de trabajo en el clúster. Por ejemplo, algunas secciones de la página web de Ambari usan nombres de dominio interno, como **headnode0.mycluster.d1.internal.cloudapp.net**. Estos nombres no se pueden resolver desde el exterior del clúster; sin embargo, las solicitudes tunelizadas sobre SSH se original dentro del clúster y se resolverán correctamente.

Use los siguientes pasos para crear un túnel SSH y configure el explorador para que lo use para conectarse al clúster.

1. El siguiente comando se puede usar para crear un túnel SSH al nodo principal del clúster.

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	Con esto se crea una conexión que enruta el tráfico al puerto local 9876 al clúster sobre SSH. Las opciones son:

	* **D 8080**: el puerto local que enrutará el tráfico a través del túnel.

	* **C**: comprimir todos los datos, debido a que el tráfico web es principalmente texto.

	* **2**: forzar a SSH a que intente solo la versión 2 del protocolo.

	* **q**: modo silencioso.

	* **T**: deshabilitar la asignación seudotty, debido a que solo estamos desviando un puerto.
	
	* **n**: evita la lectura de STDIN, debido a que solo estamos desviando un puerto.

	* **N**: no ejecutar un comando remoto, debido a que solo estamos desviando un puerto.

	* **f**: ejecutar en segundo plano.

	Si configuró el clúster con una **clave SSH**, es posible que necesite usar el parámetro `-i` y especifique la ruta de acceso a la clave SSH privada.

	Una vez que se completa el comando, el tráfico enviado al puerto 9876 del equipo local se enrutará sobre SSL al nodo principal del clúster y aparecerá como originado ahí.

2. Configure el programa cliente, como Firefox, para que use **localhost:9876** como el proxy **SOCKS v5**. La configuración de Firefox se verá de la siguiente manera.

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE] Seleccionar **DNS remoto** resolverá las solicitudes de DNS mediante el uso del clúster de HDInsight. Si no se selecciona, el DNS se resolverá de manera local.

	Para comprobar que el tráfico se esté enrutando a través del túnel, visite un sitio como <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> con la configuración de proxy habilitada y deshabilitada en Firefox. Cuando esté habilitada, la dirección IP será la de un equipo en el centro de datos de Microsoft Azure.

### Extensiones del explorador

A pesar de que la configuración del explorador para que use el túnel funciona, normalmente no desearía enrutar todo el tráfico a través del túnel. Extensiones para el explorador, como <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>  , son compatibles con la coincidencia de patrones para las solicitudes de dirección URL (FoxyProxy Standard o Plus solamente), de manera tal que solo las solicitudes para direcciones URL específicas se enviarán a través del túnel.

Si instaló **FoxyProxy Standard**, use los siguientes pasos para configurarlo para que solo desvíe tráfico para HDInsight a través del túnel.

1. Abra la extensión FoxyProxy en el explorador. Por ejemplo, en Firefox, seleccione el icono de FoxyProxy que se encuentra junto al campo de dirección.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. Seleccione **Agregar proxy nuevo**, la pestaña **General** y, a continuación, escriba un nombre de proxy de **HDInsight**.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. Seleccione la pestaña **Detalles de proxy** y rellene los campos siguientes.

	* **Host o dirección IP**: localhost, debido a que usamos un túnel SSH en el equipo local.

	* **Puerto**: el puerto que usó para el túnel SSH.

	* **Proxy SOCKS**: seleccione para permitir que el explorador use el túnel como proxy.

	* **SOCKS v5**: seleccione para definir la versión requerida del proxy.

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. Seleccione la pestaña **Patrones de dirección URL** y, a continuación, seleccione **Agregar patrón nuevo**. Use lo siguiente para definir el patrón y, a continuación, haga clic en **Aceptar**.

	* **Nombre de patrón** - **headnode**: es solo un nombre fácil de usar para el patrón.

	* **Patrón de dirección URL** - **\*headnode\*** : define un patrón que coincide con cualquier dirección URL que contenga la palabra **headnode**.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. Seleccione **Aceptar** para agregar el proxy y cierre la **Configuración del proxy**

5. En la parte superior del cuadro de diálogo FoxyProxy, cambie **Seleccionar modo** a **Uso de servidores proxies según sus patrones y prioridades predefinidos** y, a continuación, seleccione **Cerrar**.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

Después de seguir estos pasos, solo las solicitudes de direcciones URL que contienen la cadena **headnode** se enrutarán a través del túnel SSL.

## Pasos siguientes

Ahora que sabe cómo autenticarse con una clave SSH, aprenda a usar MapReduce con Hadoop en HDInsight.

* [Uso de Hive con HDInsight](../hdinsight-use-hive/)

* [Uso de Pig con HDInsight](../hdinsight-use-pig/)

* [Uso de trabajos de MapReduce con HDInsight](../hdinsight-use-mapreduce/)
 
<!--HONumber=45--> 

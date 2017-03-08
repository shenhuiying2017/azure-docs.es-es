---
title: Uso SSH con HDInsight (Hadoop) desde Windows, Linux, Unix u OS X | Microsoft Docs
description: " Puede acceder a HDInsight mediante Secure Shell (SSH). En este documento se proporciona información acerca del uso de SSH con HDInsight desde clientes Windows, Linux, Unix u OS X."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 4cde035f75bfa3c448f12e9ebf2896b9a54a6873
ms.lasthandoff: 03/01/2017

---
# <a name="use-ssh-with-hdinsight-hadoop-from-bash-on-windows-10-linux-unix-or-os-x"></a>Uso de SSH con HDInsight (Hadoop) desde Bash en Windows 10, Linux, Unix u OS X

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

[Shell seguro (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite registrarse en un clúster de HDInsight basado en Linux y ejecutar comandos con una interfaz de línea de comandos. Este documento proporciona información básica sobre SSH e información específica acerca del uso de SSH con HDInsight.

## <a name="what-is-ssh"></a>¿Qué es SSH?

SSH es un protocolo de red criptográfico que permite comunicarse de forma segura con un servidor remoto a través de una red no segura. SSH se usa para proporcionar un inicio de sesión seguro de línea de comandos en un servidor remoto. En este caso, los nodos principales o el nodo perimetral de un clúster de HDInsight.

También se puede usar SSH para tunelizar el tráfico de red desde el cliente al clúster de HDInsight. Con un túnel se puede acceder a los servicios del clúster de HDInsight que no se exponen directamente en Internet. Para más información sobre cómo utilizar la tunelización SSH con HDInsight, consulte [Uso de la tunelización SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ssh-clients"></a>Clientes SSH

Muchos sistemas operativos proporcionan funcionalidad de cliente SSH a través de las utilidades `ssh` y `scp` de la línea de comandos.

* __ssh__: cliente SSH general que se puede usar para establecer una sesión de línea de comandos remota y crear túneles.
* __scp__: utilidad que copia los archivos entre los sistemas locales y remotos mediante el protocolo SSH.

Windows 10 Anniversary Edition proporciona Bash como una característica para el desarrollador. Proporciona `ssh`, `scp` y otros comandos de Linux. Para más información acerca del uso de Bash en Windows 10, consulte [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) (Bash en Ubuntu en Windows).

Si se utiliza Windows y no se tiene acceso a Bash en Windows 10, se recomiendan los siguientes clientes SSH:

* [GIT para Windows](https://git-for-windows.github.io/): proporciona las utilidades de línea de comandos `ssh` y `scp`.
* [Cygwin](https://cygwin.com/): proporciona las utilidades de línea de comandos `ssh` y `scp`.

> [!NOTE]
> Para los pasos descritos en este documento, se da por supuesto que se dispone de acceso al comando `ssh`. Si utiliza un cliente como puTTY o MobaXterm, consulte la documentación del producto para los parámetros y el comando equivalentes.

## <a name="ssh-authentication"></a>Autenticación SSH

Se puede autenticar una conexión SSH mediante una contraseña o una [criptografía de clave pública (https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography). Una clave es la opción más segura, ya que no es vulnerable a muchos de los ataques a los que sí lo son las contraseñas. Sin embargo, la creación y administración de claves resultan más complejas que el uso de una contraseña.

El uso de criptografía de clave pública implica la creación de un par de claves _pública_ y _privada_.

* La **clave pública** se carga en los nodos del clúster deHDInsight o en cualquier otro servicio que se desee usar con una criptografía de clave pública.

* La **clave privada** es lo que se muestra al clúster de HDInsight al iniciar sesión con un cliente SSH para verificar la identidad. Esta clave se debe proteger, por lo que no se debe compartir.

    Se puede agregar seguridad adicional mediante la creación de una frase de contraseña para la clave privada. Si usa una frase de contraseña, debe escribirla al autenticarse mediante SSH.

### <a name="create-a-public-and-private-key"></a>Creación de una clave pública y privada

La utilidad `ssh-keygen` es la manera más fácil de crear un par de claves pública y privada para usarlas con HDInsight. En una línea de comandos, con el comando siguiente, cree un nuevo par de claves que va a utilizar con HDInsight:

> [!NOTE]
> Si utiliza un cliente SSH de GUI como MobaXTerm o puTTY, consulte cómo generar las claves en la documentación del cliente.

    ssh-keygen -t rsa -b 2048

Se solicitará la información siguiente:

* La ubicación del archivo: se define de manera predeterminada en `~/.ssh/id_rsa`.

* Una frase de contraseña opcional: si especifica una frase de contraseña, debe volver a indicarla al autenticarse en el clúster de HDInsight.

> [!IMPORTANT]
> La frase de contraseña es una contraseña para la clave privada. Siempre que use la clave privada para la autenticación, antes deberá proporcionar la frase de contraseña. Si alguien obtiene la clave privada, no podrá utilizarla sin la frase de contraseña.
>
> Si se le olvida la frase de contraseña, no hay forma de restablecerla ni de recuperarla.

Al finalizar el comando, tendrá dos nuevos archivos:

* __id\_rsa__: este archivo contiene la clave privada.

    > [!WARNING]
    > Restrinja el acceso a este archivo para impedir el acceso no autorizado a los servicios protegidos por la clave pública.

* __id\_rsa.pub__: este archivo contiene la clave pública. Use este archivo al crear un clúster de HDInsight.

    > [!NOTE]
    > No importa quién tenga acceso a la clave _pública_. Por sí misma, todo lo que la clave pública puede hacer es comprobar la clave privada. Servicios como el servidor SSH usan la clave pública para comprobar su identidad cuando se autentica mediante la clave privada.

## <a name="configure-ssh-on-hdinsight"></a>Configuración de SSH en HDInsight

Cuando se crea un clúster de HDInsight basado en Linux, debe proporcionar un _nombre de usuario SSH_ y una _contraseña_ o _clave pública_. Durante la creación del clúster, esta información se utiliza para crear un inicio de sesión en los nodos del clúster de HDInsight. La contraseña o la clave pública se utilizan para proteger la cuenta de usuario.

Para más información sobre la configuración de SSH durante la creación del clúster, consulte uno de los siguientes documentos:

* [Creación de HDInsight con Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Creación de HDInsight con la CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [Creación de HDInsight con Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Creación de HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [Creación de HDInsight con SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Creación de HDInsight con REST](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>Otros usuarios SSH

Aunque se pueden agregar más usuarios SSH al clúster después de crearlo, no es lo recomendable.

* Se deben agregar nuevos usuarios de SSH a cada nodo del clúster.

* Los nuevos usuarios SSH tienen el mismo acceso a HDInsight que el usuario predeterminado. No hay forma de restringir el acceso a datos o trabajos de HDInsight en función de la cuenta de usuario SSH.

Para restringir el acceso usuario por usuario, debe usar un clúster de HDInsight unido a un dominio. HDInsight unido a un dominio utiliza Active Directory para controlar el acceso a los recursos del clúster.

Un clúster de HDInsight unido a un dominio permite autenticarse mediante Active Directory después de conectarse con SSH. Varios usuarios pueden conectarse con SSH y autenticarse en sus cuentas de Active Directory una vez conectados. Para más información, consulte la sección [HDInsight unido a un dominio](#domainjoined).

##<a id="connect"></a> Conexión a HDInsight

Aunque todos los nodos de un clúster de HDInsight ejecutan el servidor SSH, solo puede conectarse a los nodos principales o nodos perimetrales a través de la red de Internet pública.

* Para conectarse a los _nodos principales_, use `CLUSTERNAME-ssh.azurehdinsight.net`, donde __CLUSTERNAME__ es el nombre del clúster de HDInsight. Al conectarse en el puerto 22 (el valor predeterminado de SSH), se conecta al nodo principal. El puerto 23 se conecta al nodo secundario.

* Para conectarse a un _nodo perimetral_, utilice `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`, donde __EDGENAME__ es el nombre del nodo perimetral y __CLUSTERNAME__, el nombre del clúster de HDInsight. Utilice el puerto 22 al conectarse al nodo perimetral.

Los ejemplos siguientes muestran cómo conectarse a los nodos principales y al nodo perimetral de un clúster denominado __myhdi__ con un nombre de usuario SSH de __sshuser__. El nodo perimetral se denomina __myedge__.

| Para hacer esto... | Use esto... |
| ----- | ----- |
| Conexión con el nodo principal | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| Conexión con el nodo secundario | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| Conexión con el nodo perimetral | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

Si usa una contraseña para proteger la cuenta SSH, se le solicita que escriba la contraseña.

Si utiliza una clave pública para proteger la cuenta SSH, quizá deba especificar la ruta de acceso a la clave privada correspondiente con el conmutador `-i`. En el siguiente ejemplo se muestra el uso del conmutador `-i`:

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>Conexión a otros nodos

No es posible tener acceso a los nodos de trabajador y nodos Zookeeper directamente desde fuera del clúster, pero sí es posible hacerlo desde los nodos principales o perimetrales del clúster. Estos son los pasos generales para conectarse a otros nodos:

1. Use SSH para conectarse al nodo principal o perimetral:

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. En la conexión SSH con el nodo principal o perimetral, use el comando `ssh` para conectarse a un nodo de trabajador en el clúster:

        ssh sshuser@wn0-myhdi

    Para recuperar una lista de los nodos de trabajador del clúster, consulte el ejemplo de cómo recuperar el nombre de dominio completo de los nodos del clúster en el documento [Administración de clústeres de HDInsight con la API de REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Si la cuenta SSH se protege con una contraseña, se le pide que la escriba y se establece la conexión.

Si usa una clave SSH para autenticar la cuenta de usuario, debe asegurarse de que su entorno local está configurado para desviar el agente SSH.

> [!IMPORTANT]
> Los pasos siguientes presuponen un sistema basado en Linux/UNIX y funcionan con Bash en Windows 10. Si estos pasos no funcionan en su sistema, es posible que deba consultar la documentación para el cliente SSH.

1. Abra `~/.ssh/config`con un editor de texto. Si este archivo no existe, puede crearlo si escribe `touch ~/.ssh/config` en la línea de comandos.

2. Agregue al archivo lo siguiente. Reemplace *CLUSTERNAME* por el nombre del clúster de HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Esta entrada configura el desvío del agente SSH para el clúster de HDInsight.

3. Use el siguiente comando desde el terminal para probar el desvío del agente SSH:

        echo "$SSH_AUTH_SOCK"

    Este comando devuelve información similar al texto siguiente:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si no se devuelve nada, `ssh-agent` no se está ejecutando. Consulte la información de los scripts de inicio de agente en [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Uso de ssh-agent con ssh) o la documentación del cliente SSH para ver pasos específicos sobre cómo instalar y configurar `ssh-agent`.

4. Una vez que haya comprobado que **ssh-agent** está en ejecución, use lo siguiente para agregar la clave privada SSH al agente:

        ssh-add ~/.ssh/id_rsa

    Si la clave privada está almacenada en un archivo distinto, reemplace `~/.ssh/id_rsa` por la ruta de acceso al archivo.

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>HDInsight unido a un dominio

[HDInsight unido a un dominio](hdinsight-domain-joined-introduction.md) integra Kerberos con Hadoop en HDInsight. Dado que el usuario SSH no es un usuario de dominio de Active Directory, no puede ejecutar comandos de Hadoop hasta que se autentique con Active Directory. Siga estos pasos para autenticar la sesión SSH con Active Directory:

1. Conéctese a un clúster de HDInsight unido a un dominio mediante SSH. Por ejemplo, el siguiente comando se conecta a un clúster de HDInsight denominado __myhdi__ con una cuenta SSH denominada __sshuser__.

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Utilice lo siguiente para autenticarse con un usuario de dominio y una contraseña:

        kinit

     Cuando se le solicite, escriba un nombre de usuario de dominio y la contraseña para el usuario de dominio.

    Para más información sobre cómo configurar usuarios del dominio para clústeres de HDInsight unidos a un dominio, consulte [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configuración de clústeres de HDInsight unidos a un dominio).

Después de autenticar mediante el comando `kinit`, ya puede usar comandos de Hadoop como `hdfs dfs -ls /` o `hive`.

## <a id="tunnel"></a>Tunelización de SSH

SSH se puede usar para tunelizar las solicitudes locales, como solicitudes web, al clúster de HDInsight. La solicitud se reenvía al clúster y, a continuación, se resuelve en el clúster.

> [!IMPORTANT]
> El túnel SSH es un requisito para acceder a la interfaz de usuario web de algunos servicios de Hadoop. Por ejemplo, solo se puede acceder a la interfaz de usuario del historial de trabajos o la interfaz de usuario del administrador de recursos usando un túnel SSH.

Para más información sobre la creación y el uso de un túnel SSH, consulte [Uso de la tunelización SSH para tener acceso a la interfaz de usuario Ambari Web, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo realizar la autenticación con una clave SSH, aprenda a usar MapReduce con Hadoop en HDInsight.

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/


---
title: Uso SSH con HDInsight (Hadoop) desde Windows, Linux, Unix u OS X | Microsoft Docs
description: " Puede acceder a HDInsight mediante Secure Shell (SSH). En este documento se proporciona información acerca del uso de SSH para conectar con HDInsight desde clientes Windows, Linux, Unix u OS X."
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
ms.date: 04/03/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 248e820ccd2c68a8500aab3233c5beea3c8cc868
ms.lasthandoff: 04/04/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Conexión a través de SSH con HDInsight (Hadoop)

Aprenda a usar [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) para conectarse de forma segura con HDInsight. HDInsight puede usar Linux (Ubuntu) como sistema operativo para los nodos dentro del clúster. SSH puede utilizarse para conectarse a los nodos principal y perimetral de un clúster basado en Linux y ejecutar comandos directamente en esos nodos.

La tabla siguiente contiene la información de dirección y puerto que es necesaria cuando se conecta a través de SSH a HDInsight:

| Dirección | Port | Se conecta a... |
| ----- | ----- | ----- |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Nodo perimetral (R Server en HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Nodo perimetral (cualquier otro tipo de clúster, si existe un nodo perimetral) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nodo principal primario |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nodo principal secundario |

> [!NOTE]
> Reemplace `<edgenodename>` con el nombre del nodo perimetral.
>
> Reemplace `<clustername>` por el nombre del clúster de HDInsight.
>
> Se recomienda __conectarse siempre al nodo perimetral__ si dispone de uno. Los servicios de hospedaje de nodos principales que son críticos para el buen funcionamiento del clúster. El nodo perimetral ejecuta solo lo que incluya en él.
>
> Para más información, consulte [Uso de nodos perimetrales en HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).

## <a name="ssh-clients"></a>Clientes SSH

La mayoría de los sistemas de operativos proporcionan el cliente de `ssh`. Microsoft Windows no proporciona un cliente de SSH de forma predeterminada. En cada uno de los siguientes paquetes hay disponible un cliente de SSH para Windows:

* [Bash on Ubuntu on Windows 10](https://msdn.microsoft.com/commandline/wsl/about) (Bash en Ubuntu en Windows 10): el comando `ssh` se proporciona a través de Bash en la línea de comandos de Windows.

* [Git (https://git-scm.com/)](https://git-scm.com/): el comando `ssh` se proporciona a través de la línea de comandos GitBash.

* [GitHub Desktop (https://desktop.github.com/)](https://desktop.github.com/): el comando `ssh` se proporciona a través de la línea de comandos Git Shell. GitHub Desktop se puede configurar para usar Bash, el símbolo del sistema de Windows, o PowerShell como línea de comandos para el Git Shell.

* [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH): el equipo de PowerShell está realizando la portabilidad de OpenSSH a Windows y proporciona versiones de prueba.

    > [!WARNING]
    > El paquete OpenSSH incluye el componente de servidor SSH, `sshd`. Este componente inicia un servidor SSH en el sistema, lo que permite a otros conectarse a él. No configure este componente o abra el puerto 22 a menos que desee hospedar un servidor SSH en el sistema. No es necesario para comunicarse con HDInsight.

También hay varios clientes SSH gráficos, como [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) y [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Aunque estos clientes se pueden usar para conectar con HDInsight, el proceso de conexión a un servidor es diferente a usar la utilidad `ssh`. Para más información, consulte la documentación del cliente gráfico que esté usando.

## <a id="sshkey"></a>Autenticación: claves SSH

La claves SSH usan [criptografía de clave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) para proteger el clúster. Las claves SSH son más seguras que las contraseñas y proporcionan una manera fácil para proteger el clúster de HDInsight.

Si su cuenta SSH se protege utilizando una clave, el cliente tiene que proporcionar la clave privada correspondiente al conectarse:

* La mayoría de los clientes pueden configurarse para utilizar una __clave predeterminada__. Por ejemplo, el cliente `ssh` busca una clave privada en `~/.ssh/id_rsa` en entornos Linux y Unix.

* Puede especificar la __ruta de acceso a una clave privada__. Con el cliente `ssh`, el parámetro `-i` se utiliza para especificar la ruta de acceso a la clave privada. Por ejemplo: `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Si tiene __varias claves privadas__ para su uso con distintos servidores, se pueden usar utilidades como [SSH-Agent (https://es.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) para seleccionar automáticamente la clave que se usará.

> [!IMPORTANT]
>
> Si se protege la clave privada con una frase de contraseña, tiene que escribir la frase de contraseña cuando se use la clave. Utilidades como `ssh-agent` puede almacenar en caché la contraseña para su comodidad.

### <a name="create-an-ssh-key-pair"></a>Creación de un par de claves SSH

Use el comando `ssh-keygen` para crear archivos de clave pública y privada. El comando siguiente genera un par de claves de RSA de 2048 bits que puede utilizarse con HDInsight:

    ssh-keygen -t rsa -b 2048

Se le pedirá información durante el proceso de creación de claves. Por ejemplo, dónde se almacenan las claves o si se debe usar una frase de contraseña. Una vez completado el proceso, se crean dos archivos: una clave pública y una clave privada.

* La __clave pública__ se utiliza para crear un clúster de HDInsight. La clave pública tiene una extensión `.pub`.

* La __clave privada__ se utiliza para autenticar el cliente al clúster de HDInsight.

> [!IMPORTANT]
> Puede proteger las claves con una frase de contraseña. Se trata de una contraseña para la clave privada. Incluso si alguien obtiene la clave privada, tienen que tener la frase de contraseña para poder usar la clave.

### <a name="create-hdinsight-using-the-public-key"></a>Creación de HDInsight usando la clave pública

| Método de creación | Cómo usar la clave pública |
| ------- | ------- |
| **Azure Portal** | Desactive la opción __Utilizar la misma contraseña que en el inicio de sesión del clúster__y, a continuación, seleccione __Clave pública__ como el tipo de autenticación de SSH. Por último, seleccione el archivo de clave pública o pegue el contenido de texto del archivo en el campo __Clave pública SSH__.</br>![Cuadro de diálogo de clave pública SSH en la creación de clústeres de HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | Use el parámetro `-SshPublicKey` del cmdlet `New-AzureRmHdinsightCluster` y pase el contenido de la clave pública como una cadena.|
| **CLI de Azure 1.0** | Use el parámetro `--sshPublicKey` del comando `azure hdinsight cluster create` y pase el contenido de la clave pública como una cadena. |
| **Plantilla de Resource Manager** | Para obtener un ejemplo del uso de claves SSH con una plantilla, consulte la [implementación de HDInsight en Linux con una clave SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). El elemento `publicKeys` en el archivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) se usa para pasar las claves a Azure al crear el clúster. |

## <a id="sshpassword"></a>Autenticación: contraseña

Las cuentas SSH se pueden proteger mediante una contraseña. Cuando se conecta a HDInsight usando SSH, se le pedirá que escriba la contraseña.

> [!WARNING]
> No se recomienda usar autenticación de contraseña para SSH. Las contraseñas se pueden adivinar y son vulnerables a ataques por fuerza bruta. En su lugar, recomendamos que use [claves SSH para la autenticación](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>Creación de HDInsight usando una contraseña

| Método de creación | Cómo especificar la contraseña |
| --------------- | ---------------- |
| **Portal de Azure** | De forma predeterminada, la cuenta de usuario SSH tiene la misma contraseña que la cuenta de inicio de sesión de clúster. Para usar una contraseña diferente, desactive la opción __Utilizar la misma contraseña que en el inicio de sesión del clúster__y, a continuación, escriba la contraseña en el campo __Contraseña SSH__.</br>![Cuadro de diálogo de contraseña SSH en la creación de clústeres de HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | Use el parámetro `--SshCredential` del cmdlet `New-AzureRmHdinsightCluster` y pase un objeto `PSCredential` que contiene el nombre de cuenta de usuario SSH y la contraseña. |
| **CLI de Azure 1.0** | Use el parámetro `--sshPassword` del comando `azure hdinsight cluster create` y proporcione el valor de contraseña. |
| **Plantilla de Resource Manager** | Para obtener un ejemplo del uso de contraseña con una plantilla, consulte la [implementación de HDInsight en Linux con una contraseña SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). El elemento `linuxOperatingSystemProfile` en el archivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) se usa para pasar el nombre de cuenta de SSH y la contraseña a Azure al crear el clúster.|

### <a name="change-the-ssh-password"></a>Cambio de contraseña de SSH

Para información acerca de cómo cambiar la contraseña de cuenta de usuario SSH, consulte la sección __Cambio de contraseñas__ del documento sobre [administración de HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a id="domainjoined"></a>Autenticación: HDInsight unido a un dominio

Si está usando un __clúster de HDInsight unido a un dominio__, tiene que utilizar el comando `kinit` después de conectarse con SSH. Este comando le pide un usuario de dominio y una contraseña y autentica la sesión con el dominio de Azure Active Directory asociado con el clúster.

Para más información, consulte [Configuración de clústeres de HDInsight unidos a un dominio](hdinsight-domain-joined-configure.md).

## <a name="connect-to-worker-and-zookeeper-nodes"></a>Conexión a los nodos de Zookeeper y de trabajo

No es posible tener acceso directo a los nodos de trabajo y los nodos Zookeeper directamente desde Internet, pero sí es posible hacerlo desde los nodos principales o perimetrales del clúster. Estos son los pasos generales para conectarse a otros nodos:

1. Use SSH para conectarse al nodo principal o perimetral:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. En la conexión SSH con el nodo principal o perimetral, use el comando `ssh` para conectarse a un nodo de trabajador en el clúster:

        ssh sshuser@wn0-myhdi

    Para recuperar una lista de los nombres de dominio de los nodos del clúster, consulte los ejemplos en el documento [Administración de clústeres de HDInsight con la API de REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Si la cuenta SSH se protege usando una __contraseña__, se le pide que la escriba y se establece la conexión.

Si a cuenta SSH se protege usando __claves SSH__, tiene que asegurarse de que su entorno local está configurado para reenviar el agente SSH.

> [!NOTE]
> Otra manera de obtener acceso directo a todos los nodos del clúster es instalar HDInsight en una instancia de Azure Virtual Network. A continuación, puede unir la máquina remota a la misma red virtual y tener acceso directamente a todos los nodos del clúster.
>
> Para más información, consulte el documento sobre el [uso de una red virtual con HDInsight](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>Configuración del reenvío del agente SSH

> [!IMPORTANT]
> Los pasos siguientes presuponen un sistema basado en Linux/UNIX y funcionan con Bash en Windows 10. Si estos pasos no funcionan en su sistema, es posible que deba consultar la documentación para el cliente SSH.

1. Abra `~/.ssh/config`con un editor de texto. Si este archivo no existe, puede crearlo si escribe `touch ~/.ssh/config` en la línea de comandos.

2. Agregue al archivo `config` el texto siguiente.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Reemplace la información de __Host__ con la dirección del nodo al que se conecta a través de SSH. El ejemplo anterior utiliza el nodo perimetral. Esta entrada configura el reenvío del agente SSH para el nodo especificado.

3. Use el siguiente comando desde el terminal para probar el desvío del agente SSH:

        echo "$SSH_AUTH_SOCK"

    Este comando devuelve información similar al texto siguiente:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si no se devuelve nada, `ssh-agent` no se está ejecutando. Consulte la información de los scripts de inicio de agente en [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Uso de ssh-agent con ssh) o la documentación del cliente SSH para ver pasos específicos sobre cómo instalar y configurar `ssh-agent`.

4. Una vez que haya comprobado que **ssh-agent** está en ejecución, use lo siguiente para agregar la clave privada SSH al agente:

        ssh-add ~/.ssh/id_rsa

    Si la clave privada está almacenada en un archivo distinto, reemplace `~/.ssh/id_rsa` por la ruta de acceso al archivo.

5. Conecte con el nodo perimetral o nodos principales del clúster usando SSH. A continuación, use el comando SSH para conectarse a un nodo de trabajo o de zookeeper. La conexión se establece mediante la clave reenviada.

## <a name="next-steps"></a>Pasos siguientes

* [Uso de la tunelización de SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)
* [Uso de una red virtual con HDInsight](hdinsight-extend-hadoop-virtual-network.md)
* [Uso de nodos perimetrales en HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)

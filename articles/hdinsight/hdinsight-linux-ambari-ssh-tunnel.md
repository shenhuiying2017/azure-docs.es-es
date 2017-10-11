---
title: "Uso de la tunelización de SSH para acceder a Azure HDInsight | Microsoft Docs"
description: "Obtenga información acerca de cómo usar un túnel SSH para ir con seguridad a los recursos web alojados en los nodos de HDInsight basados en Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/21/2017
ms.author: larryfr
ms.openlocfilehash: 4b606ea3797d685b9deacf72f1bd31e0ef007f98
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Uso de la tunelización SSH para tener acceso a la interfaz de usuario Ambari Web, JobHistory, NameNode, Oozie y otras interfaces de usuario web

Los clústeres de HDInsight basados en Linux proporcionan acceso a la interfaz de usuario web de Ambari en Internet, pero no a algunas características de la interfaz de usuario. Por ejemplo, la interfaz de usuario web para otros servicios que se muestran en Ambari. Para usar la funcionalidad completa de la interfaz de usuario web de Ambari, use un túnel SSH para el nodo principal del clúster.

## <a name="why-use-an-ssh-tunnel"></a>¿Por qué usar un túnel SSH?

Algunos de los menús de Ambari solo funcionan a través de un túnel SSH. Estos menús se basan en sitios web y servicios que se ejecutan en otros tipos de nodo, como nodos de trabajo. A menudo, estos sitios web no están protegidos, por lo que no es seguro exponerlos directamente en internet.

Las siguientes interfaces de usuario web requieren un túnel SSH:

* Historial de trabajos
* NameNode
* Pilas de subprocesos
* Interfaz de usuario web de Oozie
* Interfaz de usuario de registros y maestro de HBase

Si usa las acciones de script para personalizar el clúster, todos los servicios o utilidades que instale y expongan una interfaz de usuario web requerirán un túnel SSH. Por ejemplo, si instala Hue mediante una acción de script, debe usar un túnel SSH para tener acceso a la interfaz de usuario web de Hue.

> [!IMPORTANT]
> Si tiene acceso directo a HDInsight a través de una red virtual, no es necesario usar túneles SSH. Para ver un ejemplo de acceso directo a HDInsight a través de una red virtual, consulte el documento [Conexión de HDInsight a la red local](connect-on-premises-network.md).

## <a name="what-is-an-ssh-tunnel"></a>¿Qué es un túnel SSH?

[Secure Shell (SSH) tunneling](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) (Tunelización de Secure Shell [SSH]) enruta el tráfico enviado a un puerto en la estación de trabajo local. El tráfico se enruta a través de una conexión SSH al nodo principal del clúster de HDInsight. La solicitud se resuelve como si se originara en el nodo principal. A continuación, la respuesta se enruta a través del túnel a la estación de trabajo.

## <a name="prerequisites"></a>Requisitos previos

* Un cliente SSH. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Un explorador web que se puede configurar para usar un proxy SOCKS5.

    > [!WARNING]
    > La compatibilidad con el proxy SOCKS integrada en Windows no incluye SOCKS5 y no funciona con los pasos descritos en este documento. Los siguientes exploradores se basan en la configuración de proxy de Windows y actualmente no funcionan con los pasos descritos en este documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome también se basa en la configuración de proxy de Windows. Sin embargo, se pueden instalar extensiones que admiten SOCKS5. Se recomienda [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Creación de un túnel con el comando SSH

Use el siguiente comando para crear un túnel SSH con el comando `ssh` . Reemplace **USERNAME** por un usuario SSH para su clúster de HDInsight y reemplace **CLUSTERNAME** por el nombre de su clúster de HDInsight:

```bash
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

Este comando crea una conexión que enruta el tráfico al puerto local 9876 al clúster a través de SSH. Las opciones son:

* **D 9876** : el puerto local que enruta el tráfico a través del túnel.
* **C** : comprimir todos los datos, debido a que el tráfico web es principalmente texto.
* **2** : forzar a SSH a que intente solo la versión 2 del protocolo.
* **q** : modo silencioso.
* **T** : deshabilitar la asignación seudotty, debido a que solo estamos desviando un puerto.
* **n** : evitar la lectura de STDIN, debido a que solo estamos desviando un puerto.
* **N** : no ejecutar un comando remoto, debido a que solo estamos desviando un puerto.
* **f** : ejecutar en segundo plano.

Una vez que se completa el comando, el tráfico enviado al puerto 9876 de la máquina local se enruta al nodo principal del cluster.

## <a name="useputty"></a>Creación de un túnel mediante PuTTY

[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) es un cliente SSH gráfico para Windows. Use los siguientes pasos para crear un túnel SSH con PuTTY:

1. Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, consulte la [documentación de PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html).

2. En la sección **Category** (Categoría) a la izquierda del cuadro de diálogo, expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Tunnels** (Túneles).

3. Proporcione la siguiente información en el formulario **Options controlling SSH port forwarding** (Opciones que controlan el desvío de puertos SSH):
   
   * **Source port** : el puerto en el cliente que desea desviar. Por ejemplo, **9876**.

   * **Destination** : la dirección SSH del clúster de HDInsight basado en Linux. Por ejemplo, **mycluster-ssh.azurehdinsight.net**.

   * **Dynamic** : habilita el enrutamiento dinámico del proxy SOCKS.
     
     ![imagen de las opciones de tunelización](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Haga clic en **Add** (Agregar) para agregar la configuración y, a continuación, en **Open** (Abrir) para abrir una conexión SSH.

5. Cuando se le solicite, inicie sesión en el servidor.

## <a name="use-the-tunnel-from-your-browser"></a>Uso del túnel desde el explorador

> [!IMPORTANT]
> Los pasos de esta sección usan el explorador Mozilla FireFox, ya que proporciona la misma configuración de proxy para todas las plataformas. Otros exploradores modernos, como Google Chrome, pueden requerir una extensión como FoxyProxy para funcionar con el túnel.

1. Configure el explorador para usar **localhost** y el puerto que utilizó al crear el túnel como un proxy **SOCKS v5**. La configuración de Firefox se verá de la siguiente manera. Si usa un puerto que no es 9876, cambie el puerto al que usa:
   
    ![imagen de la configuración de Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > La selección de **DNS remoto** resuelve las solicitudes del sistema de nombres de dominio (DNS) mediante el uso del clúster de HDInsight. Esta configuración resuelve el DNS con el nodo principal del clúster.

2. Compruebe que el túnel funciona, para ello, visite un sitio como [http://www.whatismyip.com/](http://www.whatismyip.com/). La dirección IP devuelta debe ser una que use el centro de datos de Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Compruebe con la interfaz de usuario web de Ambari

Una vez que se ha establecido el clúster, siga estos pasos para comprobar que puede tener acceso a las interfaces de usuario web del servicio desde la web de Ambari:

1. En el explorador, vaya a http://headnodehost:8080. La dirección `headnodehost` se envía al clúster a través del túnel y se resuelve en el nodo principal en el que se ejecuta Ambari. Cuando se le solicite, escriba el nombre de usuario administrador (admin) y la contraseña del clúster. Es posible que se le pida una segunda vez mediante la interfaz de usuario web de Ambari. Si es así, vuelva a escribir la información.

   > [!NOTE]
   > Si utiliza la dirección de http://headnodehost:8080 para conectarse al clúster, se está conectando a través del túnel. La comunicación se protege si usa el túnel SSH en lugar de HTTPS. Para conectarse a través de Internet mediante HTTPS, use https://CLUSTERNAME.azurehdinsight.net, donde **CLUSTERNAME** es el nombre del clúster.

2. En la interfaz de usuario de Ambari Web, seleccione HDFS en la lista de la izquierda de la página.

    ![Imagen con HDFS seleccionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Cuando se muestra la información del servicio HDFS, seleccione **Vínculos rápidos**. Aparece una lista de los nodos del clúster principal. Seleccione uno de los nodos principales y luego seleccione **IU de NameNode**.

    ![Imagen con el menú Vínculos rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > Al seleccionar __Vínculos rápidos__, es posible que obtenga un indicador de espera. Esta condición puede ocurrir si tiene una conexión lenta a Internet. Espere un minuto o dos hasta que se reciban los datos del servidor e intente de nuevo la lista.
   >
   > Algunas entradas en el menú **Vínculos rápidos** pueden quedar cortadas en el lado derecho de la pantalla. Si es así, expanda el menú con el mouse y use la tecla de dirección derecha para desplazarse por la pantalla hacia la derecha para ver el resto del menú.

4. Aparece una página similar a la siguiente imagen:

    ![Imagen de la interfaz de usuario de NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]
   > Observe la dirección URL de esta página, debe ser similar a **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Este identificador URI usa el nombre de dominio completo interno (FQDN) del nodo y solo es accesible si se usa un túnel SSH.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a crear y usar un túnel SSH, consulte el documento siguiente para conocer otras formas de usar Ambari:

* [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).


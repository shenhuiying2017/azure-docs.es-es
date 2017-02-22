---
title: "Uso de la tunelización de SSH para acceder a servicios de Azure HDInsight | Microsoft Docs"
description: "Obtenga información acerca de cómo usar un túnel SSH para ir con seguridad a los recursos web alojados en los nodos de HDInsight basados en Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5ec4b964066687b506686709c3dc5ed5b402fbaf
ms.openlocfilehash: 8045f9d927e9c877573085eb43eaadcd60f96a67


---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Uso de la tunelación SSH para tener acceso a la interfaz de usuario Ambari Web, JobHistory, NameNode, Oozie y otras interfaces de usuario web

Los clústeres de HDInsight basados en Linux proporcionan acceso a la interfaz de usuario web de Ambari en Internet, pero no a algunas características de la interfaz de usuario. Por ejemplo, la interfaz de usuario web para otros servicios que se muestran en Ambari. Para usar la funcionalidad completa de la interfaz de usuario web de Ambari, use un túnel SSH para el nodo principal del clúster.

## <a name="why-use-an-ssh-tunnel"></a>¿Por qué usar un túnel SSH?

Algunos de los menús de Ambari no se rellenan totalmente sin un túnel SSH, ya que se basan en sitios web y servicios expuestos por otros servicios de Hadoop que se ejecutan en el clúster. A menudo, estos sitios web no están protegidos, por lo que no es seguro exponerlos directamente en internet. A veces, el servicio ejecuta el sitio web en otro nodo del clúster como un nodo de Zookeeper.

Los siguientes son servicios usados por la interfaz de usuario web de Ambari a los que no se puede tener acceso sin un túnel SSH:

* Historial de trabajos
* NameNode
* Pilas de subprocesos
* Interfaz de usuario web de Oozie
* Interfaz de usuario de registros y maestro de HBase

Si usa las acciones de script para personalizar el clúster, todos los servicios o utilidades que instale y expongan una interfaz de usuario web requerirán un túnel SSH. Por ejemplo, si instala Hue mediante una acción de script, debe usar un túnel SSH para tener acceso a la interfaz de usuario web de Hue.

## <a name="what-is-an-ssh-tunnel"></a>¿Qué es un túnel SSH?

[Túnel Shell seguro (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) enruta el tráfico enviado a un puerto en su estación de trabajo local, a través de una conexión SSH con el nodo principal del clúster de HDInsight, donde la solicitud se resuelve como si se hubiera originado en el nodo principal. A continuación, la respuesta se enruta a través del túnel a la estación de trabajo.

## <a name="prerequisites"></a>Requisitos previos

Cuando se usa un túnel SSH para el tráfico web, debe tener lo siguiente:

* Un cliente SSH. Para las distribuciones de Linux y Unix, Macintosh OS X y Bash en Windows 10, el comando `ssh` se proporciona con el sistema operativo. Para las versiones de Windows que no incluyen el comando `ssh`, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
  
  > [!NOTE]
  > Si desea usar un cliente SSH distinto de `ssh` o PuTTY, vea la documentación de su cliente sobre cómo establecer un túnel SSH.

* Un explorador web que se puede configurar para usar un proxy SOCKS

## <a name="a-nameusesshacreate-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Creación de un túnel con el comando SSH

Use el siguiente comando para crear un túnel SSH con el comando `ssh` . Reemplace **USERNAME** por un usuario SSH para su clúster de HDInsight y reemplace **CLUSTERNAME** por el nombre de su clúster de HDInsight

```
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

Con esto se crea una conexión que enruta el tráfico al puerto local 9876 al clúster sobre SSH. Las opciones son:

* **D 9876** : el puerto local que enruta el tráfico a través del túnel.
* **C** : comprimir todos los datos, debido a que el tráfico web es principalmente texto.
* **2** : forzar a SSH a que intente solo la versión 2 del protocolo.
* **q** : modo silencioso.
* **T** : deshabilitar la asignación seudotty, debido a que solo estamos desviando un puerto.
* **n** : evitar la lectura de STDIN, debido a que solo estamos desviando un puerto.
* **N** : no ejecutar un comando remoto, debido a que solo estamos desviando un puerto.
* **f** : ejecutar en segundo plano.

Si ha configurado el clúster con una clave SSH, es posible que tenga que usar el parámetro `-i` y especificar la ruta de acceso a la clave SSH privada.

Una vez que se completa el comando, el tráfico enviado al puerto 9876 del equipo local se enruta sobre Capa de sockets seguros (SSL) al nodo principal del clúster y aparece como originado ahí.

## <a name="a-nameuseputtyacreate-a-tunnel-using-putty"></a><a name="useputty"></a>Creación de un túnel mediante PuTTY

Use los siguientes pasos para crear un túnel SSH con PuTTY.

1. Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre cómo usarlo con HDInsight.

2. En la sección **Category** (Categoría) a la izquierda del cuadro de diálogo, expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Tunnels** (Túneles).

3. Proporcione la siguiente información en el formulario **Options controlling SSH port forwarding** (Opciones que controlan el desvío de puertos SSH):
   
   * **Source port** : el puerto en el cliente que desea desviar. Por ejemplo, **9876**.

   * **Destination** : la dirección SSH del clúster de HDInsight basado en Linux. Por ejemplo, **mycluster-ssh.azurehdinsight.net**.

   * **Dynamic** : habilita el enrutamiento dinámico del proxy SOCKS.
     
     ![imagen de las opciones de tunelización](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Haga clic en **Add** (Agregar) para agregar la configuración y, a continuación, en **Open** (Abrir) para abrir una conexión SSH.

5. Cuando se le solicite, inicie sesión en el servidor. Esto establece una sesión SSH y habilita el túnel.

## <a name="use-the-tunnel-from-your-browser"></a>Uso del túnel desde el explorador

> [!NOTE]
> Los pasos de esta sección usan el explorador FireFox, ya que es gratuito para sistemas Linux, Unix, Macintosh OS X y Windows. También funcionan correctamente otros exploradores modernos que admiten el uso de un proxy SOCKS.

1. Configure el explorador para usar **localhost** y el puerto que utilizó al crear el túnel como un proxy **SOCKS v5**. La configuración de Firefox se verá de la siguiente manera. Si usa un puerto que no es 9876, cambie el puerto al que usa:
   
    ![imagen de la configuración de Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > La selección de **DNS remoto** resuelve las solicitudes del sistema de nombres de dominio (DNS) mediante el uso del clúster de HDInsight. Si no se selecciona, el DNS se resuelve de manera local.

2. Compruebe que el tráfico se enruta a través del túnel en un sitio como [http://www.whatismyip.com/](http://www.whatismyip.com/) con la configuración del proxy habilitada y deshabilitada en Firefox. Cuando la configuración está habilitada, la dirección IP se devuelve desde una máquina del centro de datos de Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Compruebe con la interfaz de usuario web de Ambari

Una vez que se ha establecido el clúster, siga estos pasos para comprobar que puede tener acceso a las interfaces de usuario web del servicio desde la web de Ambari:

1. En el explorador, vaya a http://headnodehost:8080. La dirección `headnodehost` se envía al clúster a través del túnel y se resuelve en el nodo principal en el que se ejecuta Ambari. Cuando se le solicite, escriba el nombre de usuario administrador (admin) y la contraseña del clúster. Es posible que se le pida una segunda vez mediante la interfaz de usuario web de Ambari. Si es así, vuelva a escribir la información.
   
   > [!NOTE]
   > Cuando se usa la dirección http://headnodehost:8080 para conectarse al clúster, se estará conectando directamente, a través del túnel, al nodo principal en el que se está ejecutando Ambari mediante HTTP (la comunicación se protege usando el túnel SSH). Si se conecta a través de Internet sin utilizar un túnel, la comunicación se protegerá mediante HTTPS. Para conectarse a través de Internet mediante HTTPS, use https://CLUSTERNAME.azurehdinsight.net, donde **CLUSTERNAME** es el nombre del clúster.

2. En la interfaz de usuario de Ambari Web, seleccione HDFS en la lista de la izquierda de la página.
   
    ![Imagen con HDFS seleccionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)
3. Cuando se muestra la información del servicio HDFS, seleccione **Vínculos rápidos**. Aparece una lista de los nodos del clúster principal. Seleccione uno de los nodos principales y luego seleccione **IU de NameNode**.
   
    ![Imagen con el menú Vínculos rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)
   
   > [!NOTE]
   > Si tiene una conexión a Internet lenta o el nodo principal está muy ocupado, es posible que obtenga un indicador de espera en lugar de un menú al seleccionar **Vínculos rápidos**. Si es así, espere un minuto o dos hasta que se reciban los datos del servidor e intente de nuevo la lista.
   > 
   > Si tiene un monitor con una resolución inferior o no se maximiza la ventana del explorador, algunas entradas del menú **Vínculos rápidos** puede aparecer cortadas por el lado derecho de la pantalla. Si es así, expanda el menú con el mouse, use la tecla de flecha derecha para desplazarse por la pantalla hacia la derecha para ver el resto del menú.
   > 
   > 
4. Debería ver una página similar a la siguiente:
   
    ![Imagen de la interfaz de usuario de NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)
   
   > [!NOTE]
   > Observe la dirección URL de esta página, debe ser similar a **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Esto usa el nombre de dominio completo interno (FQDN) del nodo y no es accesible sin usar un túnel SSH.
   > 
   > 

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a crear y usar un túnel SSH, consulte lo siguiente para obtener información sobre supervisión y administración del clúster con Ambari:

* [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

Para obtener más información sobre el uso de SSH con HDInsight, vea lo siguiente:

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)




<!--HONumber=Feb17_HO2-->



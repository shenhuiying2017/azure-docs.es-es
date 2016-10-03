<properties
	pageTitle="Uso de un espacio aislado de Hadoop para obtener información sobre Hadoop | Microsoft Azure"
	description="Para empezar a obtener información sobre el ecosistema de Hadoop, puede configurar un espacio aislado de Hadoop desde Hortonworks en una máquina virtual de Azure. "
	keywords="emulador de hadoop, espacio aislado de hadoop"
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="nitinme"/>

# Introducción al ecosistema de Hadoop con un espacio aislado de Hadoop en una máquina virtual

Aprenda a instalar el espacio aislado de Hadoop desde Hortonworks en una máquina virtual para obtener información sobre el ecosistema de Hadoop. El espacio aislado proporciona un entorno de desarrollo local para comprender Hadoop, el sistema de archivos distribuido de Hadoop (HDFS) y el envío de trabajos.

## Requisitos previos

* [Oracle VirtualBox](https://www.virtualbox.org/)

Cuando se haya familiarizado con Hadoop, puede empezar a usarlo en Azure mediante la creación de un clúster de HDInsight. Para obtener más información sobre cómo empezar, consulte [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

## Descarga e instalación de la máquina virtual

1. En [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox), seleccione el elemento para __DESCARGAR VIRTUALBOX__ para HDP 2.4 en Sandbox de Hortonworks. Se le pedirá que se registre en Hortonworks antes de que comience la descarga.

    ![Imagen del vínculo para descargar Sandbox de Hortonworks para VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)

2. En la misma página web, seleccione el enlace que corresponde a la __guía de instalación de VirtualBox__ para HDP 2.4 en Sandbox de Hortonworks. Se descargará un PDF que contiene instrucciones de instalación para la máquina virtual.

    ![Consultar la guía de instalación](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png)

## Inicio de la máquina virtual

1. Inicie VirtualBox, seleccione Hortonworks Sandbox y luego __Start__ (Inicio) y __Normal Start__ (Inicio normal).

    ![Inicio normal](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)

2. Una vez que la máquina virtual ha terminado el proceso de arranque, mostrará las instrucciones de inicio de sesión. Abra un explorador web y vaya a la dirección URL que se muestra (normalmente http://127.0.0.1:8888).

## Establecimiento de contraseñas

1. En el paso __get started__ (introducción) de la página de Sandbox de Hortonworks, seleccione __View Advanced Options__ (Ver opciones avanzadas). Utilice la información de esta página para iniciar sesión en el espacio aislado usando SSH. Utilice el nombre y la contraseña proporcionada.

    > [AZURE.NOTE] Si no tiene instalado un cliente SSH, puede usar el SSH basado en web que se proporciona en la máquina virtual en http://localhost:4200/__.

    La primera vez que se conecta a través de SSH, se le solicitará que cambie la contraseña para la cuenta raíz. Escriba una contraseña nueva, que se utilizará cuando inicie sesión con SSH en el futuro.

2. Una vez que haya iniciado sesión, escriba el comando siguiente:

        ambari-admin-password-reset
    
    Cuando se le solicite, proporcione una contraseña para la cuenta de administración de Ambari. Esta se utilizará para obtener acceso a la interfaz de usuario de la web de Ambari.

## Uso del comando de Hive

1. Desde una conexión SSH en el espacio aislado, utilice el siguiente comando para iniciar el shell de Hive:

        hive

2. Una vez que se inició el shell, use lo siguiente para ver las tablas que se proporcionan con el espacio aislado:

        show tables;

3. Utilice lo siguiente para recuperar 10 filas de la tabla `sample_07`:

        select * from sample_07 limit 10;

## Pasos siguientes

* [Aprenda a usar Visual Studio con Sandbox de Hortonworks](hdinsight-hadoop-emulator-visual-studio.md)
* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/) (Aprendizaje del funcionamiento de Sandbox de Hortonworks)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/) (Tutorial de Hadoop: introducción a HDP)

<!---HONumber=AcomDC_0921_2016-->
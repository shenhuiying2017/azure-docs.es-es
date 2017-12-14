---
title: Empleo de un espacio aislado o emulador de Hadoop - Azure HDInsight | Microsoft Docs
description: "Para empezar a obtener información sobre el ecosistema de Hadoop, puede configurar un espacio aislado de Hadoop desde Hortonworks en una máquina virtual de Azure. "
keywords: emulador de hadoop, espacio aislado de hadoop
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: d7df18a80470beb8dc25cf6add6b7a61f45dcfe7
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Empleo de un espacio aislado de Hadoop, un emulador en una máquina virtual

Aprenda a instalar el espacio aislado de Hadoop desde Hortonworks en una máquina virtual para obtener información sobre el ecosistema de Hadoop. El espacio aislado proporciona un entorno de desarrollo local para comprender Hadoop, el sistema de archivos distribuido de Hadoop (HDFS) y el envío de trabajos. Cuando se haya familiarizado con Hadoop, puede empezar a usarlo en Azure mediante la creación de un clúster de HDInsight. Para obtener más información sobre cómo empezar, consulte [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Requisitos previos
* [Oracle VirtualBox](https://www.virtualbox.org/). Descárguelo e instálelo [aquí](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Descarga e instalación de la máquina virtual
1. Vaya a las [descargas de Hortonworks](http://hortonworks.com/downloads/#sandbox).

2. Haga clic en **DOWNLOAD FOR VIRTUALBOX** (DESCARGAR PARA VIRTUALBOX) para descargar la última versión de Hortonworks Sandbox en una máquina virtual. Se le pedirá que se registre en Hortonworks para poder descargar. La descarga tarda de una a dos horas según la velocidad de la red.
   
    ![Imagen del vínculo para descargar Sandbox de Hortonworks para VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. En la misma página web, haga clic en el vínculo **Import on Virtual Box** (Importar en Virtual Box para descargar un archivo PDF que contiene las instrucciones de instalación de la máquina virtual.

Para descargar un espacio aislado de una versión más antigua de HDP, expanda el archivo:

![Archivo de Hortonworks Sandbox](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Inicio de la máquina virtual

1. Abra Oracle VM VirtualBox.
2. En el menú **File** (Archivo), haga clic en **Import Appliance** (Importar aplicación) y luego especifique la imagen de Hortonworks Sandbox.
1. Seleccione el espacio aislado Hortonworks Sandbox, haga clic en **Start** (Iniciar) y luego en **Normal Start** (Inicio normal). Una vez que la máquina virtual ha terminado el proceso de arranque, muestra instrucciones de inicio de sesión.
   
    ![Normal Start](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Abra un explorador web y vaya a la dirección URL que se muestra (normalmente http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Establecimiento de las contraseñas del espacio aislado

1. En el paso **get started** (introducción) de la página Sandbox de Hortonworks, seleccione **View Advanced Options** (Ver opciones avanzadas). Use la información de esta página para iniciar sesión en el espacio aislado mediante SSH. Utilice el nombre y la contraseña proporcionada.
   
   > [!NOTE]
   > Si no tiene instalado un cliente SSH, puede usar el SSH web que proporciona la máquina virtual en **http://localhost:4200/**.
   > 
   
    La primera vez que se conecte mediante SSH, se le pedirá que cambie la contraseña de la cuenta raíz. Escriba una contraseña nueva, que se usará al iniciar sesión mediante SSH.

2. Una vez que haya iniciado sesión, escriba el comando siguiente:
   
        ambari-admin-password-reset
   
    Cuando se le solicite, proporcione una contraseña para la cuenta de administración de Ambari. Esta se usará para tener acceso a la interfaz de usuario web de Ambari.

## <a name="use-hive-commands"></a>Uso de comandos de Hive

1. Desde una conexión SSH en el espacio aislado, utilice el siguiente comando para iniciar el shell de Hive:
   
        hive
2. Una vez que se inició el shell, use lo siguiente para ver las tablas que se proporcionan con el espacio aislado:
   
        show tables;
3. Utilice lo siguiente para recuperar 10 filas de la tabla `sample_07` :
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Pasos siguientes
* [Aprenda a usar Visual Studio con Sandbox de Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)
* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)


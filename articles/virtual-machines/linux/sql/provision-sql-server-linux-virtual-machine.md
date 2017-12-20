---
title: "Creación de una máquina virtual Linux con SQL Server 2017 en Azure | Microsoft Docs"
description: "Este tutorial muestra cómo crear una máquina virtual Linux con SQL Server 2017 en Azure Portal."
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 10/25/2017
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: 8d714718e4fc27f0abbfede8fe6d0e2693698ec1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Aprovisionamiento de una máquina virtual Linux con SQL Server en Azure Portal

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

En este tutorial de inicio rápido, se usa Azure Portal para crear una máquina virtual Linux con SQL Server 2017 instalado.

En este tutorial, aprenderá lo siguiente:

* [Crear una máquina virtual Linux con SQL desde la Galería](#create)
* [Conectarse a la nueva máquina virtual con SSH](#connect)
* [Cambiar la contraseña de SA](#password)
* [Realizar la configuración para conexiones remotas](#remote)

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free) antes de empezar.

## <a id="create"></a>Creación de una máquina virtual Linux con SQL Server instalado

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

1. En el panel izquierdo, haga clic en **Nuevo**.

1. En el panel **Nuevo**, haga clic en **Proceso**.

1. Haga clic en **See all** (Ver todos) junto al encabezado **Featured** (Destacados).

   ![Ver todas las imágenes de máquina virtual](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. En el cuadro de búsqueda, escriba **SQL Server 2017** y presione **Entrar** para iniciar la búsqueda.

1. Haga clic en el icono **Filtro**, limite la búsqueda a las imágenes **basadas en Linux** de **Microsoft** y haga clic en **Hecho**.

    ![Filtro de búsqueda para imágenes de máquina virtual con SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Seleccione una imagen Linux con SQL Server 2017 de los resultados de búsqueda. En este tutorial se usa **Free SQL Server License: SQL Server 2017 Developer on Red Hat Enterprise Linux 7.4**.

   > [!TIP]
   > La edición Developer permite probar o desarrollar con las características de la edición Enterprise, pero sin los costos de licencia de SQL Server. Solo se paga el costo de ejecutar la máquina virtual Linux.

1. Haga clic en **Crear**.

1. En la ventana **Fundamentos**, rellene los detalles de la máquina virtual Linux. 

    ![Ventana Fundamentos](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > Para la autenticación, puede una clave pública SSH o una contraseña. La opción de SSH es más segura. Para obtener instrucciones acerca de cómo generar una clave SSH, consulte [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Haga clic en **Aceptar**.

1. En la ventana **Tamaño**, elija el tamaño de la máquina. Para ver otros tamaños, seleccione **Ver todos**. Para más información acerca de los tamaños de máquina virtual, consulte [Tamaños de las máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Selección del tamaño de la máquina virtual](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Para el desarrollo y las pruebas funcionales, se recomienda un tamaño de memoria virtual **DS2**, o superior. Para las pruebas de rendimiento, use **DS13**, o un tamaño superior.

1. Haga clic en **Seleccionar**.

1. En la ventana **Configuración** ventana, puede realizar cambios en la configuración o mantener la configuración predeterminada.

1. Haga clic en **Aceptar**.

1. En la página **Resumen**, haga clic en **Comprar** para crear la máquina virtual.

## <a id="connect"></a>Conexión a la máquina virtual Linux

Si ya utiliza un shell de BASH, conéctese a la máquina virtual de Azure mediante el comando **ssh**. En el siguiente comando, reemplace el nombre de usuario y la dirección IP de la máquina virtual para conectarse a su máquina virtual Linux.

```bash
ssh azureadmin@40.55.55.555
```

La dirección IP de cualquier máquina virtual se puede encontrar en Azure Portal.

![Dirección IP en Azure Portal](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Si utiliza Windows y no tiene un shell de BASH, puede instalar un cliente de SSH, como PuTTY.

1. [Descargue e instale PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Ejecute PuTTY.

1. En la pantalla de configuración de PuTTY, escriba la dirección IP pública de la máquina virtual.

1. Haga clic en Abrir y escriba el nombre de usuario y contraseña en los cuadros.

Para más información acerca de cómo conectarse a máquinas virtuales Linux, consulte [Creación de máquinas virtuales Linux con Azure Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

## <a id="password"></a>Cambio de la contraseña de SA

La máquina virtual nueva instala SQL Server con una contraseña de SA aleatoria. Dicha contraseña se debe restablecer para poder conectarse a SQL Server con el inicio de sesión de SA.

1. Después de conectarse a su máquina virtual Linux, abra un terminal de comandos nuevo.

1. Cambie la contraseña de SA con los siguientes comandos:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Escriba una nueva contraseña de SA y la confirmación de contraseña cuando se le solicite.

1. Reinicie el servicio de SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Adición de las herramientas a la ruta de acceso (opcional)

De manera predeterminada se instalan varios [paquetes](sql-server-linux-virtual-machines-overview.md#packages) de SQL Server, entre los que se incluye el paquete de herramientas de línea de comandos de SQL Server. El paquete de herramientas contiene las herramientas **sqlcmd** y **bcp**. Para mayor comodidad, puede agregar la ruta de acceso de herramientas, `/opt/mssql-tools/bin/`, a su variable de entorno **PATH**.

1. Ejecute los siguientes comandos para modificar **PATH** tanto para sesiones de inicio de sesión como para sesiones interactivas o no de inicio de sesión:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a>Configuración para conexiones remotas

Si necesita conectarse remotamente a SQL Server en la máquina virtual de Azure, debe configurar una regla de entrada en el grupo de seguridad de red. La regla permite el tráfico en el puerto en el que SQL Server escucha (valor predeterminado de 1433). Los pasos siguientes muestran cómo usar Azure Portal para este paso. 

1. En el portal, seleccione **Máquinas virtuales**y, luego, seleccione su máquina virtual de SQL Server.

1. En la lista de propiedades, seleccione **Redes**.

1. En la ventana **Redes** ventana, haga clic en el botón **Agregar** situado bajo **Reglas de puerto de entrada**.

   ![Reglas de puerto de entrada](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. En la lista **Servicio**, seleccione **MS SQL**.

    ![Regla de grupo de seguridad de red de MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Haga clic en **Aceptar** para guardar la regla correspondiente a su máquina virtual.

### <a name="open-the-firewall-on-rhel"></a>Apertura del firewall en RHEL

Este tutorial le ha indicado que cree una máquina virtual de Red Hat Enterprise Linux (RHEL). Si desea conectarse de forma remota a máquinas virtuales de RHEL, también tiene que abrir el puerto 1433 en el firewall de Linux.

1. [Conéctese](#connect) a su máquina virtual de RHEL.

1. En el shell de BASH, ejecute los siguientes comandos:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una máquina virtual con SQL Server 2017 en Azure, puede conectarse localmente con **sqlcmd** para ejecutar consultas Transact-SQL.

Si ha configurado la máquina virtual de Azure para conexiones remotas con SQL Server, también debería poder conectarse de forma remota. Para ver un ejemplo de cómo conectarse de forma remota a SQL Server en Linux desde Windows, consulte [Usar SQL Server Management Studio (SSMS) de Windows para conectarse a SQL Server en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Para conectar con Visual Studio Code, consulte [Usar Visual Studio Code para crear y ejecutar scripts de Transact-SQL para SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

Para obtener información general acerca de SQL Server en Linux, consulte [Información general de SQL Server de 2017 en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Para más información acerca de cómo usar máquinas virtuales Linux con SQL Server 2017, consulte [Overview of SQL Server 2017 virtual machines on Azure](sql-server-linux-virtual-machines-overview.md) (Introducción a las máquinas virtuales con SQL Server 2017 en Azure).

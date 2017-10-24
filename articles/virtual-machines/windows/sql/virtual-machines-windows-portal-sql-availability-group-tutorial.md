---
title: Grupos de disponibilidad de SQL Server - Azure Virtual Machines - Tutorial | Microsoft Docs
description: "Este tutorial muestra cómo crear un grupo de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 228ca9ca5fddc493d27bfd6a40df5ee7306d6aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Configuración manual de grupos de disponibilidad AlwaysOn en máquinas virtuales de Azure

Este tutorial muestra cómo crear un grupo de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines. El tutorial crea un grupo de disponibilidad con una réplica de base de datos en dos servidores SQL Server.

**Tiempo estimado**: una vez cumplidos los requisitos previos, este tutorial se tardará en completar unos 30 minutos.

El diagrama muestra lo que va a crear en el tutorial.

![Grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Requisitos previos

En el tutorial se da por supuesto que tiene conocimientos básicos de grupos de disponibilidad de SQL Server AlwaysOn. Para más información, consulte [Información general de los grupos de disponibilidad AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).

En la tabla siguiente se enumeran los requisitos previos que debe completar antes de iniciar este tutorial:

|  |Requisito |Descripción |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dos servidores SQL Server | - En un conjunto de disponibilidad de Azure <br/> - En un solo dominio <br/> - Con la característica Clústeres de conmutación por error instalada |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Recurso compartido de archivos para testigo de clúster |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Cuenta de servicio de SQL Server | Cuenta de dominio |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Cuenta de servicio Agente SQL Server | Cuenta de dominio |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Puertos de firewall abiertos | - SQL Server: **1433** para la instancia predeterminada <br/> - Punto de conexión de reflejo de la base de datos: **5022** o cualquier puerto disponible <br/> - Sondeo de Azure Load Balancer: **59999** o cualquier puerto disponible |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Agregar característica Clústeres de conmutación por error | Ambos servidores SQL Server necesitan esta característica |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Cuenta de dominio de la instalación | - Administrador local en cada servidor SQL Server <br/> - Miembro del rol fijo del servidor sysadmin de SQL Server para cada instancia de SQL Server  |


Antes de comenzar con este tutorial, debe completar los requisitos de [Finalización de requisitos previos para crear grupos de disponibilidad AlwaysOn en Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Si estos requisitos previos ya se han completado, puede ir a [Creación del clúster](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## Creación del clúster

Una vez completados los requisitos previos, el primer paso es crear un clúster de conmutación por error de Windows Server que incluya dos servidores SQL Server y un servidor testigo.  

1. Inicie RDP para el primer servidor SQL Server con una cuenta de dominio que sea administrador de los dos servidores SQL Server y el servidor testigo.

   >[!TIP]
   >Si ha seguido el [documento de requisitos previos](virtual-machines-windows-portal-sql-availability-group-prereq.md), ha creado una cuenta llamada **CORP\Install**. Utilice esta cuenta.

2. En el panel **Administrador del servidor**, seleccione **Herramientas** y después haga clic en **Administrador de clústeres de conmutación por error**.
3. En el panel izquierdo, haga clic con el botón derecho en **Administrador de clústeres de conmutación por error** y, a continuación, haga clic en **Crear clúster**.
   ![Crear clúster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. En el Asistente para crear clúster, cree un clúster de un solo nodo avanzando por las páginas con la configuración de la tabla siguiente:

   | Page | Configuración |
   | --- | --- |
   | Antes de empezar |Usar predeterminados |
   | Seleccionar servidores |Escriba el primer nombre de SQL Server en **Escriba un nombre de servidor** y haga clic en **Agregar**. |
   | Advertencia de validación |Seleccione **No. No necesito compatibilidad con Microsoft para este clúster y por tanto no deseo ejecutar las pruebas de validación. Al hacer clic en Siguiente, continuar la creación del clúster**. |
   | Punto de acceso para administrar el clúster |Escriba un nombre de clúster, por ejemplo **SQLAGCluster1** en **Nombre del clúster**.|
   | Confirmación |Use los valores predeterminados a menos que use Espacios de almacenamiento. Consulte la nota que sigue a esta tabla. |

### <a name="set-the-cluster-ip-address"></a>Configuración de la dirección IP del clúster

1. En **Administrador de clústeres de conmutación por error**, desplácese hacia abajo hasta **Recursos principales de clúster** y expanda los detalles del clúster. Debería de ver los recursos **Nombre** y **Dirección IP** en el estado **Con error**. El recurso de dirección IP no se puede poner en línea porque al clúster se le asigna la misma dirección IP que la de la propia máquina, por lo que es una dirección duplicada.

2. Haga clic con el botón secundario en el recurso **dirección IP** erróneo y después haga clic en **Propiedades**.

   ![Propiedades de clúster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Seleccione **Dirección IP estática** y especifique una dirección disponible en la subred en la que está SQL Server en el cuadro de texto Dirección. A continuación, haga clic en **Aceptar**.
4. En la sección **Recursos principales de clúster**, haga clic con el botón derecho en el nombre del clúster y haga clic en **Poner en línea**. Después espere hasta que ambos recursos estén en línea. Cuando el recurso de nombre de clúster está en línea, actualiza el servidor DC con una nueva cuenta de equipo de AD. Use esta cuenta de AD para ejecutar más tarde el servicio de clúster del grupo de disponibilidad.

### <a name="addNode"></a>Agregar el otro servidor SQL Server al clúster

Agregue el otro servidor SQL Server al clúster.

1. En el árbol del explorador, haga clic con el botón derecho en el clúster y haga clic en **Agregar nodo**.

    ![Agregar nodo al clúster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. En el **Asistente para agregar nodo**, haga clic en **Siguiente**. En la página **Seleccionar servidores**, agregue el segundo servidor de SQL Server. Escriba el nombre del servidor en **Escriba un nombre de servidor** y haga clic en **Agregar**. Cuando haya terminado, haga clic en **Siguiente**.

1. En la página **Advertencia de validación**, haga clic en **No** (en un escenario de producción debe realizar las pruebas de validación). A continuación, haga clic en **Siguiente**.

8. En la página **Confirmación**, si está usando espacios de almacenamiento, tiene que desactivar la casilla **Add all eligible storage to the cluster** (Agregar todo el almacenamiento apto al clúster).

   ![Agregar nodo de confirmación](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Si utiliza espacios de almacenamiento y no desactiva la casilla **Add all eligible storage to the cluster** (Agregar todo el almacenamiento apto al clúster), Windows separa los discos virtuales durante el proceso de agrupación en clústeres. Como resultado, no aparecen en el Administrador de discos ni el Explorador hasta que se quiten los espacios de almacenamiento del clúster y se vuelvan a asociar mediante PowerShell. Espacios de almacenamiento agrupa varios discos en grupos de almacenamiento. Para obtener más información, consulte el artículo sobre [espacios de almacenamiento](https://technet.microsoft.com/library/hh831739).

1. Haga clic en **Siguiente**.

1. Haga clic en **Finalizar**

   El Administrador de clústeres de conmutación por error indica que el clúster tiene un nuevo nodo y lo muestra en el contenedor **Nodos**.

10. Cierre sesión en la sesión de escritorio remoto.

### <a name="add-a-cluster-quorum-file-share"></a>Agregar un recurso compartido de cuórum de clúster

En este ejemplo, el clúster de Windows usa un recurso compartido de archivos para crear un cuórum de clúster. Este tutorial utiliza un cuórum de mayoría de recurso compartido de archivos y nodo. Para más información, consulte [Descripción de las configuraciones de cuórum en un clúster de conmutación por error](http://technet.microsoft.com/library/cc731739.aspx).

1. Conéctese al servidor miembro de testigo de recurso compartido de archivos con una sesión de Escritorio remoto.

1. En **Administrador del servidor**, haga clic en **Herramientas**. Abra **Administración de equipos**.

1. Haga clic en **Carpetas compartidas**.

1. Haga clic con el botón derecho en **Recursos compartidos** y, a continuación, haga clic en **Nuevo recurso compartido...**

   ![Nuevo recurso compartido](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Use **Asistente para crear una carpeta compartida** para crear un recurso compartido.

1. En **Ruta de acceso a la carpeta**, haga clic en **Examinar** y busque o cree una ruta de acceso para la carpeta compartida. Haga clic en **Siguiente**.

1. En **Nombre, descripción y configuración**, compruebe el nombre del recurso compartido y la ruta de acceso. Haga clic en **Siguiente**.

1. En **Permisos de la carpeta compartida**, establezca **Personalizar permisos**. Clic en **Personalizado...**

1. En **Personalizar permisos**, haga clic en **Agregar...**

1. Asegúrese de que la cuenta utilizada para crear el clúster tiene control total.

   ![Nuevo recurso compartido](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Haga clic en **Aceptar**.

1. En **Permisos de la carpeta compartida**, haga clic en **Finalizar**. Haga clic de nuevo en **Finalizar**.  

1. Cierre sesión en el servidor.

### <a name="configure-cluster-quorum"></a>Configuración del cuórum de clúster

A continuación, establezca el quórum de clúster.

1. Conéctese con el primer nodo del clúster mediante el Escritorio remoto.

1. En **Administrador de clústeres de conmutación por error**, haga clic con el botón derecho en el clúster, seleccione **Más acciones** y haga clic en **Configurar opciones de quórum de clúster...**

   ![Nuevo recurso compartido](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. En **Asistente para configurar quórum de clúster**, haga clic en **Siguiente**.

1. En **Seleccionar opción de configuración de quórum**, elija **Seleccionar el testigo de quórum** y haga clic en **Siguiente**.

1. En **Seleccionar el testigo de quórum**, haga clic en **Configurar un testigo de recurso compartido de archivos**.

   >[!TIP]
   >Windows Server 2016 admite un testigo en la nube. Si elige este tipo de testigo, no necesita ningún testigo de recurso compartido de archivos. Para más información, consulte [Implementación de un testigo en la nube para un clúster de conmutación por error](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Este tutorial usa un testigo de recurso compartido de archivos, que es compatible con los sistemas operativos anteriores.

1. En **Configurar un testigo de recurso compartido de archivos**, escriba la ruta de acceso para el recurso compartido que creó. Haga clic en **Siguiente**.

1. Compruebe la configuración en **Confirmación**. Haga clic en **Siguiente**.

1. Haga clic en **Finalizar**

Los recursos principales de clúster se configuran con un testigo de recurso compartido de archivos.

## <a name="enable-availability-groups"></a>Habilitación de grupos de disponibilidad

A continuación, habilite la característica **Grupos de disponibilidad AlwaysOn**. Siga estos pasos para ambos servidores SQL Server.

1. Desde la pantalla **Inicio**, abra el **Administrador de configuración de SQL Server**.
2. En el árbol del explorador, haga clic en **Servicios SQL Server**, luego haga clic con el botón derecho en el servicio **SQL Server (MSSQLSERVER)** y luego haga clic en **Propiedades**.
3. Haga clic en la pestaña **Alta disponibilidad de AlwaysOn** y seleccione **Habilitar los grupos de disponibilidad de AlwaysOn** del modo siguiente:

    ![Habilitación de grupos de disponibilidad AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Haga clic en **Apply**. Haga clic en **Aceptar** en el cuadro de diálogo emergente.

5. Reinicie el servicio de SQL Server.

Repita estos pasos en el otro servidor SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Creación de una base de datos en el primer servidor SQL Server

1. Inicie el archivo RDP para el primer servidor SQL Server con una cuenta de dominio que sea miembro del rol fijo del servidor sysadmin.
1. Abra SQL Server Management Studio y conéctese al primer servidor SQL Server.
7. En el **Explorador de objetos**, haga clic con el botón derecho en **Bases de datos** y luego en **Nueva base de datos**.
8. En **Nombre de base de datos**, escriba **MyDB1** y después haga clic en **Aceptar**.

### <a name="backupshare"></a> Creación de un recurso compartido de copia de seguridad

1. En el primer servidor SQL Server, en **Administrador del servidor**, haga clic en **Herramientas**. Abra **Administración de equipos**.

1. Haga clic en **Carpetas compartidas**.

1. Haga clic con el botón derecho en **Recursos compartidos** y, a continuación, haga clic en **Nuevo recurso compartido...**

   ![Nuevo recurso compartido](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Use **Asistente para crear una carpeta compartida** para crear un recurso compartido.

1. En **Ruta de acceso a la carpeta**, haga clic en **Examinar** y busque o cree una ruta de acceso para la carpeta compartida de copia de seguridad de base de datos. Haga clic en **Siguiente**.

1. En **Nombre, descripción y configuración**, compruebe el nombre del recurso compartido y la ruta de acceso. Haga clic en **Siguiente**.

1. En **Permisos de la carpeta compartida**, establezca **Personalizar permisos**. Clic en **Personalizado...**

1. En **Personalizar permisos**, haga clic en **Agregar...**

1. Asegúrese de que las cuentas de servicio de SQL Server y del Agente SQL Server para ambos servidores tienen control total.

   ![Nuevo recurso compartido](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Haga clic en **Aceptar**.

1. En **Permisos de la carpeta compartida**, haga clic en **Finalizar**. Haga clic de nuevo en **Finalizar**.  

### <a name="take-a-full-backup-of-the-database"></a>A continuación, haga una copia de seguridad completa de la base de datos.

Debe hacer una copia de seguridad de la nueva base de datos para inicializar la cadena de registros. Si no se realiza una copia de seguridad de la nueva base de datos, no puede incluirse en un grupo de disponibilidad.

1. En el **Explorador de objetos**, haga clic en la base de datos, seleccione **Tareas...** y haga clic en **Hacer copia de seguridad**.

1. Haga clic en **Aceptar** para realizar una copia de seguridad completa en la ubicación de copia de seguridad predeterminada.

## <a name="create-the-availability-group"></a>Creación del grupo de disponibilidad
Ahora ya puede configurar un grupo de disponibilidad siguiendo estos pasos:

* Crear una base de datos en el primer servidor SQL Server.
* Realizar una copia de seguridad completa y una copia de seguridad del registro de transacciones de la base de datos
* Restaurar las copias de seguridad completas y de registro en el segundo servidor SQL Server con la opción **NORECOVERY**.
* Crear el grupo de disponibilidad (**AG1**) con confirmación sincrónica, conmutación automática por error y réplicas secundarias legibles.

### <a name="create-the-availability-group"></a>Creación del grupo de disponibilidad:

1. En la sesión de Escritorio remoto en el primer servidor de SQL Server. En el **Explorador de objetos** de SSMS, haga clic con el botón derecho en **Alta disponibilidad de AlwaysOn** y en **Asistente para nuevo grupo de disponibilidad**.

    ![Iniciar el Asistente para nuevo grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. En la página **Introducción**, haga clic en **Siguiente**. En la página **Especificar nombre de grupo de disponibilidad**, escriba el nombre del grupo de disponibilidad, por ejemplo **AG1**, en **Nombre de grupo de disponibilidad**. Haga clic en **Siguiente**.

    ![Asistente para nuevo grupo de disponibilidad, especificar el nombre del grupo](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. En la página **Seleccionar bases de datos**, seleccione la base de datos y haga clic en **Siguiente**.

   >[!NOTE]
   >La base de datos cumple los requisitos previos para un grupo de disponibilidad porque ha realizado al menos una copia de seguridad completa en la réplica principal prevista.

   ![Asistente para nuevo grupo de disponibilidad, seleccionar bases de datos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. En la página **Especificar réplicas**, haga clic en **Agregar réplica**.

   ![Asistente para nuevo grupo de disponibilidad, especificar réplicas](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Se abre el cuadro de diálogo **Conectar con el servidor** . Escriba el nombre del segundo servidor en **Nombre de servidor**. Haga clic en **Conectar**.

   De nuevo en la página **Especificar réplicas**, ahora debería ver el segundo servidor en **Réplicas de disponibilidad**. Configure las réplicas tal como se muestra a continuación.

   ![Asistente para nuevo grupo de disponibilidad, especificar réplicas (completo)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Haga clic en **Puntos de conexión** para ver el punto de conexión de creación de reflejo de la base de datos para este grupo de disponibilidad. Utilice el mismo puerto que usó al configurar la [regla de firewall para los puntos de conexión de creación de reflejo de la base de datos](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Asistente para nuevo grupo de disponibilidad, seleccionar sincronización de datos iniciales](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. En la página **Seleccionar sincronización de datos iniciales**, seleccione **Completa** y especifique una ubicación de red compartida. Para la ubicación, utilice el [recurso compartido de copia de seguridad que creó](#backupshare). En el ejemplo, era **\\\\\<Primer servidor SQL Server\>\Backup\**. Haga clic en **Siguiente**.

   >[!NOTE]
   >La sincronización completa realiza una copia de seguridad completa de la base de datos en la primera instancia de SQL Server y la restaura en la segunda instancia. Para bases de datos grandes, no se recomienda la sincronización completa porque puede llevar mucho tiempo. Puede reducir este tiempo realizando manualmente una copia de seguridad de la base de datos y restaurándola con `NO RECOVERY`. Si ya se ha restaurado la base de datos con `NO RECOVERY` en el segundo servidor SQL Server antes de configurar el grupo de disponibilidad, elija **Solo unirse**. Si desea realizar la copia de seguridad después de configurar el grupo de disponibilidad, elija **Omitir la sincronización de datos iniciales**.

    ![Asistente para nuevo grupo de disponibilidad, seleccionar sincronización de datos iniciales](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. En la página **Validación**, haga clic en **Siguiente**. Esta página debe tener un aspecto similar a la siguiente imagen:

    ![Asistente para nuevo grupo de disponibilidad, validación](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Hay una advertencia para la configuración del agente de escucha porque no ha configurado un agente de escucha de grupo de disponibilidad. Puede omitir esta advertencia porque en Azure Virtual Machines el agente de escucha se crea después de crear el equilibrador de carga de Azure.

10. En la página **Resumen**, haga clic en **Finalizar** y espere hasta que el asistente configure el nuevo grupo de disponibilidad. En la página **Progreso**, puede hacer clic en **Más detalles** para ver el progreso detallado. Una vez que el asistente haya finalizado, inspeccione la página **Resultados** para comprobar que se ha creado correctamente el grupo de disponibilidad.

     ![Asistente para nuevo grupo de disponibilidad, resultados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Haga clic en **Cerrar** para salir del asistente.

### <a name="check-the-availability-group"></a>Comprobación de grupo de disponibilidad

1. En el **Explorador de objetos**, expanda **Alta disponibilidad de AlwaysOn** y después expanda **Grupos de disponibilidad**. Ahora debería de ver el nuevo grupo de disponibilidad en este contenedor. Haga clic con el botón derecho en el grupo de disponibilidad y luego haga clic en **Show Dashboard** (Mostrar panel).

   ![Mostrar panel de grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   El **panel AlwaysOn** debería ser similar a este.

   ![Panel de grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Puede ver las réplicas, el modo de conmutación por error de cada réplica y el estado de sincronización.

2. En **Administrador de clústeres de conmutación por error**, haga clic en el clúster. Seleccione **Roles**. El nombre del grupo de disponibilidad que usó es un rol en el clúster. Ese grupo de disponibilidad no tiene una dirección IP para las conexiones de cliente porque no ha configurado un agente de escucha. Va a configurar el agente de escucha después de crear un equilibrador de carga de Azure.

   ![Grupo de disponibilidad en el administrador de clústeres de conmutación por error.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > No trate de realizar una conmutación por error del grupo de disponibilidad desde el Administrador de clústeres de conmutación por error. Todas las operaciones de conmutación por error deben realizarse desde el **Panel AlwaysOn** de SSMS. Para obtener más información, consulte el artículo de [restricciones en el uso del Administrador de clústeres de conmutación por error con grupos de disponibilidad](https://msdn.microsoft.com/library/ff929171.aspx).
    >

En este punto, tiene un grupo de disponibilidad con réplicas en dos instancias de SQL Server. Puede mover el grupo de disponibilidad entre instancias. No se puede conectar al grupo de disponibilidad aún porque no tiene un agente de escucha. En Azure Virtual Machines, el agente de escucha requiere un equilibrador de carga. El siguiente paso consiste en crear el equilibrador de carga en Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Creación de un equilibrador de carga de Azure

En Azure Virtual Machines, un grupo de disponibilidad de SQL Server necesita un equilibrador de carga. El equilibrador de carga almacena la dirección IP del agente de escucha del grupo de disponibilidad. En esta sección se resume cómo crear el equilibrador de carga en Azure Portal.

1. En Azure Portal, vaya al grupo de recursos donde están los servidores SQL Server y haga clic en **+Agregar**.
2. Busque **Equilibrador de carga**. Elija el equilibrador de carga publicado por Microsoft.

   ![Grupo de disponibilidad en el administrador de clústeres de conmutación por error.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1.  Haga clic en **Crear**.
3. Configure los parámetros siguientes para el equilibrador de carga.

   | Configuración | Campo |
   | --- | --- |
   | **Name** |Use un nombre de texto para el equilibrador de carga, por ejemplo **sqlLB**. |
   | **Tipo** |Interno |
   | **Red virtual** |Use el nombre de la red virtual de Azure. |
   | **Subred** |Utilice el nombre de la subred en la que se encuentra la máquina virtual.  |
   | **Asignación de dirección IP** |Estática |
   | **Dirección IP** |Use una dirección disponible en la subred. |
   | **Suscripción** |Utilice la misma suscripción que la de la máquina virtual. |
   | **Ubicación** |Use la misma ubicación que la de la máquina virtual. |

   La hoja de Azure Portal debe tener el siguiente aspecto:

   ![Cree un equilibrador de carga](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Haga clic en **Crear** para generar el equilibrador de carga.

Para configurar el equilibrador de carga, debe crear un grupo de back-end, un sondeo y establecer la reglas de equilibrio de carga. Esto se hace en Azure Portal.

### <a name="add-backend-pool"></a>Agregar grupo de back-end

1. En Azure Portal, vaya a su grupo de disponibilidad. Es posible que deba actualizar la vista para ver el equilibrador de carga recién creado.

   ![Buscar equilibrador de carga en el grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Haga clic en el equilibrador de carga, en **Grupos de back-end** y en **+Agregar**. Configure el grupo de back-end del modo siguiente:

   | Configuración | Descripción | Ejemplo
   | --- | --- |---
   | **Name** | Escribir un nombre de texto | SQLLBBE
   | **Asociado a** | Elegir de la lista | Conjunto de disponibilidad
   | **El conjunto de disponibilidad** | Usar un nombre del conjunto de disponibilidad en el que se encuentren las VM con SQL Server | sqlAvailabilitySet |
   | **Máquinas virtuales** |Los dos nombres de VM con SQL Server de Azure | sqlserver-0, sqlserver-1

1. Escriba el nombre para el grupo de back-end.

1. Haga clic en **+ Agregar máquina virtual**.

1. Para el conjunto de disponibilidad, elija el conjunto de disponibilidad en el que están los servidores SQL Server.

1. Para máquinas virtuales, incluya ambos servidores SQL Server. No incluya el servidor testigo del recurso compartido de archivos.

1. Haga clic en **Aceptar** para crear el grupo de back-end.

### <a name="set-the-probe"></a>Configuración del sondeo

1. Haga clic en el equilibrador de carga, en **Health probes** (Sondeos de estado) y en **+Agregar**.

1. Configure el sondeo de estado del modo siguiente:

   | Configuración | Descripción | Ejemplo
   | --- | --- |---
   | **Name** | Texto | SQLAlwaysOnEndPointProbe |
   | **Protocolo** | Elija TCP | TCP |
   | **Puerto** | Cualquier puerto no utilizado | 59999 |
   | **Intervalo**  | Cantidad de tiempo entre los intentos de sondeo, en segundos |5 |
   | **Umbral incorrecto** | Número de errores de sondeo consecutivos que deben producirse para que una máquina virtual se considere en mal estado  | 2 |

1. Haga clic en **Aceptar** para configurar el sondeo de estado.

### <a name="set-the-load-balancing-rules"></a>Configuración de las reglas de equilibrio de carga

1. Haga clic en el equilibrador de carga, en **Reglas de equilibrio de carga** y en **+Agregar**.

1. Configure las reglas de equilibrio de carga del modo siguiente.
   | Configuración | Descripción | Ejemplo
   | --- | --- |---
   | **Name** | Texto | SQLAlwaysOnEndPointListener |
   | **Frontend IP address** (Dirección IP de front-end) | Elija una dirección |Use la dirección que creó al crear el equilibrador de carga. |
   | **Protocolo** | Elija TCP |TCP |
   | **Puerto** | Utilice el puerto para la instancia de SQL Server | 1433 |
   | **Puerto back-end** | Este campo no se utiliza cuando la IP flotante está establecida para Direct Server Return | 1433 |
   | **Sondeo** |Nombre especificado para el sondeo | SQLAlwaysOnEndPointProbe |
   | **Persistencia de la sesión** | Lista desplegable | **None** |
   | **Tiempo de espera de inactividad** | Minutos para mantener abierta una conexión TCP | 4 |
   | **IP flotante (Direct Server Return)** | |Enabled |

   > [!WARNING]
   > Direct Server Return se establece durante la creación. No se puede modificar.

1. Haga clic en **Aceptar** para configurar las reglas de equilibrio de carga.

## <a name="configure-listener"></a> Configuración del agente de escucha

El siguiente paso es configurar un agente de escucha del grupo de disponibilidad en el clúster de conmutación por error.

> [!NOTE]
> En este tutorial se muestra cómo crear un único agente de escucha (con una dirección IP de ILB). Para crear uno o varios agentes de escucha con una o varias direcciones IP, consulte [Creación de un agente de escucha del grupo de disponibilidad y un equilibrador de carga | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Configuración del puerto del agente de escucha

En SQL Server Management Studio, establezca el puerto del agente de escucha.

1. Abra SQL Server Management Studio y conéctese a la réplica principal.

1. Vaya a **Alta disponibilidad de AlwaysOn** | **Grupos de disponibilidad** | **Agentes de escucha del grupo de disponibilidad**.

1. Ahora tienes que ver el nombre del agente de escucha que creaste en el Administrador de clústeres de conmutación por error. Haga clic con el botón derecho en el nombre del agente de escucha y luego en **Propiedades**.

1. En el cuadro **Puerto**, especifique el número de puerto del agente de escucha del grupo de disponibilidad mediante el valor $EndpointPort que ha utilizado antes (1433 era el valor predeterminado) y haga clic en **Aceptar**.

Ahora tiene un grupo de disponibilidad de SQL Server en Azure Virtual Machines que se ejecuta en el modo de Resource Manager.

## <a name="test-connection-to-listener"></a>Prueba de la conexión con el agente de escucha

Para probar la conexión:

1. Conéctese con RDP a un servidor SQL Server que esté en la misma red virtual, pero que no sea el propietario de la réplica; Puede utilizar el otro servidor SQL Server del clúster.

1. Use la utilidad **sqlcmd** para probar la conexión. Por ejemplo, el siguiente script establece una conexión **sqlcmd** con la réplica principal por medio del agente de escucha con autenticación de Windows:

    ```
    sqlcmd -S <listenerName> -E
    ```

    Si el agente de escucha usa un puerto distinto del predeterminado (1433), especifíquelo en la cadena de conexión. Por ejemplo, el siguiente comando sqlcmd se conecta a un agente de escucha en el puerto 1435:

    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

La conexión SQLCMD se establece automáticamente con la instancia de SQL Server en la que se hospede la réplica principal.

> [!TIP]
> Asegúrese de que el puerto especificado esté abierto en el firewall de los dos servidores SQL Server. En estos dos servidores, es necesario definir una regla de entrada para el puerto TCP. Consulte [Agregar o editar regla de firewall](http://technet.microsoft.com/library/cc753558.aspx) para más información.
>
>



<!--**Notes**: *Notes provide just-in-time info: A Note is “by the way” info, an Important is info users need to complete a task, Tip is for shortcuts. Don’t overdo*.-->


<!--**Procedures**: *This is the second “step." They often include substeps. Again, use a short title that tells users what they’ll do*. *("Configure a new web project.")*-->

<!--**UI**: *Note the format for documenting the UI: bold for UI elements and arrow keys for sequence. (Ex. Click **File > New > Project**.)*-->

<!--**Screenshot**: *Screenshots really help users. But don’t include too many since they’re difficult to maintain. Highlight areas you are referring to in red.*-->

<!--**No. of steps**: *Make sure the number of steps within a procedure is 10 or fewer. Seven steps is ideal. Break up long procedure logically.*-->


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Pasos siguientes

- [Agregar una dirección IP a un equilibrador de carga para un segundo grupo de disponibilidad](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).

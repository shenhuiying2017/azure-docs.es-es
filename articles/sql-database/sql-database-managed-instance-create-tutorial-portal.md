---
title: 'Azure Portal: Creación de una Instancia administrada de SQL Database | Microsoft Docs'
description: Cree una Instancia administrada de Azure SQL Database en una red virtual.
keywords: tutorial de sql database, creación de una instancia administrada de sql database
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 1e04b12241121d3894e2d71df423bae4f000a60d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Creación de una Instancia administrada de Azure SQL Database en Azure Portal

En este tutorial se muestra cómo crear una Instancia administrada de Azure SQL Database mediante Azure Portal en una subred dedicada de una red virtual y luego conectarse a la Instancia administrada mediante SQL Server Management Studio en una máquina virtual en la misma red virtual.

> [!div class="checklist"]
> * Agregar la suscripción a la lista de permitidos
> * Configuración de una red virtual
> * Creación de nueva tabla de rutas y de una ruta
> * Aplicación de la tabla de rutas a la subred de Instancia administrada
> * Creación de una instancia administrada
> * Creación de una nueva subred en la red virtual para una máquina virtual
> * Creación de una máquina virtual en la nueva subred de la red virtual
> * Conexión a la máquina virtual
> * Instalación de SSMS y conexión a la instancia administrada


Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

> [!IMPORTANT]
> Para una lista de las regiones en las que está actualmente disponible Instancia administrada, consulte [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/) (Migración de las bases de datos a un servicio completamente administrado con Instancia administrada de Azure SQL Database).
 
## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="whitelist-your-subscription"></a>Agregar la suscripción a la lista de permitidos

La instancia administrada se publica inicialmente como una versión preliminar pública validada que requiere que su suscripción aparezca en la lista de permitidos. Si la suscripción aún no está en la lista de permitidos, siga estos pasos para que se le ofrezcan los términos de la versión preliminar, los acepte y envíe una solicitud para su incorporación a la lista de permitidos.

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Busque **Instancia administrada** y seleccione **Instancia administrada Azure SQL Database (versión preliminar)**.
3. Haga clic en **Create**(Crear).

   ![creación de instancia administrada](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Seleccione su suscripción, haga clic en **Términos de la versión preliminar** y, a continuación, proporcione la información solicitada.

   ![términos de la versión preliminar de la instancia administrada](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. Cuando haya terminado, haga clic en **Aceptar**.

   ![términos de la versión preliminar de la instancia administrada](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > Mientras espera la aprobación de la versión preliminar, puede continuar y completar las siguientes secciones de este tutorial.

## <a name="configure-a-virtual-network-vnet"></a>Configuración de una red virtual

En los pasos siguientes se muestra cómo crear una nueva red virtual de [Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) para su uso por la instancia administrada. Para más información acerca de la configuración de redes virtuales, consulte [Configuración de una red virtual de instancia administrada](sql-database-managed-instance-vnet-configuration.md).

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Busque y haga clic en **Red virtual**, compruebe que **Resource Manager** está seleccionado como modo de implementación y, finalmente, haga clic en **Crear**.

   ![creación de una red virtual](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Rellene el formulario de la red virtual con la información solicitada, utilizando los datos de la siguiente tabla:

   | Configuración| Valor sugerido | Descripción |
   | ------ | --------------- | ----------- |
   |**Name**|Cualquier nombre válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Espacio de direcciones**|Cualquier intervalo de direcciones válido, como 10.14.0.0/24|El nombre de dirección de la red virtual en notación CIDR.|
   |**Suscripción**|Su suscripción|Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions).|
   |**Grupo de recursos**|Cualquier grupo de recursos válido (nuevo o existente)|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Ubicación**|Cualquier ubicación válida| Para obtener información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).|
   |**Nombre de subred**|Cualquier nombre de subred válido, como mi_subnet|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Intervalo de direcciones de subred**|Cualquier dirección de subred válida, como 10.14.0.0/28. Utilice un espacio de direcciones de subred más pequeño que el mismo espacio de direcciones para crear otras subredes en la misma red virtual, como una subred para hospedar aplicaciones de prueba/cliente o subredes de puerta de enlace para conectarse desde una red local u otras redes virtuales.|El intervalo de direcciones de la subred en notación CIDR. Debe incluirse en el espacio de direcciones de la red virtual.|
   |**Puntos de conexión de servicio**|Disabled|Permite habilitar uno o varios puntos de conexión de servicio para esta subred.|
   ||||

   ![formulario de creación de una red virtual](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Haga clic en **Create**(Crear).

## <a name="create-new-route-table-and-a-route"></a>Creación de nueva tabla de rutas y de una ruta

Los pasos siguientes muestran cómo crear una ruta de Internet de próximo salto 0.0.0.0/0.

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Busque una **tabla de rutas** y haga clic en ella y, después, haga clic en **Crear** en la página Tabla de rutas. 

   ![creación de la tabla de rutas](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. Rellene el formulario de la tabla de rutas con la información solicitada, utilizando los datos de la siguiente tabla:

   | Configuración| Valor sugerido | Descripción |
   | ------ | --------------- | ----------- |
   |**Name**|Cualquier nombre válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Suscripción**|Su suscripción|Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions).|
   |**Grupo de recursos**|Seleccione el grupo de recursos que ha creado en el procedimiento anterior.|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Ubicación**|Seleccione la ubicación que ha especificado en el procedimiento anterior.| Para obtener información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).|
   |**Deshabilitar la propagación de rutas BGP**|Disabled||
   ||||

   ![formulario de creación de la tabla de rutas](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. Haga clic en **Create**(Crear).
5. Una vez creada la tabla de rutas, ábrala.

   ![tabla de rutas](./media/sql-database-managed-instance-tutorial/route-table.png)

6. Haga clic en **Rutas** y, después, en **Agregar**.

   ![adición de tabla de rutas](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  Agregue la **ruta de Internet de próximo salto 0.0.0.0/0** como la **única** ruta, mediante la información de la tabla siguiente:

    | Configuración| Valor sugerido | DESCRIPCIÓN |
    | ------ | --------------- | ----------- |
    |**Nombre de ruta**|Cualquier nombre válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
    |**Prefijo de dirección**|0.0.0.0/0|La dirección IP de destino en notación CIDR a la que se aplica esta ruta.|
    |**Tipo del próximo salto**|Internet|El próximo salto controla los paquetes que coinciden con esta ruta.|
    |||

    ![ruta](./media/sql-database-managed-instance-tutorial/route.png)

8. Haga clic en **OK**.

## <a name="apply-the-route-table-to-the-managed-instance-subnet"></a>Aplicación de la tabla de rutas a la subred de Instancia administrada

En los siguientes pasos se muestra cómo configurar la nueva tabla de rutas en la subred de Instancia administrada.

1. Para establecer la tabla de rutas en la subred de Instancia administrada, abra la red virtual que ha creado anteriormente.
2. Haga clic en **Subredes** y, después, haga clic en la subred de Instancia administrada (**mi_subnet** en la siguiente captura de pantalla).

    ![subred](./media/sql-database-managed-instance-tutorial/subnet.png)

11. Haga clic en **Tabla de rutas** y seleccione **myMI_route_table**.

    ![establecimiento de la tabla de rutas](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. Haga clic en **Guardar**

    ![establecimiento de la tabla de rutas: guardar](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>Creación de una instancia administrada

Los pasos siguientes muestran cómo crear la instancia administrada cuando se haya aprobado la versión preliminar.

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Busque **Instancia administrada** y seleccione **Instancia administrada Azure SQL Database (versión preliminar)**.
3. Haga clic en **Create**(Crear).

   ![creación de instancia administrada](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Seleccione la suscripción y compruebe que los términos de la versión preliminar muestran **Aceptado**.

   ![versión preliminar de la instancia administrada aceptada](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. Rellene el formulario de la instancia administrada con la información solicitada, utilizando los datos de la siguiente tabla:

   | Configuración| Valor sugerido | DESCRIPCIÓN |
   | ------ | --------------- | ----------- |
   |**Nombre de la instancia administrada**|Cualquier nombre válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Inicio de sesión de administrador de la instancia administrada**|Cualquier nombre de usuario válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Password**|Cualquier contraseña válida|La contraseña debe tener al menos 16 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Grupo de recursos**|El grupo de recursos que ha creado anteriormente||
   |**Ubicación**|La ubicación que ha seleccionado anteriormente|Para obtener información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).|
   |**Red virtual**|La red virtual que ha creado anteriormente|

   ![formulario de creación de instancia administrada](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. Haga clic en **Plan de tarifa** para cambiar el tamaño de los recursos de almacenamiento y de proceso, así como para revisar las opciones del plan de tarifa. De forma predeterminada, la instancia obtiene 32 GB de espacio de almacenamiento de forma gratuita, lo que puede no ser suficiente para sus aplicaciones.
6. Utilice los controles deslizantes o cuadros de texto para especificar la cantidad de almacenamiento y el número de núcleos virtuales. 
   ![formulario de creación de instancia administrada](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. Cuando haya terminado, haga clic en **Aplicar** para guardar la selección.  
8. Haga clic en **Crear** para implementar la instancia administrada.
9. Haga clic en el icono de **notificaciones** para ver el estado de la implementación.
 
   ![progreso de la implementación](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. Haga clic en **Deployment in progress** (Implementación en curso) para abrir la ventana de la instancia administrada y seguir la supervisión del progreso de la implementación.
 
   ![progreso de la implementación 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Mientras se realiza la implementación, continúe con el siguiente procedimiento.

> [!IMPORTANT]
> Para la primera instancia de una subred, el tiempo de implementación es típicamente mucho mayor que en las instancias siguientes, a veces más de 24 horas en completarse. No cancele la operación de implementación porque dure más de lo previsto. La duración de este período para implementar su primera instancia es una situación temporal. Espere una reducción significativa del tiempo de implementación poco después del comienzo de la versión preliminar pública.

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>Creación de una nueva subred en la red virtual para una máquina virtual

En los pasos siguientes se muestra cómo crear una segunda subred en la red virtual para una máquina virtual en la que instala SQL Server Management Studio y se conecta a la instancia administrada.

1. Abra el recurso de red virtual.
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. Haga clic en **Subredes** y en **+Subred**.
 
   ![adición de subred](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. Rellene el formulario de la subred con la información solicitada, utilizando los datos de la siguiente tabla:

   | Configuración| Valor sugerido | Descripción |
   | ------ | --------------- | ----------- |
   |**Name**|Cualquier nombre válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Intervalo de direcciones (bloque CIDR)**|Cualquier intervalo de direcciones válido dentro de la red virtual (use el valor predeterminado)||
   |**Grupo de seguridad de red**|None||
   |**Tabla de rutas**|None||
   |**Puntos de conexión de servicio**|None||

   ![detalles de la subred de máquina virtual](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Haga clic en **OK**.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Creación de una máquina virtual en la nueva subred de la red virtual

En los pasos siguientes se muestra cómo crear una máquina virtual en la misma red virtual en la que se crea la instancia administrada. 

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Proceso** y, después, seleccione **Windows Server 2016 Datacenter** o **Windows 10**. En esta sección del tutorial se utiliza Windows Server. La configuración de Windows 10 es esencialmente similar. 

   ![proceso](./media/sql-database-managed-instance-tutorial/compute.png)

3. Rellene el formulario de la máquina virtual con la información solicitada, utilizando los datos de la siguiente tabla:

   | Configuración| Valor sugerido | Descripción |
   | ------ | --------------- | ----------- |
   |**Name**|Cualquier nombre válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Tipo de disco de máquina virtual**|SSD|Las SSD ofrecen el mejor equilibrio entre precio y rendimiento.|   
   |**Nombre de usuario**|Cualquier nombre de usuario válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Password**|Cualquier contraseña válida|La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).| 
   |**Suscripción**|Su suscripción|Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions).|
   |**Grupo de recursos**|El grupo de recursos que ha creado anteriormente||
   |**Ubicación**|La ubicación que ha seleccionado anteriormente||
   |**¿Ya tiene una licencia de Windows Server?**|Sin |Si dispone de licencias activas de Windows con Software Assurance (SA), utilice la Ventaja híbrida de Azure para ahorrar costos de proceso.|
   ||||

   ![formulario de creación de máquina virtual](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. Haga clic en **OK**.
4. Seleccione un tamaño para la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). Para este tutorial, solo necesita una máquina virtual pequeña.

    ![Tamaños de VM](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. Haga clic en **Seleccionar**.
6. En el formulario **Configuración**, haga clic en **Subred** y, después, seleccione **vm_subnet**. No elija la subred en la que se aprovisiona la instancia administrada, sino otra subred de la misma red virtual.

    ![Configuración de máquina virtual](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. Haga clic en **OK**.
8. En la página de resumen, revise los detalles de la oferta y haga clic en **Crear** para iniciar la implementación de la máquina virtual.
 
## <a name="connect-to-virtual-machine"></a>Conexión a la máquina virtual

En los pasos siguientes se muestra cómo conectarse a la máquina virtual recién creada mediante una conexión de escritorio remoto.

1. Una vez finalizada la implementación, vaya al recurso de máquina virtual.

    ![máquina virtual](./media/sql-database-managed-instance-tutorial/vm.png)  

2. Haga clic en el botón **Conectar** en las propiedades de la máquina virtual. Se crea y se descarga un archivo de Protocolo de Escritorio remoto (archivo .rdp).
3. Para conectarse a la máquina virtual, abra el archivo RDP descargado. 
4. Cuando se le solicite, haga clic en **Conectar**. En un equipo Mac, necesita un cliente RDP como este [Cliente de Escritorio remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) de Mac App Store.

5. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y, a continuación, haga clic en **Aceptar**.

6. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en **Sí** o **Conectar** para continuar con la conexión.

Está conectado a la máquina virtual en el panel de Administrador del servidor.

> [!IMPORTANT]
> No continúe hasta que la instancia administrada esté correctamente aprovisionada. Una vez aprovisionada, recupere el nombre de host de la instancia en el campo **Instancia administrada** de la pestaña **Información general** de la instancia administrada. El nombre es similar al siguiente: **drfadfadsfd.tr23.westus1-a.worker.database.windows.net**.

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>Instalación de SSMS y conexión a la instancia administrada

En los pasos siguientes se muestra cómo descargar e instalar SSMS y, después, conectarse a la instancia administrada.

1. En Administrador del servidor, haga clic en **Servidor local** en el panel izquierdo.

    ![propiedades del administrador del servidor](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. En el panel **Propiedades**, haga clic en **On** (Activar) para modificar la configuración de seguridad mejorada de Internet Explorer.
3. En el cuadro de diálogo **Configuración de seguridad mejorada de Internet Explorer**, haga clic en **Off** (Desactivar) en la sección Administradores del cuadro de diálogo y, finalmente, haga clic en **Aceptar**.

    ![configuración de seguridad mejorada de internet explorer](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. Abra **Internet Explorer** desde la barra de tareas.
5. Seleccione **Usar la configuración de compatibilidad y seguridad recomendada** y haga clic en **Aceptar** para completar la configuración de Internet Explorer 11.
6. Escriba https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms en el cuadro de direcciones URL y haga clic en **Entrar**. 
7. Descargue la versión más reciente de SQL Server Management Studio y haga clic en **Ejecutar** cuando se le solicite.
8. Cuando se le pida, haga clic en **Instalar** para comenzar.
9. Una vez completada la instalación, haga clic en **Cerrar**.
10. Abra SSMS.
11. En el cuadro de diálogo **Conectar al servidor**, especifique el **nombre de host* de la instancia administrada en el cuadro **Nombre del servidor**, seleccione **Autenticación de SQL Server**, proporcione el nombre de usuario y contraseña y haga clic en **Conectar**.

    ![conexión ssms](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Después de conectarse, puede ver las bases de datos del sistema y de los usuarios en el nodo Bases de datos, así como varios objetos en los nodos Seguridad, Objetos de servidor, Replicación, Administración, Agente SQL Server y XEvent Profiler.

> [!NOTE]
> Para restaurar una base de datos SQL existente en una Instancia administrada, puede utilizar [Azure Database Migration Service (DMS) para la migración ](../dms/tutorial-sql-server-to-managed-instance.md) para restaurar desde un archivo de copia de seguridad de base de datos, el [comando T-SQL RESTORE](sql-database-managed-instance-restore-from-backup-tutorial.md) para restaurar desde un archivo de copia de seguridad de base de datos, o [importar desde un archivo BACPAC](sql-database-import.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a crear una Instancia administrada de Azure SQL Database mediante Azure Portal en una subred dedicada de una red virtual y luego conectarse a la Instancia administrada mediante SQL Server Management Studio en una máquina virtual en la misma red virtual.  Ha aprendido a: 

> [!div class="checklist"]
> * Agregar la suscripción a la lista de permitidos
> * Configuración de una red virtual
> * Creación de nueva tabla de rutas y de una ruta
> * Aplicación de la tabla de rutas a la subred de Instancia administrada
> * Creación de una instancia administrada
> * Creación de una nueva subred en la red virtual para una máquina virtual
> * Creación de una máquina virtual en la nueva subred de la red virtual
> * Conexión a la máquina virtual
> * Instalación de SSMS y conexión a la instancia administrada

Avance al siguiente tutorial para aprender a restaurar una copia de seguridad de datos en una Instancia administrada de Azure SQL Database.

> [!div class="nextstepaction"]
>[Restauración de una copia de seguridad de datos a una Instancia administrada de Azure SQL Database](sql-database-managed-instance-restore-from-backup-tutorial.md)

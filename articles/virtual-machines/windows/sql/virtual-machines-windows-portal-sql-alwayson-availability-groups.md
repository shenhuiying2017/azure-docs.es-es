---
title: "Configuración de alta disponibilidad para máquinas virtuales de Azure Resource Manager | Microsoft Docs"
description: "En este tutorial se muestra cómo crear un grupo de disponibilidad AlwaysOn con Azure Virtual Machines en el modo de Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d430febee23081b26eee0a68d4beb43228549f52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configuración automática de grupos de disponibilidad AlwaysOn en Azure Virtual Machines: Resource Manager

Este tutorial muestra cómo crear un grupo de disponibilidad de SQL Server que usa máquinas virtuales de Azure Resource Manager. El tutorial utiliza hojas de Azure para configurar una plantilla. Podrá revisar la configuración predeterminada, escribir la configuración requerida y actualizar las hojas en el portal a medida que recorra este tutorial.

El tutorial completo crea un grupo de disponibilidad de SQL Server en Azure Virtual Machines que incluye los siguientes elementos:

* Una red virtual que tiene varias subredes, incluida una subred front-end y back-end
* Dos controladores de dominio que tienen un dominio de Active Directory
* Dos máquinas virtuales que ejecutan SQL Server y están implementadas en la subred de back-end y unidas al dominio de Active Directory
* Un clúster de conmutación por error de tres nodos con el modelo de cuórum Mayoría de nodo
* Un grupo de disponibilidad que tiene dos réplicas de confirmación sincrónica de una base de datos de disponibilidad

En la ilustración siguiente se representa la solución completa.

![Arquitectura de pruebas para grupos de disponibilidad en Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos los recursos de esta solución pertenecen a un único grupo de recursos.

Antes de comenzar este tutorial, confirme lo siguiente:

* Ya tiene una cuenta de Azure. Si no tiene ninguna, [suscríbase para obtener una cuenta de prueba](http://azure.microsoft.com/pricing/free-trial/).
* Ya sabe cómo usar la GUI para aprovisionar una máquina virtual de SQL Server desde la galería de máquinas virtuales. Para más información, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Ya tiene un conocimiento sólido de los grupos de disponibilidad. Para obtener más información, consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Si le interesa utilizar los grupos de disponibilidad con SharePoint, consulte también [Configuración de grupos de disponibilidad AlwaysOn de SQL Server 2012 para SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).
>
>

En este tutorial, podrá usar Azure Portal para:

* Seleccionar la plantilla AlwaysOn en el portal.
* Revisar la configuración de la plantilla y actualizar algunas configuraciones para el entorno.
* Supervisar Azure mientras crea todo el entorno.
* Conectarse a un controlador de dominio y luego a un servidor que ejecuta SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Aprovisionar el clúster desde la Galería
Azure ofrece una galería de imágenes para toda la solución. Para ubicar la plantilla:

1. Inicie sesión en Azure Portal con su cuenta.
2. En Azure Portal, haga clic en **+Nuevo** para abrir la hoja **Nuevo**.
3. En la hoja **Nuevo**, busque **AlwaysOn**.
   ![Buscar la plantilla de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. En los resultados de la búsqueda, busque **Clúster AlwaysOn de SQL Server**.
   ![Plantilla de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. En **Seleccionar un modelo de implementación,** elija **Resource Manager**.

### <a name="basics"></a>Aspectos básicos
Haga clic en **Basics** (Aspectos básicos) y configure las opciones siguientes:

* **Nombre de usuario del administrador** es una cuenta de usuario que tiene permisos de administrador de dominio y un miembro del rol de servidor fijo sysadmin de SQL Server en ambas instancias de SQL Server. Use **DomainAdmin**en este tutorial.
* **Contraseña** es la contraseña de la cuenta de administrador del dominio. Utilice una contraseña compleja. Confirme la contraseña.
* **Suscripción** es la suscripción que Azure facturará por ejecutar todos los recursos implementados para el grupo de disponibilidad. Si su cuenta tiene varias suscripciones, puede especificar una suscripción distinta.
* **Grupo de recursos** es el nombre del grupo al que pertenecen todos los recursos de Azure creados mediante esta plantilla. Use **SQL-HA-RG**en este tutorial. Para más información, consulte [Información general de Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Ubicación** es la región de Azure en la que el tutorial crea los recursos. Elija una región de Azure.

En la captura de pantalla siguiente hay una hoja **Aspectos básicos** completada:

![Aspectos básicos](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Haga clic en **Aceptar**.

### <a name="domain-and-network-settings"></a>Configuración de red y dominio
Esta plantilla de la galería de Azure crea un dominio y controladores de dominio. También crea una red y dos subredes. La plantilla no puede crear servidores en un dominio o una red virtual existente. En el siguiente paso se configura el dominio y la red.

En la hoja **Configuración de dominio y red**, revise los valores preestablecidos para la configuración del dominio y de la red:

* **Nombre de dominio raíz del bosque** es el nombre de dominio para el dominio de Active Directory que hospeda al clúster. Use **contoso.com** en el tutorial.
* **Nombre de red virtual** es el nombre de la red virtual de Azure. Use **autohaVNET** en el tutorial.
* **Domain Controller subnet name** (Nombre de subred del controlador de dominio) es el nombre de la parte de la red virtual que hospeda el controlador de dominio. Utilice **subnet-1**. Esta subred usa el prefijo de dirección **10.0.0.0/24**.
* **Nombre de subred de SQL Server** es el nombre de la parte de la red virtual que hospeda los servidores que ejecutan SQL Server y el testigo del recurso compartido de archivos. Utilice **subnet-2**. Esta subred usa el prefijo de dirección **10.0.1.0/26**.

Para más información sobre las redes virtuales en Azure, consulte [Información general sobre redes virtuales](../../../virtual-network/virtual-networks-overview.md).  

**Configuración de dominio y red** debería ser similar a esta imagen:

![Configuración de red y dominio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Si es necesario, puede cambiar estos valores. En este tutorial use los valores preestablecidos.

Revise la configuración y, luego, haga clic en **Aceptar**.

### <a name="availability-group-settings"></a>Configuración de grupos de disponibilidad
En **Availability group settings** (Configuración de grupo de disponibilidad), revise los valores preestablecidos tanto del grupo de disponibilidad como del agente de escucha.

* **Availablity group name** (Nombre del grupo de disponibilidad) es el nombre del recurso en clúster para el grupo de disponibilidad. Use **Contoso-ag**en este tutorial.
* **Nombre de escucha de grupo de disponibilidad** lo usan tanto el clúster como el equilibrador de carga interno. Los clientes que se conectan a SQL Server pueden usar este nombre para conectarse a la réplica de la base de datos adecuada. Use **Contoso-listener** en este tutorial.
* **Puerto de escucha de grupo de disponibilidad** especifica el puerto TCP que usa el agente de escucha de SQL Server. Use el puerto predeterminado, **1433**, en este tutorial.

Si es necesario, puede cambiar estos valores. En este tutorial use los valores preestablecidos.  

![Configuración de grupos de disponibilidad](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Haga clic en **Aceptar**.

### <a name="virtual-machine-size-storage-settings"></a>Configuración de tamaño y almacenamiento de máquina virtual
En **Configuración de tamaño y almacenamiento de máquina virtual**, elija un tamaño de máquina virtual con SQL Server y revise el resto de las opciones.

* **Tamaño de máquina virtual de SQL Server** es el tamaño de ambas máquinas virtuales que ejecutan SQL Server. Elija un tamaño de máquina virtual adecuado para la carga de trabajo. Si va a crear este entorno en el tutorial, use **DS2**. Para cargas de trabajo de producción elija un tamaño de máquina virtual que pueda admitir la carga de trabajo. Muchas cargas de trabajo de producción requieren **DS4** o superior. La plantilla crea dos máquinas virtuales de este tamaño e instala SQL Server en cada una de ellas. Para más información, consulte [Tamaños de las máquinas virtuales Linux en Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure instala Enterprise Edition de SQL Server. El costo depende de la edición y el tamaño de la máquina virtual. Para más información sobre los costos actuales, consulte los [precios de máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Domain controller virtual machine size** (Tamaño de la máquina virtual del controlador de dominio) es el tamaño de la máquina virtual de los controladores de dominio. Use **D2**en este tutorial.
* **File Share Witness virtual machine size** (Tamaño de la máquina virtual del testigo del recurso compartido de archivos) es el tamaño de la máquina virtual del testigo del recurso compartido de archivos. Use **A1**en este tutorial.
* **Cuenta de almacenamiento SQL** es el nombre de la cuenta de almacenamiento que contiene los discos del sistema operativo y los datos de SQL Server. Use **alwaysonsql01** en este tutorial.
* **DC Storage account** (Cuenta de almacenamiento de controladores de dominio) es el nombre de la cuenta de almacenamiento para los controladores de dominio. Use **alwaysondc01**en este tutorial.
* **SQL Server data disk size** (Tamaño de disco de datos de SQL Server) es el tamaño del disco de datos de SQL Server, en TB. Especifique un número entre 1 y 4 Use **1** en este tutorial.
* **Storage optimization** (Optimización de almacenamiento) establece una configuración de almacenamiento específica para las máquinas virtuales con SQL Server en función del tipo de carga de trabajo. Todas las máquinas virtuales de SQL Server en este escenario usan almacenamiento premium con caché de host de disco de Azure establecida en solo lectura. Además, puede optimizar la configuración de SQL Server para la carga de trabajo eligiendo una de estas tres configuraciones:

  * **Carga de trabajo general** no establece ninguna configuración específica.
  * **Procesamiento transaccional** establece las marcas de seguimiento 1117 y 1118.
  * **Almacenamiento de datos** establece las marcas de seguimiento 1117 y 610.

Use **Carga de trabajo general** en este tutorial.

![Configuración de almacenamiento del tamaño de VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Revise la configuración y, luego, haga clic en **Aceptar**.

#### <a name="a-note-about-storage"></a>Una nota acerca de almacenamiento
Las optimizaciones adicionales dependen del tamaño de los discos de datos de SQL Server. Por cada terabyte de disco de datos, Azure agrega un almacenamiento premium adicional de 1 TB. Cuando un servidor requiere de 2 TB o más, la plantilla crea un bloque de almacenamiento en cada máquina virtual de SQL Server. Un bloque de almacenamiento es una forma de virtualización del almacenamiento donde varios discos se configuran para proporcionar mayor capacidad, resistencia y rendimiento.  Después, la plantilla crea un espacio de almacenamiento en el grupo de almacenamiento y presenta un disco de datos único en el sistema operativo. La plantilla designa este disco como disco de datos para SQL Server. La plantilla optimiza el bloque de almacenamiento para SQL Server con la siguiente configuración:

* El tamaño de franja es la configuración de intercalación para el disco virtual. Las cargas de trabajo transaccionales usan 64 KB. Las cargas de almacenamiento de datos utilizan 256 KB.
* La resistencia es simple (sin resistencia).

> [!NOTE]
> El almacenamiento premium de Azure tiene redundancia local y mantiene tres copias de los datos dentro de una sola región, por lo que no se necesita resistencia adicional en el bloque de almacenamiento.
>
>

* El número de columnas es igual al número de discos del bloque de almacenamiento.

Para información adicional sobre el espacio de almacenamiento y los bloques de almacenamiento, consulte:

* [Introducción a los espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx)
* [Copias de seguridad de Windows Server y bloques de almacenamiento](http://technet.microsoft.com/library/dn390929.aspx)

Para más información sobre los procedimientos recomendados para configurar SQL Server, consulte [Procedimientos recomendados para mejorar el rendimiento para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Configuración de SQL Server
En **Configuración de SQL Server** , revise y modifique el prefijo del nombre de máquina virtual con SQL Server, la versión de SQL Server, la cuenta de servicio y la contraseña de SQL Server y la programación del mantenimiento de la aplicación de revisión automatizada de SQL.

* **Prefijo de nombre de SQL Server** se usa para crear un nombre para cada máquina virtual de SQL Server. Use **sqlserver** en este tutorial. La plantilla nombra a las máquinas virtuales de SQL Server *sqlserver-0* y *sqlserver-1*.
* **Versión de SQL Server** es la versión de SQL Server. Use **SQL Server 2014**en este tutorial. También puede elegir **SQL Server 2012** o **SQL Server 2016**.
* **SQL Server service account user name** (Nombre de usuario de la cuenta del servicio SQL Server) es el nombre de la cuenta del dominio del servicio SQL Server. Use **sqlservice** en este tutorial.
* **Contraseña** es la contraseña de la cuenta del servicio SQL Server.  Utilice una contraseña compleja. Confirme la contraseña.
* **Programación de mantenimiento para aplicar revisiones automáticas de SQL** identifica el día de la semana en que Azure aplica revisiones automáticamente a los servidores SQL Server. Escriba **Domingo** en este tutorial.
* **SQL Auto Patching maintenance start hour** (Hora de inicio del mantenimiento de aplicación de revisiones automáticas de SQL) es la hora del día de la región de Azure en que comienza la aplicación de revisiones automática.

> [!NOTE]
> La ventana de aplicación de revisiones para cada máquina virtual se escalona en una hora. Solo se aplican revisiones a una máquina virtual a la vez para evitar la interrupción de los servicios.
>
>

![Configuración de SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Revise la configuración y, luego, haga clic en **Aceptar**.

### <a name="summary"></a>Resumen
Azure valida la configuración en la página de resumen. También puede descargar la plantilla. Revise el resumen. Haga clic en **Aceptar**.

### <a name="buy"></a>Comprar
Esta hoja final contiene las **condiciones de uso** y la **directiva de privacidad**. Revise esta información. Cuando esté listo para que Azure empiece a crear las máquinas virtuales y el resto de los recursos requeridos para el grupo de disponibilidad, haga clic en **Crear**.

Azure Portal creará el grupo de recursos y todos los recursos.

## <a name="monitor-deployment"></a>Supervisión de la implementación
Supervise el progreso de la implementación desde el portal de Azure. Un icono que representa la implementación se ancla automáticamente en el panel de Azure Portal.

![Panel de Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Conexión con SQL Server
Las nuevas instancias de SQL Server se ejecutan en máquinas virtuales que tienen direcciones IP conectadas a Internet. Puede usar Escritorio remoto (RDP) directamente con cada máquina virtual de SQL Server.

Para RDP a servidor SQL Server, siga estos pasos:

1. Desde el panel de Azure Portal, compruebe que la implementación se realizó correctamente.
2. Haga clic en **Recursos**.
3. En la hoja **Recursos**, haga clic en **sqlserver-0**, que es el nombre del equipo de una de las máquinas virtuales que ejecuta SQL Server.
4. En la hoja de **sqlserver 0**, haga clic en **Conectar**. El explorador le preguntará si desea abrir o guardar el objeto de conexión remota. Haga clic en **Abrir**.
5. **Conexión a Escritorio remoto** puede advertirle de que no se puede identificar el publicador de esta conexión remota. Haga clic en **Conectar**.
6. Seguridad de Windows le pide que escriba sus credenciales para conectarse a la dirección IP del controlador de dominio principal. Haga clic en **Usar otra cuenta**. En **Nombre de usuario**, escriba **contoso\DomainAdmin**. Ha configurado esta cuenta al establecer el nombre de usuario del administrador en la plantilla. Utilice la contraseña compleja que eligió al configurar la plantilla.
7. **Escritorio remoto** puede advertirle de que el equipo remoto no se pudo autenticar debido a problemas con su certificado de seguridad. Le muestra el nombre del certificado de seguridad. Si siguió el tutorial, el nombre será **sqlserver-0.contoso.com**. Haga clic en **Sí**.

Ahora está conectado con RDP a la máquina virtual de SQL Server. Puede abrir SQL Server Management Studio, conectarse a la instancia predeterminada de SQL Server y comprobar que el grupo de disponibilidad está configurado.

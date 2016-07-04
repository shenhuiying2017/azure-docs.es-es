<properties
	pageTitle="Configuración manual de grupos de disponibilidad Always On en máquinas virtuales de Azure Resource Manager"
	description="Creación de un grupo de disponibilidad AlwaysOn con máquinas virtuales de Azure. Este tutorial usa principalmente la interfaz de usuario y herramientas en lugar de scripts."
	services="virtual-machines"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="06/15/2016"
	ms.author="MikeRayMSFT" />

# Configuración manual de grupos de disponibilidad Always On en máquinas virtuales de Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure Resource Manager: configuración automática](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Azure Resource Manager: configuración manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Portal de Azure clásico: interfaz de usuario](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Portal de Azure clásico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

En este tutorial completo se muestra cómo implementar grupos de disponibilidad de SQL Server en máquinas virtuales de Azure Resource Manager.

Al final del tutorial, la solución constará de los siguientes elementos:

- Una red virtual que contiene dos subredes, una front-end y otra back-end

- Dos controladores de dominio en un conjunto de disponibilidad con un dominio de Active Directory (AD)

- Dos máquinas virtuales de SQL Server en un conjunto de disponibilidad implementadas en la subred back-end y unidas al dominio de AD

- Un clúster WSFC de tres nodos con el modelo de cuórum Mayoría de nodo

- Un equilibrador de carga interno para proporcionar una dirección IP a los grupos de disponibilidad

- Un grupo de disponibilidad con dos réplicas de confirmación sincrónica de una base de datos de disponibilidad

La siguiente ilustración es una representación gráfica de la solución.

![Estructura para grupos de disponibilidad en ARM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/00-EndstateSampleNoELB.png)

Tenga en cuenta que se trata de una de las posibles configuraciones. Por ejemplo, puede minimizar el número de máquinas virtuales para un grupo de disponibilidad de dos réplicas para ahorrar horas de cálculo en Azure con el controlador de dominio como el testigo de recurso compartido de archivos de cuórum en un clúster WSFC de dos nodos. Este método reduce el recuento de máquinas virtuales en uno respecto a la configuración anterior.

>[AZURE.NOTE] Terminar este tutorial conlleva un tiempo importante. También puede crear automáticamente toda la solución. El Portal de Azure incluye una configuración de la galería para grupos de disponibilidad Always On con un agente de escucha. Así se configura automáticamente todo lo necesario para los grupos de disponibilidad. Para obtener más información, consulte [Azure Portal - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) (Portal de Azure Resource Manager).

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

En este tutorial se da por hecho lo siguiente:

- Ya tiene una cuenta de Azure.

- Ya sabe cómo aprovisionar una máquina virtual de SQL Server desde la galería de máquina virtual mediante la interfaz gráfica de usuario (GUI). Para obtener más información, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

- Ya tiene un conocimiento sólido de los grupos de disponibilidad. Para más información, consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Si le interesa utilizar los grupos de disponibilidad con SharePoint, consulte también [Configuración de grupos de disponibilidad AlwaysOn de SQL Server 2012 para SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Creación de un grupo de recursos

1. Inicie sesión en el [Portal de Azure](http://portal.azure.com). 

1. Haga clic en **+Nuevo** y, después, escriba **Grupo de recursos** en la ventana de búsqueda de **Marketplace**.

    ![El grupos de recursos](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-resourcegroupsymbol.png)

1. Haga clic en **Grupo de recursos**.

    ![Nuevo grupo de recursos](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-newresourcegroup.png)

1. Haga clic en **Crear**.

1. En **Nombre del grupo de recursos** de la hoja **Grupo de recursos**, escriba **SQL-HA-RG**.

1. Si tiene varias suscripciones de Azure, compruebe que es la suscripción de Azure en la que quiere crear el grupo de disponibilidad.

1. Seleccione una ubicación. La ubicación es la ubicación de Azure donde se ejecutará el grupo de disponibilidad. Para este tutorial vamos a generar todos los recursos en una ubicación de Azure.

1. Compruebe que la función **Anclar al panel** está activada. Este parámetro opcional coloca un acceso directo para el grupo de recursos en el panel del Portal de Azure.

1. Haga clic en **Crear** para crear el grupo de recursos.

Azure creará el nuevo grupo de recursos y anclará un acceso directo al grupo de recursos en el portal.

## Creación de redes y subredes

El siguiente paso es crear las redes y subredes en el grupo de recursos de Azure.

La solución usa una red virtual con dos subredes. Debe entender los conceptos básicos de las redes y su funcionamiento en Azure. En [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md) encontrará más información sobre las redes de Azure.

Para crear la red virtual:

1. En el Portal de Azure, haga clic en el nuevo grupo de recursos y después en **+** para agregar un nuevo elemento al grupo de recursos. Azure abre la hoja **Todo**. 

    ![Nuevo elemento](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/02-newiteminrg.png)

1. Busque **red virtual**.

    ![Búsqueda de una red virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/04-findvirtualnetwork.png)

1. Haga clic en **Red virtual**.

1. En la hoja **Red virtual**, haga clic en el modelo de implementación de **Resource Manager** y en **Crear**.

    ![Cree una red virtual:](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/05-createvirtualnetwork.png)
 

 
1. En la hoja **Crear red virtual**, configure la red virtual.

    La siguiente tabla muestra la configuración de la red virtual.

    | **Campo** | Valor |
| ----- | ----- |
| **Name** | autoHAVNET |
| **Espacio de direcciones** | 10\.0.0.0/16 |
| **Nombre de subred** | Subnet-1 |
| **Intervalo de direcciones de subred** | 10\.0.0.0/24 |
| **Suscripción** | Especifique la suscripción que quiere usar. Si solo tiene una suscripción, esta opción aparecerá en blanco. |
| **Ubicación** | Especifique la ubicación de Azure donde se implementará el grupo de disponibilidad |

    Tenga en cuenta que el intervalo de direcciones de subred y el espacio de direcciones pueden ser distintos a los de la tabla. Dependiendo de su suscripción, Azure especificará automáticamente un espacio de direcciones disponible y el intervalo de direcciones de subred correspondiente. Si no hay suficiente espacio de direcciones disponible, use otra suscripción.

1. Haga clic en **Crear**

    ![Configuración de una red virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/06-configurevirtualnetwork.png)

Azure le devolverá al panel del portal y le informará cuando se cree la nueva red.

### Creación de la segunda subred

En este momento la red virtual contiene una subred, denominada Subnet-1. Los controladores de dominio usarán esta subred. Los servidores SQL Server usarán una segunda subred denominada **Subnet-2**. Para configurar la subred Subnet-2

1. En el panel, haga clic en el grupo de recursos que ha creado, **SQL-HA-RG**. En **Recursos**, busque la red en el grupo de recursos.

  Si **SQL-HA-RG** no está visible, puede buscarlo haciendo clic en **Grupos de recursos** y filtrando por el nombre del grupo de recursos que ha creado.

1.  Haga clic en **autoHAVNET** en la lista de recursos para abrir la hoja de configuración de red.

1.  En la red virtual **autoHAVNET**, haga clic en **Toda la configuración*.

1. En la hoja **Configuración**, haga clic en **Subredes**.

    Observe la subred que ya ha creado.

    ![Configuración de una red virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/07-addsubnet.png)

1. Cree una segunda subred. Haga clic en **+ Subred**.

    En la hoja **Agregar subred**, configure la subred escribiendo **subnet-2** en **Nombre**. Azure especificará automáticamente un **intervalo de direcciones** válido. Compruebe que este intervalo de direcciones tiene al menos 10 direcciones. En un entorno de producción puede necesitar más direcciones.

1. Haga clic en **Aceptar**.
 
![Configuración de una red virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/08-configuresubnet.png)
   
Este es un resumen de la configuración de la red virtual y las dos subredes.

| **Campo** | Valor |
| ----- | ----- |
| **Name** | **autoHAVNET** |
| **Espacio de direcciones** | Depende de los espacios de direcciones disponibles en su suscripción. Un valor típico es 10.0.0.0/16 |
| **Nombre de subred** | **Subnet-1** |
| **Intervalo de direcciones de subred** | Depende de los espacios de direcciones disponibles en su suscripción. Un valor típico es 10.0.0.0/24. |
| **Nombre de subred** | **Subnet-2** |
| **Intervalo de direcciones de subred** | Depende de los espacios de direcciones disponibles en su suscripción. Un valor típico es 10.0.1.0/24. |
| **Suscripción** | Especifique la suscripción que quiere usar. |
| **Grupo de recursos** | **SQL-HA-RG** |
| **Ubicación** | Especifique la misma ubicación que eligió para el grupo de recursos. |

## Creación de conjuntos de disponibilidad

Antes de crear máquinas virtuales, debe crear conjuntos de disponibilidad. Los conjuntos de disponibilidad reducen el tiempo de inactividad para el mantenimiento, tanto programado como esporádico. Un conjunto de disponibilidad de Azure es un grupo lógico de recursos que Azure coloca en dominios de error y de actualización físicos. Un dominio de error garantiza que los miembros del conjunto de disponibilidad tengan recursos de energía y red independientes. Un dominio de actualización garantiza que los miembros del conjunto de disponibilidad no pasen a inactividad por mantenimiento al mismo tiempo. [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-windows-manage-availability.md).

Necesitará dos conjuntos de disponibilidad. Uno es para los controladores de dominio; el otro, para los servidores SQL Server.

Para crear un conjunto de disponibilidad, vaya al grupo de recursos y haga clic en **Agregar**. Filtre los resultados escribiendo **Conjunto de disponibilidad**. Haga clic en la opción **Conjunto de disponibilidad** de los resultados. Haga clic en **Crear**.

Configure dos conjuntos de disponibilidad según los parámetros de la tabla siguiente.

| **Campo** | Conjunto de disponibilidad del controlador de dominio | Conjunto de disponibilidad del servidor SQL Server |
| ----- | ----- | ----- |
| **Name** | adAvailablitySet | sqlAvailabilitySet|
| **Grupos de recursos** | SQL-HA-RG | SQL-HA-RG |
| **Dominios de error** | 3 | 3 |
| **Dominios de actualización** | 5 | 3 |

Después de crear los conjuntos de disponibilidad, vuelva al grupo de recursos en el Portal de Azure.

## Creación de controladores de dominio

En este momento ha creado la red, subredes, conjuntos de disponibilidad y un equilibrador de carga accesible desde Internet. Está listo para crear las máquinas virtuales para los controladores de dominio.

### Creación de las máquinas virtuales para los controladores de dominio

Para crear y configurar los controladores de dominio, vuelva al grupo de recursos **SQL-HA-RG**.

1. Haga clic en Agregar. Se abrirá la hoja **Todo**.

1. Escriba **Windows Server 2012 R2 Datacenter**.

1. Haga clic en **Centro de datos de Windows Server 2012 R2**. En la hoja **Windows Server 2012 R2 Datacenter**, compruebe que el modelo de implementación está establecido en **Resource Manager** y haga clic en **Crear**. Azure abre la hoja **Crear máquina virtual**.

Para crear dos máquinas virtuales, pasará dos veces por ese proceso. Asigne el nombre a las dos máquinas virtuales:

- ad-primary-dc
- ad-secondary-dc

 >[AZURE.NOTE] **ad-secondary-dc** es un componente opcional cuyo fin es proporcionar alta disponibilidad para servicios de dominio de Active Directory.

La siguiente tabla muestra la configuración de estas dos máquinas.

| **Campo** | Valor 
| ----- | ---- 
| **Nombre de usuario** | DomainAdmin
| **Password** | Contoso!000 |
| **Suscripción** | *su suscripción* |
| **Grupos de recursos** | SQL-HA-RG |
| **Ubicación** | *su ubicación* 
| **Tamaño** | D1\_V2 (estándar)
| **Tipo de almacenamiento** | estándar
| **Cuenta de almacenamiento** | *Se crea automáticamente*
| **Red virtual** | autoHAVNET
| **Subred** | subnet-1
| **Dirección IP pública** | *Mismo nombre que la máquina virtual*
| **Grupo de seguridad de red (NSG)** | *Mismo nombre que la máquina virtual*
| **Diagnóstico** | Enabled
| **Cuenta de almacenamiento de información de diagnóstico** | *Se crea automáticamente*
| **El conjunto de disponibilidad** | adAvailabilitySet

>[AZURE.NOTE] Una vez creada una máquina virtual no se puede cambiar el conjunto de disponibilidad.

Azure creará las máquinas virtuales.

Después de crear las máquinas virtuales, configure el controlador de dominio.

### Configuración del controlador de dominio

En los pasos siguientes configurará la máquina **ad-primary-dc** como controlador de dominio para corp.contoso.com.

1. En el portal, abra el grupo de recursos **SQL-HA-RG** y seleccione la máquina **ad-primary-dc**. En la hoja **ad-primary-dc**, haga clic en **Conectar** para abrir un archivo RDP con el fin de obtener acceso de escritorio remoto.

	![Conectarse a máquinas virtuales](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/20-connectrdp.png)

1. Inicie sesión con su cuenta de administrador configurada (**\\DomainAdmin**) y la contraseña (**Contoso!000**).

1. De forma predeterminada, se debe mostrar el panel **Administrador del servidor** .

1. Haga clic en el vínculo **Agregar roles y características** en el panel.

	![Explorador de servidores Agregar roles](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784623.png)

1. Seleccione **Siguiente** hasta llegar a la sección **Roles de servidor**.

1. Seleccione los roles **Servicios de dominio de Active Directory** y **Servidor DNS**. Cuando se le pida, agregue las características adicionales que requieran estos roles.

	>[AZURE.NOTE] Recibirá una advertencia de validación que le indicará que no hay ninguna dirección IP estática. Si está probando la configuración, haga clic en Continuar En los escenarios de producción, defina la dirección IP como estática en el Portal de Azure o [use PowerShell para establecer la dirección IP estática de la máquina del controlador de dominio](./virtual-network/virtual-networks-reserved-private-ip.md).

	![Diálogo Agregar roles](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784624.png)

1. Haga clic en **Siguiente** hasta llegar a la sección **Confirmación**. Active la casilla **Reiniciar automáticamente el servidor de destino en caso necesario**.

1. Haga clic en **Instalar**.

1. Cuando terminen de instalarse las características, vuelva al panel **Administrador del servidor**.

1. Seleccione la nueva opción **AD DS** en el panel izquierdo.

1. Haga clic en el vínculo **Más** en la barra de advertencia amarilla.

	![Cuadro de diálogo AD DS en la máquina virtual del servidor de DNS](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784625.png)

1. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, haga clic en **Promover este servidor a controlador de dominio**.

1. En el **Asistente de configuración de Servicios de dominio de Active Directory**, use los siguientes valores:

| **Page** |Configuración|
|---|---|
|**Configuración de la implementación** |**Agregar un nuevo bosque** = seleccionado<br/>**Nombre del dominio raíz** = corp.contoso.com|
|**Opciones del controlador de dominio**|**Contraseña de DSRM** = Contoso!000<br/>**Confirmar contraseña** = Contoso!000|

1. Haga clic en **Siguiente** para avanzar por las otras páginas del asistente. En la página **Comprobación de requisitos previos**, compruebe que ve el mensaje siguiente: **Todas las comprobaciones de requisitos previos se realizaron correctamente**. Tenga en cuenta que debe revisar todos los mensajes de advertencia aplicables, pero puede continuar con la instalación.

1. Haga clic en **Instalar**. La máquina virtual **ad-primary-dc** se reiniciará automáticamente.

### Configuración del segundo controlador de dominio

Una vez reiniciado el controlador de dominio principal, puede configurar el segundo controlador de dominio. Este paso opcional es para lograr alta disponibilidad. Para completar este paso, debe conocer la dirección IP privada para el controlador de dominio. La encontrará en el Portal de Azure. Para configurar el segundo controlador de dominio siga estos pasos.

1. Vuelva a iniciar sesión en la máquina **ad-primary-dc**. 

1. Abra el escritorio remoto y conéctese al controlador de dominio secundario por la dirección IP. Si no conoce la dirección IP del controlador de dominio secundario, vaya al Portal de Azure y compruebe la dirección asignada a la interfaz de red del controlador de dominio secundario.

1. Cambie la dirección del servidor DNS preferido por la dirección del controlador de dominio.

1. Inicie el archivo RDP en el controlador de dominio principal (**ad-primary-dc**) e inicie sesión en la máquina virtual con la cuenta de administrador configurada (**BUILTIN\\DomainAdmin**) y la contraseña (**Contoso!000**).

1. Desde el controlador de dominio principal, inicie una sesión de escritorio remoto en **ad-secondary-dc** con la dirección IP. Use la misma cuenta y contraseña.

1. Una vez iniciada la sesión, debería ver el panel **Administrador del servidor**. En el panel izquierdo, haga clic en **Servidor local**.

1. Seleccione el vínculo **Dirección IPv4 asignada por DHCP, IPv6 habilitado** .

1. En la ventana **Conexiones de red**, seleccione el icono de red.

	![Cambio del servidor DNS preferido de máquina virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)

1. En la barra de comandos, haga clic en **Cambiar la configuración de esta conexión** (según el tamaño de la ventana, tendrá que hacer clic en la flecha doble que está mirando hacia la derecha para ver este comando).

1. Seleccione **Protocolo de Internet versión 4 (TCP/IPv4)** y haga clic en Propiedades.

1. Seleccione Usar las siguientes direcciones de servidor DNS y especifique la dirección del controlador de dominio principal en **Servidor DNS preferido**.

1. La dirección es la que se asigna a una máquina virtual en subnet-1 de la red virtual de Azure y esa máquina virtual es **ad-primary-dc**. Para comprobar la dirección IP de **ad-primary-dc**, use la opción **nslookup ad-primary-dc** en el símbolo del sistema, según se muestra a continuación.

	![Uso de NSLOOKUP para buscar la dirección IP para DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)

  >[AZURE.NOTE] Después de configurar el DNS, puede perder la sesión RDP para el servidor miembro. En ese caso, reinicie la máquina virtual desde el Portal de Azure.


1. Haga clic en **Aceptar** y, después, en **Cerrar** para confirmar los cambios. Ahora ya puede unir la máquina virtual a **corp.contoso.com**.

1. Repita los pasos que siguió para crear el primer controlador de dominio, excepto en el **Asistente para configuración de Servicios de dominio de Active Directory**, donde deberá usar los siguientes valores:

|Page|Configuración|
|---|---|
|**Configuración de la implementación**|**Agregar un controlador de dominio a un dominio existente** = Seleccionado<br/>**Raíz** = corp.contoso.com|
|**Opciones del controlador de dominio**|**Contraseña de DSRM** = Contoso!000<br/>**Confirmar contraseña** = Contoso!000|


### Configuración de cuentas de dominio

Los pasos siguientes configuran las cuentas de Active Directory (AD) para su uso posterior.

1. Vuelva a iniciar sesión en la máquina **ad-primary-dc**.

1. En **Administrador del servidor**, seleccione **Herramientas** y, después, haga clic en **Centro de administración de Active Directory**.

	![Centro de administración de Active Directory](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784626.png)

1. En el **Centro de administración de Active Directory** seleccione **corp (local)** en el panel izquierdo.

1. En el panel **Tareas** de la derecha, seleccione **Nuevo** y, después, haga clic en **Usuario**. Use la configuración siguiente:

|Configuración|Valor|
|---|---|
|**Nombre**|Instalación|
|**Usuario NombreDeCuentaSam**|Instalación|
|**Password**|Contoso!000|
|**Confirmar contraseña**|Contoso!000|
|**Otras opciones de contraseña**|Seleccionado|
|**La contraseña nunca expira**|Activado|

1. Haga clic en **Aceptar** para crear el usuario **Install**. Esta cuenta se usará para configurar el clúster de conmutación por error y el grupo de disponibilidad.

1. Cree dos usuarios adicionales con los mismos pasos: **CORP\\SQLSvc1** y **CORP\\SQLSvc2**. Estas cuentas se usarán para las instancias de SQL Server. A continuación, tiene que conceder a **CORP\\Install** los permisos necesarios para configurar la agrupación en clústeres de conmutación por error de Windows Service (WSFC).

1. En el **Centro de administración de Active Directory**, seleccione **corp (local)** en el panel izquierdo. A continuación, en el panel **Tareas** a la derecha, haga clic en **Propiedades**.

	![Propiedades de usuario CORP](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784627.png)

1. Seleccione **Extensiones** y luego haga clic en el botón **Avanzadas** situado en la pestaña **Seguridad**.

1. En el cuadro de diálogo **Configuración de seguridad avanzada** para corp. Haga clic en **Agregar**.

1. Haga clic en **Seleccionar una entidad de seguridad**. Después busque **CORP\\Install**. Haga clic en **Aceptar**.

1. Seleccione los permisos **Leer todas las propiedades** y **Crear objetos de equipo**.

	![Permisos de usuario Corp](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784628.png)

1. Haga clic en **Aceptar** y luego vuelva a hacer clic en **Aceptar**. Cierre la ventana de propiedades de corp.

Ahora que ha terminado de configurar Active Directory y los objetos de usuario, creará dos máquinas virtuales de SQL Server y una de servidor testigo, y las unirá a este dominio.

## Creación de instancias de SQL Server

###Creación y configuración de las máquinas virtuales de SQL Server

A continuación, cree tres máquinas virtuales, incluidas dos de SQL Server y un nodo de clúster WSFC. Para crear las máquinas virtuales, vuelva al grupo de recursos **HA-AG-RG**, haga clic en **Agregar**, busque el elemento apropiado de la galería, **Máquina virtual**, y, después, haga clic en **De la galería**. Después use las plantillas de la tabla siguiente para ayudarle a crear las máquinas virtuales.

|Page|VM1|VM2|VM3|
|---|---|---|---|
|Selección del elemento adecuado de la galería|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 SP1 Enterprise en Windows Server 2012 R2**|**SQL Server 2014 SP1 Enterprise en Windows Server 2012 R2**|
| Configuración de máquina virtual: **Aspectos básicos** | **Nombre** = cluster-fsw<br/>**Nombre de usuario** = DomainAdmin<br/>**Contraseña** = Contoso!000<br/>**Suscripción** = Su suscripción<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Ubicación** = Su ubicación de Azure | **Nombre** = sqlserver-0<br/>**Nombre de usuario** = DomainAdmin<br/>**Contraseña** = Contoso!000<br/>**Suscripción** = Su suscripción<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Ubicación** = Su ubicación de Azure | **Nombre** = sqlserver-1<br/>**Nombre de usuario** = DomainAdmin<br/>**Contraseña** = Contoso!000<br/>**Suscripción** = Su suscripción<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Ubicación** = Su ubicación de Azure |
|Configuración de máquina virtual: **Tamaño** |DS1 (1 núcleo, 3,5 GB de memoria)|**TAMAÑO** = DS 2 (2 núcleos y 7 GB de memoria)|**TAMAÑO** = DS 2 (2 núcleos y 7 GB de memoria)|
|Configuración de máquina virtual **Configuración**|**Almacenamiento** = Premium (SSD)<br/>**SUBREDES** = autoHAVNET<br/>**CUENTA DE ALMACENAMIENTO** = Usar una cuenta de almacenamiento generada automáticamente<br/>**Subred** = subnet-2(10.1.1.0/24)<br/>**Dirección IP pública** = Ninguno<br/>**Grupo de seguridad de red** = Ninguno<br/>**Supervisión y diagnóstico** = Habilitado<br/>**Cuenta de almacenamiento de información de diagnóstico** = Usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = sqlAvailabilitySet<br/>|**Almacenamiento** = Premium (SSD)<br/>**SUBREDES** = autoHAVNET<br/>**CUENTA DE ALMACENAMIENTO** = Usar una cuenta de almacenamiento generada automáticamente<br/>**Subred** = subnet-2(10.1.1.0/24)<br/>**Dirección IP pública** = Ninguno<br/>**Grupo de seguridad de red** = Ninguno<br/>**Supervisión y diagnóstico** = Habilitado<br/>**Cuenta de almacenamiento de información de diagnóstico** = Usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = sqlAvailabilitySet<br/>|**Almacenamiento** = Premium (SSD)<br/>**SUBREDES** = autoHAVNET<br/>**CUENTA DE ALMACENAMIENTO** = Usar una cuenta de almacenamiento generada automáticamente<br/>**Subred** = subnet-2(10.1.1.0/24)<br/>**Dirección IP pública** = Ninguna<br/>**Grupo de seguridad de red** = Ninguno<br/>**Supervisión y diagnóstico** = Habilitado<br/>**Cuenta de almacenamiento de información de diagnóstico** = Usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = sqlAvailabilitySet<br/>
|Configuración de máquina virtual **Configuración de SQL Server**|No aplicable|**Conectividad SQL** = Privada (dentro de la red virtual)<br/>**Puerto** = 1433<br/>**Autenticación SQL** = Deshabilitar<br/>**Configuración de almacenamiento** = General<br/>**Aplicación de revisión automatizada** = El domingo a las 2:00<br/>**Copia de seguridad automatizada** = Deshabilitada</br>**Integración del Almacén de claves de Azure** = Deshabilitado|**Conectividad SQL** = Privada (dentro de la red virtual)<br/>**Puerto** = 1433<br/>**Autenticación SQL** = Deshabilitar<br/>**Configuración de almacenamiento** = General<br/>**Aplicación de revisión automatizada** = El domingo a las 2:00<br/>**Copia de seguridad automatizada** = deshabilitada</br>**Integración del Almacén de claves de Azure** = Deshabilitado|

<br/>

>[AZURE.NOTE] La configuración anterior le sugiere usar máquinas virtuales de nivel ESTÁNDAR, porque las máquinas de nivel BÁSICO no admiten puntos de conexión con equilibrio de carga, y estos son necesarios para el agente de escucha de los grupos de disponibilidad. Asimismo, los tamaños de máquina que se sugieren aquí están diseñados para probar los grupos de disponibilidad en máquinas virtuales de Azure. Para optimizar el rendimiento de las cargas de trabajo de producción, consulte las recomendaciones de tamaños de máquina y la configuración de SQL Server en [Procedimientos recomendados de SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md).



Una vez que las tres máquinas virtuales estén totalmente aprovisionadas, tendrá que unirlas al dominio **corp.contoso.com** y conceder a CORP\\Install los derechos administrativos sobre las máquinas.

Le será más fácil si escribe las direcciones IP virtuales de Azure para cada máquina virtual. Consiga la dirección IP de cada servidor. En el grupo de recursos SQL-HA-RG de Azure, haga clic en el recurso **autohaVNET**. La hoja **autohaVNET** mostrará las direcciones IP de cada máquina de la red. Registre las direcciones IP de los siguientes dispositivos:

| Rol de VM | Dispositivo | Dirección IP
| ----- | ----- | -----
| Controlador de dominio principal | ad-primary-dc |
| Controlador de dominio secundario | ad-secondary-dc |
| Testigo de recurso compartido de archivos de clúster | cluster-fsw |
| SQL Server | sqlserver-0 | 
| SQL Server | sqlserver-1 | 

Estas direcciones se usarán para configurar el servicio DNS para cada máquina virtual. Para ello, siga estos pasos para cada una de las tres máquinas virtuales.


1. En primer lugar, cambie la dirección del servidor DNS preferido para cada servidor miembro. 

1. Inicie el archivo RDP en el controlador de dominio principal (**ad-primary-dc**) e inicie sesión en la máquina virtual con la cuenta de administrador configurada (**BUILTIN\\DomainAdmin**) y la contraseña (**Contoso!000**).

1. Desde el controlador de dominio principal, inicie una sesión de escritorio remoto en **sqlserver-0** con la dirección IP. Use la misma cuenta y contraseña.

1. Una vez iniciada la sesión, debería ver el panel **Administrador del servidor**. En el panel izquierdo, haga clic en **Servidor local**.

1. Seleccione el vínculo **Dirección IPv4 asignada por DHCP, IPv6 habilitado** .

1. En la ventana **Conexiones de red**, seleccione el icono de red.

	![Cambio del servidor DNS preferido de máquina virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)

1. En la barra de comandos, haga clic en **Cambiar la configuración de esta conexión** (según el tamaño de la ventana, tendrá que hacer clic en la flecha doble que está mirando hacia la derecha para ver este comando).

1. Seleccione **Protocolo de Internet versión 4 (TCP/IPv4)** y haga clic en Propiedades.

1. Seleccione Usar las siguientes direcciones de servidor DNS y especifique la dirección del controlador de dominio principal en **Servidor DNS preferido**.

1. La dirección es la que se asigna a una máquina virtual en subnet-1 de la red virtual de Azure y esa máquina virtual es **ad-primary-dc**. Para comprobar la dirección IP de **ad-primary-dc**, use la opción **nslookup ad-primary-dc** en el símbolo del sistema, según se muestra a continuación.

	![Uso de NSLOOKUP para buscar la dirección IP para DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)

  >[AZURE.NOTE] Después de configurar el DNS, puede perder la sesión RDP para el servidor miembro. En ese caso, reinicie la máquina virtual desde el Portal de Azure.


1. Haga clic en **Aceptar** y, después, en **Cerrar** para confirmar los cambios. Ahora ya puede unir la máquina virtual a **corp.contoso.com**.

1. De vuelta en la ventana **Servidor Local**, haga clic en el vínculo **GRUPO\_DE\_TRABAJO**.

1. En la sección **Nombre de equipo**, haga clic en **Cambiar**.

1. Active la casilla **Dominio** y escriba **corp.contoso.com** en el cuadro de texto. Haga clic en **Aceptar**.

1. En el cuadro de diálogo emergente **Seguridad de Windows**, especifique las credenciales de la cuenta de administrador del dominio predeterminado (**CORP\\DomainAdmin**) y la contraseña (**Contoso!000**).

1. Cuando vea el mensaje "Bienvenida al dominio corp.contoso.com", haga clic en **Aceptar**.

1. Haga clic en **Cerrar** y, a continuación, en **Reiniciar ahora** en el cuadro de diálogo emergente.

1. Repita estos pasos para el servidor del testigo del recurso compartido de archivos y los servidores SQL Server.

### Agregue el usuario Corp\\Install como administrador en cada máquina virtual de clúster:

1. Espere hasta que se reinicie la máquina virtual y, después, inicie el archivo RDP de nuevo desde el controlador de dominio principal para iniciar sesión en **sqlserver-0** con la cuenta **BUILTIN\\DomainAdmin**.

1. En **Administrador del servidor** seleccione **Herramientas** y, a continuación, haga clic en **Administración de equipos**.

	![Administración de equipos](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784630.png)

1. En la ventana **Administración de equipos**, expanda **Usuarios y grupos locales** y, a continuación, seleccione **Grupos**.

1. Haga doble clic en el grupo **Administradores**.

1. En el cuadro de diálogo **Propiedades de administradores**, haga clic en el botón **Agregar**.

1. Especifique el usuario **CORP\\Install** y haga clic en **Aceptar**. Cuando se le pidan las credenciales, use la cuenta **DomainAdmin** con la contraseña **Contoso!000**.

1. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de administradores**.

1. Repita los pasos anteriores para **sqlserver-1** y **cluster-fsw**.

## Creación de clústeres

### Agregue la característica **Clústeres de conmutación por error** a cada máquina virtual de clúster.

1. RDP a **sqlserver-0**.

1. En el panel **Administrador del servidor** haga clic en **Agregar roles y características**.

1. En el **Asistente para agregar roles y características**, haga clic en **Siguiente** hasta llegar a la página **Características**.

1. Seleccione **Clústeres de conmutación por error**. Cuando se le pida, agregue todas las características dependientes.

	![Agregar la característica Clústeres de conmutación por error a máquina virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784631.png)

1. Haga clic en **Siguiente** y después en **Instalar** en la página **Confirmación**.

1. Cuando la instalación de la característica **Clústeres de conmutación por error** esté completada, haga clic en **Cerrar**.

1. Cierre sesión en la máquina virtual.

1. Repita los pasos de esta sección para **sqlserver-1** y **cluster-fsw**.

Las máquinas virtuales de SQL Server están aprovisionadas y en ejecución, pero se instalan con SQL Server con las opciones predeterminadas.

### Creación del clúster WSFC

En esta sección, cree el clúster de WSFC que hospedará el grupo de disponibilidad que creará más adelante. Por ahora, debería haber hecho lo siguiente para cada una de las tres máquinas virtuales que se usarán en el clúster de WSFC:

- Están totalmente aprovisionadas en Azure

- La máquina virtual está unida al dominio

- Se agregó **CORP\\Install** al grupo de administradores local.

- Se agregó la característica Clústeres de conmutación por error.

Todos estos son los requisitos previos en cada máquina virtual para que pueda unirse al clúster de WSFC.

Además, tenga en cuenta que la red virtual de Azure no se comporta de la misma manera que una red local. Tendrá que crear el clúster en el orden siguiente:

1. Cree un clúster de nodo único en uno de los nodos (**sqlserver-0**).

1. Cambie la dirección IP del clúster a una dirección IP sin usar de **sqlsubnet**.

1. Ponga el nombre de clúster en línea.

1. Agregue los demás nodos (**sqlserver-1** y **cluster-fsw**).

Siga los pasos a continuación para realizar estas tareas y configurar totalmente el clúster.

1. Inicie el archivo RDP de **sqlserver-0** e inicie sesión con la cuenta de dominio **CORP\\Install**.

1. En el panel **Administrador del servidor**, seleccione **Herramientas** y, a continuación, haga clic en **Administrador de clústeres de conmutación por error**.

1. En el panel izquierdo, haga clic con el botón derecho en **Administrador de clústeres de conmutación por error** y, después, haga clic en **Crear un clúster**.

	![Crear clúster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784632.png)

1. En el Asistente para crear clúster, cree un clúster de un solo nodo avanzando por las páginas con la siguiente configuración:

|Page|Settings|
|---|---|
|Antes de empezar|Usar predeterminados|
|Seleccionar servidores|Escriba **sqlserver-0** en **Introduzca un nombre de servidor** y haga clic en **Agregar**|
|Advertencia de validación|Seleccione **No. No necesito compatibilidad con Microsoft para este clúster y por tanto no deseo ejecutar las pruebas de validación. Al hacer clic en Siguiente, continuar con la creación del clúster**.|
|Punto de acceso para administrar el clúster|Escriba **Cluster1** en **Nombre de clúster**|
|Confirmación|Use los valores predeterminados a menos que use Espacios de almacenamiento. Consulte la nota que sigue a esta tabla.|

>[AZURE.NOTE] Si está usando [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739), que agrupa varios discos en grupos de almacenamiento, tiene que desactivar la casilla **Agregar todo el almacenamiento apto al clúster** en la página **Confirmación**. Si no desactiva esta opción, los discos virtuales se desasociarán durante el proceso de agrupación en clústeres. Como resultado, tampoco aparecerán en el Administrador de discos o el explorador hasta que se quiten los espacios de almacenamiento del clúster y se vuelvan a asociar mediante PowerShell.

Ahora que ha creado el clúster, compruebe la configuración y agregue los nodos restantes.

1. En el panel central, desplácese hacia abajo hasta la sección **Recursos principales de clúster** y expanda los detalles de **Nombre: Cluster1**. Debería de ver los recursos **Nombre** y **Dirección IP** en el estado **Con error**. El recurso de dirección IP no se puede poner en línea porque al clúster se le asigna la misma dirección IP que la de la propia máquina, que es una dirección duplicada.

1. Haga clic con el botón secundario en el recurso **dirección IP** erróneo y, a continuación, haga clic en **Propiedades**.

	![Propiedades de clúster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784633.png)

1. Seleccione **Dirección IP estática** y especifique una dirección de subnet-2 disponible en el cuadro de texto de Dirección. A continuación, haga clic en **Aceptar**.

1. En la sección **Recursos principales de clúster**, haga clic con el botón derecho en **Nombre: Cluster1** y haga clic en **Poner en línea**. Después espere hasta que ambos recursos estén en línea. Cuando el recurso de nombre de clúster está en línea, actualiza el servidor DC con una nueva cuenta de equipo de AD. Esta cuenta de AD se usará para ejecutar más tarde el servicio de clúster del grupo de disponibilidad.

1. Finalmente, agregue los nodos restantes al clúster. En el árbol del explorador, haga clic con el botón derecho en **Cluster1.corp.contoso.com** y haga clic en **Agregar nodo**, tal como se muestra a continuación.

	![Agregar nodo al clúster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784634.png)

1. En el **Asistente para agregar nodo**, haga clic en **Siguiente**. En la página **Seleccionar servidores**, agregue **sqlserver-1** y **cluster-fsw** a la lista; para ello, escriba el nombre del servidor en **Introduzca un nombre de servidor** y haga clic en **Agregar**. Cuando haya terminado, haga clic en **Siguiente**.

1. En la página **Advertencia de validación**, haga clic en **No** (en un escenario de producción debe realizar las pruebas de validación). A continuación, haga clic en **Siguiente**.

1. En la página **Confirmación**, haga clic en **Siguiente** para agregar los nodos.

	>[AZURE.WARNING] Si está usando [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739), que agrupa varios discos en grupos de almacenamiento, tiene que desactivar la casilla **Agregar todo el almacenamiento apto al clúster**. Si no desactiva esta opción, los discos virtuales se desasociarán durante el proceso de agrupación en clústeres. Como resultado, tampoco aparecerán en el Administrador de discos o el explorador hasta que se quiten los espacios de almacenamiento del clúster y se vuelvan a asociar mediante PowerShell.

1. Una vez que se agregan los nodos al clúster, haga clic en **Finalizar**. Ahora el Administrador de clústeres de conmutación por error tiene que indicar que el clúster tiene tres nodos y mostrarlos en una lista en el contenedor **Nodos**.

1. Cierre sesión en la sesión de escritorio remoto.

## Configuración de grupos de disponibilidad

En esta sección hará lo siguiente, tanto en **sqlserver-0** como en **sqlserver-1**:

- Agregar **CORP\\Install** como un rol sysadmin a la instancia de SQL Server predeterminada

- Abrir el firewall para el acceso remoto a SQL Server para el proceso de SQL Server y el puerto de sondeo

- Habilitar la característica de grupos de disponibilidad

- Cambiar la cuenta de servicio de SQL Server a **CORP\\SQLSvc1** y **CORP\\SQLSvc2**, respectivamente

Estas acciones pueden realizarse en cualquier orden. No obstante, los pasos siguientes se seguirán en orden. Siga los pasos para **sqlserver-0** y **sqlserver-1**:

### Incorporación de la cuenta de instalación como rol de servidor fijo sysadmin a cada servidor de SQL Server

1. Si no ha cerrado la sesión de escritorio remoto para la máquina virtual, hágalo ahora.

1. Inicie los archivos RDP para **sqlserver-0** y **sqlserver-1** e inicie sesión como **BUILTIN\\DomainAdmin**.

1. Inicie **SQL Server Management Studio** y agregue **CORP\\Install** como rol **sysadmin** a la instancia de SQL Server predeterminada. En el **Explorador de objetos**, haga clic con el botón derecho en **Inicios de sesión** y en **Nuevo inicio de sesión**.

1. Escriba **CORP\\Install** en **Nombre de inicio de sesión**.

1. En la página **Roles de servidor**, seleccione **sysadmin**. A continuación, haga clic en **Aceptar**. Una vez creado el inicio de sesión, puede verlo expandiendo **Inicios de sesión** en **Explorador de objetos**.

### Apertura del firewall para el acceso remoto a SQL Server y el puerto de sondeo en cada servidor SQL Server

Esta solución requiere dos reglas de firewall en cada servidor SQL Server. La primera regla proporciona acceso entrante a SQL Server, la segunda proporciona acceso entrante para el equilibrador de carga y el agente de escucha.

1. Después cree una regla de firewall para SQL Server. Desde la pantalla **Inicio**, abra **Firewall de Windows con seguridad avanzada**.

1. En el panel izquierdo, seleccione **Reglas de entrada**. En el panel derecho, haga clic en **Nueva regla**.

1. En la página **Tipo de regla**, seleccione **Programa** y, a continuación, haga clic en **Siguiente**.

1. En la página **Programa**, seleccione **Esta ruta de acceso del programa** y escriba **%ProgramFiles%\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe** en el cuadro de texto (si sigue estas instrucciones, pero usa SQL Server 2012, el directorio de SQL Server es **MSSQL11. MSSQLSERVER**). A continuación, haga clic en **Siguiente**.

1. En la página **Acción**, mantenga seleccionado **Permitir la conexión** y haga clic en **Siguiente**.

1. En la página **Perfiles**, acepte la configuración predeterminada y haga clic en **Siguiente**.

1. En la página **Nombre**, especifique el nombre de una regla, como **SQL Server (regla de programa)** en el cuadro de texto **Nombre** y haga clic en **Finalizar**.

1. Cree una regla de firewall de entrada adicional para el puerto de sondeo. Esta regla es una regla de entrada para el puerto TCP 59999, para los fines de este tutorial. Asigne un nombre a la regla **Agente de escucha de SQL Server**.

Complete todos los pasos en ambos servidores SQL Server.

### Habilitación de la característica de grupos de disponibilidad en cada instancia de SQL Server

Siga estos pasos para ambos servidores SQL Server.

1. Después, habilite la característica **Grupos de disponibilidad AlwaysOn**. Desde la pantalla **Inicio**, abra el **Administrador de configuración de SQL Server**.

1. En el árbol del explorador, haga clic en **Servicios SQL Server**, luego haga clic con el botón derecho en el servicio **SQL Server (MSSQLSERVER)** y, a continuación, haga clic en **Propiedades**.

1. Haga clic en la pestaña **Alta disponibilidad de AlwaysOn** y seleccione **Habilitar los Grupos de disponibilidad AlwaysOn**, tal como se muestra a continuación, y haga clic en **Aplicar**. Haga clic en **Aceptar** en el cuadro de diálogo emergente y no cierre la ventana de propiedades todavía. Reiniciará el servicio SQL Server tras cambiar la cuenta de servicio.

	![Habilitación de grupos de disponibilidad AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665520.gif)

### Definición de la cuenta de servicio de SQL Server en cada servidor SQL Server

Siga estos pasos para ambos servidores SQL Server.

1. A continuación, cambie la cuenta del servicio de SQL Server. Haga clic en la pestaña **Iniciar sesión** y escriba **CORP\\SQLSvc1** (para **sqlserver-0**) o **CORP\\SQLSvc2** (para **sqlserver-1**) en **Nombre de cuenta**; escriba y confirme la contraseña y, finalmente, haga clic en **Aceptar**.

1. En la ventana emergente, haga clic en **Sí** para reiniciar el servicio de SQL Server. Después de reiniciar el servicio SQL Server, entran en vigor los cambios que realizó en la ventana Propiedades.

1. Cierre sesión en las máquinas virtuales.

### Creación del grupo de disponibilidad

Ahora está en disposición de configurar un grupo de disponibilidad. A continuación se resume lo que debe hacer:

- Crear una nueva base de datos (**MyDB1**) en **sqlserver-0**

- Realizar una copia de seguridad completa y una copia de seguridad del registro de transacciones de la base de datos

- Restaurar las copias de seguridad completas y de registro en **sqlserver-1** con la opción **NORECOVERY**

- Crear el grupo de disponibilidad (**AG1**) con confirmación sincrónica, conmutación automática por error y réplicas secundarias legibles

### Creación de la base de datos de MyDB1 en sqlserver-0:

1. Si no cerró aún las sesiones de escritorio remoto para **sqlserver-0** y **sqlserver-1**, hágalo ahora.

1. Inicie el archivo RDP para **sqlserver-0** e inicie sesión como **CORP\\Install**.

1. En el **Explorador de archivos**, en **C:**, cree un directorio denominado **copia de seguridad**. Usará este directorio para la copia de seguridad y restauración de la base de datos.

1. Haga clic con el botón derecho en el nuevo directorio, seleccione **Compartir con**, y, a continuación, haga clic en **Personas específicas**, tal como se muestra a continuación.

	![Creación de una carpeta de copia de seguridad](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665521.gif)

1. Agregue **CORP\\SQLSvc1** y asígnele el permiso **Lectura/escritura**; a continuación, agregue **CORP\\SQLSvc2** y asígnele el permiso **Lectura/escritura**, tal como se muestra a continuación y, finalmente, haga clic en **Compartir**. Una vez que se complete el proceso de uso compartido de archivos, haga clic en **Listo**.

	![Conceder permisos para la carpeta de copia de seguridad](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665522.gif)

1. A continuación, cree la base de datos. Desde el menú **Inicio**, abra **SQL Server Management Studio** y haga clic en **Conectar** para conectar con la instancia predeterminada de SQL Server.

1. En el **Explorador de objetos**, haga clic con el botón derecho en **Bases de datos** y luego en **Nueva base de datos**.

1. En **Nombre de base de datos**, escriba **MyDB1** y, a continuación, haga clic en **Aceptar**.

### Copia de seguridad completa de MyDB1 y restauración en sqlserver-1:

1. A continuación, haga una copia de seguridad completa de la base de datos. En el **Explorador de objetos**, expanda **Bases de datos**, haga clic con el botón derecho en **MyDB1**, seleccione **Tareas** y, finalmente, haga clic en **Copia de seguridad**.

1. En la sección **Origen**, mantenga el **Tipo de copia de seguridad** establecido en **Completa**. En la sección **Destino**, haga clic en **Quitar** para quitar la ruta de acceso del archivo predeterminado para el archivo de copia de seguridad.

1. En la sección **Destino**, haga clic en **Agregar**.

1. En el cuadro de texto **Nombre de archivo**, escriba **\\\sqlserver-0\\backup\\MyDB1.bak**. Seguidamente, haga clic en **Aceptar** y, finalmente, haga clic en **Aceptar** de nuevo para realizar una copia de seguridad de la base de datos. Cuando finalice la operación de copia de seguridad, vuelva a hacer clic en **Aceptar** para cerrar el cuadro de diálogo.

1. A continuación, haga una copia de seguridad del registro de transacciones de la base de datos. En el **Explorador de objetos**, expanda **Bases de datos**, haga clic con el botón derecho en **MyDB1**, seleccione **Tareas** y, finalmente, haga clic en **Copia de seguridad**.

1. En **Tipo de copia de seguridad**, seleccione **Registro de transacciones**. Mantenga la ruta de acceso al archivo de **Destino** establecida en la que especificó anteriormente y haga clic en **Aceptar**. Una vez se complete la operación de copia de seguridad, haga clic de nuevo en **Aceptar**.

1. A continuación, restaure las copias de seguridad completas y de registros de transacción en **sqlserver-1**. Inicie el archivo RDP para **sqlserver-1** e inicie sesión como **CORP\\Install**. Deje abierta la sesión de escritorio remoto para **sqlserver-0**.

1. Desde el menú **Inicio**, abra **SQL Server Management Studio** y haga clic en **Conectar** para conectar con la instancia predeterminada de SQL Server.

1. En el **Explorador de objetos**, haga clic con el botón derecho en **Bases de datos** y luego en **Restaurar base de datos**.

1. En la sección **Origen**, seleccione **Dispositivo** y haga clic en el botón **...**.

1. En **Seleccionar dispositivos de copia de seguridad**, haga clic en **Agregar**.

1. En la ubicación del archivo de copia de seguridad, escriba **\\\sqlserver-0\\backup**, haga clic en Actualizar, seleccione MyDB1.bak y haga clic en Aceptar; luego, haga clic de nuevo en Aceptar. Ahora debería ver la copia de seguridad completa y la copia de seguridad del registro en el panel Conjuntos de copia de seguridad para restaurar.

1. Vaya a la página Opciones, seleccione RESTAURAR CON NORECOVERY en Estado de recuperación y, a continuación, haga clic en Aceptar para restaurar la base de datos. Una vez se complete la operación de copia de seguridad, haga clic en Aceptar.

### Cree el conjunto de disponibilidad:

1. Vuelva a la sesión de escritorio remoto para **sqlserver-0**. En el **Explorador de objetos** de SSMS, haga clic en **Alta disponibilidad AlwaysOn** y en **Asistente para nuevo grupo de disponibilidad**, tal como se muestra a continuación.

	![Iniciar el Asistente para nuevo grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665523.gif)

1. En la página **Introducción**, haga clic en **Siguiente**. En la página **Especificar nombre de grupo de disponibilidad**, escriba **AG1** en **Nombre del grupo de disponibilidad** y, a continuación, vuelva a hacer clic en **Siguiente**.

	![Asistente para nuevo grupo de disponibilidad, especificar el nombre del grupo](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665524.gif)

1. En la página **Seleccionar bases de datos**, seleccione **MyDB1** y haga clic en **Siguiente**. La base de datos cumple los requisitos previos para un grupo de disponibilidad porque ha realizado al menos una copia de seguridad completa en la réplica principal pretendida.

	![Asistente para nuevo grupo de disponibilidad, seleccionar bases de datos](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665525.gif)

1. En la página **Especificar réplicas**, haga clic en **Agregar réplica**.

	![Asistente para nuevo grupo de disponibilidad, especificar réplicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665526.png)

1. Se abre el cuadro de diálogo **Conectar con el servidor**. Escriba **sqlserver-1** en **Nombre del servidor** y, después, haga clic en **Conectar**.

	![Asistente para nuevo de AG, conectar a servidor](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665527.png)

1. De vuelta en la página **Especificar réplicas**, ahora debería ver **sqlserver-1** en la lista **Réplicas de disponibilidad**. Configure las réplicas como se muestra a continuación. Cuando haya terminado, haga clic en **Siguiente**.

	![Asistente para nuevo grupo de disponibilidad, especificar réplicas (completo)](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665528.png)

1. En la página **Seleccionar sincronización de datos iniciales**, seleccione **Solo unión** y después haga clic en **Siguiente**. Ya realizó la sincronización de datos de forma manual cuando efectuó las copias de seguridad completas y de transacciones en **sqlserver-0** y las restauró en **sqlserver-1**. En lugar de esto, puede elegir no realizar las operaciones de copia de seguridad y restauración en la base de datos y seleccionar **Total** para permitir que el Asistente del nuevo grupo de disponibilidad realice la sincronización de datos en su lugar. Sin embargo, esto no se recomienda para bases de datos de gran tamaño como las que se encuentran en algunas empresas.

	![Asistente para nuevo grupo de disponibilidad, seleccionar sincronización de datos iniciales](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665529.png)

1. En la página **Validación**, haga clic en **Siguiente**. Esta página debe ser similar a la siguiente. Hay una advertencia para la configuración del agente de escucha porque no ha configurado un agente de escucha de grupo de disponibilidad. Puede omitir esta advertencia, ya que este tutorial no configura un agente de escucha. Este tutorial le enseñará a crear el agente de escucha más adelante. Si quiere más datos sobre cómo configurar un agente de escucha, consulte [Configure an internal load balancer for an AlwaysOn availability group in Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md) (Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn de Azure).

	![Asistente para nuevo grupo de disponibilidad, validación](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665530.gif)

1. En la página **Resumen**, haga clic en **Finalizar** y espere hasta que el asistente configure el nuevo grupo de disponibilidad. En la página **Progreso**, puede hacer clic en **Más detalles** para ver el progreso detallado. Cuando el asistente termine, inspeccione la página **Resultados** para comprobar que se creó correctamente el grupo de disponibilidad y, a continuación, haga clic en **Cerrar** para salir del asistente.

	![Asistente para nuevo grupo de disponibilidad, resultados](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665531.png)

1. En el **Explorador de objetos**, expanda **Alta disponibilidad AlwaysOn** y después, expanda **Grupos de disponibilidad**. Ahora debería de ver el nuevo grupo de disponibilidad en este contenedor. Haga clic con el botón derecho en **AG1 (principal)** y luego en **Mostrar panel**.

	![Mostrar panel de grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665532.png)

1. El **panel AlwaysOn** debería ser similar al que se muestra a continuación. Puede ver las réplicas, el modo de conmutación por error de cada réplica y el estado de sincronización.

	![Panel de grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665533.png)

1. Vuelva a **Administrador del servidor**, seleccione **Herramientas** y, después, inicie **Administrador de clústeres de conmutación por error**.

1. Expanda **Cluster1.corp.contoso.com** y expanda **Servicios y aplicaciones**. Elija **Roles** y tenga en cuenta que se ha creado el rol del grupo de disponibilidad **AG1**. Tenga en cuenta que AG1 no tiene ninguna dirección IP por la que los clientes de la base de datos puedan conectarse al grupo de disponibilidad, porque no configuró ningún agente de escucha. Puede conectarse directamente al nodo principal para las operaciones de lectura y escritura y al nodo secundario para las consultas de sólo lectura.

	![Grupo de disponibilidad en el administrador de clústeres de conmutación por error.](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665534.png)

>[AZURE.WARNING] No trate de realizar una conmutación por error del grupo de disponibilidad desde el Administrador de clústeres de conmutación por error. Todas las operaciones de conmutación por error deben realizarse desde el **Panel AlwaysOn** de SSMS. Para obtener más información, consulte [Restricciones en el uso del Administrador de clústeres de conmutación por error de WSFC con Grupos de disponibilidad](https://msdn.microsoft.com/library/ff929171.aspx).

## Configuración del equilibrador de carga interno

Para conectarse directamente al grupo de disponibilidad, debe configurar un equilibrador de carga interno en Azure y, a continuación, crear el agente de escucha en el clúster. Esta sección proporciona una introducción de alto nivel de esos pasos. Si quiere instrucciones detalladas, consulte [Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn de Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).

### Creación del equilibrador de carga en Azure

1. En el Portal de Azure, vaya a **SQL-HA-RG** y haga clic en **+ Agregar**.

1. Busque **Equilibrador de carga**. Elija el equilibrador de carga publicado por Microsoft y haga clic en **Crear**.

1. Configure los parámetros siguientes para el equilibrador de carga.

| Configuración | Campo |
| --- | ---
| **Name** | sqlLB
| **Esquema** | Interno
| **Red virtual** | autoHAVNET
| **Subred** | subnet-2. Se trata de la dirección IP que va a establecer para el agente de escucha en el recurso de clúster.  
| **Asignación de dirección IP** | Estática
| **Dirección IP** | Use una dirección de subnet-2 disponible.
| **Suscripción** | Use la misma suscripción que los demás recursos de esta solución.
| **Ubicación** | Use la misma ubicación que los demás recursos de esta solución.

Haga clic en **Crear**.

Configure el equilibrador de carga como sigue:

| Configuración | Campo |
| --- | ---|
| Nombre de **Grupo de back-end** | sqlLBBE 
| **SQLLBBE Availability set** (Conjunto de disponibilidad SQLLBBE) | sqlAvailabilitySet
| **SQLLBBE máquinas virtuales** | sqlserver-0, sqlserver-1
| **SQLLBBE Used by** (SQLLBBE usado por) | SQLAlwaysOnEndPointListener
| Nombre de **Sondeo** | SQLAlwaysOnEndPointProbe
| **Protocolo de sondeo** | TCP
| **Puerto de sondeo** | 59999\. Puede usar cualquier puerto que no esté en uso.
| **Intervalo de sondeo** | 5
| **Probe Unhealthy threshold** (Umbral de sondeo incorrecto) | 2
| **Probe Used by** (Sondeo usado por) | SQLAlwaysOnEndPointListener
| Nombre de **Reglas de equilibrio de carga** | SQLAlwaysOnEndPointListener
| **Load balancing rules Protocol** (Protocolo de reglas de equilibrio de carga) | TCP
| **Load balancing rules Port** (Puerto de reglas de equilibrio de carga) | 1433\. Esto es así porque este es el puerto predeterminado de SQL Server.
| **Load balancing rules Port** (Puerto de reglas de equilibrio de carga) | 1433\. Esto es así porque este es el puerto predeterminado de SQL Server.
| **Load balancing rules Backend Port** (Puerto de back-end de reglas de equilibrio de carga) | 1433
| **Load balancing rules Probe** (Sondeo de reglas de equilibrio de carga) | SQLAlwaysOnEndPointProbe
| **Load balancing rules Session Persistence** (Persistencia de la sesión de reglas de equilibrio de carga) | None
| **Load balancing rules Idle Timeout** (Tiempo de espera de inactividad de reglas de equilibrio de carga) | 4
| **Load balancing rules Floating IP (direct server return)** (IP flotante de reglas de equilibrio de carga [Direct Server Return]) | Enabled

>[AZURE.NOTE] Debe habilitar Direct Server Return en las reglas de equilibrio de carga al crearlas.

Una vez configurado el equilibrador de carga, configure el agente de escucha en el clúster de conmutación por error.

### Configuración del equilibrador de carga en el clúster de conmutación por error

El siguiente paso es configurar un agente de escucha del grupo de disponibilidad AlwaysOn en el clúster de conmutación por error.

1. De RDP a SQL Server de ad-principal-dc a sqlserver-0.

1. En el Administrador de clústeres de conmutación por error, anote el nombre de la red de clústeres. Para determinar el nombre de red del clúster en **Administrador de clústeres de conmutación por error**, en el panel izquierdo, haga clic en **Redes**. Este es el nombre que va a usar en la variable `$ClusterNetworkName` del script de PowerShell.

1. En el Administrador de clústeres de conmutación por error, expanda el nombre del clúster y haga clic en **Roles**.

1. En **Roles**, haga clic con el botón derecho en el nombre del grupo de disponibilidad y después seleccione **Agregar recurso** > **Punto de acceso cliente**.

1. Para **Nombre**, escriba **aglistener**. Haga clic en **Siguiente** dos veces y, a continuación, en **Finalizar**. No pongas el agente de escucha o el recurso en línea en este momento.

1. Haz clic en la pestaña **Recursos** y después amplía el punto de acceso de cliente que acabas de crear. Haga clic con el botón derecho en el recurso de IP y, después, en Propiedades. Anote el nombre de la dirección IP. Este es el nombre que va a utilizar en la variable `$IPResourceName` del script de PowerShell.

1. En **Dirección IP**, haga clic en **Dirección IP estática** y especifique la misma dirección que usó en el Portal de Azure para el equilibrador de carga **sqlLB**. También usará esta misma dirección IP en la variable `$ILBIP` del script de Powershell. Habilite NetBIOS para esta dirección y haga clic en Aceptar.

1. En el nodo de clúster donde actualmente se hospeda la réplica principal, abra una instancia de PowerShell ISE con privilegios elevados y pegue los siguientes comandos en un nuevo script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = "<X.X.X.X>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    
1. Actualice las variables y ejecute el script de PowerShell para configurar la dirección IP y el puerto del nuevo agente de escucha.

1. En **Administrador de clústeres de conmutación por error**, haga clic con el botón derecho en el recurso de grupo de disponibilidad y haga clic en **Propiedades**. En la pestaña **Dependencias**, configure el grupo de recursos para que dependa del nombre de la red del agente de escucha.

1. Establezca la propiedad del puerto de escucha en 1433. Para ello, abra SQL Server Management Studio, haga clic con el botón derecho en el agente de escucha del grupo de disponibilidad y seleccione Propiedades. Establezca el **Puerto** en 1433.

1. En este momento puede [conectar el agente de escucha](virtual-machines-windows-portal-sql-alwayson-int-listener.md#2-bring-the-listener-online).

### Comprobación de la conexión con el agente de escucha

Para probar la conexión:

1. De RDP al servidor SQL Server que no es propietario de la réplica.

1. Compruebe la conexión con la utilidad sqlcmd. Por ejemplo, el siguiente script establece una conexión sqlcmd con la réplica principal a través del agente de escucha mediante la autenticación de Windows:

        sqlcmd -S "<listenerName>" -E



## Pasos siguientes

Para obtener más información sobre el uso de SQL Server en Azure, consulte [SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0622_2016-->
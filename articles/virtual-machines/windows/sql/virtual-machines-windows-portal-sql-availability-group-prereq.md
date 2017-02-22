---
title: Grupos de disponibilidad de SQL Server - Azure Virtual Machines - Requisitos previos | Microsoft Docs
description: "Este tutorial muestra cómo configurar los requisitos previos para crear un grupo de disponibilidad de SQL Server AlwaysOn en máquinas virtuales de Azure."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/10/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 4326cc342088ff16a72b8c460245bda1f2cd17c9
ms.openlocfilehash: 3e0c58af3566ea443efaa012495e5b736fafb46d


---

# <a name="complete-prerequisites-for-creating-always-on-availability-groups-in-azure-virtual-machines"></a>Finalización de requisitos previos para crear grupos de disponibilidad AlwaysOn en máquinas virtuales de Azure

Este tutorial muestra cómo completar los requisitos previos para crear un[ grupo de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Cuando complete los requisitos previos, tendrá un controlador de dominio, dos servidores SQL Server y un servidor testigo en un único grupo de recursos.

**Tiempo estimado**: puede tardar un par de horas en completar los requisitos previos. Gran parte de este tiempo se invierte en crear máquinas virtuales.

El diagrama muestra lo que va a crear en el tutorial.

![Grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Revisión de la documentación del grupo de disponibilidad

En el tutorial se da por supuesto que tiene conocimientos básicos de grupos de disponibilidad de SQL Server AlwaysOn. Si no está familiarizado con esta tecnología, lea la [introducción a los grupos de disponibilidad AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Crear una cuenta de Azure
* Necesitará una cuenta de Azure. Puede [abrir una cuenta gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

## <a name="create-resource-group"></a>Creación de un grupo de recursos
1. Inicie sesión en el [Portal de Azure](http://portal.azure.com). 
2. Haga clic en **+** para crear un nuevo objeto en el portal.

   ![Nuevo](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

1. Haga clic en **grupo de recursos** en la ventana de búsqueda de **Marketplace**.
   
   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
1. Haga clic en **Grupo de recursos**.
4. Haga clic en **Crear**. 
5. En la hoja **Grupo de recursos**, en **Nombre del grupo de recursos** escriba un nombre para el grupo de recursos. Por ejemplo, escriba **sql-ha-rg**.
6. Si tiene varias suscripciones de Azure, compruebe que la suscripción es la suscripción de Azure en la que quiere crear el grupo de disponibilidad. 
7. Seleccione una ubicación. La ubicación es la región de Azure donde desea crear el grupo de disponibilidad. Para este tutorial, vamos a generar todos los recursos en una ubicación de Azure. 
8. Compruebe que la función **Anclar al panel** está activada. Este parámetro opcional coloca un acceso directo para el grupo de recursos en el panel del Portal de Azure.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

9. Haga clic en **Crear** para crear el grupo de recursos.

Azure crea el grupo de recursos y ancla un acceso directo a él en el portal.

## <a name="create-network-and-subnets"></a>Creación de redes y subredes
El siguiente paso es crear las redes y subredes en el grupo de recursos de Azure.

La solución usa una red virtual con dos subredes. En [Información general sobre redes virtuales](../../../virtual-network/virtual-networks-overview.md) encontrará más información sobre las redes de Azure.

Para crear la red virtual:

1. En Azure Portal, en el grupo de recursos, haga clic en **+ Agregar**. Azure abre la hoja **Todo** . 
   
   ![Nuevo elemento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Busque **red virtual**.
   
     ![Búsqueda de una red virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Haga clic en **Red virtual**.
4. En la hoja **Red virtual**, haga clic en el modelo de implementación de **Resource Manager** y en **Crear**.
   
   La siguiente tabla muestra la configuración de la red virtual:
   
   | **Campo** | Valor |
   | --- | --- |
   | **Name** |autoHAVNET |
   | **Espacio de direcciones** |10.33.0.0/24 |
   | **Nombre de subred** |Administrador |
   | **Intervalo de direcciones de subred** |10.33.0.0/29 |
   | **Suscripción** |Especifique la suscripción que quiere usar. El campo **Suscripción** está en blanco si solo tiene una suscripción. |
   | **Ubicación** |Especifique la ubicación de Azure. |
   
   El intervalo de direcciones de subred y el espacio de direcciones pueden ser distintos a los de la tabla. Según su suscripción, el portal recomienda un espacio de direcciones disponible y el intervalo de direcciones de subred correspondiente. Si no hay suficiente espacio de direcciones disponible, use otra suscripción. 

   En el ejemplo se usa el nombre de subred **Admin**. Esta subred es para los controladores de dominio. 

6. Haga clic en **Crear**.
   
   ![Configuración de una red virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure le devuelve al panel del portal y le informa cuando se cree la red.

### <a name="create-a-second-subnet"></a>Cree una segunda subred.
La nueva red virtual tiene una subred denominada **Admin**. Los controladores de dominio usan esta subred. Los servidores SQL Server usan una segunda subred denominada **SQL**. Para configurar esta subred, siga estos pasos:

1. En el panel, haga clic en el grupo de recursos que ha creado, **SQL-HA-RG**. En **Recursos**, busque la red en el grupo de recursos.
   
    Si **SQL-HA-RG** no está visible, haga clic en **Grupos de recursos** y filtre por el nombre del grupo de recursos para encontrarlo.
2. Haga clic en **autoHAVNET** en la lista de recursos. Azure abre la hoja de configuración de red.
3. En la red virtual **autoHAVNET**, haga clic en **All settings** (Toda la configuración).
4. En la hoja **Configuración**, haga clic en **Subredes**.
   
    Observe la subred que ya ha creado. 
   
   ![Configuración de una red virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Cree una segunda subred. Haga clic en **+ Subred**. 
6. En la hoja **Agregar subred**, configure la subred escribiendo **sqlsubnet** en **Nombre**. Azure especifica automáticamente un valor válido en **Intervalo de direcciones**. Compruebe que este intervalo de direcciones tiene al menos 10 direcciones. En un entorno de producción, puede que necesite más direcciones. 
7. Haga clic en **Aceptar**.
   
    ![Configuración de una red virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

En la tabla siguiente se resumen las opciones de configuración de red:

| **Campo** | Valor |
| --- | --- |
| **Name** |**autoHAVNET** |
| **Espacio de direcciones** |Depende de los espacios de direcciones disponibles en su suscripción. Un valor típico es 10.0.0.0/16 |
| **Nombre de subred** |**admin** |
| **Intervalo de direcciones de subred** |Depende de los intervalos de direcciones disponibles en su suscripción. Un valor típico es 10.0.0.0/24. |
| **Nombre de subred** |**sqlsubnet** |
| **Intervalo de direcciones de subred** |Depende de los intervalos de direcciones disponibles en su suscripción. Un valor típico es 10.0.1.0/24. |
| **Suscripción** |Especifique la suscripción que quiere usar. |
| **Grupo de recursos** |**SQL-HA-RG** |
| **Ubicación** |Especifique la misma ubicación que eligió para el grupo de recursos. |

## <a name="create-availability-sets"></a>Creación de conjuntos de disponibilidad

Antes de crear máquinas virtuales, debe crear conjuntos de disponibilidad. Los conjuntos de disponibilidad reducen el tiempo de inactividad para el mantenimiento, tanto programado como imprevisto. Un conjunto de disponibilidad de Azure es un grupo lógico de recursos que Azure coloca en dominios de error y de actualización físicos. Un dominio de error garantiza que los miembros del conjunto de disponibilidad tengan recursos de energía y red independientes. Un dominio de actualización garantiza que los miembros del conjunto de disponibilidad no pasen a inactividad por mantenimiento al mismo tiempo. [Administración de la disponibilidad de las máquinas virtuales](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Necesita dos conjuntos de disponibilidad. El primero es para los controladores de dominio y el segundo, para los servidores SQL Server.

Para crear un conjunto de disponibilidad, vaya al grupo de recursos y haga clic en **Agregar**. Filtre los resultados escribiendo **Conjunto de disponibilidad**. Haga clic en la opción **Conjunto de disponibilidad** de los resultados. Haga clic en **Crear**.

Configure dos conjuntos de disponibilidad según los parámetros de la tabla siguiente:

| **Campo** | Conjunto de disponibilidad del controlador de dominio | Conjunto de disponibilidad del servidor SQL Server |
| --- | --- | --- |
| **Name** |adavailabilityset |sqlavailabilityset |
| **Grupos de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Dominios de error** |3 |3 |
| **Dominios de actualización** |5 |3 |

Después de crear los conjuntos de disponibilidad, vuelva al grupo de recursos en el Portal de Azure.

## <a name="create-domain-controllers"></a>Creación de controladores de dominio
Ha creado la red, las subredes, los conjuntos de disponibilidad y un equilibrador de carga accesible desde Internet. Está listo para crear las máquinas virtuales para los controladores de dominio.

### <a name="create-the-virtual-machines-for-the-domain-controllers"></a>Creación de las máquinas virtuales para los controladores de dominio
Para crear y configurar los controladores de dominio, vuelva al grupo de recursos **SQL-HA-RG** .

1. Haga clic en Agregar. Se abrirá la hoja **Todo** .
2. Escriba **Windows Server 2016 Datacenter**. 
3. Haga clic en **Windows Server 2016 Datacenter**. En la hoja **Windows Server 2016 Datacenter**, compruebe que el modelo de implementación sea **Resource Manager** y haga clic en **Crear**. Azure abre la hoja **Crear máquina virtual** . 

Repita los pasos anteriores pasos para crear dos máquinas virtuales. Asigne nombre a las dos máquinas virtuales:

* ad-primary-dc
* ad-secondary-dc
  
  > [!NOTE]
  > **ad-secondary-dc** es un componente opcional cuyo fin es proporcionar alta disponibilidad para Servicios de dominio de Active Directory. 
  > 
  > 

La siguiente tabla muestra la configuración de estas dos máquinas:

| **Campo** | Valor |
| --- | --- |
| **Tipo de disco de máquina virtual** |SSD |
| **Nombre de usuario** |DomainAdmin |
| **Password** |Contoso!0000 |
| **Suscripción** |*su suscripción* |
| **Grupos de recursos** |SQL-HA-RG |
| **Ubicación** |*su ubicación* |
| **Tamaño** |DS1_V2 |
| **Cuenta de almacenamiento** |*Se crea automáticamente* |
| **Red virtual** |autoHAVNET |
| **Subred** |admin |
| **Dirección IP pública** |*Mismo nombre que la máquina virtual* |
| **Grupo de seguridad de red (NSG)** |*Mismo nombre que la máquina virtual* |
| **El conjunto de disponibilidad** |adavailabilityset |
| **Diagnóstico** |Enabled |
| **Cuenta de almacenamiento de información de diagnóstico** |*Se crea automáticamente* |


   >[!IMPORTANT]
   >Solo puede colocar una máquina virtual en un conjunto de disponibilidad al crearlo. Una vez creada una máquina virtual, no se puede cambiar el conjunto de disponibilidad. Consulte [Administración de la disponibilidad de las máquinas virtuales](../../virtual-machines-windows-manage-availability.md).

Azure crea las máquinas virtuales.

Después de crear las máquinas virtuales, configure el controlador de dominio.

### <a name="configure-the-domain-controller"></a>Configuración del controlador de dominio
En los pasos siguientes, configurará la máquina **ad-primary-dc** como controlador de dominio para corp.contoso.com.

1. En el portal, abra el grupo de recursos **SQL-HA-RG** y seleccione la máquina **ad-primary-dc**. En la hoja **ad-primary-dc**, haga clic en **Conectar** para abrir un archivo RDP con el fin de obtener acceso a Escritorio remoto.
   
    ![Conectarse a la máquina virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Inicie sesión con su cuenta de administrador configurada (**\DomainAdmin**) y la contraseña (**Contoso!0000**).
3. De forma predeterminada, se debe mostrar el panel **Administrador del servidor** .
4. Haga clic en el vínculo **Agregar roles y características** en el panel.
   
    ![Explorador de servidores Agregar roles](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Seleccione **Siguiente** hasta llegar a la sección **Roles de servidor**.
6. Seleccione los roles **Active Directory Domain Services** y **Servidor DNS**. Cuando se le pida, agregue las características adicionales que requieran estos roles.
   
   > [!NOTE]
   > Windows le advierte de que no hay ninguna dirección IP estática. Si está probando la configuración, haga clic en Continuar En los escenarios de producción, establezca la dirección IP en estática en Azure Portal o [use PowerShell para establecer la dirección IP estática de la máquina del controlador de dominio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Diálogo Agregar roles](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Haga clic en **Siguiente** hasta llegar a la sección **Confirmación**. Active la casilla **Reiniciar automáticamente el servidor de destino en caso necesario** .
8. Haga clic en **Instalar**.
9. Cuando terminen de instalarse las características, vuelva al panel **Administrador del servidor** .
10. Seleccione la nueva opción **AD DS** en el panel izquierdo.
11. Haga clic en el vínculo **Más** en la barra de advertencia amarilla.
    
    ![Cuadro de diálogo AD DS en la máquina virtual del servidor de DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, haga clic en **Promover este servidor a controlador de dominio**.
13. En el **Asistente de configuración de Servicios de dominio de Active Directory**, use los siguientes valores:
    
    | **Page** | Configuración |
    | --- | --- |
    | **Configuración de la implementación** |**Incorporación de nuevos bosques**<br/> **Nombre del dominio raíz** = corp.contoso.com |
    | **Opciones del controlador de dominio** |**Contraseña de DSRM** = Contoso!0000<br/>**Confirmar contraseña** = Contoso!0000 |
14. Haga clic en **Siguiente** para avanzar por las otras páginas del asistente. En la página **Comprobación de requisitos previos**, compruebe que ve el mensaje siguiente: **Todas las comprobaciones de requisitos previos se realizaron correctamente**. Revise todos los mensajes de advertencia aplicables, pero puede continuar con la instalación.
15. Haga clic en **Instalar**. La máquina virtual **ad-primary-dc** se reinicia automáticamente.

### <a name="note-ip-address-of-primary-domain-controller"></a>Anotación de la dirección IP del controlador de dominio principal

Utilice el controlador de dominio principal para DNS. Anote la dirección IP del controlador de dominio principal.

Una manera de obtener la dirección IP del controlador de dominio principal es a través de Azure Portal. 

1. En Azure Portal, abra el grupo de recursos. 

1. Haga clic en el controlador de dominio principal.

1. En la hoja del controlador del dominio principal, haga clic en **Interfaces de red**.

![Nuevo elemento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Anote la dirección IP privada de este servidor. 

### <a name="configure-the-second-domain-controller"></a>Configuración del segundo controlador de dominio
Una vez reiniciado el controlador de dominio principal, puede configurar el segundo controlador de dominio. Este paso opcional es para lograr alta disponibilidad. Para configurar el segundo controlador de dominio siga estos pasos:

1. En el portal, abra el grupo de recursos **SQL-HA-RG** y seleccione la máquina **ad-secondary-dc**. En la hoja **ad-secondary-dc**, haga clic en **Conectar** para abrir un archivo RDP con el fin de obtener acceso a Escritorio remoto.
4. Inicie sesión en la máquina virtual con la cuenta de administrador configurada (**BUILTIN\DomainAdmin**) y la contraseña (**Contoso!0000**).
3. Cambie la dirección del servidor DNS preferido por la dirección del controlador de dominio. 
1. En **Centro de redes y recursos compartidos**, haga clic en la interfaz de red. 
   ![NetworkInterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Haga clic en **Propiedades**.
10. Seleccione **Protocolo de Internet versión 4 (TCP/IPv4)** y haga clic en Propiedades.
11. Seleccione **Usar las siguientes direcciones de servidor DNS** y especifique la dirección del controlador de dominio principal en **Servidor DNS preferido**.
1. Haga clic en **Aceptar** y en **Cerrar** para confirmar los cambios. Ahora ya puede unir la máquina virtual a **corp.contoso.com**.

   >[!IMPORTANT]
   >Si pierde la conexión a Escritorio remoto después de cambiar la configuración de DNS, vaya a Azure Portal y reinicie la máquina virtual.

1. En el escritorio remoto del controlador de dominio secundario, abra el **panel Administrador del servidor**.
4. Haga clic en el vínculo **Agregar roles y características** en el panel.
   
    ![Explorador de servidores Agregar roles](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Seleccione **Siguiente** hasta llegar a la sección **Roles de servidor**.
6. Seleccione los roles **Active Directory Domain Services** y **Servidor DNS**. Cuando se le pida, agregue las características adicionales que requieran estos roles.

9. Cuando terminen de instalarse las características, vuelva al panel **Administrador del servidor** .
10. Seleccione la nueva opción **AD DS** en el panel izquierdo.
11. Haga clic en el vínculo **Más** en la barra de advertencia amarilla.
12. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, haga clic en **Promover este servidor a controlador de dominio**.
1. En **Configuración de implementación**, seleccione **Agregar un controlador de dominio a un dominio existente**. 
   ![NetworkInterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)

1. Haga clic en **Seleccionar**.
1. Conéctese con la cuenta de administrador (**CORP.CONTOSO.COM\domainadmin**) y la contraseña (**Contoso!0000**).
1. En **Seleccionar un dominio del bosque**, haga clic en el dominio y en **Aceptar**. 

1. En **Opciones del controlador de dominio**, utilice los valores predeterminados y establezca una contraseña DSRM.

   >[!NOTE]
   >La página **Opciones de DNS** puede avisarlo de que no se puede crear una delegación de este servidor DNS. Puede pasar por alto esta advertencia en entornos de no producción. 
1. Haga clic en **Siguiente** hasta que se muestre la casilla **Requisitos previos**. Luego haga clic en **Instalar**.

Después de que el servidor complete los cambios de configuración, reinicie el servidor. 

### <a name="a-namedomainaccountsa-configure-domain-accounts"></a><a name=DomainAccounts></a> Configuración de cuentas de dominio

Los pasos siguientes configuran las cuentas de Active Directory (AD). En la tabla siguiente se muestran las cuentas:

| |Cuenta de instalación<br/> |sqlserver-0 <br/>Cuenta del servicio Agente SQL y SQL Server |sqlserver-1<br/>Cuenta del servicio Agente SQL y SQL Server
| --- | --- | --- | --- 
|**Nombre** |Instalar |SQLSvc1 | SQLSvc2
|**Usuario NombreDeCuentaSam** |Instalar |SQLSvc1 | SQLSvc2

Para crear cada cuenta, siga estos pasos. 

1. Vuelva a iniciar sesión en la máquina **ad-primary-dc** .
2. En **Administrador del servidor**, seleccione **Herramientas** y, después, haga clic en **Centro de administración de Active Directory**.   
3. Seleccione **corp (local)** en el panel izquierdo.
4. En el panel **Tareas** de la derecha, seleccione **Nuevo** y, después, haga clic en **Usuario**. 
   ![Centro de administración de Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Establezca una contraseña compleja para cada cuenta.<br/> Para entornos de no producción, establezca que la cuenta de usuario no expire nunca.

5. Haga clic en **Aceptar** para crear el usuario. 
6. Repita los pasos anteriores para cada una de las tres cuentas. 

### <a name="grant-required-permissions-to-the-installation-account"></a>Concesión de los permisos necesarios para la cuenta de instalación
1. En el **Centro de administración de Active Directory**, seleccione **corp (local)** en el panel izquierdo. Luego, en el panel **Tareas** a la derecha, haga clic en **Propiedades**.
   
    ![Propiedades de usuario CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
8. Seleccione **Extensiones** y luego haga clic en el botón **Avanzadas** situado en la pestaña **Seguridad**.
9. En el cuadro de diálogo **Configuración de seguridad avanzada** para corp. Haga clic en **Agregar**.
10. Haga clic en **Seleccionar una entidad de seguridad**. Después busque **CORP\Install**. Haga clic en **Aceptar**.
11. Marque **Leer todas las propiedades**.

1. Marque **Crear objetos de equipo**.
    
     ![Permisos de usuario Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
12. Haga clic en **Aceptar** y luego vuelva a hacer clic en **Aceptar**. Cierre la ventana de propiedades de corp.

Ahora que ha terminado de configurar Active Directory y los objetos de usuario, cree dos máquinas virtuales de SQL Server y una máquina virtual de servidor testigo. Después, una las tres al dominio.

## <a name="create-sql-servers"></a>Creación de instancias de SQL Server
### <a name="create-and-configure-the-sql-server-vms"></a>Creación y configuración de las máquinas virtuales de SQL Server
A continuación, cree tres máquinas virtuales, incluidas dos de SQL Server y un nodo de clúster WSFC. Para crear las máquinas virtuales, vuelva al grupo de recursos **SQL-HA-RG**, haga clic en **Agregar**, busque el elemento apropiado de la galería, **Máquina virtual**, y haga clic en **De la galería**. Use la información de la tabla siguiente para ayudarlo a crear las máquinas virtuales:

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selección del elemento adecuado de la galería |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise en Windows Server 2016** |**SQL Server 2016 SP1 Enterprise en Windows Server 2016** |
| Configuración de máquina virtual: **Datos básicos** |**Nombre** = clúster-fsw<br/>**Nombre de usuario** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Suscripción** = su suscripción<br/>**Grupo de recursos** = SQL-HA-GR<br/>**Ubicación** = Su ubicación de Azure |**Nombre** = sqlserver-0<br/>**Nombre de usuario** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Suscripción** = su suscripción<br/>**Grupo de recursos** = SQL-HA-GR<br/>**Ubicación** = Su ubicación de Azure |**Nombre** = sqlserver-1<br/>**Nombre de usuario** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Suscripción** = su suscripción<br/>**Grupo de recursos** = SQL-HA-GR<br/>**Ubicación** = Su ubicación de Azure |
| Configuración de máquina virtual: **Tamaño** |DS1\_V2 (1 núcleo y 3,5 GB) |**TAMAÑO** = DS2\_V2 (2 núcleos y 7 GB) |**TAMAÑO** = DS2\_V2 (2 núcleos y 7 GB) |
| Configuración de máquina virtual: **Configuración** |**Almacenamiento** = Premium (SSD)<br/>**SUBREDES DE RED** = REDHAVauto<br/>**CUENTA DE ALMACENAMIENTO** = usar una cuenta de almacenamiento generada automáticamente<br/>**Subred** = sqlsubnet(10.1.1.0/24)<br/>**Dirección IP pública** = ninguna<br/>**Grupo de seguridad de red** = ninguno<br/>**Diagnósticos de supervisión** = habilitado<br/>**Cuenta de almacenamiento de diagnósticos** = usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = sqlAvailabilitySet<br/> |**Almacenamiento** = Premium (SSD)<br/>**SUBREDES DE RED** = REDHAVauto<br/>**CUENTA DE ALMACENAMIENTO** = usar una cuenta de almacenamiento generada automáticamente<br/>**Subred** = sqlsubnet(10.1.1.0/24)<br/>**Dirección IP pública** = ninguna<br/>**Grupo de seguridad de red** = ninguno<br/>**Diagnósticos de supervisión** = habilitado<br/>**Cuenta de almacenamiento de diagnósticos** = usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = sqlAvailabilitySet<br/> |**Almacenamiento** = Premium (SSD)<br/>**SUBREDES DE RED** = REDHAVauto<br/>**CUENTA DE ALMACENAMIENTO** = usar una cuenta de almacenamiento generada automáticamente<br/>**Subred** = sqlsubnet(10.1.1.0/24)<br/>**Dirección IP pública** = ninguna<br/>**Grupo de seguridad de red** = ninguno<br/>**Diagnósticos de supervisión** = habilitado<br/>**Cuenta de almacenamiento de diagnósticos** = usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = sqlAvailabilitySet<br/> |
| Configuración de máquina virtual: **Configuración de SQL Server** |No aplicable |**Conectividad de SQL** = privado (dentro de la red virtual)<br/>**Puerto** = 1433<br/>**Autenticación SQL** = deshabilitar<br/>**Configuración de almacenamiento** = general<br/>**Aplicación de revisiones automatizada** = el domingo a las 2:00<br/>**Copia de seguridad automatizada** = deshabilitado</br>**Integración del Almacén de claves de Azure** = Deshabilitada |**Conectividad de SQL** = privado (dentro de la red virtual)<br/>**Puerto** = 1433<br/>**Autenticación SQL** = deshabilitar<br/>**Configuración de almacenamiento** = general<br/>**Aplicación de revisiones automatizada** = el domingo a las 2:00<br/>**Copia de seguridad automatizada** = deshabilitado</br>**Integración del Almacén de claves de Azure** = Deshabilitada |

<br/>

> [!NOTE]
> Los tamaños de máquina que se sugieren aquí están diseñados para probar los grupos de disponibilidad en máquinas virtuales de Azure. Para optimizar el rendimiento de las cargas de trabajo de producción, consulte las recomendaciones de tamaños de máquina y la configuración de SQL Server en [Procedimientos recomendados de SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> 

Una vez que las tres máquinas virtuales estén totalmente aprovisionadas, tendrá que unirlas al dominio **corp.contoso.com** y conceder a CORP\Install los derechos administrativos sobre las máquinas.

### <a name="set-dns-on-each-server"></a>Configuración de DNS en cada servidor
En primer lugar, cambie la dirección del servidor DNS preferido para cada servidor miembro. Siga estos pasos:

1. En el portal, abra el grupo de recursos **SQL-HA-RG** y seleccione la máquina **sqlserver-0**. En la hoja **sqlserver-0**, haga clic en **Conectar** para abrir un archivo RDP con el fin de obtener acceso a Escritorio remoto.
2. Inicie sesión con su cuenta de administrador configurada (**\DomainAdmin**) y la contraseña (**Contoso!0000**).
3. De forma predeterminada, se debe mostrar el panel **Administrador del servidor** . En el panel izquierdo, haga clic en **Servidor local** .
5. Seleccione el vínculo **Dirección IPv4 asignada por DHCP, IPv6 habilitado** .
6. En la ventana **Conexiones de red** , seleccione el icono de red.
7. En la barra de comandos, haga clic en **Cambiar la configuración de esta conexión**. Si no ve esta opción, haga clic en la flecha doble derecha.
8. Seleccione **Protocolo de Internet versión 4 (TCP/IPv4)** y haga clic en Propiedades.
9. Seleccione **Usar las siguientes direcciones de servidor DNS** y especifique la dirección del controlador de dominio principal en **Servidor DNS preferido**.
   >[!TIP]
   >Para obtener la dirección IP del servidor, use `nslookup`.<br/>
   >En el símbolo del sistema, escriba `nslookup ad-primary-dc`. 
11. Haga clic en **Aceptar** y en **Cerrar** para confirmar los cambios. 

   >[!IMPORTANT]
   >Si pierde la conexión a Escritorio remoto después de cambiar la configuración de DNS, vaya a Azure Portal y reinicie la máquina virtual.

Repita estos pasos en todos los servidores.

### <a name="a-namejoindomainajoin-the-servers-to-the-domain"></a><a name="joinDomain"></a>Una los servidores al dominio.

Ahora ya puede unir la máquina virtual a **corp.contoso.com**. Realice los siguientes pasos en los servidores AQL y el servidor testigo del recurso compartido de archivos: 

1. Conéctese de forma remota a la máquina virtual con **BUILTIN\DomainAdmin**. 
1. En **Administrador del servidor**, haga clic en **servidor Local**.
1. Haga clic en el vínculo **GRUPO DE TRABAJO**.
1. En la sección **Nombre de equipo**, haga clic en **Cambiar**.
1. Active la casilla **Dominio** y escriba **corp.contoso.com** en el cuadro de texto. Haga clic en **Aceptar**.
1. En el cuadro de diálogo emergente **Seguridad de Windows**, especifique las credenciales de la cuenta de administrador del dominio predeterminado (**CORP\DomainAdmin**) y la contraseña (**Contoso!0000**).
1. Cuando vea el mensaje "Bienvenida al dominio corp.contoso.com", haga clic en **Aceptar**.
1. Haga clic en **Cerrar** y luego en **Reiniciar ahora** en el cuadro de diálogo emergente.



### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Agregue el usuario Corp\Install como administrador en cada máquina virtual de clúster:

Después de que cada máquina virtual se reinicie como un miembro del dominio, agregue **CORP\Install** como miembro del grupo de administradores local. 

1. Espere hasta que se reinicie la máquina virtual y, después, inicie el archivo RDP de nuevo desde el controlador de dominio principal para iniciar sesión en **sqlserver-0** con la cuenta **CORP\DomainAdmin**.
   >[!TIP]
   >Asegúrese de que inicie sesión con la cuenta de administrador del dominio. En los pasos anteriores, usó la cuenta de administrador BUILTIN. Ahora que el servidor está en el dominio, utilice la cuenta de dominio. En la sesión RDP, especifique *DOMINIO*\\*nombre de usuario*.

2. En **Administrador del servidor** seleccione **Herramientas** y después haga clic en **Administración de equipos**.
3. En la ventana **Administración de equipos**, expanda **Usuarios y grupos locales** y luego seleccione **Grupos**.
4. Haga doble clic en el grupo **Administradores** .
5. En el cuadro de diálogo **Propiedades de administradores**, haga clic en el botón **Agregar**.
6. Especifique el usuario **CORP\Install** y haga clic en **Aceptar**. 
7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de administradores**.
8. Repita los pasos anteriores para **sqlserver-1** y **cluster-fsw**.

### <a name="a-namesetserviceaccountaset-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Configuración de las cuentas de servicio de SQL Server

Establezca la cuenta de servicio de SQL Server en cada servidor SQL Server. Utilice las cuentas que creó cuando [configuró las cuentas de dominio](#DomainAccounts).

1. Abra el **Administrador de configuración de SQL Server**.

1. Haga clic con el botón derecho en el servicio de SQL Server y haga clic en **Propiedades**.

1. Establezca la cuenta y contraseña. 

1. Repita estos pasos en el otro servidor SQL.  

En lo que respecta a los grupos de disponibilidad de SQL Server, cada servidor SQL Server debe ejecutarse como una cuenta de dominio. 

### <a name="create-login-on-each-sql-server-for-installation-account"></a>Creación de un inicio de sesión en cada servidor SQL Server para la cuenta de instalación

Use la cuenta de instalación para configurar el grupo de disponibilidad. Esta cuenta debe ser miembro del rol del servidor fijo **sysadmin** en cada servidor SQL Server. Con los pasos siguientes se crea un inicio de sesión para la cuenta de instalación:

1. Conéctese mediante RDP al servidor mediante la cuenta * \<nombreMáquina\>\administraciónDominio*.

1. Abra SQL Server Management Studio y conéctese a la instancia local de SQL Server. 

1. En **Explorador de objetos**, haga clic en **Seguridad**.

1. Haga clic con el botón derecho en **Inicios de sesión**. Haga clic en **Nuevo inicio de sesión**.

1. En **Inicio de sesión - Nuevo**, haga clic en **Buscar**.

1. Haga clic en **Ubicaciones**.

1. Escriba las credenciales del administrador de dominio. 

1. Utilice la cuenta de instalación.

1. Establezca el inicio de sesión como un miembro del rol del servidor fijo **sysadmin**. 

1. Haga clic en Aceptar. 

Repita los pasos anteriores en el otro servidor SQL Server. 

## <a name="add-failover-cluster-features-to-both-sql-servers"></a>Incorporación de características de clúster de conmutación por error en ambos servidores SQL Server

Para agregar las características de clúster de conmutación por error, siga los pasos de abajo en ambos servidores SQL Server:

1. En el escritorio remoto del controlador de dominio secundario, abra el **panel Administrador del servidor**.
4. Haga clic en el vínculo **Agregar roles y características** en el panel.
   
    ![Explorador de servidores Agregar roles](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Seleccione **Siguiente** hasta llegar a la sección **Características de servidor**.
1. En **Características**, seleccione **Clúster de conmutación por error**. 
1. Agregue más características necesarias. 
1. Haga clic en instalar para agregar todas las características.

Repita los pasos en el otro servidor SQL Server. 


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server"></a><a name="endpoint-firewall"> Configuración del firewall en cada servidor SQL Server

La solución requiere que los siguientes puertos TCP estén abiertos en el firewall:

- **SQL Server**<br/>
  Puerto 1433 para una instancia predeterminada de SQL Server. 
- **Sondeo de Azure Load Balancer**<br/>
   Cualquier puerto disponible. A menudo, los ejemplos utilizan el 59999.
- **Punto de conexión de reflejo de la base de datos** <br/>
   Cualquier puerto disponible. A menudo, los ejemplos utilizan el 5022. 

Los puertos de firewall necesitan estar abiertos en ambos servidores SQL Server.

La manera de abrir los puertos depende de la solución de firewall que use. La siguiente sección explica cómo abrir los puertos del Firewall de Windows. Abra los puertos necesarios en cada uno de sus servidores SQL Server. 

### <a name="open-a-tcp-port-on-a-firewall"></a>Apertura de un puerto TCP en un firewall 

1. En la pantalla **Inicio** del primer servidor SQL Server, abra **Firewall de Windows con seguridad avanzada**.

2. En el panel izquierdo, seleccione **Reglas de entrada**. En el panel derecho, haga clic en **Nueva regla**.

3. En **Tipo de regla**, elija **Puerto**.

1. Para el puerto, especifique TCP y escriba los números de puerto adecuados. Consulte el ejemplo siguiente:

   ![SQLFirewall](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

1. Haga clic en **Siguiente**. 

5. En la página **Acción**, mantenga seleccionado **Permitir la conexión** y haga clic en **Siguiente**.

6. En la página **Perfiles**, acepte la configuración predeterminada y haga clic en **Siguiente**.

7. En la página **Nombre**, especifique el nombre de una regla, como **Sondeo de Azure Load Balancer** en el cuadro de texto **Nombre** y haga clic en **Finalizar**.

Repita estos pasos en el segundo servidor SQL Server de la misma manera.



## <a name="next-steps"></a>Pasos siguientes

* [Crear un grupo de disponibilidad de SQL Server AlwaysOn con máquinas virtuales de Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)


<!--HONumber=Jan17_HO2-->



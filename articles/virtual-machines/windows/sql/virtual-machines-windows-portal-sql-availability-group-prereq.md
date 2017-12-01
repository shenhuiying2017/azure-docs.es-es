---
title: "Grupos de disponibilidad de SQL Server: máquinas virtuales de Azure (requisitos previos) | Microsoft Docs"
description: "En este tutorial se muestra cómo configurar los requisitos previos para crear un grupo de disponibilidad de SQL Server AlwaysOn en máquinas virtuales de Azure."
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
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 0748e0ffa405fc02f6da7e2c412beec12510fde5
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Realización de los requisitos previos para crear grupos de disponibilidad AlwaysOn en máquinas virtuales de Azure.

En este tutorial se muestra cómo completar los requisitos previos para crear un [grupo de disponibilidad de SQL Server AlwaysOn en máquinas virtuales (VM) de Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Cuando haya completado los requisitos previos, tendrá un controlador de dominio, dos máquinas virtuales SQL Server y un servidor testigo en un único grupo de recursos.

**Tiempo estimado**: los requisitos previos pueden tardar hasta un par de horas en completarse. Gran parte de este tiempo se invierte en crear máquinas virtuales.

En el diagrama se muestra lo que va a crear en el tutorial.

![Grupo de disponibilidad](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Revisión de la documentación del grupo de disponibilidad

En este tutorial se da por supuesto que tiene conocimientos básicos de grupos de disponibilidad de SQL Server AlwaysOn. Si no está familiarizado con esta tecnología, consulte [Información general de los grupos de disponibilidad AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Crear una cuenta de Azure
Necesitará una cuenta de Azure. Puede [abrir una cuenta gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
1. Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2. Haga clic en **+** para crear un nuevo objeto en el portal.

   ![Nuevo objeto](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Haga clic en **grupo de recursos** en la ventana de búsqueda de **Marketplace**.

   ![Grupos de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Haga clic en **Grupo de recursos**.
5. Haga clic en **Crear**.
6. En la hoja **Grupo de recursos**, en **Nombre del grupo de recursos**, escriba un nombre para el grupo de recursos. Por ejemplo, escriba **sql-ha-rg**.
7. Si tiene varias suscripciones de Azure, compruebe que la suscripción es la suscripción de Azure en la que quiere crear el grupo de disponibilidad.
8. Seleccione una ubicación. La ubicación es la región de Azure donde desea crear el grupo de disponibilidad. Para este tutorial, vamos a crear todos los recursos en una ubicación de Azure.
9. Compruebe que la función **Anclar al panel** está activada. Este parámetro opcional coloca un acceso directo para el grupo de recursos en el panel del Portal de Azure.

   ![Grupos de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Haga clic en **Crear** para crear el grupo de recursos.

Azure crea el grupo de recursos y ancla un acceso directo a él en el portal.

## <a name="create-the-network-and-subnets"></a>Creación de redes y subredes
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
   | **Grupos de recursos** |Elija la opción **Usar existente** y seleccione el nombre del grupo de recursos. |
   | **Ubicación** |Especifique la ubicación de Azure. |

   El intervalo de direcciones de subred y el espacio de direcciones podrían ser distintos a los de la tabla. Según su suscripción, el portal recomienda un espacio de direcciones disponible y el intervalo de direcciones de subred correspondiente. Si no hay suficiente espacio de direcciones disponible, use otra suscripción.

   En el ejemplo se usa el nombre de subred **Admin**. Esta subred es para los controladores de dominio.

5. Haga clic en **Crear**.

   ![Configuración de la red virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure le devuelve al panel del portal y le informa cuando se cree la red.

### <a name="create-a-second-subnet"></a>Cree una segunda subred.
La nueva red virtual tiene una subred denominada **Admin**. Los controladores de dominio usan esta subred. Las máquinas virtuales con SQL Server usan una segunda subred denominada **SQL**. Para configurar esta subred, siga estos pasos:

1. En el panel, haga clic en el grupo de recursos que ha creado, **SQL-HA-RG**. En **Recursos**, busque la red en el grupo de recursos.

    Si **SQL-HA-RG** no está visible, haga clic en **Grupos de recursos** y filtre por el nombre del grupo de recursos para encontrarlo.
2. Haga clic en **autoHAVNET** en la lista de recursos. Azure abre la hoja de configuración de red.
3. En la hoja de la red virtual **autoHAVNET**, en **Configuración** haga clic en **Subredes**.

    Observe la subred que ya ha creado.

   ![Configuración de la red virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Cree una segunda subred. Haga clic en **+ Subred**.
6. En la hoja **Agregar subred**, configure la subred escribiendo **sqlsubnet** en **Nombre**. Azure especifica automáticamente un valor válido en **Intervalo de direcciones**. Compruebe que este intervalo de direcciones tiene al menos 10 direcciones. En un entorno de producción, puede que necesite más direcciones.
7. Haga clic en **Aceptar**.

    ![Configuración de la red virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

En la tabla siguiente se resumen las opciones de configuración de red:

| **Campo** | Valor |
| --- | --- |
| **Name** |**autoHAVNET** |
| **Espacio de direcciones** |Este valor depende de los espacios de direcciones disponibles en su suscripción. Un valor típico es 10.0.0.0/16. |
| **Nombre de subred** |**admin** |
| **Intervalo de direcciones de subred** |Este valor depende de los intervalos de direcciones disponibles en su suscripción. Un valor típico es 10.0.0.0/24. |
| **Nombre de subred** |**sqlsubnet** |
| **Intervalo de direcciones de subred** |Este valor depende de los intervalos de direcciones disponibles en su suscripción. Un valor típico es 10.0.1.0/24. |
| **Suscripción** |Especifique la suscripción que quiere usar. |
| **Grupo de recursos** |**SQL-HA-RG** |
| **Ubicación** |Especifique la misma ubicación que eligió para el grupo de recursos. |

## <a name="create-availability-sets"></a>Creación de conjuntos de disponibilidad

Antes de crear máquinas virtuales, debe crear conjuntos de disponibilidad. Los conjuntos de disponibilidad reducen el tiempo de inactividad de cara al mantenimiento, tanto planeado como no planeado. Un conjunto de disponibilidad de Azure es un grupo lógico de recursos que Azure coloca en dominios de error y de actualización físicos. Un dominio de error garantiza que los miembros del conjunto de disponibilidad tengan recursos de energía y red independientes. Un dominio de actualización garantiza que los miembros del conjunto de disponibilidad no pasen a inactividad por mantenimiento al mismo tiempo. Para más información, consulte [Administración de la disponibilidad de las máquinas virtuales](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Necesita dos conjuntos de disponibilidad. El primero es para los controladores de dominio y el segundo, para las máquinas virtuales con SQL Server.

Para crear un conjunto de disponibilidad, vaya al grupo de recursos y haga clic en **Agregar**. Para filtrar los resultados, escriba **conjunto de disponibilidad**. Haga clic en **Conjunto de disponibilidad** en los resultados y, a continuación, haga clic en **Crear**.

Configure dos conjuntos de disponibilidad según los parámetros de la tabla siguiente:

| **Campo** | Conjunto de disponibilidad del controlador de dominio | Conjunto de disponibilidad de SQL Server |
| --- | --- | --- |
| **Name** |adavailabilityset |sqlavailabilityset |
| **Grupos de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Dominios de error** |3 |3 |
| **Dominios de actualización** |5 |3 |

Después de crear los conjuntos de disponibilidad, vuelva al grupo de recursos en el Portal de Azure.

## <a name="create-domain-controllers"></a>Creación de controladores de dominio
Después de haber creado la red, las subredes, los conjuntos de disponibilidad y un equilibrador de carga accesible desde Internet, está preparado para crear las máquinas virtuales para los controladores de dominio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Creación de máquinas virtuales para controladores de dominio
Para crear y configurar los controladores de dominio, vuelva al grupo de recursos **SQL-HA-RG** .

1. Haga clic en **Agregar**. Se abrirá la hoja **Todo** .
2. Escriba **Windows Server 2016 Datacenter**.
3. Haga clic en **Windows Server 2016 Datacenter**. En la hoja **Windows Server 2016 Datacenter**, compruebe que el modelo de implementación sea **Resource Manager** y haga clic en **Crear**. Azure abre la hoja **Crear máquina virtual** .

Repita los pasos anteriores para crear dos máquinas virtuales. Asigne nombre a las dos máquinas virtuales:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > La máquina virtual **ad-secondary-dc** es opcional. Se usa para proporcionar alta disponibilidad para Active Directory Domain Services.
  >
  >

La siguiente tabla muestra la configuración de estas dos máquinas:

| **Campo** | Valor |
| --- | --- |
| **Name** |Primer controlador de dominio: *ad-primary-dc*.</br>Segundo controlador de dominio: *ad-secondary-dc*. |
| **Tipo de disco de máquina virtual** |SSD |
| **Nombre de usuario** |DomainAdmin |
| **Password** |Contoso!0000 |
| **Suscripción** |*Su suscripción* |
| **Grupos de recursos** |SQL-HA-RG |
| **Ubicación** |*Su ubicación* |
| **Tamaño** |DS1_V2 |
| **Storage** | **Usar discos administrados** - **Sí** |
| **Red virtual** |autoHAVNET |
| **Subred** |admin |
| **Dirección IP pública** |*Mismo nombre que la máquina virtual* |
| **Grupo de seguridad de red** |*Mismo nombre que la máquina virtual* |
| **El conjunto de disponibilidad** |adavailabilityset </br>**Dominios de error**: 2</br>**Dominios de actualización**: 2|
| **Diagnóstico** |Enabled |
| **Cuenta de almacenamiento de información de diagnóstico** |*Se crea automáticamente* |

   >[!IMPORTANT]
   >Solo puede colocar una máquina virtual en un conjunto de disponibilidad al crearlo. Una vez creada una máquina virtual, no se puede cambiar el conjunto de disponibilidad. Consulte [Administración de la disponibilidad de las máquinas virtuales](../manage-availability.md).

Azure crea las máquinas virtuales.

Después de crear las máquinas virtuales, configure el controlador de dominio.

### <a name="configure-the-domain-controller"></a>Configuración del controlador de dominio
En los pasos siguientes, configurará la máquina **ad-primary-dc** como controlador de dominio para corp.contoso.com.

1. En el portal, abra el grupo de recursos **SQL-HA-RG** y seleccione la máquina **ad-primary-dc**. En la hoja **ad-primary-dc**, haga clic en **Conectar** para abrir un archivo RDP con el fin de obtener acceso a Escritorio remoto.

    ![Conexión a una máquina virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Inicie sesión con su cuenta de administrador configurada (**\DomainAdmin**) y la contraseña (**Contoso!0000**).
3. De forma predeterminada, se debe mostrar el panel **Administrador del servidor** .
4. Haga clic en el vínculo **Agregar roles y características** en el panel.

    ![Administrador del servidor: Agregar roles](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Seleccione **Siguiente** hasta llegar a la sección **Roles de servidor**.
6. Seleccione los roles **Active Directory Domain Services** y **Servidor DNS**. Cuando se le pida, agregue características adicionales que son necesarias para estos roles.

   > [!NOTE]
   > Windows le advierte de que no hay ninguna dirección IP estática. Si está probando la configuración, haga clic en **Continuar**. En los escenarios de producción, establezca la dirección IP en estática en Azure Portal o [use PowerShell para establecer la dirección IP estática de la máquina del controlador de dominio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Cuadro de diálogo Agregar roles](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Haga clic en **Siguiente** hasta llegar a la sección **Confirmación**. Active la casilla **Reiniciar automáticamente el servidor de destino en caso necesario** .
8. Haga clic en **Instalar**.
9. Cuando terminen de instalarse las características, vuelva al panel **Administrador del servidor** .
10. Seleccione la nueva opción **AD DS** en el panel izquierdo.
11. Haga clic en el vínculo **Más** en la barra de advertencia amarilla.

    ![Cuadro de diálogo AD DS en la máquina virtual del servidor DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, haga clic en **Promover este servidor a controlador de dominio**.
13. En el **Asistente de configuración de Active Directory Domain Services**, use los siguientes valores:

    | **Page** | Configuración |
    | --- | --- |
    | **Configuración de la implementación** |**Incorporación de nuevos bosques**<br/> **Nombre del dominio raíz** = corp.contoso.com |
    | **Opciones del controlador de dominio** |**Contraseña de DSRM** = Contoso!0000<br/>**Confirmar contraseña** = Contoso!0000 |
14. Haga clic en **Siguiente** para avanzar por las otras páginas del asistente. En la página **Comprobación de requisitos previos**, compruebe que ve el mensaje siguiente: **Todas las comprobaciones de requisitos previos se realizaron correctamente**. Puede revisar todos los mensajes de advertencia aplicables, pero es posible continuar con la instalación.
15. Haga clic en **Instalar**. La máquina virtual **ad-primary-dc** se reinicia automáticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Anote la dirección IP del controlador de dominio principal.

Utilice el controlador de dominio principal para DNS. Anote la dirección IP del controlador de dominio principal.

Una manera de obtener la dirección IP del controlador de dominio principal es a través de Azure Portal.

1. En Azure Portal, abra el grupo de recursos.

2. Haga clic en el controlador de dominio principal.

3. En la hoja del controlador del dominio principal, haga clic en **Interfaces de red**.

![Interfaces de red](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Anote la dirección IP privada de este servidor.

### <a name="configure-the-virtual-network-dns"></a>Configuración del DNS de la red virtual
Después de crear el primer controlador de dominio y habilitar el DNS en el primer servidor, configure la red virtual para usar este servidor para DNS.

1. En Azure Portal, haga clic en la red virtual.

2. En **Configuración**, haga clic en **Servidor DNS**.

3. Haga clic en **Personalizar** y escriba la dirección IP privada del controlador de dominio principal.

4. Haga clic en **Guardar**.

### <a name="configure-the-second-domain-controller"></a>Configuración del segundo controlador de dominio
Una vez reiniciado el controlador de dominio principal, puede configurar el segundo controlador de dominio. Este paso opcional es para lograr alta disponibilidad. Para configurar el segundo controlador de dominio siga estos pasos:

1. En el portal, abra el grupo de recursos **SQL-HA-RG** y seleccione la máquina **ad-secondary-dc**. En la hoja **ad-secondary-dc**, haga clic en **Conectar** para abrir un archivo RDP con el fin de obtener acceso a Escritorio remoto.
2. Inicie sesión en la máquina virtual con la cuenta de administrador configurada (**BUILTIN\DomainAdmin**) y la contraseña (**Contoso!0000**).
3. Cambie la dirección del servidor DNS preferido por la dirección del controlador de dominio.
4. En **Centro de redes y recursos compartidos**, haga clic en la interfaz de red.
   ![Interfaz de red](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Haga clic en **Propiedades**.
6. Seleccione **Protocolo de Internet versión 4 (TCP/IPv4)** y haga clic en **Propiedades**.
7. Seleccione **Usar las siguientes direcciones de servidor DNS** y especifique la dirección del controlador de dominio principal en **Servidor DNS preferido**.
8. Haga clic en **Aceptar** y en **Cerrar** para confirmar los cambios. Ahora ya puede unir la máquina virtual a **corp.contoso.com**.

   >[!IMPORTANT]
   >Si pierde la conexión a Escritorio remoto después de cambiar la configuración de DNS, vaya a Azure Portal y reinicie la máquina virtual.

9. En el escritorio remoto del controlador de dominio secundario, abra el **panel Administrador del servidor**.
10. Haga clic en el vínculo **Agregar roles y características** en el panel.

    ![Administrador del servidor: Agregar roles](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Seleccione **Siguiente** hasta llegar a la sección **Roles de servidor**.
12. Seleccione los roles **Active Directory Domain Services** y **Servidor DNS**. Cuando se le pida, agregue características adicionales que son necesarias para estos roles.
13. Cuando terminen de instalarse las características, vuelva al panel **Administrador del servidor** .
14. Seleccione la nueva opción **AD DS** en el panel izquierdo.
15. Haga clic en el vínculo **Más** en la barra de advertencia amarilla.
16. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, haga clic en **Promover este servidor a controlador de dominio**.
17. En **Configuración de implementación**, seleccione **Agregar un controlador de dominio a un dominio existente**.
   ![Configuración de la implementación](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Haga clic en **Seleccionar**.
19. Conéctese con la cuenta de administrador (**CORP.CONTOSO.COM\domainadmin**) y la contraseña (**Contoso!0000**).
20. En **Seleccionar un dominio del bosque**, haga clic en el dominio y en **Aceptar**.
21. En **Opciones del controlador de dominio**, utilice los valores predeterminados y establezca una contraseña de DSRM.

   >[!NOTE]
   >La página **Opciones de DNS** puede avisarle de que no se puede crear una delegación de este servidor DNS. Puede pasar por alto esta advertencia en entornos de no producción.
22. Haga clic en **Siguiente** hasta que se muestre la casilla **Requisitos previos**. Luego haga clic en **Instalar**.

Después de que el servidor complete los cambios de configuración, reinícielo.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Incorporación de la dirección IP privada del controlador de dominio secundario al servidor DNS de VPN

En Azure Portal, en la red virtual, cambie la opción Servidor DNS para que incluya la dirección IP del controlador de dominio secundario. Esto permitirá la redundancia del servicio DNS.

### <a name=DomainAccounts></a> Configuración de las cuentas de dominio

En los pasos siguientes configurará las cuentas de Active Directory. En la tabla siguiente se muestran las cuentas:

| |Cuenta de instalación<br/> |sqlserver-0 <br/>Cuenta del servicio Agente SQL y SQL Server |sqlserver-1<br/>Cuenta del servicio Agente SQL y SQL Server
| --- | --- | --- | ---
|**Nombre** |Instalar |SQLSvc1 | SQLSvc2
|**Usuario NombreDeCuentaSam** |Instalar |SQLSvc1 | SQLSvc2

Para crear cada cuenta, siga estos pasos.

1. Inicie sesión en la máquina **ad-primary-dc** .
2. En **Administrador del servidor**, seleccione **Herramientas** y, después, haga clic en **Centro de administración de Active Directory**.   
3. Seleccione **corp (local)** en el panel izquierdo.
4. En el panel **Tareas** de la derecha, seleccione **Nuevo** y, después, haga clic en **Usuario**.
   ![Centro de administración de Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Establezca una contraseña compleja para cada cuenta.<br/> Para entornos de no producción, establezca que la cuenta de usuario no expire nunca.

5. Haga clic en **Aceptar** para crear el usuario.
6. Repita los pasos anteriores para cada una de las tres cuentas.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Concesión de los permisos necesarios para la cuenta de instalación
1. En el **Centro de administración de Active Directory**, seleccione **corp (local)** en el panel izquierdo. Luego, en el panel **Tareas** a la derecha, haga clic en **Propiedades**.

    ![Propiedades de usuario CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Seleccione **Extensiones** y luego haga clic en el botón **Avanzadas** situado en la pestaña **Seguridad**.
3. En el cuadro de diálogo **Configuración de seguridad avanzada**, haga clic en **Agregar**.
4. Haga clic en **Seleccionar una entidad de seguridad**, busque **CORP\Install** y, a continuación, haga clic en **Aceptar**.
5. Active la casilla **Leer todas las propiedades**.

6. Active la casilla **Create Computer objects** (Crear objetos de proceso).

     ![Permisos de usuario Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Haga clic en **Aceptar** y luego vuelva a hacer clic en **Aceptar**. Cierre la ventana de propiedades **corp**.

Ahora que ha terminado de configurar Active Directory y los objetos de usuario, cree dos máquinas virtuales con SQL Server y una máquina virtual de servidor testigo. Después, una las tres al dominio.

## <a name="create-sql-server-vms"></a>Creación de las máquinas virtuales con SQL Server

Cree tres máquinas virtuales adicionales. La solución requiere dos máquinas virtuales con instancias de SQL Server. Una tercera máquina virtual funciona como testigo. Windows Server 2016 puede usar un [testigo en la nube](http://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness). No obstante, para mantener la coherencia con los sistemas operativos anteriores, este documento usa una máquina virtual como testigo.  

Tenga en cuenta las siguientes decisiones de diseño antes de continuar.

* **Almacenamiento: Azure Managed Disks**

   Para el almacenamiento de máquina virtual, use Azure Managed Disks. Microsoft recomienda el uso de Managed Disks para máquinas virtuales de SQL Server. Managed Disks controla el almacenamiento en segundo plano. Además, cuando las máquinas virtuales con Managed Disks están en el mismo conjunto de disponibilidad, Azure distribuye los recursos de almacenamiento para proporcionar la redundancia adecuada. Para más información, consulte [Introducción a Azure Managed Disks](../managed-disks-overview.md). Para obtener información específica acerca de Managed Disks en un conjunto de disponibilidad, consulte [Uso de Managed Disks para las máquinas virtuales de un conjunto de disponibilidad](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Red: direcciones IP privadas en producción**

   Para las máquinas virtuales, este tutorial usa direcciones IP públicas. Esto permite conectarse de forma remota directamente a la máquina virtual a través de Internet y facilita pasos de configuración. En entornos de producción, Microsoft recomienda usar únicamente direcciones IP privadas con el fin de reducir el grado de vulnerabilidad del recurso de máquina virtual de la instancia de SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Creación y configuración de las máquinas virtuales de SQL Server
A continuación, cree tres máquinas virtuales: dos de SQL Server y una para el nodo de clúster adicional. Para crear las máquinas virtuales, vuelva al grupo de recursos **SQL-HA-RG**, haga clic en **Agregar**, busque el elemento apropiado de la galería, haga clic en **Máquina virtual** y luego en **De la galería**. Use la información de la tabla siguiente para ayudarlo a crear las máquinas virtuales:


| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selección del elemento adecuado de la galería |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise en Windows Server 2016** |**SQL Server 2016 SP1 Enterprise en Windows Server 2016** |
| Configuración de máquina virtual: **Datos básicos** |**Nombre** = clúster-fsw<br/>**Nombre de usuario** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Suscripción** = su suscripción<br/>**Grupo de recursos** = SQL-HA-GR<br/>**Ubicación** = Su ubicación de Azure |**Nombre** = sqlserver-0<br/>**Nombre de usuario** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Suscripción** = su suscripción<br/>**Grupo de recursos** = SQL-HA-GR<br/>**Ubicación** = Su ubicación de Azure |**Nombre** = sqlserver-1<br/>**Nombre de usuario** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Suscripción** = su suscripción<br/>**Grupo de recursos** = SQL-HA-GR<br/>**Ubicación** = Su ubicación de Azure |
| Configuración de máquina virtual: **Tamaño** |**TAMAÑO** = DS1\_V2 (1 vCPU; 3,5 GB) |**TAMAÑO** = DS1\_V2 (2 vCPU, 7 GB)</br>El tamaño debe admitir el almacenamiento SSD (soporte de disco Premium. )) |**TAMAÑO** = DS1\_V2 (2 vCPU, 7 GB) |
| Configuración de máquina virtual: **Configuración** |**Storage**: use Managed Disks.<br/>**Red virtual** = autoHAVNET<br/>**Subred** = sqlsubnet(10.1.1.0/24)<br/>**Dirección IP pública** generada automáticamente.<br/>**Grupo de seguridad de red** = ninguno<br/>**Diagnósticos de supervisión** = habilitado<br/>**Cuenta de almacenamiento de diagnósticos** = usar una cuenta de almacenamiento generada automáticamente<br/>**Conjunto de disponibilidad** = sqlAvailabilitySet<br/> |**Storage**: use Managed Disks.<br/>**Red virtual** = autoHAVNET<br/>**Subred** = sqlsubnet(10.1.1.0/24)<br/>**Dirección IP pública** generada automáticamente.<br/>**Grupo de seguridad de red** = ninguno<br/>**Diagnósticos de supervisión** = habilitado<br/>**Cuenta de almacenamiento de diagnósticos** = usar una cuenta de almacenamiento generada automáticamente<br/>**Conjunto de disponibilidad** = sqlAvailabilitySet<br/> |**Storage**: use Managed Disks.<br/>**Red virtual** = autoHAVNET<br/>**Subred** = sqlsubnet(10.1.1.0/24)<br/>**Dirección IP pública** generada automáticamente.<br/>**Grupo de seguridad de red** = ninguno<br/>**Diagnósticos de supervisión** = habilitado<br/>**Cuenta de almacenamiento de diagnósticos** = usar una cuenta de almacenamiento generada automáticamente<br/>**Conjunto de disponibilidad** = sqlAvailabilitySet<br/> |
| Configuración de máquina virtual: **Configuración de SQL Server** |No aplicable |**Conectividad de SQL** = privado (dentro de la red virtual)<br/>**Puerto** = 1433<br/>**Autenticación SQL** = deshabilitar<br/>**Configuración de almacenamiento** = general<br/>**Aplicación de revisiones automatizada** = el domingo a las 2:00<br/>**Copia de seguridad automatizada** = deshabilitado</br>**Integración de Azure Key Vault** = Deshabilitada |**Conectividad de SQL** = privado (dentro de la red virtual)<br/>**Puerto** = 1433<br/>**Autenticación SQL** = deshabilitar<br/>**Configuración de almacenamiento** = general<br/>**Aplicación de revisiones automatizada** = el domingo a las 2:00<br/>**Copia de seguridad automatizada** = deshabilitado</br>**Integración de Azure Key Vault** = Deshabilitada |

<br/>

> [!NOTE]
> Los tamaños de máquina que se sugieren aquí están diseñados para probar los grupos de disponibilidad en máquinas virtuales de Azure. Para optimizar el rendimiento de las cargas de trabajo de producción, consulte las recomendaciones de tamaños de máquina y la configuración de SQL Server en [Procedimientos recomendados de SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Una vez que las tres máquinas virtuales estén totalmente aprovisionadas, tendrá que unirlas al dominio **corp.contoso.com** y conceder a CORP\Install los derechos administrativos sobre las máquinas.

### <a name="joinDomain"></a>Unión de los servidores al dominio

Ahora ya puede unir las máquinas virtuales a **corp.contoso.com**. Realice los pasos siguientes en las máquinas virtuales con SQL Server y el servidor testigo del recurso compartido de archivos:

1. Conéctese de forma remota a la máquina virtual con **BUILTIN\DomainAdmin**.
2. En **Administrador del servidor**, haga clic en **Servidor local**.
3. Haga clic en el vínculo **GRUPO DE TRABAJO**.
4. En la sección **Nombre de equipo**, haga clic en **Cambiar**.
5. Active la casilla **Dominio** y escriba **corp.contoso.com** en el cuadro de texto. Haga clic en **Aceptar**.
6. En el cuadro de diálogo emergente **Seguridad de Windows**, especifique las credenciales de la cuenta de administrador del dominio predeterminado (**CORP\DomainAdmin**) y la contraseña (**Contoso!0000**).
7. Cuando vea el mensaje "Bienvenida al dominio corp.contoso.com", haga clic en **Aceptar**.
8. Haga clic en **Cerrar** y luego en **Reiniciar ahora** en el cuadro de diálogo emergente.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Agregue el usuario Corp\Install como administrador en cada máquina virtual del clúster:

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
8. Repita los pasos anteriores en **sqlserver-1** y **cluster-fsw**.

### <a name="setServiceAccount"></a>Configuración de las cuentas de servicio de SQL Server

Establezca la cuenta de servicio de SQL Server en cada máquina virtual con SQL Server. Utilice las cuentas que creó cuando [configuró las cuentas de dominio](#DomainAccounts).

1. Abra el **Administrador de configuración de SQL Server**.
2. Haga clic con el botón derecho en el servicio SQL Server y haga clic en **Propiedades**.
3. Establezca la cuenta y contraseña.
4. Repita estos pasos en la otra máquina virtual con SQL Server.  

En lo que respecta a los grupos de disponibilidad de SQL Server, cada máquina virtual con SQL Server debe ejecutarse como una cuenta de dominio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Creación de un inicio de sesión en cada máquina virtual con SQL Server para la cuenta de instalación

Use la cuenta de instalación (CORP\install) para configurar el grupo de disponibilidad. Esta cuenta debe ser miembro del rol de servidor fijo **sysadmin** en cada máquina virtual con SQL Server. Con los pasos siguientes se crea un inicio de sesión para la cuenta de instalación:

1. Conéctese al servidor mediante el Protocolo de escritorio remoto (RDP) mediante la cuenta *\<MachineName\>\DomainAdmin*.

1. Abra SQL Server Management Studio y conéctese a la instancia local de SQL Server.

1. En **Explorador de objetos**, haga clic en **Seguridad**.

1. Haga clic con el botón derecho en **Inicios de sesión**. Haga clic en **Nuevo inicio de sesión**.

1. En **Inicio de sesión - Nuevo**, haga clic en **Buscar**.

1. Haga clic en **Ubicaciones**.

1. Escriba las credenciales del administrador de dominio.

1. Utilice la cuenta de instalación.

1. Establezca el inicio de sesión como un miembro del rol de servidor fijo **sysadmin**.

1. Haga clic en **Aceptar**.

Repita los pasos anteriores en la otra máquina virtual con SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Adición de características de clúster de conmutación por error en ambas máquinas virtuales con SQL Server

Para agregar características de clúster de conmutación por error, realice las siguientes acciones en ambas máquinas virtuales con SQL Server:

1. Conéctese a la máquina virtual de SQL Server con el Protocolo de escritorio remoto (RDP) mediante la cuenta *CORP\install*. Abra el **panel Administrador de servidores**.
2. Haga clic en el vínculo **Agregar roles y características** en el panel.

    ![Administrador del servidor: Agregar roles](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Seleccione **Siguiente** hasta llegar a la sección **Características de servidor**.
4. En **Características**, seleccione **Clúster de conmutación por error**.
5. Agregue más características necesarias.
6. Haga clic en **Instalar** para agregar las características.

Repita los pasos en la otra máquina virtual con SQL Server.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Configuración del firewall en cada máquina virtual con SQL Server

La solución requiere que los siguientes puertos TCP estén abiertos en el firewall:

- **Máquina virtual con SQL Server**:<br/>
   Puerto 1433 para una instancia predeterminada de SQL Server.
- **Sondeo de Azure Load Balancer:**<br/>
   Cualquier puerto disponible. A menudo, los ejemplos utilizan el 59999.
- **Punto de conexión de reflejo de la base de datos:** <br/>
   Cualquier puerto disponible. A menudo, los ejemplos utilizan el 5022.

Los puertos de firewall deben estar abiertos en ambas máquinas virtuales con SQL Server.

El método de abrir los puertos depende de la solución de firewall que use. En la siguiente sección se explica cómo abrir los puertos en Firewall de Windows. Abra los puertos necesarios en cada una de las máquinas virtuales con SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Apertura de un puerto TCP en el firewall

1. En la pantalla **Inicio** del primer servidor SQL Server, abra **Firewall de Windows con seguridad avanzada**.
2. En el panel izquierdo, seleccione **Reglas de entrada**. En el panel derecho, haga clic en **Nueva regla**.
3. En **Tipo de regla**, elija **Puerto**.
4. Para el puerto, especifique **TCP** y escriba los números de puerto adecuados. Consulte el ejemplo siguiente:

   ![Firewall de SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Haga clic en **Siguiente**.
6. En la página **Acción**, mantenga seleccionado **Permitir la conexión** y luego haga clic en **Siguiente**.
7. En la página **Perfiles**, acepte la configuración predeterminada y luego haga clic en **Siguiente**.
8. En la página **Nombre**, especifique el nombre de una regla, como **Sondeo de Azure Load Balancer** en el cuadro de texto **Nombre** y haga clic en **Finalizar**.

Repita estos pasos en la segunda máquina virtual con SQL Server.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un grupo de disponibilidad de SQL Server AlwaysOn en máquinas virtuales de Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

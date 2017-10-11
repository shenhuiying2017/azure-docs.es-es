---
title: "Configuración de grupos de disponibilidad Always On en Azure Virtual Machines (implementación clásica) | Microsoft Docs"
description: Cree un grupo de disponibilidad AlwaysOn con Azure Virtual Machines. Este tutorial usa principalmente la interfaz de usuario y herramientas en lugar de scripts.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b360fe9f28eeb9b10c82fce729165b1b572ac3c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>Configuración de grupos de disponibilidad Always On en Azure Virtual Machines (implementación clásica)
> [!div class="op_single_selector"]
> * [Portal de Azure clásico: interfaz de usuario](../classic/portal-sql-alwayson-availability-groups.md)
> * [Clásico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de comenzar, considere que ahora puede completar esta tarea en un modelo de Azure Resource Manager. Se recomienda el modelo de Azure Resource Manager para las implementaciones nuevas. Consulte [Grupos de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT] 
> Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artículo explica cómo utilizar el modelo de implementación clásico. 

Para completar esta tarea con el modelo de Azure Resource Manager, consulte el artículo [Grupos de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

Este tutorial integral muestra cómo implementar grupos de disponibilidad mediante SQL Server AlwaysOn ejecutándose en Azure Virtual Machines.

Al final del tutorial, la solución SQL Server AlwaysOn en Azure constará de los siguientes elementos:

* Una red virtual que contiene varias subredes, incluida una subred front-end y back-end
* Un controlador de dominio con un dominio de Active Directory (Azure AD)
* Dos máquinas virtuales que ejecutan SQL Server y están implementadas en la subred de back-end y unidas al dominio de Azure AD
* Un clúster de conmutación por error de tres nodos con el modelo de cuórum Mayoría de nodo
* Un grupo de disponibilidad que tiene dos réplicas de confirmación sincrónica de una base de datos de disponibilidad

La siguiente ilustración es una representación gráfica de la solución.

![Arquitectura de pruebas para grupos de disponibilidad en Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Tenga en cuenta que se trata de una de las posibles configuraciones. Por ejemplo, puede minimizar el número de máquinas virtuales para un grupo de disponibilidad de dos réplicas. Esta configuración guarda las horas de proceso en Azure mediante el controlador de dominio como el testigo del recurso compartido del archivo de quórum en un clúster de dos nodos. Este método reduce el número de máquinas virtuales en uno desde la configuración mostrada.

En este tutorial se da por hecho lo siguiente:

* Ya tiene una cuenta de Azure.
* Ya sabe cómo usar la GUI en la galería de máquinas virtuales para aprovisionar una máquina virtual clásica con SQL Server.
* Ya tiene un conocimiento sólido de los grupos de disponibilidad AlwaysOn. Para obtener más información, consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Si le interesa utilizar los grupos de disponibilidad Always On con SharePoint, consulte también [Configuración de grupos de disponibilidad AlwaysOn de SQL Server 2012 para SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Creación de la red virtual y el servidor del controlador de dominio
Comience con una nueva cuenta de prueba de Azure. Una vez que haya configurado la cuenta, debería estar en la pantalla de inicio del Portal de Azure clásico.

1. Haga clic en el botón **Nuevo** de la esquina izquierda de la parte inferior de la página, como se muestra en la captura de pantalla siguiente.
   
    ![Haga clic en Nuevo, en el Portal](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Haga clic en **Network Services** > **Red virtual** > **Creación personalizada** tal como se muestra en la siguiente ilustración.
   
    ![Crear red virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. En el cuadro de diálogo **CREAR UNA RED VIRTUAL** , cree una nueva red virtual recorriendo las páginas y usando la configuración de la siguiente tabla. 
   
   | Page | Settings |
   | --- | --- |
   | Detalles de red virtual |**NOMBRE = ContosoNET**<br/>**REGIÓN = Oeste de EE. UU.** |
   | Servidores DNS y conectividad VPN |None |
   | Espacios de direcciones de la red virtual |La configuración se muestra en la siguiente captura de pantalla: ![Crear red virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Cree la máquina virtual que se usará como controlador de dominio (DC). Haga clic en **Nuevo** > **Proceso** > **Máquina virtual** > **Desde galería**, como se muestra en la siguiente captura de pantalla.
   
    ![de una máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. En el cuadro de diálogo **CREAR UNA MÁQUINA VIRTUAL** , configure una nueva máquina virtual recorriendo las páginas y usando la configuración de la siguiente tabla. 
   
   | Page | Settings |
   | --- | --- |
   | Seleccionar el sistema operativo de la máquina virtual |Windows Server 2012 R2 Datacenter |
   | Configuración de la máquina virtual |**FECHA DE LANZAMIENTO DE LA VERSIÓN** = (última)<br/>**NOMBRE DE MÁQUINA VIRTUAL** = ContosoDC<br/>**NIVEL**: ESTÁNDAR<br/>**TAMAÑO** = A2 (2 núcleos)<br/>**NUEVO NOMBRE de USUARIO** = AzureAdmin<br/>**NUEVA CONTRASEÑA** = Contoso!000<br/>**CONFIRMAR** = Contoso!000 |
   | Configuración de la máquina virtual |**SERVICIO EN LA NUBE**: Crear un nuevo servicio en la nube<br/>**NOMBRE DE DNS DEL SERVICIO EN LA NUBE** = un nombre de servicio en la nube único<br/>**NOMBRE DNS** = un nombre único (por ejemplo, ContosoDC123)<br/>**REGION/GRUPO DE AFINIDAD/RED VIRTUAL** = ContosoNET<br/>**SUBREDES DE LA RED VIRTUAL** = Back(10.10.2.0/24)<br/>**CUENTA DE ALMACENAMIENTO** = usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD**: (Ninguno) |
   | Opciones de la máquina virtual |Usar predeterminados |

Después de configurar la nueva máquina virtual, espere a que se realice el aprovisionamiento de la máquina virtual. Este proceso tarda algo de tiempo en finalizarse. Si hace clic en la pestaña **Máquina virtual** en el Portal de Azure clásico, puede ver que ContosoDC recorre los estados de **Iniciando (aprovisionamiento)** a **Detenido**, **Iniciando**, **En ejecución (aprovisionamiento)** y, finalmente, **En ejecución**.

El servidor DC ya está aprovisionado correctamente. A continuación, configurará el dominio de Active Directory en este servidor DC.

## <a name="configure-the-domain-controller"></a>Configuración del controlador de dominio
En los pasos siguientes configurará la máquina ContosoDC como controlador de dominio para corp.contoso.com.

1. En el Portal, seleccione la máquina **ContosoDC** . En la pestaña **Panel** haga clic en **Conectar** para abrir un archivo de escritorio remoto (RDP) para el acceso de escritorio remoto.
   
    ![Conectarse a máquinas virtuales](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Inicie sesión con su cuenta de administrador configurada (**\AzureAdmin**) y la contraseña (**Contoso!000**).
3. De forma predeterminada, se debe mostrar el panel **Administrador del servidor** .
4. Haga clic en el vínculo **Agregar roles y características** en el panel.
   
    ![Explorador de servidores Agregar roles](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Haga clic en **Siguiente** hasta llegar a la sección **Roles de servidor**.
6. Seleccione los roles **Active Directory Domain Services** y **Servidor DNS**. Cuando se le solicite, agregue más características que requieran estos roles.
   
   > [!NOTE]
   > Recibirá una advertencia de validación que le indicará que no hay ninguna dirección IP estática. Si está probando la configuración, haga clic en **Continuar**. En los escenarios de producción [use PowerShell para establecer la dirección IP estática de la máquina del controlador de dominio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Diálogo Agregar roles](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Haga clic en **Siguiente** hasta llegar a la sección **Confirmación**. Active la casilla **Reiniciar automáticamente el servidor de destino en caso necesario** .
8. Haga clic en **Instalar**.
9. Cuando se instalen las características, vuelva al panel **Administrador del servidor** .
10. Seleccione la nueva opción **AD DS** en el panel izquierdo.
11. Haga clic en el vínculo **Más** en la barra de advertencia amarilla.
    
     ![Cuadro de diálogo de AD DS en la máquina virtual del servidor DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, haga clic en **Promover este servidor a controlador de dominio**.
13. En el **Asistente de configuración de Servicios de dominio de Active Directory**, use los siguientes valores:
    
    | Page | Configuración |
    | --- | --- |
    | Configuración de la implementación |**Agregar un nuevo bosque** = seleccionado<br/>**Nombre del dominio raíz** = corp.contoso.com |
    | Opciones del controlador de dominio |**Password** = Contoso!000<br/>**Confirmar contraseña** = Contoso!000 |
14. Haga clic en **Siguiente** para avanzar por las otras páginas del asistente. En la página **Comprobación de requisitos previos**, compruebe que ve el mensaje siguiente: **Todas las comprobaciones de requisitos previos se realizaron correctamente**. Tenga en cuenta que debe revisar todos los mensajes de advertencia aplicables, pero puede continuar con la instalación.
15. Haga clic en **Instalar**. La máquina virtual **ContosoDC** se reiniciará automáticamente.

## <a name="configure-domain-accounts"></a>Configuración de cuentas de dominio
Los pasos siguientes configuran las cuentas de Active Directory para su uso posterior.

1. Vuelva a iniciar sesión en la máquina **ContosoDC**.
2. En **Administrador del servidor**, haga clic en **Herramientas** > **Centro de administración de Active Directory**.
   
    ![Centro de administración de Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. En el **Centro de administración de Active Directory**, seleccione **corp (local)** en el panel izquierdo.
4. En el panel derecho **Tareas**, haga clic en **Nuevo** > **Usuario**. Use la configuración siguiente:
   
   | Configuración | Valor |
   | --- | --- |
   | **Nombre** |Instalar |
   | **Usuario NombreDeCuentaSam** |Instalar |
   | **Password** |Contoso!000 |
   | **Confirmar contraseña** |Contoso!000 |
   | **Otras opciones de contraseña** |Seleccionado |
   | **La contraseña nunca expira** |Activado |
5. Haga clic en **Aceptar** para crear el usuario **Install**. Esta cuenta se usará para configurar el clúster de conmutación por error y el grupo de disponibilidad.
6. Cree dos usuarios adicionales, **CORP\SQLSvc1** y **CORP\SQLSvc2** con los mismos pasos. Estas cuentas se utilizarán para las instancias de SQL Server. Después, tendrá que proporcionar a **CORP\Install** los permisos necesarios para configurar los clústeres de conmutación por error de Windows.
7. En el **Centro de administración de Active Directory**, haga clic en **corp (local)** en el panel izquierdo. En el panel **Tareas**, haga clic en **Propiedades**.
   
    ![Propiedades de usuario CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Seleccione **Extensiones** y luego haga clic en el botón **Avanzadas** situado en la pestaña **Seguridad**.
9. En el cuadro de diálogo **Configuración de seguridad avanzada**, haga clic en **Agregar**.
10. Haga clic en **Seleccionar una entidad de seguridad**, busque **CORP\Install** y, a continuación, haga clic en **Aceptar**.
11. Seleccione los permisos **Leer todas las propiedades** y **Crear objetos de equipo**.
    
     ![Permisos de usuario Corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Haga clic en **Aceptar** y luego vuelva a hacer clic en **Aceptar**. Cierre la ventana de propiedades de corp.

Ahora que ha configurado Active Directory y los objetos de usuario, creará tres máquinas virtuales de SQL Server y las unirá a este dominio.

## <a name="create-the-sql-server-virtual-machines"></a>Creación de máquinas virtuales de SQL Server
Cree tres máquinas virtuales. Una es para un nodo de clúster y dos para SQL Server. Para crear cada una de las máquinas virtuales, vuelva al Portal de Azure clásico, haga clic en **Nuevo** > **Compute** > **Máquina virtual** > **Desde la galería**. Después use las plantillas de la tabla siguiente para ayudarle a crear las máquinas virtuales.

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Seleccionar el sistema operativo de la máquina virtual |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| Configuración de la máquina virtual |**FECHA DE LANZAMIENTO DE LA VERSIÓN** = (última)<br/>**NOMBRE DE LA MÁQUINA VIRTUAL** = ContosoWSFCNode<br/>**NIVEL**: ESTÁNDAR<br/>**TAMAÑO** = A2 (2 núcleos)<br/>**NUEVO NOMBRE de USUARIO** = AzureAdmin<br/>**NUEVA CONTRASEÑA** = Contoso!000<br/>**CONFIRMAR** = Contoso!000 |**FECHA DE LANZAMIENTO DE LA VERSIÓN** = (última)<br/>**NOMBRE DE MÁQUINA VIRTUAL** = ContosoSQL1<br/>**NIVEL**: ESTÁNDAR<br/>**TAMAÑO** = A3 (4 núcleos)<br/>**NUEVO NOMBRE de USUARIO** = AzureAdmin<br/>**NUEVA CONTRASEÑA** = Contoso!000<br/>**CONFIRMAR** = Contoso!000 |**FECHA DE LANZAMIENTO DE LA VERSIÓN** = (última)<br/>**NOMBRE DE MÁQUINA VIRTUAL** = ContosoSQL2<br/>**NIVEL**: ESTÁNDAR<br/>**TAMAÑO** = A3 (4 núcleos)<br/>**NUEVO NOMBRE de USUARIO** = AzureAdmin<br/>**NUEVA CONTRASEÑA** = Contoso!000<br/>**CONFIRMAR** = Contoso!000 |
| Configuración de la máquina virtual |**SERVICIO EN LA NUBE** = nombre DNS del servicio en la nube único creado anteriormente (por ejemplo, ContosoDC123)<br/>**REGION/GRUPO DE AFINIDAD/RED VIRTUAL** = ContosoNET<br/>**SUBREDES DE LA RED VIRTUAL** = Back(10.10.2.0/24)<br/>**CUENTA DE ALMACENAMIENTO** = usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO DE DISPONIBILIDAD** = Crear un conjunto de disponibilidad<br/>**NOMBRE DEL CONJUNTO DE DISPONIBILIDAD** = SQLHADR |**SERVICIO EN LA NUBE** = nombre DNS del servicio en la nube único creado anteriormente (por ejemplo, ContosoDC123)<br/>**REGION/GRUPO DE AFINIDAD/RED VIRTUAL** = ContosoNET<br/>**SUBREDES DE LA RED VIRTUAL** = Back(10.10.2.0/24)<br/>**CUENTA DE ALMACENAMIENTO** = usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO de DISPONIBILIDAD** = SQLHADR (también puede configurar el conjunto de disponibilidad una vez creada la máquina). Las tres máquinas deben asignarse al conjunto de disponibilidad SQLHADR). |**SERVICIO EN LA NUBE** = nombre DNS del servicio en la nube único creado anteriormente (por ejemplo, ContosoDC123)<br/>**REGION/GRUPO DE AFINIDAD/RED VIRTUAL** = ContosoNET<br/>**SUBREDES DE LA RED VIRTUAL** = Back(10.10.2.0/24)<br/>**CUENTA DE ALMACENAMIENTO** = usar una cuenta de almacenamiento generada automáticamente<br/>**CONJUNTO de DISPONIBILIDAD** = SQLHADR (también puede configurar el conjunto de disponibilidad una vez creada la máquina). Las tres máquinas deben asignarse al conjunto de disponibilidad SQLHADR). |
| Opciones de la máquina virtual |Usar predeterminados |Usar predeterminados |Usar predeterminados |

<br/>

> [!NOTE]
> La configuración anterior le sugiere usar máquinas virtuales de nivel ESTÁNDAR, porque las máquinas de nivel BÁSICO no admiten puntos de conexión de carga equilibrada. Necesitará puntos de conexión de carga equilibrada más adelante para crear un agente de escucha del grupo de disponibilidad. Asimismo, los tamaños de máquina que se sugieren aquí están diseñados para probar los grupos de disponibilidad en Azure Virtual Machines. Para optimizar el rendimiento de las cargas de trabajo de producción, consulte las recomendaciones de tamaños de máquina y la configuración de SQL Server en [Procedimientos recomendados de SQL Server en máquinas virtuales de Azure](../sql/virtual-machines-windows-sql-performance.md).
> 
> 

Una vez que las tres máquinas virtuales estén totalmente aprovisionadas, tendrá que unirlas al dominio **corp.contoso.com** y conceder a CORP\Install los derechos administrativos sobre las máquinas. Para ello, siga estos pasos para cada una de las tres máquinas virtuales.

1. En primer lugar, cambie la dirección del servidor DNS preferido. Descargue el archivo RDP de cada máquina virtual en el directorio local; para ello, seleccione la máquina virtual en la lista y haga clic en el botón **Conectar** . Para seleccionar una máquina virtual, haga clic en cualquier celda que no sea la primera de la fila, tal y como se muestra en la captura de pantalla siguiente.
   
    ![Descarga del archivo RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Abra el archivo RDP que descargó e inicie sesión en la máquina virtual con la cuenta de administrador configurada (**BUILTIN\AzureAdmin**) y la contraseña (**Contoso!000**).
3. Una vez iniciada la sesión, debería ver el panel **Administrador del servidor** . En el panel izquierdo, haga clic en **Servidor local** .
4. Haga clic en el vínculo **Dirección IPv4 asignada por DHCP, IPv6 habilitado** .
5. En el cuadro de diálogo **Conexiones de red** , haga clic en el icono de red.
   
    ![Cambio del servidor DNS de la máquina virtual preferido](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. En la barra de comandos, haga clic en **Cambiar la configuración de esta conexión**. (Según el tamaño de la ventana, tendrá que hacer clic en la flecha doble que está mirando hacia la derecha para ver este comando).
7. Seleccione **Protocolo de Internet versión 4 (TCP/IPv4)** y luego haga clic en **Propiedades**.
8. Seleccione **Usar las siguientes direcciones de servidor DNS** y luego especifique **10.10.2.4** en **Servidor DNS preferido**.
9. La dirección **10.10.2.4** es la que se asigna a una máquina virtual en la subred 10.10.2.0/24 de una instancia de Azure Virtual Network. La máquina virtual es **ContosoDC**. Para comprobar la dirección IP de **ContosoDC**, use **nslookup contosodc** en la ventana del símbolo del sistema , según se muestra en la captura de pantalla siguiente.
   
    ![Uso de NSLOOKUP para buscar la dirección IP para el controlador de dominio](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Haga clic en **Aceptar** > **Cerrar** para confirmar los cambios. Ahora puede unirse a la máquina virtual en **corp.contoso.com**.
11. De vuelta en la ventana **Servidor Local**, haga clic en el vínculo **GRUPO_DE_TRABAJO**.
12. En la sección **Nombre de equipo**, haga clic en **Cambiar**.
13. Active la casilla **Dominio** y escriba **corp.contoso.com** en el cuadro de texto y luego haga clic en **Aceptar**.
14. En el cuadro de diálogo **Seguridad de Windows**, especifique las credenciales de la cuenta de administrador del dominio predeterminado (**CORP\AzureAdmin**) y la contraseña (**Contoso!000**).
15. Cuando vea el mensaje "Bienvenida al dominio corp.contoso.com", haga clic en **Aceptar**.
16. Haga clic en **Cerrar** > **Reiniciar ahora** en el cuadro de diálogo.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>Adición del usuario Corp\Install como administrador en cada máquina virtual
1. Espere hasta que la máquina virtual se reinicie y luego abra el archivo RDP de nuevo archivo para iniciar sesión en la máquina virtual mediante la cuenta **BUILTIN\AzureAdmin**.
2. En **Administrador de servidores**, haga clic en **Herramientas** > **Administración de equipos**.
   
    ![Administración de equipos](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. En el cuadro de diálogo **Administración de equipos**, expanda **Usuarios y grupos locales** y luego haga clic en **Grupos**.
4. Haga doble clic en el grupo **Administradores** .
5. En el cuadro de diálogo **Propiedades de administradores**, haga clic en el botón **Agregar**.
6. Especifique el usuario **CORP\Install** y haga clic en **Aceptar**. Cuando se le pidan las credenciales, use la cuenta **AzureAdmin** con la contraseña **Contoso!000**.
7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de administradores**.

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>Adición de la característica Clústeres de conmutación por error a todas las máquinas virtuales
1. En el panel **Administrador del servidor** haga clic en **Agregar roles y características**.
2. En el **Asistente para agregar roles y características**, haga clic en **Siguiente** hasta llegar a la página **Características**.
3. Seleccione **Clústeres de conmutación por error**. Cuando se le solicite, agregue otras las características dependientes.
   
    ![Adición de la característica Clústeres de conmutación por error a la máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Haga clic en **Siguiente** y después en **Instalar** en la página **Confirmación**.
5. Cuando la instalación de la característica **Clústeres de conmutación por error** esté finalizada, haga clic en **Cerrar**.
6. Cierre sesión en la máquina virtual.
7. Repita los pasos de esta sección para los tres servidores: **ContosoWSFCNode**, **ContosoSQL1** y **ContosoSQL2**.

Ahora se aprovisionan máquinas virtuales de SQL Server y se ejecutan, pero cada una tiene las opciones predeterminadas para SQL Server.

## <a name="create-the-failover-cluster"></a>Creación del clúster de conmutación por error
En esta sección, cree el clúster de conmutación por error que hospedará el grupo de disponibilidad que creará más adelante. Por ahora, debería haber hecho lo siguiente para cada una de las tres máquinas virtuales que se usarán en el clúster de conmutación por error:

* Aprovisionamiento completo de la máquina virtual en Azure.
* Máquina virtual unida al dominio.
* Se ha agregado **CORP\Install** al grupo de administradores locales
* Se agregó la característica Clústeres de conmutación por error.

Todos estos son los requisitos previos en cada máquina virtual para que pueda unirse al clúster de conmutación por error.

Además, tenga en cuenta que la red virtual de Azure no se comporta de la misma manera que una red local. Tendrá que crear el clúster en el orden siguiente:

1. Cree un clúster de nodo único en un nodo (**ContosoSQL1**).
2. Modifique la dirección IP del clúster en una dirección IP sin usar (**10.10.2.101**).
3. Ponga el nombre de clúster en línea.
4. Agregue los demás nodos (**ContosoSQL2** y **ContosoWSFCNode**).

Utilice los pasos siguientes para completar las tareas que configuran totalmente el clúster.

1. Abra el archivo RDP de **ContosoSQL1** e inicie sesión con la cuenta de dominio **CORP\Install**.
2. En el panel **Administrador del servidor**, haga clic en **Herramientas** > **Administrador de clústeres de conmutación por error**.
3. En el panel izquierdo, haga clic con el botón derecho en **Administrador de clústeres de conmutación por error** y, después, haga clic en **Crear un clúster** en la siguiente captura de pantalla.
   
    ![Crear clúster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. En el Asistente para crear clúster, cree un clúster de un solo nodo avanzando por las páginas y con la configuración de la tabla siguiente:
   
   | Page | Configuración |
   | --- | --- |
   | Antes de empezar |Usar predeterminados |
   | Seleccionar servidores |Escriba **ContosoSQL1** en **Especifique el nombre del servidor** y haga clic en **Agregar**. |
   | Advertencia de validación |Seleccione **No. No necesito compatibilidad con Microsoft para este clúster y por tanto no deseo ejecutar las pruebas de validación. Al hacer clic en Siguiente, continuar con la creación del clúster**. |
   | Punto de acceso para administrar el clúster |Escriba **Cluster1** en **Nombre de clúster** |
   | Confirmación |Use los valores predeterminados a menos que use Espacios de almacenamiento. Consulte la advertencia que sigue a esta tabla. |
   
   > [!WARNING]
   > Si está usando [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739), que agrupa varios discos en grupos de almacenamiento, tiene que desactivar la casilla **Agregar todo el almacenamiento apto al clúster** en la página **Confirmación**. Si no desactiva esta opción, los discos virtuales se desasociarán durante el proceso de agrupación en clústeres. Como resultado, tampoco aparecerán en el Administrador de discos o el explorador hasta que se quiten los espacios de almacenamiento del clúster y se vuelvan a asociar mediante PowerShell.
   > 
   > 
5. En el panel izquierdo, expanda **Administrador de clústeres de conmutación por error** y, a continuación, haga clic en **Cluster1.corp.contoso.com**.
6. En el panel central, desplácese hacia abajo hasta la sección **Recursos principales de clúster** y expanda los detalles de **Nombre: Cluster1**. Debería de ver los recursos **Nombre** y **Dirección IP** en el estado **Con error**. El recurso de dirección IP no se puede poner en línea porque al clúster se le asigna la misma dirección IP que la de la propia máquina, que es una dirección duplicada.
7. Haga clic con el botón secundario en el recurso **dirección IP** erróneo y después haga clic en **Propiedades**.
   
    ![Propiedades de clúster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Seleccione **Dirección IP estática**, especifique **10.10.2.101** en el cuadro de texto **Dirección** y haga clic en **Aceptar**.
9. En la sección **Recursos principales de clúster**, haga clic con el botón derecho en **Nombre: Cluster1** y luego haga clic en **Poner en línea**. Espere hasta que los recursos estén en línea. Cuando el recurso de nombre de clúster esté en línea, el servidor DC se actualiza con una nueva cuenta de equipo de Active Directory. Esta cuenta de Active Directory se usará para ejecutar más tarde el servicio de clúster del grupo de disponibilidad.
10. Agregue los nodos restantes al clúster. En el árbol del explorador, haga clic con el botón derecho en **Cluster1.corp.contoso.com** y luego haga clic en **Agregar nodo**, tal como se muestra en la captura de pantalla siguiente.
    
     ![Adición de un nodo al clúster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. En la página **Asistente para agregar nodo**, haga clic en **Siguiente** en la página **Seleccionar servidores**, agregue**ContosoSQL2** y **ContosoWSFCNode** a la lista escribiendo el nombre del servidor en **Especifique el nombre de servidor** y luego haga clic en **Agregar**. Cuando haya terminado, haga clic en **Siguiente**.
12. En la página **Advertencia de validación**, haga clic en **No** (aunque en un escenario de producción debe realizar las pruebas de validación). A continuación, haga clic en **Siguiente**.
13. En la página **Confirmación**, haga clic en **Siguiente** para agregar los nodos.
    
    > [!WARNING]
    > Si está usando [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739), que agrupa varios discos en grupos de almacenamiento, tiene que desactivar la casilla **Agregar todo el almacenamiento apto al clúster** . Si no desactiva esta opción, los discos virtuales se desasociarán durante el proceso de agrupación en clústeres. Como resultado, tampoco aparecerán en el Administrador de discos o el explorador hasta que se quiten los espacios de almacenamiento del clúster y se vuelvan a asociar mediante PowerShell.
    > 
    > 
14. Una vez que se agregan los nodos al clúster, haga clic en **Finalizar**. Ahora el Administrador de clústeres de conmutación por error tiene que indicar que el clúster tiene tres nodos y mostrarlos en una lista en el contenedor **Nodos** .
15. Cierre sesión en la sesión de escritorio remoto.

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>Preparación de las instancias de SQL Server para el grupo de disponibilidad
En esta sección, hará lo siguiente tanto en **ContosoSQL1** como en **ContosoSQL2**:

* Agregar un inicio de sesión para **NT AUTHORITY\System** con un conjunto de permisos necesarios preestablecido para la instancia de SQL Server.
* Agregar **CORP\Install** como un rol sysadmin a la instancia de SQL Server predeterminada.
* Abra el firewall para el acceso remoto de SQL Server.
* Habilitar la característica Grupos de disponibilidad AlwaysOn.
* Cambiar la cuenta de servicio de SQL Server a **CORP\SQLSvc1** y **CORP\SQLSvc2**, respectivamente.

Estas acciones pueden realizarse en cualquier orden. No obstante, los pasos siguientes se seguirán en orden. Siga los pasos para **ContosoSQL1** y **ContosoSQL2**:

1. Si no ha cerrado la sesión de escritorio remoto para la máquina virtual, hágalo ahora.
2. Abra los archivos RDP para **ContosoSQL1** y **ContosoSQL2** e inicie sesión como **BUILTIN\AzureAdmin**.
3. Agregue **NT AUTHORITY\System** a los inicios de sesión de SQL Server con los permisos necesarios. Abra **SQL Server Management Studio**.
4. Haga clic en **Conectar** para conectarse a la instancia predeterminada de SQL Server.
5. En el **Explorador de objetos**, expanda **Seguridad** y luego expanda **Inicios de sesión**.
6. Haga clic con el botón derecho en el inicio de sesión **NT AUTHORITY\System** y luego haga clic en **Propiedades**.
7. En la página **Elementos protegibles**  para el servidor local, seleccione **Conceder** para los siguientes permisos y luego haga clic en **Aceptar**.
   
   * Modificar cualquier grupo de disponibilidad
   * Conectar SQL
   * Ver estado del servidor
8. Agregar **CORP\Install** como un rol **sysadmin** a la instancia de SQL Server predeterminada. En el **Explorador de objetos**, haga clic con el botón derecho en **Inicios de sesión** y luego en **Nuevo inicio de sesión**.
9. Escriba **CORP\Install** en **Nombre de inicio de sesión**.
10. En la página **Roles de servidor**, seleccione **sysadmin** y luego haga clic en **Aceptar**. Una vez creado el inicio de sesión, puede verlo expandiendo **Inicios de sesión** en **Explorador de objetos**.
11. Para crear una regla de firewall para SQL Server, en la pantalla de **inicio**, abra **Firewall de Windows con seguridad avanzada**.
12. En el panel izquierdo, seleccione **Reglas de entrada**. En el panel derecho, haga clic en **Nueva regla**.
13. En la página **Tipo de regla**, haga clic en **Programa** > **Siguiente**.
14. En la página **Programa**, seleccione **Esta ruta de acceso del programa**, escriba **%ProgramFiles%\Microsoft SQL Server\MSSQL12. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** en el cuadro de texto y luego haga clic en **Siguiente**. Si está siguiendo estas instrucciones, pero usa SQL Server 2012, el directorio de SQL Server es **MSSQL11. MSSQLSERVER**.
15. En la página **Acción**, mantenga seleccionado **Permitir la conexión** y luego haga clic en **Siguiente**.
16. En la página **Perfiles**, acepte la configuración predeterminada y luego haga clic en **Siguiente**.
17. En la página **Nombre**, especifique el nombre de una regla, como **SQL Server (regla de programa)** en el cuadro de texto **Nombre** y haga clic en **Finalizar**.
18. Para habilitar la característica **Grupos de disponibilidad AlwaysOn**, en la pantalla de **inicio**, abra **Administrador de configuración de SQL Server**.
19. En el árbol del explorador, haga clic en **Servicios SQL Server**, luego haga clic con el botón derecho en el servicio **SQL Server (MSSQLSERVER)** y luego haga clic en **Propiedades**.
20. Haga clic en la pestaña **Alta disponibilidad AlwaysOn** y seleccione **Habilitar los grupos de disponibilidad de AlwaysOn**, tal como se muestra en la siguiente captura de pantalla, y haga clic en **Aplicar**. Haga clic en **Aceptar** en el cuadro de diálogo y no cierre el cuadro de diálogo **Propiedades** aún. Reiniciará el servicio SQL Server tras cambiar la cuenta de servicio.
    
     ![Habilitación de grupos de disponibilidad AlwaysOn](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Para cambiar la cuenta de servicio de SQL Server, haga clic en la pestaña **Iniciar sesión**, escriba **CORP\SQLSvc1** (para **ContosoSQL1**) o **CORP\SQLSvc2** (para **ContosoSQL2**) en **Nombre de cuenta**, escriba y confirme la contraseña y luego haga clic en **Aceptar**.
22. En el cuadro de diálogo que se abre, haga clic en **Sí** para reiniciar el servicio de SQL Server. Después de reiniciar el servicio SQL Server, se aplican los cambios que realizó en el cuadro de diálogo **Propiedades**.
23. Cierre sesión en la máquina virtual.

## <a name="create-the-availability-group"></a>Creación del grupo de disponibilidad
Ahora está en disposición de configurar un grupo de disponibilidad. A continuación se resume lo que debe hacer:

* Crear una nueva base de datos (**MyDB1**) en **ContosoSQL1**.
* Realizar una copia de seguridad completa y una copia de seguridad del registro de transacciones de la base de datos.
* Restaurar las copias de seguridad completas y de registro en **ContosoSQL2** con la opción **NORECOVERY**.
* Crear el grupo de disponibilidad (**AG1**) con confirmación sincrónica, conmutación automática por error y réplicas secundarias legibles.

### <a name="create-the-mydb1-database-on-contososql1"></a>Creación de la base de datos de MyDB1 en ContosoSQL1
1. Si no cerró aún las sesiones de escritorio remoto para **ContosoSQL1** y **ContosoSQL2**, hágalo ahora.
2. Abra el archivo RDP para **ContosoSQL1** e inicie sesión como **CORP\Install**.
3. En el **Explorador de archivos**, en **\\C:**, cree un directorio denominado **backup**. Usará este directorio para la copia de seguridad y restauración de la base de datos.
4. Haga clic con el botón derecho en el nuevo directorio, seleccione **Compartir con** y luego haga clic en **Personas específicas**, tal como se muestra en la siguiente captura de pantalla.
   
    ![Creación de una carpeta de copia de seguridad](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Agregue **CORP\SQLSvc1** y luego asígnele el permiso de **lectura/escritura**. Agregue **CORP\SQLSvc2** y luego asígnele el permiso de **lectura**, como se muestra en la siguiente captura de pantalla y, a continuación, haga clic en **Compartir**. Una vez que se complete el proceso de uso compartido de archivos, haga clic en **Listo**.
   
    ![Concesión de permisos para la carpeta de copia de seguridad](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Para crear la base de datos, desde el menú **Inicio**, abra **SQL Server Management Studio** y luego haga clic en **Conectar** para conectar con la instancia predeterminada de SQL Server.
7. En el **Explorador de objetos**, haga clic con el botón derecho en **Bases de datos** y, después, haga clic en **Nueva base de datos**.
8. En **Nombre de base de datos**, escriba **MyDB1** y después haga clic en **Aceptar**.

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Realización de una copia de seguridad completa de MyDB1 y restauración en ContosoSQL2
1. Para realizar una copia de seguridad completa de la base de datos, en el **Explorador de objetos**, expanda **Bases de datos**, luego haga clic con el botón derecho en **MyDB1**, seleccione **Tareas** y, finalmente, haga clic en **Copia de seguridad**.
2. En la sección **Origen**, mantenga el **Tipo de copia de seguridad** establecido en **Completa**. En la sección **Destino**, haga clic en **Quitar** para quitar la ruta de acceso del archivo predeterminado para el archivo de copia de seguridad.
3. En la sección **Destino**, haga clic en **Agregar**.
4. En el cuadro de texto **Nombre de archivo**, escriba  **\\ContosoSQL1\backup\MyDB1.bak**, haga clic en **Aceptar** y, a continuación, haga clic en **Aceptar** de nuevo para realizar la copias de seguridad de la base de datos. Cuando finalice la operación de copia de seguridad, vuelva a hacer clic en **Aceptar** para cerrar el cuadro de diálogo.
5. Para realizar una copia de seguridad de registro de transacciones de la base de datos, en el **Explorador de objetos**, expanda **Bases de datos**, luego haga clic con el botón derecho en **MyDB1**, seleccione **Tareas** y, finalmente, haga clic en **Copia de seguridad**.
6. En **Tipo de copia de seguridad**, seleccione **Registro de transacciones**. Mantenga la ruta de acceso al archivo de **Destino** establecida en la que ha especificado anteriormente y luego haga clic en **Aceptar**. Una vez se complete la operación de copia de seguridad, haga clic de nuevo en **Aceptar** .
7. Para restaurar las copias de seguridad de registro de transacciones y completas en **ContosoSQL2**, abra el archivo RDP para **ContosoSQL2** e inicie sesión como **CORP\Install**. Deje abierta la sesión de escritorio remoto para **ContosoSQL1** .
8. Desde el menú **Inicio**, abra **SQL Server Management Studio** y luego haga clic en **Conectar** para conectar con la instancia predeterminada de SQL Server.
9. En el **Explorador de objetos**, haga clic con el botón derecho en **Bases de datos** y luego en **Restaurar base de datos**.
10. En la sección **Origen**, seleccione **Dispositivo** y haga clic en el botón de puntos suspensivos **...** .
11. En **Seleccionar dispositivos de copia de seguridad**, haga clic en **Agregar**.
12. En la **ubicación del archivo de copia de seguridad**, escriba **\\ContosoSQL1\backup**, haga clic en **Actualizar**, a continuación, seleccione **MyDB1.bak** y haga clic en **Aceptar**, luego haga clic de nuevo en **Aceptar** . Ahora debería ver la copia de seguridad completa y la copia de seguridad del registro en el panel **Conjuntos de copia de seguridad para restaurar**.
13. Vaya a la página **Opciones**, seleccione **RESTAURAR CON NORECOVERY** en **Estado de recuperación** y, a continuación, haga clic en **Aceptar** para restaurar la base de datos. Una vez se complete la operación de restauración, haga clic en **Aceptar** .

### <a name="create-the-availability-group"></a>Creación del grupo de disponibilidad
1. Vuelva a la sesión de escritorio remoto para **ContosoSQL1**. En el **Explorador de objetos** de SQL Server Management Studio, haga clic con el botón derecho en **Alta disponibilidad AlwaysOn** y luego haga clic en **Asistente para nuevo grupo de disponibilidad** como se muestra en la siguiente captura de pantalla.
   
    ![Inicio del Asistente para nuevo grupo de disponibilidad](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. En la página **Introducción**, haga clic en **Siguiente**. En la página **Especificar nombre de grupo de disponibilidad**, escriba **AG1** en **Nombre del grupo de disponibilidad** y luego vuelva a hacer clic en **Siguiente**.
   
    ![Asistente para nuevo grupo de disponibilidad: Especificación del nombre de grupo de disponibilidad](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. En la página **Seleccionar bases de datos**, seleccione **MyDB1** y luego haga clic en **Siguiente**. La base de datos cumple los requisitos previos para un grupo de disponibilidad porque ha realizado al menos una copia de seguridad completa en la réplica principal pretendida.
   
    ![Asistente para nuevo grupo de disponibilidad: Selección de base de datos](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. En la página **Especificar réplicas**, haga clic en **Agregar réplica**.
   
    ![Asistente para nuevo grupo de disponibilidad: Especificación de réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. En el cuadro de diálogo **Conectar al servidor**, escriba **ContosoSQL2** en **nombre del servidor** y, a continuación, haga clic en **Conectar**.
   
    ![Asistente para nuevo grupo de disponibilidad: Conexión al servidor](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. De vuelta en la página **Especificar réplicas**, ahora debería ver **ContosoSQL2** en **Réplicas de disponibilidad**. Configure las réplicas como se muestra en la siguiente captura de pantalla. Cuando haya terminado, haga clic en **Siguiente**.
   
    ![Asistente para nuevo grupo de disponibilidad: Especificación de réplicas (completo)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. En la página **Seleccionar sincronización de datos iniciales**, seleccione **Solo unión** y luego haga clic en **Siguiente**. Ya realizó la sincronización de datos de forma manual cuando efectuó las copias de seguridad completas y de transacciones en **ContosoSQL1** y las restauró en **ContosoSQL2**. Puede elegir no realizar las operaciones de copia de seguridad y restauración en la base de datos y seleccionar **Total** para permitir que el Asistente del nuevo grupo de disponibilidad realice la sincronización de datos en su lugar. Sin embargo, no se recomienda esta opción para bases de datos de gran tamaño como las que se encuentran en algunas empresas.
   
    ![Asistente para nuevo grupo de disponibilidad, seleccionar sincronización de datos iniciales](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. En la página **Validación**, haga clic en **Siguiente**. Esta página debe tener un aspecto similar a la siguiente captura de pantalla. Hay una advertencia para la configuración del agente de escucha porque no ha configurado un agente de escucha de grupo de disponibilidad. Puede omitir esta advertencia, ya que este tutorial no configura un agente de escucha. Para configurar el agente de escucha después de completar este tutorial, consulte [Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure](../classic/ps-sql-int-listener.md).
   
    ![Asistente para nuevo grupo de disponibilidad: Validación](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. En la página **Resumen**, haga clic en **Finalizar** y espere hasta que el asistente configure el nuevo grupo de disponibilidad. En la página **Progreso**, puede hacer clic en **Más detalles** para ver el progreso detallado. Cuando el asistente termine, inspeccione la página **Resultados** para comprobar que se ha creado correctamente el grupo de disponibilidad, como se muestra en la siguiente captura de pantalla, y después haga clic en **Cerrar** para salir del asistente.
   
    ![Asistente para nuevo grupo de disponibilidad: Resultados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. En el **Explorador de objetos**, expanda **Alta disponibilidad AlwaysOn** y después, expanda **Grupos de disponibilidad**. Ahora debería de ver el nuevo grupo de disponibilidad en este contenedor. Haga clic con el botón derecho en **AG1 (principal)** y luego en **Mostrar panel**.
    
     ![Visualización del panel del grupo de disponibilidad](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. El **Panel AlwaysOn** debería ser similar al que se muestra en la captura de pantalla siguiente. Puede ver las réplicas, el modo de conmutación por error de cada réplica y el estado de sincronización.
    
     ![Panel del grupo de disponibilidad](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Vuelva a **Administrador del servidor**, haga clic en **Herramientas** y, después, inicie **Administrador de clústeres de conmutación por error**.
13. Expanda **Cluster1.corp.contoso.com** y expanda **Servicios y aplicaciones**. Elija **Roles** y tenga en cuenta que se ha creado el rol del grupo de disponibilidad **AG1**. Tenga en cuenta que AG1 no tiene ninguna dirección IP por la que los clientes de la base de datos puedan conectarse al grupo de disponibilidad, porque no configuró ningún agente de escucha. Puede conectarse directamente al nodo principal para las operaciones de lectura y escritura y al nodo secundario para las consultas de solo lectura.
    
     ![Grupo de disponibilidad en el administrador de clústeres de conmutación por error.](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> No trate de realizar una conmutación por error del grupo de disponibilidad desde el Administrador de clústeres de conmutación por error. Todas las operaciones de conmutación por error deben realizarse desde el **Panel AlwaysOn** de SQL Server Management Studio. Para obtener más información, consulte el artículo de [restricciones en el uso del Administrador de clústeres de conmutación por error con grupos de disponibilidad](https://msdn.microsoft.com/library/ff929171.aspx).
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Ha implementado correctamente SQL Server AlwaysOn mediante la creación de un grupo de disponibilidad en Azure. Para configurar un agente de escucha para este grupo de disponibilidad, consulte [Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure](../classic/ps-sql-int-listener.md).

Para obtener más información sobre el uso de SQL Server en Azure, consulte [SQL Server en Máquinas virtuales de Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).


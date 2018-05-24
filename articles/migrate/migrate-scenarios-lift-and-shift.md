---
title: Migración de cargas de trabajo a Azure con Azure DMS y Site Recovery | Microsoft Docs
description: Aprenda a preparar a Azure para la migración de máquinas locales mediante Azure Database Migration Service y el servicio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182245"
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>Escenario 2: Migración a Azure mediante lift-and-shift

La empresa Contoso está pensando en migrar a Azure. Para hacer una prueba, quieren evaluar y migrar una pequeña aplicación de viajes local a Azure. Se trata de una aplicación de dos niveles en la que una aplicación web se ejecuta en una máquina virtual y una base de datos de SQL Server en la segunda máquina virtual. La aplicación se implementa en VMware, y el entorno lo controla vCenter Server. 

En [Escenario 1: Evaluación para la migración a Azure](migrate-scenarios-assessment.md), se usó Data Migration Assistant (DMA) para evaluar la base de datos de SQL Server para la aplicación. Además, se utilizó el servicio Azure Migrate para evaluar las máquinas virtuales de la aplicación. Ahora, en este escenario, una vez que se ha completado correctamente la evaluación, se quiere migrar la base de datos a una instancia administrada de Azure SQL, usando Azure Database Migration Service (DMS) y máquinas locales a las máquinas virtuales de Azure con el servicio de Azure Site Recovery.

Si quiere probar el [Escenario 1](migrate-scenarios-assessment.md), y luego este escenario utilizando esta aplicación de viajes ilustrativa, puede descargarla en [github](https://github.com/Microsoft/SmartHotel360).



**Servicio** | **Descripción** | **Costee
--- | --- | ---
[Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | DMS facilita las migraciones sin problemas desde varios orígenes de base de datos a plataformas de datos de Azure, con un tiempo de inactividad mínimo. | El servicio está actualmente en versión preliminar pública (abril de 2018) y se puede utilizar de forma gratuita durante la etapa de versión preliminar.<br/><br/> Tenga en cuenta que para la versión preliminar pública, DMS está disponible en un [número limitado de regiones](https://docs.microsoft.com/azure/dms/dms-overview).
[Instancia administrada de Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Instancia administrada es un servicio de base de datos administrada que representa una instancia de SQL Server completamente administrada en la nube de Azure. Comparte el mismo código con la versión más reciente del motor de base de datos de SQL Server y tiene las características, mejoras de rendimiento y revisiones de seguridad más recientes. | El uso de instancias administradas de Azure SQL Database que se ejecutan en Azure incurrirá en gastos basados en la capacidad. [Más información](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | El servicio orquesta y administra la migración y la recuperación ante desastres de las máquinas virtuales de Azure, así como las máquinas virtuales y servidores físicos locales.  | Durante la replicación en Azure, se incurre en gastos de Azure Storage.  Las máquinas virtuales de Azure se crean, e incurren en gastos, cuando se produce una conmutación por error. [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre cargos y precios.

En este escenario, vamos a configurar una VPN de sitio a sitio para que DMS pueda conectarse a la base de datos local, crear una instancia administrada de Azure SQL en Azure y migrar la base de datos. Para Site Recovery, se deberá preparar el entorno de VMware local, configurar la replicación y migrar las máquinas virtuales a Azure.  


> [!IMPORTANT]
> Es necesario estar inscrito en la versión preliminar pública limitada de la instancia administrada de SQL. También es necesaria una suscripción a Azure para [registrarse](https://portal.azure.com#create/Microsoft.SQLManagedInstance). El registro puede tardar unos días hasta completarse, asegúrese de que lo tiene todo listo antes de empezar a implementar este escenario.

## <a name="architecture"></a>Architecture

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

En este escenario:

- Contoso es un nombre ficticio que representa una organización empresarial típica. Contoso quiere evaluar y migrar su aplicación de viajes local de dos niveles.
- Contoso tiene un centro de datos local (contoso-datacenter), con un controlador de dominio local (**contosodc1**).
- La aplicación de viajes interna se organiza en niveles en dos máquinas virtuales, WEBVM y SQLVM, y se encuentra en el host de VMware ESXi (**contosohost1.contoso.com**).
- El entorno de VMware lo administra vCenter Server (**vcenter.contoso.com**), que se ejecuta en una máquina virtual.

## <a name="prerequisites"></a>requisitos previos

Si desea ejecutar este escenario, esto es lo que debe tener.

Requisitos | Detalles
--- | ---
**Suscripción de Azure** | Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si crea una cuenta gratuita, será el administrador de su suscripción y podrá realizar todas las acciones.<br/><br/> Si usa una suscripción existente y no es el administrador, tendrá que solicitar al administrador que le asigne permisos de propietario o colaborador.<br/><br/> Si necesita permisos más específicos, consulte [este artículo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Recuperación del sitio (local)** | Una instancia local de vCenter Server que ejecute las versiones 5.5, 6.0 o 6.5.<br/><br/> Un host ESXi que ejecute la versión 5.5, 6.0 o 6.5<br/><br/> Una o más máquinas virtuales VMware que se ejecuten en el host ESXi.<br/><br/> Las máquinas virtuales tienen que cumplir los [requisitos de Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuración de [red](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) y [almacenamiento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) compatible.
**DMS** | Para DMS necesita un [dispositivo VPN local compatible](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Tiene que poder configurar el dispositivo VPN local. Tiene que tener una dirección IPv4 pública externa, y la dirección no puede encontrarse detrás de un dispositivo NAT.<br/><br/> Asegúrese de que tiene acceso a la base de datos local de SQL Server.<br/><br/> El Firewall de Windows debe poder obtener acceso al motor de base de datos de origen. [Más información](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Si hay un firewall delante de la máquina de la base de datos, agregue reglas para permitir el acceso a la base de datos y a los archivos a través del puerto 445 de SMB.<br/><br/> Las credenciales usadas para conectar con la instancia de SQL Server de origen y la instancia administrada de destino tienen que ser miembros del rol de servidor sysadmin.<br/><br/> Necesita un recurso compartido de red en la base de datos local que GMS pueda usar para hacer copia de seguridad de la base de datos de origen.<br/><br/> Asegúrese de que la cuenta de servicio que ejecuta la instancia de SQL Server de origen tenga privilegios de escritura sobre el recurso compartido de red.<br/><br/> Anote un usuario de Windows (y una contraseña) que tenga privilegio de control total sobre el recurso compartido de red. Azure Database Migration Service suplanta estas credenciales de usuario para cargar los archivos de copia de seguridad en el contenedor de Azure Storage.<br/><br/> El proceso de instalación de SQL Server Express establece el protocolo TCP/IP en **deshabilitado** de forma predeterminada. Asegúrese de que esté habilitado.


# <a name="scenario-overview"></a>Información general de escenario

Esto es lo que vamos a hacer:

> [!div class="checklist"]
> * **Paso 1: Configurar una conexión VPN de sitio a sitio**: DMS se conecta a la base de datos local mediante una conexión de sitio a sitio VPN. Creamos una red virtual para la conexión VPN y después, la volvemos a utilizar para Site Recovery. La red que cree tiene que encontrarse en la región en la que cree un almacén de Recovery Services.
> * **Paso 2: Configurar una instancia administrada de SQL Azure**: necesita una instancia administrada creada previamente a la que se migrará la base de datos de SQL Server local.
> * **Paso 3: Configurar un URI de SAS para DMS**: un identificador uniforme de recursos (URI) de firma de acceso compartido (SAS) proporciona acceso delegado a los recursos en la cuenta de almacenamiento, por lo que puede conceder permisos limitados a los objetos del almacenamiento. Configurar un URI de SAS para que DMS pueda acceder al contenedor de la cuenta de almacenamiento a la que el servicio carga los archivos de copia de seguridad de SQL Server.
> * **Paso 4: Preparar DMS**: registre el proveedor de Database Migration, cree una instancia y, a continuación, cree un proyecto DMS.
> * **Paso 5: Preparar Azure para Site Recovery**: cree una cuenta de almacenamiento de Azure para contener los datos replicados.
> * **Paso 6: Preparar VMware local para Site Recovery**: prepare las cuentas para la detección de máquinas virtuales y la instalación del agente, y prepárese para conectar a las máquinas virtuales de Azure tras una conmutación por error. 
> * **Paso 7: Replicar máquinas virtuales**: configure el entorno de origen y de destino de Site Recovery, configure una directiva de replicación y empiece a replicar las máquinas virtuales en Azure Storage.
> * **Paso 8: Migrar la base de datos con DMS**: ejecute una migración de base de datos.
> * **Paso 9: Migrar las máquinas virtuales con Site Recovery**: ejecute una conmutación por error para migrar las máquinas virtuales a Azure.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>Paso 1: Configurar una conexión VPN de sitio a sitio

Para prepararse para la conexión VPN de sitio a sitio , configure una red virtual y subredes, cree una puerta de enlace VPN para la red virtual y configure una puerta de enlace de red local para que Azure sepa cómo conectarse a la VPN local. A continuación, cree y compruebe la conexión de sitio a sitio.

### <a name="set-up-a-virtual-network"></a>Configuración de una red virtual

Cree una red virtual de Azure para proporcionar DMS con conectividad de sitio a sitio a la instancia local de SQL Server.


1. En [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso**. Seleccione **Redes** > **Red virtual**.
2. En **Red virtual** > **Seleccionar un modelo de implementación de red virtual** seleccione **Resource Manager** > **Crear**
3. En **Crear red virtual** > **Nombre**, escriba un nombre de red único. Para este escenario, **ContosoVNET**.
4. En **Espacio de direcciones**, agregue el intervalo de direcciones IP. El intervalo no debe solaparse con el espacio de direcciones locales.
5. En **Grupo de recursos** cree un nuevo grupo o seleccione uno existente. En este escenario, usamos **ContosoRG**.
6. En **Ubicación**, seleccione la región en la que se va a crear la red virtual. Se usa **Este de EE. UU. 2**.
7. En **Subred** agregue el nombre y el intervalo de direcciones de la primera subred. Hemos creado la subred **Apps**.
8. Deshabilite los puntos de conexión de servicio

    ![Crear una red virtual](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. Seleccione **Pin to dashboard** (Anclar al panel) para poder encontrar fácilmente la red en el futuro y, a continuación, haga clic en **Crear**.
10. La red virtual tarda unos segundos en crearse. Una vez creada, compruebe que aparece en el panel de Azure Portal.
11. Asegúrese de que estos puertos de comunicación se permiten en la red virtual: 443, 53, 9354, 445, 12000.


### <a name="set-up-subnets"></a>Configuración de subredes

Tenemos cuatro subredes en la red de Azure:

![Lista de subredes](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

La primera subred (Apps) la configuró al crear la red. A continuación, cree el resto de las subredes. La subred de puerta de enlace la utiliza la conexión de puerta de enlace VPN para que Azure envíe tráfico a través de una conexión VPN a su sitio local.

1. En la red virtual, en **Configuración**, haga clic en **Subred** > **+Subred**.
2. Configure una subred **Datamigration**, con este intervalo de direcciones y, a continuación, y luego haga clic en **Aceptar**.

    ![Subred de datos](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. En **+Subred** configure una subred **Identity**, con este intervalo de direcciones y luego haga clic en **Aceptar**.
    ![Subred de identidad](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. En **Subred**, haga clic en **Subred de puerta de enlace**y, a continuación, haga clic en **Aceptar**.
    - Esta subred contiene las direcciones IP que la puerta de enlace VPN va a usar para las conexiones VPN.
    - El nombre de esta subred se establece automáticamente para que Azure pueda reconocerla.
    - Ajuste el intervalo de direcciones de relleno automático para que coincida con la subred local. 

    ![subred de puerta de enlace](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>Establecimiento de una puerta de enlace de red virtual

1. A la izquierda de la página del portal, haga clic en **+** y escriba "Puerta de enlace de red virtual" en la búsqueda. En **Resultados**, haga clic en **Puerta de enlace de red virtual**.
2. En la parte inferior de la página "Puerta de enlace de red virtual", haga clic en **Crear**.
3. En **Crear puerta de enlace de red virtual** >  **Nombre**, especifique un nombre para el objeto de puerta de enlace.
4. En **Tipo de puerta de enlace** seleccione **VPN**.
5. En **Tipo de VPN** seleccione **Basada en rutas**.
6. En **SKU** seleccione la SKU de puerta de enlace en la lista desplegable. Las SKU que aparecen en la lista desplegable dependen del tipo de VPN que seleccione. No habilite el modo activo-activo. [Más información](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) acerca de las SKU.
7. En **Red Virtual** > **Elegir red virtual**, agregue la red que ha creado. La puerta de enlace se agregará a esta red.
8. En **Primera configuración de IP**, especifique una dirección IP pública.  Una puerta de enlace VPN tiene que tener una dirección IP pública. Esta dirección se asigna dinámicamente al recurso cuando se crea la puerta de enlace VPN. Actualmente se admiten solo las direcciones dinámicas pero la dirección IP no cambia después de su asignación.
    - Haga clic en **Crear configuración de IP de puerta de enlace** En **Elegir dirección IP pública** haga clic en **+Crear nuevo**.
    - En **Crear dirección IP pública**, especifique un nombre para la dirección IP pública (Contoso-Gateway). Deje la SKU como **Básica**y haga clic en **Aceptar** para guardar los cambios.
        
    ![Puerta de enlace de red virtual](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. Haga clic en **Crear** para empezar a crear la puerta de enlace de VPN. Se validará la configuración y aparecerá "Implementando la puerta de enlace de red virtual" en el panel.

    ![Validación de la puerta de enlace de red virtual](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
La creación de una puerta de enlace puede tardar hasta 45 minutos. Es posible que tenga que actualizar la página del portal para ver el estado completado.

### <a name="set-up-a-local-network-gateway"></a>Configuración de una puerta de enlace de red local

Configure una puerta de enlace de red local en Azure para representar su sitio local.

1. En el portal, haga clic en **+Crear un recurso**.
2. En el cuadro de búsqueda, escriba **puerta de enlace de red local**, a continuación, presione **Entrar** para buscar. En los resultados, haga clic en **Puerta de enlace de red local** > **Crear**.
3. En **Crear puerta de enlace de red local** > **Nombre** especifique un nombre que Azure usará para el objeto de puerta de enlace de red local.
4. **Dirección IP**, especifique la dirección IP pública del dispositivo VPN local al que se conectará Azure. No se aplica la traducción de direcciones de red (NAT) a la dirección IP y Azure tiene que poder llegar a ella.
5. En **Espacio de direcciones**, escriba los intervalos de direcciones de red local que se enrutarán a través de la puerta de enlace VPN al dispositivo local. Asegúrese de que no se solapen con intervalos de la red virtual de Azure.
6. **Configurar BGP** no es relevante para este escenario.
7. En **Suscripción** compruebe que se muestra la suscripción correcta.
8. En **Grupo de recursos** seleccione el grupo de recursos que se usó para crear la red (ContosoRG).
9. En **Ubicación** seleccione la región en la que se creó la red virtual.

    ![Puerta de enlace local](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. Haga clic en **Crear** para crear la puerta de enlace local.

### <a name="configure-your-on-premises-vpn-device"></a>Configuración del dispositivo VPN local

Esto es lo que necesita para configurar el dispositivo VPN local:

- La dirección IPv4 pública de la puerta de enlace de red virtual de Azure que acaba de crear.
- La clave previamente compartida (la clave que usó al crear la conexión de sitio a sitio VPN).  

Para ayudarle a configurar el dispositivo VPN local:

- En función del dispositivo VPN local que tenga, es posible que pueda descargar un script de configuración de dispositivo VPN. [Más información](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript).
- Consulte [recursos más útiles](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice).

### <a name="create-the-vpn-connection"></a>Creación de la conexión VPN

Ahora cree la conexión VPN de sitio a sitio entre la puerta de enlace de la red virtual y el dispositivo VPN local.

1. Abra la página de red virtual en **Redes virtuales** > **Introducción** > **Dispositivos conectados**. 
2. Haga clic en **Conexiones** > **+Agregar**.
3. En **Agregar conexión** > **Nombre**, especifique un nombre para la conexión.
4. En **Tipo de conexión**: seleccione **Sitio a sitio (IPsec)**.
5. El valor **Puerta de enlace de red virtual** es fijo porque se está conectando desde esta puerta de enlace.
6. En **Puerta de enlace de red local** seleccione la puerta de enlace de red local que creó.
7. En **Clave compartida** seleccione la clave que coincide con el valor que está usando para el dispositivo VPN local. En el ejemplo se usa "abc123", pero puede (y debería) utilizar algo más complejo. Lo importante es que el valor que especifique aquí debe ser el mismo que el que se especificó al configurar el dispositivo VPN.
8. Los valores de **Suscripción**, **Grupo de recursos** y **Ubicación** son fijos.

    ![Conexión VPN](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. Haga clic en **Aceptar** para crear la conexión. El mensaje "Creando la conexión" aparecerá rápidamente en la pantalla.
5. Una vez que se ha creado la conexión, esta se puede ver en la página **Conexiones** de la puerta de enlace de red virtual. El estado pasará de Desconocido a Conectando y luego a Correcto.

### <a name="verify-the-vpn-connection"></a>Comprobación de la conexión VPN

En Azure Portal, puede ver el estado de la conexión de una instancia de VPN Gateway de Resource Manager navegando a la conexión. 

1. Haga clic en **Todos los recursos** y navegue a la puerta de enlace de la red virtual.
2. En la página de puerta de enlace de red virtual, haga clic en **Conexiones**. Puede ver el estado de cada conexión.
3. Haga clic en el nombre de la conexión, y visualice más información en **Essentials**. El valor de estado es "Correcto" y "Conectado" cuando haya realizado una conexión satisfactoria.

Ahora compruebe que puede conectarse a la máquina virtual de SQL Server a través de VPN. Abra una conexión a Escritorio remoto y conéctese a las dirección IP de la máquina virtual.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>Paso 2: Preparar una instancia administrada de SQL Azure

### <a name="set-up-a-virtual-network"></a>Configuración de una red virtual

En este escenario tendrá que crear otra red virtual que se dedique a la instancia administrada de Azure SQL.  Tenga en cuenta los siguientes requisitos:

- Para crear la instancia administrada es necesaria una subred dedicada.
- La subred tiene que estar vacía y no puede contener ningún otro servicio de nube, tampoco puede ser una subred de puerta de enlace. Una vez creada la instancia administrada, no puede agregar recursos a la subred.
- La subred no puede tener un NSG asociado a ella.
- La subred tiene que tener una tabla de rutas de usuario (UDR) con Internet de próximo salto 0.0.0.0/0 como única ruta asignada. 
- DNS personalizado opcional: si se especifica un DNS personalizado en la red virtual, es necesario agregar la dirección IP de las resoluciones recursivas de Azure (por ejemplo, 168.63.129.16) a la lista. [Más información](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- La subred no puede tener un punto de conexión de servicio (Storage o SQL) asociado a ella. Los puntos de conexión de servicio se deben deshabilitar en la red virtual.
- La subred tiene que tener como mínimo 16 direcciones IP. [Más información](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) acerca de cómo ajustar el tamaño de la subred de la instancia administrada.

Configure la red virtual de la forma siguiente:

1.  En Azure Portal, haga clic en **Crear un recurso**. 
2. Seleccione **Redes** > **Red virtual**.
3. En **Red virtual** > **Seleccionar un modelo de implementación** seleccione **Resource Manager** > **Crear**
4. En **Crear red virtual** > **Nombre**, escriba un nombre de red único. Para nuestro escenario, **ContosoVNETSQLMI**.
5. En **Espacio de direcciones**, agregue el intervalo de direcciones IP que se muestra a continuación. El intervalo no puede solaparse con el espacio de direcciones locales y de ContosoVNET.
6. En **Grupo de recursos** cree un nuevo grupo o seleccione uno existente. En este escenario, usamos **ContosoRG**.
7. En **Ubicación**, seleccione la región en la que se va a crear la red virtual. Se usa **Este de EE. UU. 2**.
8. En **Subred** agregue el nombre y el intervalo de direcciones de la primera subred. Hemos creado la subred **ManagedInstances**.
9. Deshabilite los puntos de conexión de servicio

    ![Red de la instancia administrada](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. Una vez implementada la red, tiene que cambiar la configuración de DNS. En este escenario, el DNS primero apunta a un controlador de dominio en la subred de identidad utilizando una dirección IP privada estática (172.16.3.4) y, a continuación al solucionador de Azure DNS 168.63.129.16.

    ![Red de la instancia administrada](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>Configuración del enrutamiento

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal. Haga clic en la página **Tabla de rutas** > **Crear tabla de rutas**.
2. En **Crear tabla de rutas** > **Nombre**, especifique un nombre para la tabla.
3. Seleccione la suscripción, grupo de recursos y ubicación. Deje BGP deshabilitado y haga clic en **Crear**.
    ![Tabla de rutas](media/migrate-scenarios-lift-and-shift/route-table.png)

4. Una vez creada la tabla de rutas, ábrala y haga clic en **Rutas** > **+Agregar**.
5. En **Agregar ruta** > **Nombre** especifique un nombre de ruta.
6. En **Prefijo de dirección** especifique 0.0.0.0/0.
7. En **Tipo del próximo salto** seleccione **Internet**. A continuación, haga clic en **Aceptar**.

     ![Tabla de rutas](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>Aplicación de la ruta a la subred de instancia administrada

1. Abra la red virtual que creó. Haga clic en **Subredes** > el nombre de la subred.
2. Haga clic en **Tabla de rutas** > el nombre de la tabla. A continuación, haga clic en **Guardar**.

     ![Tabla de rutas](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>Creación de una instancia administrada

1. Haga clic en **Crear recurso**, busque **Instancia administrada** y seleccione **Instancia administrada Azure SQL Database (versión preliminar)**.
2. Haga clic en **Create**(Crear).
3. En **Instancia administrada de SQL** seleccione la suscripción y compruebe que los **Términos de la versión preliminar** muestran **Aceptado**.
4. En **Nombre de instancia administrada**, especifique un nombre. 
5. Especifique un nombre de usuario y una contraseña como administrador para la instancia.
6. Seleccione el grupo de recursos, la ubicación y la red virtual para la instancia.
7. Haga clic en **Plan de tarifa** para cambiar el tamaño de los recursos de almacenamiento y de proceso. De forma predeterminada, la instancia obtiene 32 GB de espacio de almacenamiento de forma gratuita (abril de 2018).
8. Especifique el almacenamiento y el número de núcleos virtuales.
9. Haga clic en **Aplicar** para guardar la configuración y **Crear** para implementar la instancia administrada. Para ver el estado de implementación, haga clic en **Notificaciones** e **Implementación en curso**.

    ![Instancia administrada](media/migrate-scenarios-lift-and-shift/mi-1.png)

Después de que finalice la implementación de la instancia administrada, dos nuevos recursos aparecen en el grupo de recursos ContosoRG: un clúster virtual para las instancias administradas y la instancia administrada de SQL. Ambos se encuentran en la ubicación Este de EE. UU. 2.

![Instancia administrada](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>Paso 3: Obtener un URI de SAS para DMS

Obtener un URI de SAS para que DMS pueda tener acceso al contenedor de la cuenta de almacenamiento, con el fin de cargar los archivos de copia de seguridad que se usan para migrar las bases de datos a la instancia administrada de SQL Database. 

1. Abra el Explorador de Storage (versión preliminar).
2. En el panel izquierdo, expanda la cuenta de almacenamiento que contiene el contenedor de blobs para el que desea obtener una SAS. Expanda **Contenedores de blob**de la cuenta de almacenamiento.
3. Haga clic con el botón derecho en el contenedor y seleccione **Obtener firma de acceso compartido**.

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. En el cuadro de diálogo **Firma de acceso compartido**, especifique la directiva, las fechas de inicio y expiración, la zona horaria y los niveles de acceso que desea para el recurso. A continuación, haga clic en **Crear**.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. Un segundo cuadro de diálogo muestra el contenedor de blobs junto con la dirección URL y las QueryStrings que se pueden utilizar para acceder al recurso de almacenamiento. Seleccione **Copiar** para copiar los valores. Consérvelos en un lugar seguro. Los necesitará cuando configure DMS.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>Paso 4: Preparar DMS

Registre el proveedor de Database Migration y cree una instancia.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>Registro del proveedor de recursos Microsoft.DataMigration

1. En la suscripción, seleccione **Proveedores de recursos**. 
2. Busque "migración" y después, a la derecha de Microsoft.DataMigration, seleccione **Registrar**. 


### <a name="create-an-instance"></a>Creación de una instancia

1. Seleccione **+Crear un recurso**, busque "Azure Database Migration Service" y, luego seleccione **Azure Database Migration Service** en la lista desplegable.
2. En la pantalla Azure Database Migration Service  (versión preliminar) seleccione Crear.
3. Especifique un nombre para el servicio, la suscripción, el grupo de recursos, la red virtual y el plan de tarifa.
4. Seleccione **Crear** para crear el servicio.

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>Paso 5: Preparar Azure para el servicio de Site Recovery

### <a name="set-up-a-virtual-network"></a>Configuración de una red virtual

Necesita una red de Azure en la que las máquinas virtuales de Azure se ubicarán cuando se creen después de la conmutación por error, y una cuenta de almacenamiento de Azure en la que se almacenarán los datos.

- Para los fines de este escenario, vamos a usar la red de Azure que hemos creado con anterioridad, para DMS.
- Tenga en cuenta que la red que utilice tiene que estar en la misma región que el almacén de Site Recovery.


### <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

Site Recovery replica los datos de máquina virtual en Azure Storage. Las máquinas virtuales de Azure se crean desde el almacenamiento cuando se realiza la conmutación por error desde el entorno local en Azure.

1. En el menú [Azure Portal](https://portal.azure.com), seleccione **Nuevo** > **Almacenamiento** > **Cuentas de almacenamiento**.
2. En **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta. En estos tutoriales, se usará el nombre **contosovmsacct1910171607**. El nombre debe ser único en Azure, tener entre 3 y 24 caracteres, y contener solo números y letras minúsculas.
3. En **Modelo de implementación**, seleccione **Resource Manager**.
4. En **Tipo de cuenta** seleccione **Uso general**. En **Rendimiento**, seleccione **Estándar**. No seleccione Blob Storage.
5. En **Replicación**, seleccione el valor predeterminado **Almacenamiento con redundancia geográfica con acceso de lectura** como redundancia de almacenamiento.
6. En **Suscripción**, seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.
7. En **Grupo de recursos**, especifique un nuevo grupo de recursos. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En estos tutoriales, se usa el nombre **ContosoRG**.
8. En **Ubicación**, seleccione la ubicación geográfica de la cuenta de almacenamiento. La cuenta de almacenamiento debe estar en la misma región que el almacén de Recovery Services. En este escenario, se usa la región **Este de EE. UU. 2**.

   ![Crear una cuenta de almacenamiento](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. Seleccione **Crear** para crear la cuenta de almacenamiento.

### <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

1. En Azure Portal, seleccione **Crear un recurso** > **Supervisión y administración** > **Backup y Site Recovery**.
2. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. En este tutorial, se usa **ContosoVMVault**.
3. En **Grupo de recursos**, seleccione el grupo de recursos existente denominado **contosoRG**.
4. En **Ubicación** especifique la región de Azure **Este de EE. UU. 2**.
5. Para acceder rápidamente al almacén desde el panel, seleccione **Anclar al panel** > **Crear**.
El nuevo almacén aparecerá en **Panel** > **Todos los recursos** y en la página principal de **Almacenes de Recovery Services**.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>Paso 6: Preparar VMware local para Site Recovery

Para preparar VMware para Site Recovery esto es lo que tiene que hacer:

- Preparar una cuenta en el servidor vCenter o en el host ESXi de vSphere para automatizar la detección de máquinas virtuales
- Preparar una cuenta para la instalación automática de Mobility Service en máquinas virtuales de VMware
- Preparar las máquinas virtuales locales si desea conectarse a las máquinas virtuales de Azure después de una conmutación por error.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparación de una cuenta de detección automática

Site Recovery necesita acceso a los servidores de VMware para:

- Detectar automáticamente las máquinas virtuales. Se requiere al menos una cuenta de solo lectura.
- Coordinar la replicación, la conmutación por error y la conmutación por recuperación. Necesita una cuenta que pueda ejecutar operaciones como la creación y eliminación de discos, así como la activación de máquinas virtuales.

Cree la cuenta como se indica a continuación:

1. Para usar una cuenta dedicada, cree un rol en el nivel de vCenter. Asigne al rol un nombre como **Azure_Site_Recovery**.
2. Asigne al rol los permisos que se resumen en la tabla siguiente.
3. Cree un usuario en el servidor vCenter o el host de vSphere. Asigne el rol al usuario.

**Task** | **Rol o permisos** | **Detalles**
--- | --- | ---
**Detección de máquina virtual** | Al menos un usuario de solo lectura<br/><br/> Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).
**Replicación completa, conmutación por error, conmutación por recuperación** |  Cree un rol (Azure_Site_Recovery) con los permisos necesarios y, después, asígnelo a un grupo o usuario de VMware.<br/><br/> Objeto de centro de datos –> Propagar a objeto secundario, rol = Azure_Site_Recovery<br/><br/> Almacén de datos -> Asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual<br/><br/> Red -> Asignación de red<br/><br/> Recursos-> Asignar máquina virtual al grupo de recursos, migrar apagado de máquina virtual, migrar máquinas virtuales encendidas<br/><br/> Tareas -> Crear tarea, actualizar tarea<br/><br/> Máquina virtual -> Configuración<br/><br/> Máquina virtual -> Interactuar -> Responder a pregunta, conexión de dispositivos, configurar soporte de CD, Configurar soporte de disquete, apagar, encender, instalación de herramientas de VMware<br/><br/> Máquina virtual -> Inventario -> Crear, registrar, anular registro<br/><br/> Máquina virtual -> Aprovisionamiento -> Permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual<br/><br/> Máquina virtual -> Instantáneas -> Quitar instantáneas | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparación de una cuenta para la instalación de Mobility Service

Mobility Service debe instalarse en la máquina que quiera replicar.

- Site Recovery puede hacer una instalación de inserción automática de este componente cuando se habilita la replicación para la máquina virtual.
- Para la instalación de inserción automática, tiene que preparar una cuenta que Site Recovery vaya a usar para obtener acceso a la máquina virtual.
- Deberá especificar esta cuenta al configurar la replicación en la consola de Azure.
- Necesita un dominio o cuenta local con permisos para instalar en la máquina virtual.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparación para la conexión a las máquinas virtuales de Azure después de la conmutación por error

Durante una conmutación por error a Azure, es posible que quiera conectarse a las máquinas virtuales replicadas en Azure desde la red local.

Para conectarse a máquinas virtuales Windows mediante RDP después de la conmutación por error, haga lo siguiente:

1. Para obtener acceso a través de Internet, habilite RDP en la máquina virtual local antes de la conmutación por error. Asegúrese de que se hayan agregado las reglas de TCP y UDP para el perfil **Público**, y que RDP se permite en **Firewall de Windows** > **Aplicaciones permitidas** para todos los perfiles.
2. Para obtener acceso a través de VPN de sitio a sitio, habilite RDP en la máquina local. RDP debe permitirse en **Firewall de Windows** -> **Aplicaciones y características permitidas** para redes de **dominio y privadas**.
3. Compruebe que la directiva SAN del sistema operativo está establecida en **OnlineAll**. [Más información](https://support.microsoft.com/kb/3031135).
4. No debe haber actualizaciones de Windows pendientes en la máquina virtual cuando se desencadene una conmutación por error. Si hay, no podrá iniciar sesión en la máquina virtual hasta que se complete la actualización.
5. En la máquina virtual de Microsoft Azure después de la conmutación por error, compruebe los **Diagnósticos de arranque** para ver una captura de pantalla de la máquina virtual. Si no puede conectarse, compruebe que se está ejecutando la máquina virtual y revise estas [sugerencias de solución de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).



## <a name="step-7-replicate-vms-with-site-recovery"></a>Paso 7: Replicar las máquinas virtuales con Site Recovery

### <a name="select-a-replication-goal"></a>Selección de un objetivo de replicación

1. En **Almacenes de Recovery Services**, haga clic en el nombre del almacén **ContosoVMVault**.
2. En **Introducción**, seleccione Site Recovery. Luego, seleccione **Preparar infraestructura**.
3. En **Objetivo de protección** > **¿Dónde están ubicadas las máquinas?**, seleccione **Local**.
4. En **¿A dónde quiere replicar las máquinas?**, seleccione **En Azure**.
5. En **¿Las máquinas están virtualizadas?**, seleccione **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor). Después seleccione **Aceptar**.

    ![Plantilla de OVF](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>Confirmación del planeamiento de la implementación

En **Planeamiento de implementación**, haga clic en **Sí, ya lo hice** en la lista desplegable.

### <a name="set-up-the-source-environment"></a>Configuración del entorno de origen


Para configurar el entorno de origen, es necesaria una máquina local, única y de alta disponibilidad que hospede los componentes locales de Site Recovery. Los componentes son el servidor de configuración y el servidor de procesos.

- El servidor de configuración coordina la comunicación entre el entorno local y Azure, además de administrar la replicación de datos.
- El servidor de procesos actúa como puerta de enlace de replicación. Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado, y los envía a Azure Storage.
- El servidor de procesos también instala Mobility Service en las máquinas virtuales que quiere replicar, además de realizar la detección automática de las máquinas virtuales de VMware locales.


Para configurar el servidor de configuración como una máquina virtual de VMware de alta disponibilidad:

- Descargue una plantilla de Open Virtualization Format (OVF) preparada.
- Importe la plantilla en VMware para crear la máquina virtual.
- Configure el servidor de configuración y regístrelo en el almacén. 

Tras el registro, Site Recovery detecta las máquinas virtuales locales de VMware.



#### <a name="download-the-vm-template"></a>Descarga de la plantilla de máquina virtual

1. En el almacén, vaya a **Preparar infraestructura** > **Origen**.
2. En **Preparar origen**, seleccione **+Servidor de configuración**.

    ![Descarga de una plantilla](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración para VMware** en **Tipo de servidor**.
2. Descargue la plantilla OVF para el servidor de configuración.

    ![Descarga de OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  Puede descargar la versión más reciente de la plantilla del servidor de configuración directamente desde el [Centro de descarga de Microsoft](https://aka.ms/asrconfigurationserver).

#### <a name="import-the-template-in-vmware"></a>Importación de la plantilla en VMware

1. Inicie sesión en el servidor VMware vCenter con el cliente de VMWare vSphere.
2. En el menú **File** (Archivo), seleccione **Deploy OVF Template** (Implementar plantilla OVF) para iniciar el Asistente para implementar plantillas OVF. 

     ![Plantilla de OVF](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. En **Select source** (Seleccionar origen), especifique la ubicación de la plantilla OVF descargada.
4. En **Review details** (Revisar detalles), seleccione **Next** (Siguiente).
5. En **Select name and folder** (Seleccionar el nombre y la carpeta) y **Select configuration** (Seleccionar la configuración), acepte la configuración predeterminada.
6. En **Select storage** (Seleccionar almacenamiento), seleccione **Thick Provision Eager Zeroed** (Aprovisionamiento grueso diligente con ceros) en **Select virtual disk format** (Seleccionar formato de disco virtual) para obtener un rendimiento mejor.
7. En el resto de las páginas del asistente, acepte la configuración predeterminada.
8. En **Ready to complete** (Listo para completar):

    * Para configurar la máquina virtual con la configuración predeterminada, seleccione **Power on after deployment** > **Finish** (Encender después de la implementación > Finalizar).

    * Si desea agregar una interfaz de red adicional, desactive **Power on after deployment** (Encender después de la implementación). A continuación, seleccione **Finish** (Finalizar). De forma predeterminada, la plantilla del servidor de configuración se implementa con una sola NIC. Puede agregar NIC adicionales después de la implementación.



#### <a name="register-the-configuration-server"></a>Registro del servidor de configuración 

1. Desde la consola de cliente de VMWare vSphere, encienda la máquina virtual.
2. La máquina virtual se inicia en una experiencia de instalación de Windows Server 2016. Acepte el contrato de licencia y especifique una contraseña de administrador.
3. Una vez finalizada la instalación, inicie sesión en la máquina virtual como administrador.
4. La primera vez que inicie sesión, se inicia la herramienta de configuración de Azure Site Recovery.
5. Especifique el nombre que se usará para registrar el servidor de configuración en Site Recovery. Luego, seleccione **Siguiente**.

    ![Plantilla de OVF](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. La herramienta comprueba que la máquina virtual pueda conectarse a Azure. Una vez establecida la conexión, haga clic en **Iniciar sesión** para iniciar sesión en la suscripción de Azure. Las credenciales deben tener acceso al almacén donde desea registrar el servidor de configuración.

    ![Plantilla de OVF](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. La herramienta realiza algunas tareas de configuración y, a continuación, se reinicia.
8. Inicie sesión de nuevo en la máquina. El Asistente para administración del servidor de configuración se inicia automáticamente.

#### <a name="configure-settings-and-add-the-vmware-server"></a>Configuración de los valores y adición del servidor de VMware

1. En el asistente para administración del servidor de configuración, seleccione **Configurar conectividad** y seleccione la NIC que va a recibir el tráfico de replicación. Después, seleccione **Guardar**. Una vez configurada, esta opción no se puede cambiar.
2. En **Seleccionar almacén de Recovery Services**, seleccione la suscripción de Azure y el grupo de recursos y almacén correspondientes.

    ![almacén](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. En **Instalar software de terceros**, acepte el contrato de licencia. Seleccione **Descargar e instalar** para instalar MySQL Server.
4. Seleccione **Install VMware PowerCLI** (Instalar VMware PowerCLI). Asegúrese de que todas las ventanas del explorador están cerradas antes. Después, seleccione **Continuar**.
5. En **Validate appliance configuration** (Comprobar configuración del dispositivo), se comprueban los requisitos previos antes de continuar.
6. En **Configure vCenter Server/vSphere ESXi server** (Configurar vCenter Server/vSphere ESXi Server), especifique la dirección IP o FQDN del servidor vCenter o host de vSphere en los que se encuentran las máquinas virtuales donde desea realizar la replicación. Especifique el puerto en el que escucha el servidor. Escriba un nombre descriptivo que se usará para el servidor de VMware en el almacén.
7. Especifique las credenciales que usará el servidor de configuración para conectarse al servidor de VMware. Site Recovery usa estas credenciales para detectar automáticamente las máquinas virtuales de VMware disponibles para la replicación. Seleccione **Agregar** y, luego, **Continuar**.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. En **Configure virtual machine credentials** (Configurar las credenciales de la máquina virtual), especifique el nombre de usuario y la contraseña que se usarán para instalar automáticamente Mobility Service en las máquinas, cuando la replicación esté habilitada. Para las máquinas Windows, la cuenta necesita privilegios de administrador local en las máquinas que se vayan a replicar. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. Seleccione **Finalize configuration** (Terminar configuración) para completar el registro. 
8. Cuando finalice el registro, en Azure Portal, verifique que el servidor de configuración y el servidor de VMware aparecen en la página **Origen** del almacén. Haga clic en **Aceptar** para configurar el destino.
9. Site Recovery se conecta a los servidores de VMware con la configuración especificada y detecta las máquinas virtuales.

> [!NOTE]
> El nombre de la cuenta podría tardar 15 minutos o más en aparecer en el portal. Para que se actualice inmediatamente, seleccione **Servidores de configuración** > ***nombre de servidor*** > **Actualizar servidor**.

### <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione y compruebe los recursos de destino.

1. En **Preparar infraestructura** > **Destino** seleccione la suscripción a Azure que quiere usar.
2. Para el modelo de implementación de destino seleccione **Azure Resource Manager**.

3. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.


### <a name="create-a-replication-policy"></a>Creación de una directiva de replicación

1. En **Preparar infraestructura** > **Configuración de la replicación** > **Directiva de replicación**, haga clic en **Crear y asociar**.
1. En **Crear la directiva de replicación**, especifique el nombre de directiva **VMwareRepPolicy**.
2. En **Umbral de RPO**, use el valor predeterminado de 60 minutos. Este valor define la frecuencia de creación de puntos de recuperación. Se genera una alerta cuando la replicación continua supera este límite.
3. En **Retención de punto de recuperación**, use el valor predeterminado de 24 horas como duración del período de retención de cada punto de recuperación. En este tutorial, use 72 horas. Las máquinas virtuales replicadas se pueden recuperar a cualquier momento de un período.
4. En **Frecuencia de instantánea coherente con la aplicación**, use el valor predeterminado de 60 minutos como frecuencia de creación de instantáneas coherentes con la aplicación. Seleccione **Aceptar** para crear la directiva.

    ![Creación de la directiva de replicación](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. La directiva se asocia automáticamente al servidor de configuración. 

    ![Asociación de la directiva de replicación](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>Habilitar replicación

Ahora empiece a replicar las máquinas virtuales. Site Recovery instala Mobility Service en cada máquina virtual cuando se habilita la replicación para esta. 


1. Seleccione **Replicar la aplicación** > **Origen**.
2. En **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas virtuales**.
4. En **vCenter/vSphere Hypervisor**, seleccione el servidor vCenter que administra el host de vSphere, o bien seleccione el host.
5. Seleccione el servidor de procesos (servidor de configuración). Después seleccione **Aceptar**.

    ![Habilitar replicación](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. En **Destino**, seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales conmutadas por error. Elija el modelo de implementación que quiera usar en Azure (clásico o de Resource Manager) para las máquinas virtuales conmutadas por error.
2. Seleccione la cuenta de Azure Storage que desea usar para los datos de replicación.
3. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error.
4. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina.
5. Seleccione la subred en la red virtual. Después seleccione **Aceptar**.

    ![Habilitar replicación](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, seleccione la máquina virtual (WEBVM) que desea. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. 

    ![Habilitar replicación](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. Para supervisar las máquinas virtuales que agregue, compruebe la última hora de detección de máquinas virtuales en **Servidores de configuración** > **Último contacto a las**. Para agregar máquinas virtuales sin esperar a la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en **Actualizar**. Los cambios pueden tardar 15 minutos o más en aplicarse y aparecer en el portal.
8. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que usará el servidor de procesos para instalar automáticamente Mobility Service en la máquina. 

    ![Habilitar replicación](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se haya seleccionado la directiva de replicación correcta.
10. Seleccione **Habilitar replicación**.

    ![Habilitar replicación](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. Realice un seguimiento del progreso del trabajo **Habilitar la protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. 
12. Después de que se ejecute el trabajo **Finalizar la protección**, la máquina está preparada para la conmutación por error, y aparece en **Introducción** > **Essentials**.

    ![Essentials](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>Paso 8: Migrar la base de datos con DMS

Cree un proyecto DMS y ejecute la migración.


### <a name="create-a-database-migration-project"></a>Creación de un proyecto de Database Migration

1. Busque el recurso DMS en Azure Portal y ábralo.
2. Seleccione **+ New Migration Project** (+ Nuevo proyecto de migración).
3. En **Nuevo proyecto de migración**, especifique un nombre para el proyecto.
4. En **Source server type** (Tipo de servidor de origen), seleccione **SQL Server**. En **Target server type** (Tipo de servidor de destino), seleccione **Instancia administrada de Azure SQL Database**.
5. Haga clic en **Crear** para crear el proyecto.
6. En **Detalles del origen**, especifique los detalles de conexión de la instancia de SQL Server de origen local. Haga clic en **Save**(Guardar).
7. En **Detalles de destino**, especifique los detalles de conexión del destino, que es la Instancia administrada de Azure SQL Database que se aprovisionó previamente, y a la que se migrará la base de datos local. A continuación, haga clic en **Guardar**.
8. En **Resumen del proyecto**, revise y compruebe los detalles relacionados con el proyecto de migración.

    ![Proyecto DMS](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>Migración de la base de datos

1. Después de crear el proyecto, aparece el Asistente para migración.
2. Especifique las credenciales para los servidores de origen y de destino y haga clic en **Guardar**. El asistente intenta iniciar sesión en la instancia local de SQL Server.

    ![Asistente de DMS](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. En **Map to target databases** (Asignar a bases de datos de destino), seleccione las bases de datos de origen que desea migrar. A continuación, haga clic en **Guardar**.

    ![Asistente de DMS](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. En **Detalles de destino**, seleccione **Conozco los detalles de mi destino**. Proporcione el nombre DNS de las instancias administradas de SQL, junto con las credenciales. A continuación, haga clic en **Guardar**.

    ![Asistente de DMS](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. En el proyecto guardado, seleccione **+ Nueva actividad**. Seleccione **Ejecutar migración**.
6. Cuando se le solicite, escriba las credenciales de los servidores de origen y de destino. A continuación, haga clic en **Guardar**.
7. En **Map to target databases** (Asignar a bases de datos de destino), seleccione las bases de datos de origen que desea migrar. A continuación, haga clic en **Guardar**
8. En **Configurar los valores de la migración** > **Ubicación de la copia de seguridad del servidor**, especifique el recurso compartido de red que creó en la máquina local. DMS lleva las copias de seguridad de origen a este recurso compartido.  Recuerde que la cuenta de servicio que ejecuta la instancia de SQL Server de origen tiene que tener privilegios de escritura en este recurso compartido.
9. Especifique el nombre de usuario y la contraseña que DMS puede utilizar para cargar los archivos de copia de seguridad en el contenedor de almacenamiento de Azure, para la restauración.
10. Especifique el URI de SAS que proporciona a DMS acceso al contenedor de la cuenta de almacenamiento en el que el servicio carga los archivos de copia de seguridad, y que se usa para la migración a la instancia administrada de Azure SQL Database.  A continuación, haga clic en **Guardar**.
11. En **Resumen de la migración** especifique un nombre para la actividad de migración > **Ejecutar la migración**.
12. En el proyecto > **Introducción**, supervise el estado de la migración. Una vez completada la migración, compruebe que las bases de datos de destino existen en la instancia administrada.


## <a name="step-9-migrate-vms-with-site-recovery"></a>Paso 9: Migrar las máquinas virtuales con Site Recovery

Se recomienda que antes de ejecutar una migración completa, se realice una conmutación por error de prueba para asegurarse de que todo funciona según lo previsto. Cuando se ejecuta una conmutación por error de prueba, ocurre lo siguiente:

1. Se ejecuta una comprobación de requisitos previos para garantizar que todas las condiciones necesarias para la conmutación por error están correctamente establecidas.
2. La conmutación por error procesa los datos, de modo que se pueda crear una máquina virtual de Azure. Si se selecciona el último punto de recuperación, se crea un punto de recuperación a partir de los datos.
3. Se crea una VM de Azure mediante los datos procesados en el paso anterior.

### <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

1. En **Elementos protegidos**, haga clic en **Elementos replicados** > VM.
2. En las propiedades de la máquina virtual, haga clic en **+ Conmutación por error de prueba**.

    ![Conmutación por error de prueba](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. Seleccione **Procesado más reciente** para conmutar por error la máquina virtual en el punto más reciente en el tiempo disponible. Se muestra la marca de tiempo. Con esta opción, no se empleó tiempo en el procesamiento de datos, por lo que se proporciona un objetivo de tiempo de recuperación (RTO) bajo.
2. En **Probar conmutación por error**, seleccione la red de Azure de destino a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.
3. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede realizar un seguimiento en el almacén > **Configuración** > **Trabajos** > **Conmutación por error de prueba**.
4. Una vez finalizada la conmutación por error, la VM de Azure de réplica aparece en Azure Portal > **Virtual Machines**. Compruebe que la VM tiene el tamaño adecuado, que está conectada a la red correspondiente y que se está ejecutando. Ahora debería poder conectarse a la VM replicada en Azure.
5. Para eliminar máquinas virtuales de Azure que se crearon durante la conmutación por error de prueba, haga clic en **Limpieza de conmutación por error de prueba** en la máquina virtual. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba.

### <a name="migrate-the-machines"></a>Migración de las máquinas

Después de comprobar que la conmutación por error de prueba funciona según lo previsto, cree un plan de recuperación para migrar a Azure.

### <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación

1. En el almacén seleccione **Planes de recuperación (Site Recovery)** > **+Plan de recuperación**.
2. En **Crear plan de recuperación**, especifique un nombre para el plan.
3. Seleccione el servidor de configuración de origen, y elija Azure como destino. Seleccione **Resource Manager** para modelo de implementación. La ubicación de origen debe tener máquinas habilitadas para conmutación por error y recuperación. 
4. En **Seleccionar elementos**, agregue las máquinas (WEBVM) al plan. A continuación, haga clic en **Aceptar**.
5. Haga clic en **Aceptar** para crear el plan.

    ![Plan de recuperación](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>Ejecución de la conmutación por error

1. En **Planes de recuperación**, haga clic en el plan > **Conmutación por error**.
2. En **Conmutación por error** > **Punto de recuperación**, seleccione el punto de recuperación más reciente.
3. La configuración de la clave de cifrado no es importante para este escenario.
4. Seleccione **Apague la máquina antes de comenzar con la conmutación por error**. Site Recovery intentará apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.

    ![Conmutación por error](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Compruebe que la máquina virtual de Azure aparece en Azure según lo previsto.

    ![Conmutación por error](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. En **Elementos replicados**, haga clic con el botón derecho en la máquina virtual > **Completar migración**. Con esta acción se completa el proceso de migración, y se detienen la replicación de la máquina virtual y la facturación de Site Recovery para la VM.

    ![Conmutación por error](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>Actualización de la cadena de conexión

El último paso del proceso de migración consiste en actualizar la cadena de conexión de la aplicación, para que apunte a la base de datos migrada que se ejecuta en la instancia administrada de SQL.

1. Busque la nueva cadena de conexión en Azure Portal haciendo clic en **Configuración** > **Cadenas de conexión**.

    ![Conmutación por error](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. Tiene que actualizar esta cadena con el nombre de usuario y la contraseña para la instancia administrada de SQL.
3. Una vez configurada la cadena, tiene que reemplazar la cadena de conexión actual en el archivo web.config de la aplicación.
4. Después de actualizar el archivo y guardarlo, reinicie IIS en WEBVM. Esto puede hacerse usando IISRESET /RESTART desde un símbolo del sistema.
5. Después de reiniciar IIS, la aplicación usará ahora la base de datos que se ejecuta en la instancia administrada de SQL.
6. En este momento puede cerrarse la máquina  SQLVM local y se completa la migración.



## <a name="conclusion"></a>Conclusión

En este escenario, hemos:

> [!div class="checklist"]
> * Realizado la migración de la base de datos local a una instancia administrada de Azure SQL con DMS.
> * Realizado la migración de las máquinas virtuales locales a máquinas virtuales de Azure, con el servicio Azure Site Recovery.

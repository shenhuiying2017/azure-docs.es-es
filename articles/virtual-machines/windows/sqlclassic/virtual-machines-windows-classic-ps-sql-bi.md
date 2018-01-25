---
title: SQL Server Business Intelligence | Microsoft Docs
description: "En este tema se utilizan recursos creados con el modelo de implementación clásica y se describen las características de Business Intelligence (BI) disponibles para SQL Server cuando se ejecuta en Azure Virtual Machines."
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: asaxton
ms.openlocfilehash: a010e60df2d86d2b1cc923b427aa7d7452f58089
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Business Intelligence de SQL Server en Azure Virtual Machines
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager.

La galería de máquinas virtuales de Microsoft Azure incluye  imágenes que contienen instalaciones de SQL Server. Las ediciones de SQL Server que se admiten en las imágenes de la galería son los mismos archivos de instalación que puede instalar en equipos locales y máquinas virtuales. En este tema se resumen las características de Business Intelligence (BI) de SQL Server instaladas en las imágenes, y los pasos de configuración necesarios después de aprovisionar una máquina virtual. En este tema también se describen las topologías de implementación admitidas para las características de BI y los procedimientos recomendados.

## <a name="license-considerations"></a>Consideraciones de licencias
Hay dos maneras de conceder licencias de SQL Server en Microsoft Azure Virtual Machines:

1. Ventajas de la movilidad de licencias que forman parte de Software Assurance. Para obtener más información, consulte [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Tarifa de pago por hora de Azure Virtual Machines con SQL Server instalado. Vea la sección "SQL Server" en [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Para obtener más información sobre las licencias y las tarifas actuales, vea [P+F sobre licencias de Máquinas virtuales](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>Imágenes de SQL Server disponibles en la galería de máquinas virtuales de Azure
La galería de máquinas virtuales de Microsoft Azure incluye varias imágenes que contienen Microsoft SQL Server. El software instalado en las imágenes de máquinas virtuales varía en función de la versión del sistema operativo y de la versión de SQL Server. La lista de imágenes disponibles en la galería de máquinas virtuales de Azure cambia con frecuencia.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Imagen de SQL en la galería de máquinas virtuales de Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) El siguiente script de PowerShell devuelve la lista de imágenes de Azure que contienen "SQL Server" en el ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Para obtener más información sobre las ediciones y las características admitidas por SQL Server, vea lo siguiente:

* [Ediciones de SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [Características admitidas por las ediciones de SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Características de BI instaladas en las imágenes de la galería de máquinas virtuales de SQL Server
En la tabla siguiente se resumen las características de Inteligencia empresarial instaladas en las imágenes de la galería de máquinas virtuales de Microsoft Azure comunes para SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Característica de BI de SQL Server | Instalada en la imagen de la galería | Notas |
| --- | --- | --- |
| **Modo nativo de Reporting Services** |Sí |Instalado pero requiere configuración, incluida la dirección URL del administrador de informes. Vea la sección [Configurar Reporting Services](#configure-reporting-services). |
| **Modo de SharePoint de Reporting Services** |Sin  |La imagen de la galería de máquinas virtuales de Microsoft Azure no incluye SharePoint o archivos de instalación de SharePoint. <sup>1</sup> |
| **Minería de datos y multidimensional de Analysis Services (OLAP)** |Sí |Instalado y configurado como la instancia de Analysis Services predeterminada |
| **Tabular de Analysis Services** |Sin  |Se admite en imágenes de SQL Server 2012, 2014 y 2016, pero no se instala de forma predeterminada. Instale otra instancia de Analysis Services. Vea la sección Instalar otras características y servicios de SQL Server en este tema. |
| **Analysis Services PowerPivot para SharePoint** |Sin  |La imagen de la galería de máquinas virtuales de Microsoft Azure no incluye SharePoint o archivos de instalación de SharePoint. <sup>1</sup> |

<sup>1</sup> Para más información sobre SharePoint y Azure Virtual Machines, consulte [Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) e [Implementación de SharePoint en Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Ejecute el siguiente comando de PowerShell para obtener una lista de los servicios instalados que contienen "SQL" en el nombre del servicio.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Recomendaciones generales y prácticas recomendadas
* El tamaño mínimo recomendado para una máquina virtual es **A3** cuando se usa SQL Server Enterprise Edition. Se recomienda el tamaño de máquina virtual **A4** para las implementaciones de SQL Server BI de Analysis Services y Reporting Services.
  
    Para obtener información sobre los tamaños actuales de máquinas virtuales, consulte [Tamaños de máquina virtual de Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Un procedimiento recomendado para la administración de discos es almacenar datos, registrar y realizar copias de seguridad de archivos en unidades distintas de **C**: y **D**:. Por ejemplo, cree los discos de datos **E**: y **F**:.
  
  * La política de almacenamiento en caché de unidad para la unidad predeterminada **C**: no es óptima para trabajar con datos.
  * La unidad **D**: es una unidad temporal que se usa principalmente para el archivo de paginación. La unidad **D**: no se conserva y no se guarda en el almacenamiento de blobs. Las tareas de administración como un cambio al tamaño de la máquina virtual restablecen la unidad de disco **D**:. Se recomienda **NO** usar la unidad **D**: para los archivos de bases de datos, incluido tempdb.
    
    Para obtener más información sobre cómo crear y acoplar discos, consulte [Acoplamiento de un disco de datos a una máquina virtual](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Detenga o desinstale servicios que no planee usar. Por ejemplo, si la máquina virtual solo se usa para Reporting Services, detenga o desinstale Analysis Services y SQL Server Integration Services. La imagen siguiente es un ejemplo de los servicios que se inician de forma predeterminada.
  
    ![Servicios de SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > El motor de base de datos de SQL Server es necesario en escenarios de BI admitidos. En una topología de máquina virtual de servidor único, el motor de base de datos debe ejecutarse en la misma máquina virtual.
  
    Para más información, consulte lo siguiente: [Desinstalar Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) y [Desinstalar una instancia de Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Consulte **Windows Update** para ver nuevas "Actualizaciones importantes". Las imágenes de máquina virtual de Microsoft Azure se actualizan con frecuencia; sin embargo, las actualizaciones importantes pueden estar disponibles en **Windows Update** después de la última actualización de la imagen de máquina virtual.

## <a name="example-deployment-topologies"></a>Topologías de implementación de ejemplo
Las siguientes son las implementaciones de ejemplo que usan Microsoft Azure Virtual Machines. Las topologías de estos diagramas son solo algunas de las topologías posibles que puede usar con las funciones de SQL Server BI y Microsoft Azure Virtual Machines.

### <a name="single-virtual-machine"></a>Máquina virtual única
Analysis Services, Reporting Services, el motor de base de datos de SQL Server y los orígenes de datos en una única máquina virtual.

![escenario de bi iass con 1 máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Dos máquinas virtuales
* Analysis Services, Reporting Services y el motor de base de datos de SQL Server en una única máquina virtual. Esta implementación incluye las bases de datos del servidor de informes.
* Orígenes de datos en una segunda máquina virtual. La segunda máquina virtual incluye el motor de base de datos de SQL Server como origen de datos.

![escenario de bi iass con 2 máquinas virtuales](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Mezcla de Azure: datos en base de datos SQL de Azure
* Analysis Services, Reporting Services y el motor de base de datos de SQL Server en una única máquina virtual. Esta implementación incluye las bases de datos del servidor de informes.
* El origen de datos es una base de datos SQL de Azure.

![escenarios de máquina virtual de bi iaas y AzureSQL como origen de datos](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Híbrido: datos locales
* En esta implementación de ejemplo, Analysis Services, Reporting Services y el motor de base de datos de SQL Server se ejecutan en una única máquina virtual. La máquina virtual hospeda las bases de datos del servidor de informes. La máquina virtual se une a un dominio local a través de Azure Virtual Network o alguna otra solución de tunelización de VPN.
* El origen de datos es local.

![escenarios de máquina virtual de bi iaas y orígenes de datos locales](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuración del modo nativo de Reporting Services
La imagen de la galería de máquinas virtuales para SQL Server incluye el modo nativo de Reporting Services instalado, pero el servidor de informes no está configurado. Los pasos de esta sección configuran el servidor de informes de Reporting Services. Para obtener más información sobre cómo configurar el modo nativo de Reporting Services, consulte [Instalar el servidor de informes del modo nativo de Reporting Services (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Para consultar contenido similar que usa scripts de Windows PowerShell para configurar el servidor de informes, consulte [Usar PowerShell para crear una máquina virtual de Azure con un servidor de informes en modo nativo](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Conectarse a la máquina virtual e iniciar el Administrador de configuración de Reporting Services
Hay dos flujos de trabajo habituales para conectarse a una máquina virtual de Azure:

* Para conectarse, haga clic en el nombre de la máquina virtual y luego en **Conectar**. Se abre una conexión a escritorio remoto y se rellena automáticamente el nombre del equipo.
  
    ![conectarse a máquina virtual de azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Conéctese a la máquina virtual con la conexión al Escritorio remoto de Windows. En la interfaz de usuario del escritorio remoto:
  
  1. Escriba el **nombre de servicio en la nube** como el nombre del equipo.
  2. Escriba dos puntos (:) y el número de puerto público que está configurado para el extremo de escritorio remoto de TCP.
     
      Myservice.cloudapp.net:63133
     
      Para obtener más información, consulte [¿Qué es un servicio en la nube?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Inicie el Administrador de configuración de Reporting Services**

En **Windows Server 2012/2016**:

1. En la pantalla **Inicio**, escriba **Reporting Services** para ver una lista de las aplicaciones.
2. Haga clic con el botón derecho en el **Administrador de configuración de Reporting Services** y haga clic en **Ejecutar como administrador**.

En **Windows Server 2008 R2**:

1. Haga clic en **Inicio** y luego haga clic en **Todos los programas**.
2. Haga clic en **Microsoft SQL Server 2016**.
3. Haga clic en **Herramientas de configuración**.
4. Haga clic con el botón derecho en el **Administrador de configuración de Reporting Services** y haga clic en **Ejecutar como administrador**.

O:

1. Haga clic en **Iniciar**.
2. En el cuadro de diálogo **Buscar programas y archivos**, escriba **Reporting Services**. Si la máquina virtual está ejecutando Windows Server 2012, escriba **Reporting Services** en la pantalla de Inicio de Windows Server 2012.
3. Haga clic con el botón derecho en el **Administrador de configuración de Reporting Services** y haga clic en **Ejecutar como administrador**.
   
    ![buscar el administrador de configuración de ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurar Reporting Services
**Cuenta de servicio y dirección URL del servicio web:**

1. Compruebe que el **nombre del servidor** es el nombre del servidor local y haga clic en **Conectar**.
2. Observe que el campo **Nombre de la base de datos del servidor de informes**está en blanco. La base de datos se crea cuando se completa la configuración.
3. Verifique que el **estado del servidor de informes** es **Iniciado**. Si quiere comprobar el servicio en el Administrador del servidor de Windows, el servicio es el servicio de Windows de **SQL Server Reporting Services** .
4. Haga clic en **Cuenta de servicio** y cambie la cuenta según sea necesario. Si se usa la máquina virtual en un entorno que no está unido a dominio, la cuenta de **ReportServer** integrada es suficiente. Para obtener más información sobre la cuenta de servicio, vea [Cuenta de servicio](https://msdn.microsoft.com/library/ms189964.aspx).
5. Haga clic en **Dirección URL del servicio web** en el panel izquierdo.
6. Haga clic en **Aplicar** para configurar los valores predeterminados.
7. Anote las **Direcciones URL del servicio web del servidor de informes**. Tenga en cuenta que el puerto TCP predeterminado es 80 y forma parte de la dirección URL. En un paso posterior, cree un extremo de máquina virtual de Microsoft Azure para el puerto.
8. En el panel **Resultados** , compruebe las acciones completadas correctamente.

**Base de datos**

1. Haga clic en **Base de datos** en el panel izquierdo.
2. Haga clic en **Cambiar base de datos**.
3. Compruebe que la opción **Crear una nueva base de datos del servidor de informes** está seleccionada y luego haga clic en Siguiente.
4. Compruebe el valor de **Nombre del servidor** y haga clic en **Probar conexión**.
5. Si el resultado es **La conexión de prueba se estableció correctamente**, haga clic en **Aceptar** y luego en **Siguiente**.
6. Tenga en cuenta que el nombre de la base de datos es **ReportServer** y el **modo del servidor de informes** es **Nativo**, luego haga clic en **Siguiente**.
7. Haga clic en **Siguiente** on the **Credenciales** .
8. Haga clic en **Siguiente** on the **Resumen** .
9. Haga clic en **Siguiente** on the **Avanzar y finalizar** .

**Dirección URL del portal web o URL del Administrador de informes de 2012 y 2014:**

1. En el panel izquierdo, haga clic en **URL del portal web** o **URL del Administrador de informes** para 2014 y 2012.
2. Haga clic en **Aplicar**.
3. En el panel **Resultados** , compruebe las acciones completadas correctamente.
4. Haga clic en **Salir**.

Para obtener información sobre los permisos del servidor de informes, consulte [Conceder permisos en un servidor de informes de modo nativo](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Ir hasta el Administrador de informes local
Para comprobar la configuración, examine hasta el administrador de informes en la máquina virtual.

1. En la máquina virtual, inicie Internet Explorer con privilegios de administrador.
2. Vaya a http://localhost/reports en la máquina virtual.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Para conectarse al portal web remoto o al Administrador de informes de 2014 y 2012
Si quiere conectarse al portal web o al Administrador de informes de 2014 y 2012, cree un nuevo punto de conexión TCP de máquina virtual en la máquina virtual desde un equipo remoto. De forma predeterminada, el servidor de informes escucha las solicitudes HTTP en el **puerto 80**. Si configura las direcciones URL del servidor de informes para usar un puerto diferente, debe especificar ese número de puerto en las instrucciones siguientes.

1. Crear un extremo para la máquina virtual del puerto 80 TCP Para obtener más información, vea la sección [Extremos de máquina virtual y puertos de firewall](#virtual-machine-endpoints-and-firewall-ports) de este documento.
2. Abra el puerto 80 en el firewall de la máquina virtual.
3. Navegue hasta el portal web o el administrador de informes. Como nombre de servidor en la URL, use el **nombre de DNS** de la máquina virtual de Azure. Por ejemplo: 
   
    **Servidor de informes**: http://uebi.cloudapp.net/reportserver **Portal web**: http://uebi.cloudapp.net/reports
   
    [Configurar un firewall para el acceso del Servidor de informes](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para crear y publicar informes en la máquina virtual de Azure
En la tabla siguiente se resumen algunas de las opciones disponibles para publicar los informes existentes desde un equipo local al servidor de informes hospedados en la máquina virtual de Microsoft Azure:

* **Generador de informes**: la máquina virtual incluye la versión de un solo clic del Generador de informes de Microsoft SQL Server para SQL 2014 y 2012. Para iniciar el Generador de informes por primera vez en la máquina virtual con SQL 2016:
  
  1. Inicie el explorador con privilegios administrativos.
  2. En la máquina virtual, navegue al portal web y seleccione el icono **Descargar** en la esquina superior derecha.
  3. Seleccione **Generador de informes**.
     
     Para obtener más información, consulte [Iniciar el Generador de informes](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools: máquina virtual**: SQL Server Data Tools se instala en la máquina virtual y se puede usar para crear **proyectos del Servidor de informes** e informes en la máquina virtual. SQL Server Data Tools puede publicar los informes en el servidor de informes en la máquina virtual.
* **SQL Server Data Tools: remoto**: en el equipo local, cree un proyecto de Reporting Services en SQL Server Data Tools que contenga informes de Reporting Services. Configure el proyecto para que se conecte a la dirección URL del servicio web.
  
    ![propiedades del proyecto de ssdt para proyecto SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Cree una unidad de disco duro de .VHD que contenga informes y luego cargue y conecte la unidad.
  
  1. Cree una unidad de disco duro de .VHD en el equipo local que contenga los informes.
  2. Cree e instale un certificado de administración.
  3. Cargue el archivo VHD en Azure mediante el cmdlet Add-AzureVHD [Crear y cargar un VHD de Windows Server a Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Acople el disco a la máquina virtual.

## <a name="install-other-sql-server-services-and-features"></a>Instalar otras características y servicios de SQL Server
Para instalar servicios de SQL Server adicionales, como Analysis Services en modo tabular, ejecute al Asistente para instalación de SQL Server. Los archivos de instalación están en el disco local de la máquina virtual.

1. Haga clic en **Inicio** y luego haga clic en **Todos los programas**.
2. Haga clic en **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** o **Microsoft SQL Server 2012** y luego haga clic en **Herramientas de configuración**.
3. Haga clic en el **Centro de instalación de SQL Server**.

O ejecute C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe o C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> La primera vez que ejecute el programa de instalación de SQL Server, se podrá descargar más archivos de instalación y requerir un reinicio de la instalación de SQL Server.
> 
> Si necesita personalizar repetidamente la imagen seleccionada de la máquina virtual de Microsoft Azure, considere la posibilidad de crear su propia imagen de SQL Server. La funcionalidad SysPrep de Analysis Services se habilitó con SQL Server 2012 SP1 CU2. Para más información, consulte [Consideraciones acerca de la instalación de SQL Server con SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) y [Sysprep Support for Server Roles (Compatibilidad de Sysprep con roles de servidor)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Para instalar el modo tabular de Analysis Services
En los pasos de esta sección se **resume** la instalación del modo tabular de Analysis Services. Para obtener más información, vea lo siguiente: 

* [Instalar Analysis Services en modo tabular](https://msdn.microsoft.com/library/hh231722.aspx)
* [Modelado tabular (Tutorial de Adventure Works)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Para instalar el modo tabular de Analysis Services:**

1. En el asistente para la instalación de SQL Server, haga clic en **Instalación** en el panel izquierdo y en **Nueva instalación independiente de SQL Server o agregar características a una instalación existente**.
   
   * Si ve **Buscar carpeta**, vaya a c:\SQLServer_13.0_full, c:\SQLServer_12.0_full o c:\SQLServer_11.0_full y haga clic en **Aceptar**.
2. Haga clic en **Siguiente** en la página de actualizaciones del producto.
3. En la página **Tipo de instalación**, seleccione **Realizar una nueva instalación de SQL Server** y haga clic en **Siguiente**.
4. En la página **Rol de instalación**, haga clic en **Instalación de características de SQL Server**.
5. En la página **Selección de características**, haga clic en **Analysis Services**.
6. En la página **Configuración de instancia**, escriba un nombre descriptivo, como **Tabular** en los cuadros de texto **Instancia con nombre** e **Id. de instancia**.
7. En la página **Configuración de Analysis Services**, seleccione **Modo tabular**. Agregue el usuario actual a la lista de permisos administrativos.
8. Complete y cierre el asistente para instalación de SQL Server.

## <a name="analysis-services-configuration"></a>Configuración de Analysis Services
### <a name="remote-access-to-analysis-services-server"></a>Acceso remoto al servidor de Analysis Services
El servidor de Analysis Services solo admite la Autenticación de Windows. Para obtener acceso a Analysis Services de manera remota desde aplicaciones cliente como SQL Server Management Studio o SQL Server Data Tools, la máquina virtual debe estar unida al dominio local mediante la instancia de Azure Virtual Network. Para obtener más información, consulte [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

Una **instancia predeterminada** de Analysis Services escucha en el puerto TCP **2383**. Abra el puerto en el firewall de máquinas virtuales. Una instancia con nombre en clúster de Analysis Services también escucha en el puerto **2383**.

Para una **instancia con nombre** de Analysis Services, se requiere el servicio de SQL Server Browser para administrar el acceso a los puertos. La configuración predeterminada de SQL Server Browser es el puerto **2382**.

En el firewall de máquinas virtuales, abra el puerto **2382** y cree un puerto de instancia con nombre de Analysis Services.

1. Para comprobar los puertos que ya están en uso en la máquina virtual y qué proceso está usando los puertos, ejecute el siguiente comando con privilegios administrativos:
   
        netstat /ao
2. Use SQL Server Management Studio para crear un puerto de instancia con nombre de Analysis Services actualizando el valor "Puerto" en las propiedades generales de la instancia de AS. Para obtener más información, vea "Usar un puerto fijo para una instancia con nombre o predeterminada" en [Configurar el Firewall de Windows para permitir el acceso a Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Reinicie la instancia tabular del servicio Analysis Services.

Para obtener más información, vea la sección **Extremos de máquina virtual y puertos de firewall** de este documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Extremos de máquina virtual y puertos de firewall
En esta sección se resumen los extremos de máquina virtual de Microsoft Azure para crear y los puertos para abrir en los firewalls de máquina virtual. En la tabla siguiente se resumen los puertos **TCP** para los que crear extremos y los puertos que se abrirán en el firewall de máquinas virtuales.

* Si está usando una sola máquina virtual y los dos elementos siguientes son verdaderos, no necesitará crear extremos de máquina virtual y no tendrá que abrir los puertos en el firewall de la máquina virtual.
  
  * No se conecta de manera remota a las características de SQL Server en la máquina virtual. El establecimiento de una conexión a escritorio remoto en la máquina virtual y el acceso a las características de SQL Server localmente en la máquina virtual no se considera una conexión remota a las características de SQL Server.
  * No une la máquina virtual a un dominio local a través de la red virtual de Azure u otra solución de tunelización de VPN.
* Si la máquina virtual no está unida a un dominio pero quiere conectarse remotamente a las características de SQL Server en máquina virtual:
  
  * Abra los puertos en el firewall en la máquina virtual.
  * Cree extremos de máquina virtual para los puertos anotados (*).
* Si la máquina virtual se une a un dominio con un túnel VPN, como las redes virtuales de Azure, los extremos no son necesarios. Sin embargo, abra los puertos en el firewall en la máquina virtual.
  
  | Port | type | DESCRIPCIÓN |
  | --- | --- | --- |
  | **80** |TCP |Acceso remoto al servidor de informes (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. Esto es necesario cuando la máquina virtual se une a un dominio. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Instancia predeterminada de SQL Server Analysis Services e instancias con nombre en clúster. |
  | **Definida por el usuario** |TCP |Cree un puerto de instancia con nombre de Analysis Services estático para un número de puerto que elija y luego desbloquee el número de puerto en el firewall. |

Para obtener más información sobre la creación de extremos, consulte lo siguiente:

* Crear extremos:[Configuración de extremos en una máquina virtual](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* SQL Server: consulte la sección "Completar los pasos de configuración para conectarse a la máquina virtual mediante SQL Server Management Studio" de [Aprovisionamiento de una máquina virtual de SQL Server en Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

En el siguiente diagrama se muestran los puertos que se abrirán en el firewall de máquina virtual para permitir el acceso remoto a las características y componentes en la máquina virtual.

![puertos que deben abrirse para aplicaciones de bi en máquinas virtuales de Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Recursos
* Revise la directiva de compatibilidad para software de servidor de Microsoft usado en el entorno de máquina virtual de Azure. En el tema siguiente se resume la compatibilidad con características como BitLocker, agrupación en clústeres de conmutación por error y equilibrio de carga de red. [Compatibilidad de software de servidor de Microsoft para Azure Virtual Machines](http://support.microsoft.com/kb/2721672).
* [Información general sobre SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Aprovisionamiento de una máquina virtual de SQL Server en Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Acoplamiento de un disco de datos a una máquina virtual](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migración de una base de datos a SQL Server en una máquina virtual de Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Determinar el modo de servidor de una instancia de Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Modelado multidimensional (Tutorial de Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centro de documentación de Azure](https://azure.microsoft.com/documentation/)
* [Usar Power BI en un entorno híbrido](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Envíe comentarios e información de contacto a través de Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Contenido de la Comunidad
* [Administración de Azure SQL Database con PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)


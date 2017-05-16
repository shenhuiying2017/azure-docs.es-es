---
title: "Aprovisionamiento de una máquina virtual de SQL Server | Microsoft Docs"
description: "Cree y conéctese a una máquina virtual de SQL Server en Azure mediante el Portal. Este tutorial utiliza el modo de Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
editor: 
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: jroth
experimental: true
experimental_id: a641df96-f27d-40
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: e16792bb762287bc16c280386981a4d442448674
ms.contentlocale: es-es
ms.lasthandoff: 04/22/2017


---
# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure
> [!div class="op_single_selector"]
> * [Portal](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
> 
> 

Este completo tutorial muestra cómo usar el Portal de Azure para aprovisionar una máquina virtual que ejecuta SQL Server.

La galería de máquinas virtuales (VM) de Azure incluye varias imágenes que contienen Microsoft SQL Server. Con unos pocos clics, puede seleccionar una de las imágenes de máquina virtual de SQL en la galería y puede aprovisionar la máquina virtual en su entorno de Azure.

En este tutorial, aprenderá lo siguiente:

* [Selección de una imagen de la máquina virtual de SQL de la galería](#select-a-sql-vm-image-from-the-gallery)
* [Configuración y creación de la máquina virtual](#configure-the-vm)
* [Apertura de la máquina virtual con Escritorio remoto](#open-the-vm-with-remote-desktop)
* [Conexión a SQL Server de manera remota](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Selección de una imagen de la máquina virtual de SQL de la galería
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) con su cuenta.

   > [!NOTE]
   > Si no tiene una cuenta de Azure, visite [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

2. En el Portal de Azure, haga clic en **Nuevo**. Se abrirá la hoja **Nuevo** . Los recursos de máquina virtual con SQL Server están en el grupo **Proceso** de Marketplace.
3. En la hoja **Nuevo**, haga clic en **Proceso** y después haga clic en **Ver todo**.
4. En el cuadro de texto **Filtro** escriba SQL Server y presione la tecla ENTRAR.

   ![Hoja Máquinas virtuales de Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade2.png)

5. Consulte las imágenes de SQL Server disponibles. Cada imagen identifica una versión de SQL Server y un sistema operativo. 
6. Seleccione la imagen de SQL Server 2016 SP1 Developer en Windows Server 2016.

   > [!TIP]
   > La edición Developer se usa en este tutorial porque es una edición completa de SQL Server que es gratuita para desarrollo y pruebas. Solo paga por el costo de ejecutar la máquina virtual.

   > [!NOTE]
   > Las imágenes de máquina virtual de SQL incluyen los costos de licencia de SQL Server en los precios por minuto de la máquina virtual que se crea (excepto en las ediciones Developer y Express). SQL Server Developer es gratis para desarrollo y pruebas (no así para producción) y SQL Express es gratis para cargas de trabajo ligeras (menos de 1 GB de memoria, menos de 10 GB de almacenamiento).
   > Existe otra opción para traer su propia licencia (BYOL) y pagar solo la máquina virtual. Esos nombres de imagen tienen el prefijo {BYOL}. Para más información sobre estas opciones, consulte [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server en Azure).

7. En **Seleccionar un modelo de implementación**, compruebe que la opción **Resource Manager** está seleccionada. Resource Manager es el modelo de implementación recomendado para las máquinas virtuales nuevas. Haga clic en **Crear**.

    ![Creación de una máquina virtual de SQL con Resource Manager](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configuración de la máquina virtual
Existen cinco hojas en las que puede configurar una máquina virtual de SQL Server.

| Paso | Description |
| --- | --- |
| **Aspectos básicos** |[Configuración básica](#1-configure-basic-settings) |
| **Tamaño** |[Elección del tamaño de la máquina virtual](#2-choose-virtual-machine-size) |
| **Configuración** |[Configuración de características opcionales](#3-configure-optional-features) |
| **Configuración de SQL Server** |[Configuración de SQL Server](#4-configure-sql-server-settings) |
| **Resumen** |[Revisión del resumen](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Configuración básica
En la hoja **Básico** , especifique la siguiente información:

* Escriba un nombre de máquina virtual único en **Nombre**.
* Especifique un valor en **Nombre de usuario** para la cuenta de administrador local de la máquina virtual. Esta cuenta se agregará también al rol fijo de servidor de SQL Server **sysadmin** .
* Proporcione una contraseña segura en **Contraseña**.
* Si tiene varias suscripciones, compruebe que la suscripción de la nueva máquina virtual es correcta.
* En el cuadro **Grupo de recursos** , escriba un nombre para el nuevo grupo de recursos. Si lo desea, también puede hacer clic en **Usar existente** para usar un grupo de recursos existente. Un grupo de recursos es una colección de recursos relacionados de Azure (máquinas virtuales, cuentas de almacenamiento, redes virtuales, etc.).
  
  > [!NOTE]
  > El uso de un grupo de recursos resulta útil si solo está probando o aprendiendo sobre las implementaciones de SQL Server en Azure. Cuando haya terminado la prueba, elimine el grupo de recursos. De ese modo, se eliminará automáticamente la máquina virtual y los recursos asociados a ese grupo. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).
  > 
  > 
* Seleccione un valor de **Ubicación** para esta implementación.
* Haga clic en **Aceptar** para guardar la configuración.
  
    ![Hoja Datos básicos de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Elección del tamaño de la máquina virtual
En el paso **Tamaño**, elija un tamaño de máquina virtual en la hoja **Elegir un tamaño**. En la hoja, aparecen los tamaños de máquina recomendados con arreglo a la imagen seleccionada.

> [!IMPORTANT]
> El costo mensual estimado que se muestra en la hoja **Elegir un tamaño** no incluye los costos de licencias de SQL Server. Este es el costo solo de la máquina virtual. Para las ediciones Express y Developer de SQL Server, este es el costo total estimado. Para conocer el costo de otras ediciones, consulte la [página de precios de máquinas virtuales Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) y seleccione la edición de destino de SQL Server. Consulte también [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Orientación de precios de máquinas virtuales de SQL Server en Azure).

![Opciones de tamaño de la máquina virtual de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Para las cargas de trabajo de producción, se recomienda seleccionar un tamaño de máquina virtual que admita [Almacenamiento premium](../../../storage/storage-premium-storage.md). Si no necesita ese nivel de rendimiento, use el botón **Ver todo** para consultar todas las opciones de tamaño de máquina. Por ejemplo, podría usar un tamaño de máquina más pequeño para un entorno de prueba o desarrollo.

> [!NOTE]
> Para más información sobre los tamaños de máquina virtual, consulte [Tamaños de máquina virtual](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para más información sobre los tamaños de las máquinas virtuales de SQL Server, consulte [Procedimientos recomendados para mejorar el rendimiento de SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md).

Elija el tamaño de la máquina y haga clic en **Seleccionar**.

## <a name="3-configure-optional-features"></a>3. Configuración de características opcionales
En la hoja **Configuración** , configure el Almacenamiento de Azure, la red y la supervisión para la máquina virtual.

* En **Almacenamiento**, especifique Estándar o Premium (SSD) en **Tipo de disco**. Se recomienda Almacenamiento premium para cargas de trabajo de producción.

> [!NOTE]
> Si selecciona Premium (SSD) para un tamaño de máquina que no admite el Almacenamiento premium, el tamaño de la máquina se ajustará automáticamente.  
> 
> 

* En **Cuenta de almacenamiento**, puede aceptar el nombre de la cuenta de almacenamiento aprovisionado automáticamente. También puede hacer clic en **Cuenta de almacenamiento** para elegir una existente y configurar el tipo de cuenta de almacenamiento. De forma predeterminada, Azure crea una nueva cuenta de almacenamiento con redundancia local. Para más información sobre las opciones de almacenamiento, consulte [Replicación de Almacenamiento de Azure](../../../storage/storage-redundancy.md).
* En **Red**, puede aceptar los valores rellenados de forma automática. También puede hacer clic en cada una de las características para configurar manualmente los campos **Red virtual**, **Subred**, **Dirección IP pública** y **Grupo de seguridad de red**. Para este tutorial, conserve los valores predeterminados.
* De forma predeterminada, Azure habilita **Supervisión** con la misma cuenta de almacenamiento que se designó para la máquina virtual. Puede cambiar estas opciones aquí.
* En **Conjunto de disponibilidad**, especifique uno. Por lo que respecta a este tutorial, puede seleccionar la opción **Ninguno**. Si va a configurar grupos de disponibilidad AlwaysOn de SQL, configure la disponibilidad para evitar volver a crear la máquina virtual.  Para obtener más información, consulte [Administración de la disponibilidad de las máquinas virtuales](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Cuando haya terminado la configuración, haga clic en **Aceptar**.

## <a name="4-configure-sql-server-settings"></a>4. Configuración de SQL Server
En la hoja **Configuración de SQL Server** , configure las optimizaciones y los valores específicos de SQL Server. Entre los valores que se pueden configurar para SQL Server se incluyen los siguientes.

| Configuración |
| --- |
| [Conectividad](#connectivity) |
| [Autenticación](#authentication) |
| [Configuración de almacenamiento](#storage-configuration) |
| [Aplicación de revisiones automatizada](#automated-patching) |
| [Copia de seguridad automatizada](#automated-backup) |
| [Integración del Almacén de claves de Azure](#azure-key-vault-integration) |
| [R Services](#r-services) |

### <a name="connectivity"></a>Conectividad
En **Conectividad SQL**, especifique el tipo de acceso que desea para la instancia de SQL Server de esta máquina virtual. En este tutorial, seleccione **Público (Internet)** para permitir que se establezcan conexiones a SQL Server desde equipos o servicios de Internet. Si esta opción está seleccionada, Azure configurará automáticamente el firewall y el grupo de seguridad de red para permitir el tráfico en el puerto 1433.  

![Opciones de conectividad de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Para conectarse a SQL Server a través de Internet, también debe habilitar la autenticación de SQL Server, que se describe en la sección siguiente.

> [!NOTE]
> Puede agregar más restricciones de las comunicaciones de red en la máquina virtual de SQL Server. Puede hacerlo mediante la edición del grupo de seguridad de red después de crear la máquina virtual. Para más información, consulte [¿Qué es un grupo de seguridad de red?](../../../virtual-network/virtual-networks-nsg.md)
> 
> 

Si prefiere no permitir las conexiones al motor de base de datos a través de Internet, elija una de las siguientes opciones:

* **Local (solo dentro de la máquina virtual)** : elija esta opción para permitir conexiones a SQL Server únicamente desde dentro de la máquina virtual.
* **Privado (dentro de la red virtual)** : elija esta opción para permitir conexiones a SQL Server desde máquinas o servicios que se encuentren en la misma red virtual.

> [!NOTE]
> La imagen de máquina virtual para SQL Server Express Edition no habilita automáticamente el protocolo TCP/IP. Esto es así incluso para las opciones de conectividad pública y privada. Para Express Edition, debe usar el Administrador de configuración de SQL Server para [habilitar manualmente el protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) después de crear la máquina virtual.
> 
> 

En general, mejore la seguridad al elegir la conectividad más restrictiva que permita su escenario. No obstante, todas las opciones se pueden proteger mediante reglas del grupo de seguridad de red y la autenticación de SQL o Windows.

**Puerto** es 1433. Puede especificar un número de puerto diferente.
Para más información, consulte [Conexión a una máquina virtual de SQL Server en Azure (Resource Manager) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Autenticación
Si la autenticación de SQL Server es necesaria, en **Habilitar** under **Habilitar**.

![Autenticación de SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Si piensa obtener acceso a SQL Server a través de Internet (es decir, con la opción de conectividad pública), debe habilitar aquí la autenticación de SQL. El acceso público a SQL Server requiere la utilización de autenticación de SQL.
> 
> 

Si habilita la autenticación de SQL Server, especifique los valores de **Nombre de inicio de sesión** y **Contraseña**. Este nombre de usuario se establecerá como inicio de sesión de autenticación de SQL Server y será miembro del rol fijo de servidor **sysadmin** . Para más información sobre los modos de autenticación, consulte [Elegir un modo de autenticación](http://msdn.microsoft.com/library/ms144284.aspx) .

Si no habilita la autenticación de SQL Server, puede utilizar la cuenta de administrador local en la máquina virtual para conectarse a la instancia de SQL Server.

### <a name="storage-configuration"></a>Configuración de almacenamiento
Haga clic en **Configuración de almacenamiento** para especificar los requisitos de almacenamiento.

![Configuración de almacenamiento de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Si selecciona Almacenamiento estándar, esta opción no está disponible. La optimización del almacenamiento automático está disponible solo para Almacenamiento premium.
> 
> 

Puede especificar requisitos como operaciones de entrada/salida por segundo (IOPS), el rendimiento en MB/s y el tamaño de almacenamiento total. Configure estas opciones mediante las escalas deslizantes. El portal calcula automáticamente el número de discos según estos requisitos.

De forma predeterminada, Azure optimiza el almacenamiento de 5000 IOPS, 200 MB y 1 TB de espacio de almacenamiento. Puede cambiar estos valores de almacenamiento en función de la carga de trabajo. En **Storage optimized for**(Optimización de almacenamiento para), seleccione una de las siguientes opciones:

* **General** es la configuración predeterminada y admite la mayoría de las cargas de trabajo.
* **Transaccional** optimiza el almacenamiento en las cargas de trabajo OLTP de las bases de datos tradicionales.
* **Almacenamiento de datos** optimiza el almacenamiento en las cargas de trabajo de informes y análisis.

> [!NOTE]
> Los límites superiores de los controles deslizantes varían según el tamaño de la máquina virtual seleccionada.
> 
> 

### <a name="automated-patching"></a>Aplicación de revisiones automatizada
**Automated patching** está habilitada de forma predeterminada. La aplicación de revisiones automatizada permite a Azure aplicar automáticamente las revisiones de SQL Server y del sistema operativo. Especifique un día de la semana, la hora y la duración de una ventana de mantenimiento. Azure realiza la aplicación de revisión en esta ventana de mantenimiento. La programación de la ventana de mantenimiento utiliza la configuración regional de la máquina virtual para la hora. Si no desea que Azure efectúe automáticamente la aplicación de revisiones de SQL Server y del sistema operativo, haga clic en **Deshabilitar**.  

![Aplicación de revisiones automatizada de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Para más información, consulte [Aplicación de revisiones automatizadas para SQL Server en Azure Virtual Machines (implementación clásica)](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Copia de seguridad automatizada
Habilite las copias de seguridad automáticas en todas las bases de datos en **Copia de seguridad automatizada**. La copia de seguridad automatizada está deshabilitada de forma predeterminada.

Si la copia de seguridad automática de SQL está habilitada, puede configurar lo siguiente:

* El período de retención (días) de las copias de seguridad
* La cuenta de almacenamiento que se utilizará para las copias de seguridad
* La opción de cifrado y la contraseña de las copias de seguridad
* La copia de seguridad de bases de datos del sistema
* La configuración de la programación de copia de seguridad

Para cifrar la copia de seguridad, haga clic en **Habilitar**. Después, especifique un valor en **Contraseña**. Azure crea un certificado para cifrar las copias de seguridad y utiliza la contraseña especificada para proteger ese certificado.

![Copia de seguridad automatizada de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup2.png)

 Para obtener más información, vea [Copia de seguridad automatizada para SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integración del Almacén de claves de Azure
Para almacenar secretos de seguridad en Azure para el cifrado, haga clic en **Integración del Almacén de claves de Azure** y en **Habilitar**.

![Integración del Almacén de claves de Azure de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

En la tabla siguiente se enumeran los parámetros necesarios para configurar la integración del Almacén de claves de Azure.

| PARÁMETRO | Description | EJEMPLO: |
| --- | --- | --- |
| **Dirección URL del Almacén de claves** |La ubicación del Almacén de claves. |https://contosokeyvault.vault.azure.net/ |
| **Nombre de entidad de seguridad** |Nombre de la entidad de servicio de Azure Active Directory Esto se conoce también como Id. Este nombre también se conoce como «identificador de cliente». |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Secreto de entidad de seguridad** |Secreto de la entidad de seguridad de servicio de Azure Active Directory Este secreto también se conoce como «secreto de cliente». |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nombre de credencial** |**Nombre de credencial**: la integración de AKV crea una credencial en SQL Server, permitiendo el acceso de la máquina virtual al Almacén de claves. Elija un nombre para esta credencial. |mycred1 |

Para más información, consulte [Configuración de la integración de Azure Key Vault para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-ps-sql-keyvault.md).

Cuando termine de definir la configuración de SQL Server, haga clic en **Aceptar**.

### <a name="r-services"></a>R Services
Tiene la opción para habilitar [SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx). Esto le permite utilizar análisis avanzado con SQL Server 2016. Haga clic en **Habilitar** on the **SQL Server Settings** .

![Habilitación de SQL Server R Services](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)


## <a name="5-review-the-summary"></a>5. Revisión del resumen
En la hoja **Resumen**, revise el resumen y haga clic en **Aceptar** para crear la instancia de SQL Server, el grupo de recursos y los recursos especificados para esta máquina virtual.

Puede supervisar la implementación desde el portal de Azure. En el botón **Notificaciones** de la parte superior de la pantalla, se muestra el estado básico de la implementación.

> [!NOTE]
> Para darle una idea de los tiempos de implementación, se implementó una máquina virtual de SQL en la región este de Estados Unidos con la configuración predeterminada. Esta implementación de prueba tardó un total de 26 minutos en completarse. Pero podría experimentar un tiempo de implementación más rápido o más lento en función de su región y de la configuración seleccionada.
> 
> 

## <a name="open-the-vm-with-remote-desktop"></a>Apertura de la máquina virtual con Escritorio remoto
Use los pasos siguientes para conectarse a la máquina virtual con Escritorio remoto.

1. Después de crear la máquina virtual de Azure, aparecerá un icono de la misma en el panel de Azure. También puede encontrarlo examinando las máquinas virtuales existentes. Haga clic en la nueva máquina virtual de SQL. Aparecerá la hoja **Máquina virtual** con los detalles de esta máquina.
2. En la parte superior de la hoja **Máquina virtual**, haga clic en **Conectar**.
3. El explorador descargará un archivo RDP para la máquina virtual. Abra el archivo RDP.
    ![Escritorio remoto para máquina virtual SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
4. La conexión a Escritorio remoto le avisará de que no se puede identificar el publicador de esta conexión remota. Haga clic en **Conectar** para continuar.
5. En el cuadro de diálogo **Seguridad de Windows**, haga clic en **Usar otra cuenta**.
6. En **Nombre de usuario**, escriba **\<nombreDeUsuario>**, donde <user name> es el nombre de usuario que especificó al configurar la máquina virtual. Tiene que agregar una barra diagonal inversa delante del nombre.
7. Escriba el valor de **Contraseña** que configuró antes para esta máquina virtual y haga clic en **Aceptar** para conectarse.
8. Si se le pregunta en otro cuadro de diálogo **Conexión a Escritorio remoto** si desea conectarse, haga clic en **Sí**.

Después de conectarse a la máquina virtual de SQL Server, puede iniciar SQL Server Management Studio y conectarse con la autenticación de Windows mediante sus credenciales de administrador local. Si ha habilitado la autenticación de SQL Server, también puede conectarse con la autenticación de SQL mediante el inicio de sesión de SQL y la contraseña configurada durante el aprovisionamiento.

El acceso a la máquina le permite cambiar directamente la máquina y la configuración de SQL Server según sus necesidades. Por ejemplo, podría configurar el firewall o cambiar la configuración de SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Conexión a SQL Server de manera remota
En este tutorial, seleccionamos el acceso **Público** para la máquina virtual y **Autenticación de SQL Server**. Estos valores configuran automáticamente la máquina virtual para permitir conexiones de SQL Server desde cualquier cliente a través de Internet (suponiendo que tengan el inicio de sesión SQL correcto).

> [!NOTE]
> Si no seleccionó la opción Público durante el aprovisionamiento, deberá seguir algunos pasos adicionales para poder acceder a la instancia de SQL Server a través de Internet. Para más información, consulte [Conexión a una máquina virtual de SQL Server en Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
> 
> 

Las secciones siguientes muestran cómo conectarse a la instancia de SQL Server en la máquina virtual desde otro equipo a través de Internet.

> [!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el uso de SQL Server en Azure, consulte Ia página sobre [SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) y las [Preguntas más frecuentes](virtual-machines-windows-sql-server-iaas-faq.md).

Para obtener información general sobre Azure Virtual Machines, vea el vídeo [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)(VM de Azure es la mejor plataforma para SQL Server 2016).

[Explore la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para SQL Server en máquinas virtuales de Azure.



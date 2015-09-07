<properties 
	pageTitle="Almacenamiento de datos de SQL Server y cargas de trabajo transaccionales en máquinas virtuales de Azure"
	description="Describe las imágenes de máquinas virtuales de SQL Server preconfiguradas y optimizadas en Azure para cargas de trabajo de almacenamiento de datos y OLTP."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth"/>

# Almacenamiento de datos de SQL Server y cargas de trabajo transaccionales en máquinas virtuales de Azure

Para usar SQL Server para el almacenamiento de datos o las cargas de trabajo transaccionales en una máquina virtual de Azure, se recomienda usar una de las imágenes de máquina virtual preconfiguradas de la Galería de máquinas virtuales de Azure. Estas imágenes se han optimizado según las recomendaciones de [Prácticas recomendadas de rendimiento para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md).

Este artículo se centra en la ejecución de estas cargas de trabajo en Máquinas virtuales de Azure (lo que también se conoce como infraestructura como servicio, o IaaS). El almacenamiento de datos y las cargas de trabajo transaccionales también se pueden ejecutar como un servicio en Azure. Para obtener más información, consulte [Documentación de Almacenamiento de datos SQL](http://azure.microsoft.com/documentation/services/sql-data-warehouse/) y [Documentación de Base de datos SQL de Azure](http://azure.microsoft.com/documentation/services/sql-database/).

## ¿Qué imágenes de máquinas virtuales preconfiguradas están disponibles?

En la Galería de máquinas virtuales de Azure están disponibles las imágenes de máquinas virtuales preconfiguradas siguientes:

- [SQL Server 2014 Enterprise optimizado para cargas de trabajo transaccionales en Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014fortransactionalworkloadswindowsserver2012r2/)

- [SQL Server 2014 Enterprise optimizado para almacenamiento de datos en Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014datawarehousingwindowsserver2012r2/)

- [SQL Server 2012 SP2 Enterprise optimizado para cargas de trabajo transaccionales en Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise optimizado para cargas de trabajo de almacenamiento de datos en Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise optimizado para cargas de trabajo transaccionales en Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012/)

- [SQL Server 2012 SP2 Enterprise optimizado para almacenamiento de datos en Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012/)

Actualmente estas imágenes se admiten en las instancias de máquina virtual que permiten hasta 16 discos de datos conectados para proporcionar el máximo rendimiento (o ancho de banda agregado). Estas instancias son A4 , A7, A8, A9, D4, D13, D14, F3, G4 y G5 de nivel Estándar y A4 de nivel Básico. Para obtener más información sobre los tamaños y opciones, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md) .

>[AZURE.NOTE]Antes de septiembre de 2014, estaban disponibles anteriores imágenes transaccionales y de la galería de DW. Sin embargo, dichas imágenes requerían que conectara discos de datos para poder usarlas. Se recomienda usar las imágenes más recientes anteriores, ya que están listas para su uso tras el aprovisionamiento.

## Aprovisionamiento de una VM de SQL desde la galería mediante imágenes transaccionales/de DW

1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).

1. Haga clic en **MÁQUINA VIRTUAL** en las opciones de menú de Azure del panel izquierdo.

1. Haga clic en **NUEVO** en la esquina inferior izquierda y, a continuación, haga clic en **PROCESO**, **MÁQUINA VIRTUAL** y **DE LA GALERÍA**.

1. En la página de selección de imágenes de máquinas virtuales, seleccione una de SQL Server para imágenes transaccionales o de almacenamiento de datos.

	![Galería de máquinas virtuales de Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814362.png)

1. En la página **Configuración de máquina virtual**, en la opción **TAMAÑO**, elija uno de los tamaños admitidos.

	![Configuración de la Galería de máquinas virtuales de Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814363.png)

	>[AZURE.NOTE]Actualmente solo se admiten A4 , A7, A8, A9, D4, D13, D14, G3, G4 y G5 de nivel Estándar y A4 de nivel Básico. Si se intentan aprovisionar tamaños de VM no compatibles, se producirá un error.

1. Espere hasta que finalice el aprovisionamiento. Mientras espera, puede ver el estado de aprovisionamiento en la página de máquinas virtuales (como muestra la imagen siguiente). Cuando haya finalizado el aprovisionamiento, el estado será **En ejecución** y aparecerá una marca de verificación.

	![Estado de la Galería de máquinas virtuales de Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814364.png)

## Uso de PowerShell para crear las imágenes transaccionales/de DW

Para crear la máquina virtual también se puede usar el cmdlet de PowerShell **New-AzureQuickVM**. Debe usar el nombre del servicio en la nube, el nombre de la máquina virtual, el nombre de la imagen, el nombre y la contraseña del usuario administrador, e información similar como parámetros. Una forma sencilla de obtener el nombre de la imagen es usar **Get-AzureVMImage** para enumerar todas las imágenes de máquina virtual disponibles.

Por ejemplo, el siguiente comando de PowerShell devuelve la última imagen que coincida con la etiqueta de imagen **SQL Server 2012 SP2 Enterprise optimizado para cargas de trabajo de almacenamiento de datos en Windows Server 2012 R2** de la lista de la sección anterior.

	(Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2"} | sort PublishedDate -Descending)[0].ImageName

Para obtener más información sobre la creación de imágenes con PowerShell, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Configuraciones específicas que se incluyen en las imágenes transaccionales/de DW

Las optimizaciones incluidas en las imágenes se basan en las [prácticas recomendadas de rendimiento para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md). En concreto, la configuración de estas imágenes incluye las siguientes optimizaciones.

>[AZURE.NOTE]Si va a traer su propia licencia y a crear una máquina virtual transaccional o de almacenamiento de datos desde el principio, puede basar las optimizaciones en el artículo de rendimiento y en el ejemplo de las optimizaciones en las siguientes imágenes de galería preconfiguradas.

### Configuraciones de disco

|Configuración|Configuración|
|---|---|
|Número de discos de datos conectados|15|
|Espacios de almacenamiento|Dos bloques de almacenamiento:<br/>: 1 bloque de datos con 12 discos de datos; tamaño fijo de 12 TB; Columna = 12<br/>: 1 bloque de registro con 3 discos de datos; tamaño fijo de 3 TB; Columna = 3<br/><br/>Un disco de datos restante que el usuario puede conectar y determinar su uso.<br/><br/>**DW**: tamaño de sección = 256 KB<br/>**Transaccional**: tamaño de sección = 64 KB|
|Tamaños de disco|1 TB cada uno|
|Almacenamiento en caché|HostCache=None|
|Tamaño de la asignación|Tamaño de la unidad de asignación NTFS = 64 KB|

### Configuraciones de SQL Server

|Configuración|Configuración|
|---|---|
|Parámetros de inicio|-T1117 para ayudar a conservar los archivos de datos con el mismo tamaño, en caso de que la base de datos necesite crecer automáticamente<br/><br/>-T1118 para ayudar en la escalabilidad de tempdb (para obtener más información, consulte [Uso de la marca de seguimiento 1118 (-T1118) de SQL Server (2005 y 2008)](http://blogs.msdn.com/b/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx?WT.mc_id=Blog_SQL_Announce_Announce).)|
|Modelo de recuperación|**DW**: establecido en SIMPLE para la base de datos modelo mediante ALTER DATABASE<br/>**Transaccional **: sin cambios|
|Ubicaciones predeterminadas de configuración|Mueva el registro de errores de SQL Server y los directorios de archivos de seguimiento a los discos de datos|
|Ubicaciones predeterminadas de las bases de datos|Bases de datos del sistema movidas a discos de datos.<br/><br/>La ubicación para crear bases de datos de usuario cambiada a discos de datos.|
|Inicialización instantánea de archivos|Enabled|
|Bloquear páginas en memoria|Habilitado (para obtener más información, consulte [Habilitar la opción Bloquear páginas en la memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).|

## P+F

- ¿Hay diferencia de precio entre las imágenes optimizadas y las que no están optimizadas?

	No. Las imágenes optimizadas siguen el mismo modelo de fijación de precios ([aquí](http://azure.microsoft.com/pricing/details/virtual-machines/) encontrará más detalles) sin costos adicionales. Tenga en cuenta que un costo mayor está asociado con tamaños superiores de instancias de VM.

- ¿Se deben tener en cuenta otras revisiones del rendimiento?

	Sí, considere la posibilidad de aplicar las revisiones de rendimiento pertinentes para SQL Server:

	- [REVISIÓN: Un rendimiento deficiente en la entrada-salida al ejecutar select en la operación de la tabla temporal en SQL Server 2012](https://support.microsoft.com/kb/2958012)

	- [REVISIÓN: "los contadores de rendimiento de SQL Server están deshabilitados" al mover el recurso de SQL Server en SQL Server de 2014](http://support.microsoft.com/kb/2973444)

- ¿Cómo encontrar más información sobre Espacios de almacenamiento?

	Para obtener más información sobre Espacios de almacenamiento, consulte [Preguntas más frecuentes (P+F) sobre Espacios de almacenamiento](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)

- ¿Cuál es la diferencia entre la nueva imagen de DW y la anterior?

	La imagen anterior de DW requiere que los clientes realicen pasos adicionales como conectar los discos de datos después de crear la máquina virtual, mientras que la nueva imagen de DW está lista para su uso tras crearla, por lo que está disponible más rápidamente y tiene menos probabilidad de errores.

- ¿Qué ocurre si necesito usar la imagen de DW anterior? ¿Hay alguna manera de acceder a ella?

	Las imágenes de máquina virtual anteriores siguen estando disponibles, pero no se puede acceder directamente a ellas desde la galería. En su lugar, puede seguir usando los cmdlets de Powershell. Por ejemplo, puede usar **Get-AzureVMImage** para enumerar todas las imágenes y cuando busque la imagen de DW anterior por su descripción y fecha de publicación, puede usar **New-AzureVM** para realizar el aprovisionamiento.

## Pasos siguientes

Después de instalar cualquier máquina virtual con SQL Server puede realizar las siguientes operaciones:

- [Migración de los datos](virtual-machines-migrate-onpremises-database.md)
- [Configuración de la conectividad](virtual-machines-sql-server-connectivity.md).

Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO9-->
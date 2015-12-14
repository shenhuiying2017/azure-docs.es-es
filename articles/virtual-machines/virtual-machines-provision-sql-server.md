<properties 
	pageTitle="Aprovisionamiento de una máquina virtual de SQL Server | Microsoft Azure" 
	description="En este tutorial se le enseña cómo crear y configurar una máquina virtual de SQL Server en Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="monicar"
	tags="azure-service-management"
	/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/26/2015" 
	ms.author="jroth"/>

# Aprovisionamiento de una máquina virtual de SQL Server en Azure

> [AZURE.SELECTOR]
- [Azure classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)

## Información general

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos. .

La galería de máquinas virtuales de Azure incluye varias imágenes que contienen Microsoft SQL Server. Puede seleccionar una de las imágenes de máquina virtual en la galería y, con unos pocos clics, puede aprovisionar la máquina virtual a su entorno de Azure.

En este tutorial, aprenderá lo siguiente:

* [Conectarse al Portal de Azure clásico y aprovisionar una máquina virtual desde la galería](#Provision)
* [Abrir la máquina virtual con Escritorio remoto y finalizar la configuración](#RemoteDesktop)
* [Finalizar los pasos de configuración para conectarse a la máquina virtual con SQL Server Management Studio en otro equipo](#SSMS)
* [Pasos siguientes](#Optional)

>[AZURE.NOTE]Este artículo describe cómo aprovisionar una máquina virtual de SQL Server con el portal existente. Sin embargo, también es posible crear y administrar VM de SQL Server en el [nuevo portal](https://manage.windowsazure.com). Existen algunas ventajas en el nuevo portal, como el uso de Almacenamiento premium de forma predeterminada, y otras opciones, como revisiones automatizadas, copias de seguridad automatizadas y configuraciones AlwaysOn. El contenido futuro cubrirá instrucciones paso a paso.

##<a id="Provision">Aprovisionamiento de una máquina virtual de SQL Server de la galería</a>

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com) con su cuenta. Si no tiene una cuenta de Azure, visite [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

2. En el Portal de Azure clásico, en la parte inferior izquierda de la página web, haga clic sucesivamente en **+NUEVO**, **PROCESO**, **MÁQUINA VIRTUAL** y **DESDE LA GALERÍA**.

3. En la página **Elegir una imagen**, haga clic en **SQL SERVER**. A continuación, seleccione una imagen de SQL Server. Haga clic en la flecha siguiente de la parte inferior derecha de la página.

	![Elegir una imagen](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Para obtener la información más actualizada sobre las imágenes compatibles de SQL Server en Azure, consulte el tema [Introducción a SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

>[AZURE.NOTE]Si tiene una máquina virtual creada con la edición de evaluación de SQL Server de imagen de plataforma, no puede actualizarla a una imagen de edición de pago por minuto en la galería. Puede elegir una de las dos siguientes opciones:
>
> - Puede crear una nueva máquina virtual mediante la edición de SQL Server pagada por minuto desde la galería y migrar los archivos de base de datos a esta nueva máquina virtual siguiendo los pasos de [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database).
> - O bien, puede actualizar una instancia existente de SQL Server Evaluation Edition a otra edición de SQL Server bajo el contrato de [Movilidad de Licencias de Microsoft a través de Software Assurance en Azure](http://azure.microsoft.com/pricing/license-mobility/) siguiendo los pasos de [Actualizar a una edición diferente de SQL Server](https://msdn.microsoft.com/library/cc707783.aspx). Para obtener información sobre cómo comprar la copia con licencia de SQL Server, consulte [Cómo comprar SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).

4. En la primera página de **Configuración de máquina virtual**, facilite la siguiente información:
	- Una **FECHA DE LANZAMIENTO DE LA VERSIÓN**. Si hay varias imágenes disponibles, seleccione la más reciente.
	- Un **NOMBRE DE LA MÁQUINA VIRTUAL** exclusivo.
	- En el cuadro **NUEVO NOMBRE DE USUARIO**, un nombre de usuario exclusivo para la cuenta de administrador local de la máquina.
	- En el cuadro **CONTRASEÑA NUEVA**, escriba una contraseña segura. 
	- En el cuadro **CONFIRMAR CONTRASEÑA**, vuelva a escribir la contraseña.
	- Seleccione el **TAMAÑO** correspondiente en la lista desplegable. 

	![Configuración de MV](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]El tamaño de la máquina virtual se especifica durante el aprovisionamiento:
 	>
	> - Para las cargas de trabajo de producción, se recomienda usar Almacenamiento premium con los siguientes tamaños mínimos recomendados: **DS3** para la edición SQL Server Enterprise y **DS2** para la edición SQL Server Standard. Para obtener más información, consulte [Prácticas recomendadas de rendimiento para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md).
	> - El tamaño seleccionado limita el número de discos de datos que se puede configurar. Para obtener la información más actualizada sobre los tamaños disponibles de máquina virtual y la cantidad de discos de datos que puede adjuntar a una máquina virtual, consulte [Tamaños de máquina virtual para Azure](virtual-machines-size-specs.md).

5. Después de escribir los detalles de configuración de máquina virtual, haga clic en la flecha siguiente en la parte inferior derecha para continuar.

5. En la segunda página de **Configuración de máquina virtual**, configure recursos para las redes, el almacenamiento y la disponibilidad:
	- En el cuadro **Servicio en la nube**, seleccione **Crear un nuevo servicio en la nube**.
	- En el cuadro **Nombre DNS de servicio en la nube**, proporcione la primera parte de un nombre DNS que elija, para que así se complete un nombre con el formato **TESTNAME.cloudapp.net** 
	- Seleccione una **SUSCRIPCIÓN**, si tiene varias suscripciones entre las que elegir. La opción determina qué **cuentas de almacenamiento **están disponibles.
- En el cuadro **REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL**, seleccione una región donde se hospedará esta imagen virtual.
	- En la **Cuenta de almacenamiento**, genere automáticamente una cuenta o seleccione una en la lista. Cambie la **SUSCRIPCIÓN** para ver más cuentas. 
	- En el cuadro **CONJUNTO DE DISPONIBILIDAD**, seleccione **(none)**.
	- Lea y acepte los términos legales.
	

6. Haga clic en la flecha de avance para continuar.


7. Haga clic en la marca de verificación de la esquina inferior derecha para continuar.

8. Espere a que Azure prepare la máquina virtual. Espere el estado de la máquina virtual para continuar:

	- **Inicio (aprovisionamiento)**
	- **Stopped**
	- **Inicio (aprovisionamiento)**
	- **Ejecución (aprovisionamiento)**
	- **Ejecución**
	

##<a id="RemoteDesktop">Apertura de la VM usando Escritorio remoto para completar la configuración</a>

1. Cuando se completa el aprovisionamiento, haga clic en el nombre de la máquina virtual para ir a la página PANEL. En la parte inferior de la página, haga clic en **Conectar**.

2. Haga clic en el botón **Abrir**.

	![Haga clic en el botón Abrir](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. En el cuadro de diálogo **Seguridad de Windows**, haga clic en **Usar otra cuenta**.

	![Hacer clic en Usar otra cuenta](./media/virtual-machines-provision-sql-server/credentials.png)

4. Use el nombre de la máquina como nombre de dominio, seguido del nombre de administrador en este formato: `machinename\username`. Escriba su contraseña y conéctese a la máquina.

4. La primera vez que inicie sesión en esta máquina virtual, se completarán varios procesos, entre los que se incluyen la configuración del escritorio, las actualizaciones de Windows y la finalización de las tareas de configuración inicial de Windows (sysprep). Una vez que Sysprep de Windows finaliza, la configuración de SQL Server completa las tareas de configuración. Estas tareas pueden causar un retraso de unos minutos mientras se completan. `SELECT @@SERVERNAME` puede no devolver el nombre correcto hasta que finalice la instalación de SQL Server. Asimismo, SQL Server Management Studio puede no estar visible en la página de inicio.

Una vez se haya conectado a la máquina virtual con el Escritorio remoto de Windows, la máquina virtual funcionará como cualquier otro equipo. Conéctese a la instancia predeterminada de SQL Server con SQL Server Management Studio (en ejecución en la máquina virtual) de manera normal.

##<a id="SSMS">Conéctese a la instancia de VM de SQL Server desde SSMS en otro equipo</a>

[AZURE.INCLUDE [Conexión a SQL Server en una máquina virtual](../../includes/virtual-machines-sql-server-connection-steps.md)]

## <a id="cdea">Conexión al motor de base de datos desde su aplicación</a>

Si puede conectarse a una instancia de SQL Server en ejecución en una máquina virtual de Azure a través de Management Studio, debería poder conectarse utilizando una cadena de conexión similar a la siguiente.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Para obtener más información, consulte [Solución de problemas de conexión al motor de base de datos de SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Pasos siguientes</a>

Ha visto cómo crear y configurar SQL Server en una máquina virtual de Azure mediante el uso de la imagen de plataforma. En muchos casos, el siguiente paso es migrar las bases de datos a esta nueva VM de SQL Server. Para obtener instrucciones sobre la migración de bases de datos, consulte [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md).

En la lista siguiente se proporcionan recursos adicionales para SQL Server en máquinas virtuales de Azure.

### Recursos recomendados para SQL Server en máquinas virtuales de Azure:
- [Información general sobre SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md)

- [Conexión a una máquina virtual de SQL Server en Azure](virtual-machines-sql-server-connectivity.md)

- [Procedimientos recomendados para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md)

- [Consideraciones de seguridad para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-security-considerations.md)

### Alta disponibilidad y recuperación ante desastres:
- [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-backup-and-restore.md)

### Cargas de trabajo de servidor SQL en Azure:
- [Business Intelligence de SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-business-intelligence.md)

### Notas del producto:
- [Descripción de Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Estrategias de desarrollo y patrones de aplicación de SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!---HONumber=AcomDC_1203_2015-->
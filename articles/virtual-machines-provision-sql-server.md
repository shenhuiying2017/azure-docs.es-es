<properties linkid="manage-windows-commontask-install-sql-server" urlDisplayName="Install SQL Server" pageTitle="Provision a SQL Server virtual machine in Azure " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="A tutorial that teaches you how to create and configure a SQL Server virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Provisioning a SQL Server Virtual Machine on Azure" authors="selcint" solutions="" manager="jeffreyg" editor="tyson" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="selcint"></tags>

# Aprovisionamiento de una máquina virtual de SQL Server en Azure

La galería de máquinas virtuales de Azure incluye varias imágenes que contienen Microsoft SQL Server. Puede seleccionar una de las imágenes de máquina virtual en la galería y, con unos pocos clics, puede aprovisionar la máquina virtual a su entorno de Azure.

En este tutorial, aprenderá lo siguiente:

-   [Conectarse al Portal de administración de Azure y aprovisionar una máquina virtual desde la galería](#Provision)
-   [Abrir la máquina virtual con Escritorio remoto y finalizar la configuración](#RemoteDesktop)
-   [Finalizar los pasos de configuración para conectarse a la máquina virtual con SQL Server Management Studio en otro equipo](#SSMS)
-   [Pasos siguientes](#Optional)

## <span id="Provision"></span>Conectarse al Portal de administración de Azure y aprovisionar una máquina virtual desde la galería</a>

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com) con su cuenta. Si no tiene una cuenta de Azure, visite [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/).

2.  En el Portal de administración de Azure, en la parte inferior izquierda de la página web, haga clic sucesivamente en **+NEW**, **PROCESO**, **MÁQUINA VIRTUAL** y, a continuación, en **FROM GALLERY**.

3.  En la página **Create a Virtual Machine**, seleccione una imagen de máquina virtual que contenga SQL Server y, a continuación, haga clic en la flecha de avance en la parte inferior de la página. Para conocer la información más actualizada sobre las imágenes compatibles de SQL Server en Azure, consulte el tema [Introducción a SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) en el conjunto de documentación [SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719).

    > [WACOM.NOTE] Si tiene una máquina virtual creada con la edición de evaluación de SQL Server de imagen de plataforma, no puede actualizarla a una imagen de edición de pago por minuto en la galería. Puede elegir una de las dos siguientes opciones:

    > -   Puede crear una nueva máquina virtual mediante la edición de SQL Server pagada por minuto desde la galería y migrar los archivos de base de datos a esta nueva máquina virtual siguiendo los pasos en [How to migrate SQL Server database files and schema between virtual machines in Azure using data disks](http://go.microsoft.com/fwlink/p/?LinkId=294738). **O bien**,

    > -   Puede actualizar una instancia existente de SQL Server Evaluation Edition a otra edición de SQL Server bajo el contrato de [Movilidad de Licencias de Microsoft a través de Software Assurance en Azure](http://www.windowsazure.com/es-es/pricing/license-mobility/) siguiendo los pasos en [Actualizar a una edición diferente de SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Para obtener información sobre cómo comprar la copia con licencia de SQL Server, consulte [Cómo comprar SQL Server](http://www.microsoft.com/es-es/sqlserver/get-sql-server/how-to-buy.aspx).

4.  En la página **Virtual Machine Configuration**, proporcione la siguiente información:

    -   Proporcione un **VIRTUAL MACHINE NAME**.
    -   En el cuadro **NEW USER NAME**, escriba el nombre de usuario único para la cuenta de administrador local de máquina virtual.
    -   En el cuadro **NEW PASSWORD**, escriba una contraseña segura. Para obtener más información, consulte [Contraseñas seguras](http://msdn.microsoft.com/library/ms161962.aspx).
    -   En el cuadro **CONFIRM PASSWORD**, vuelva a escribir la contraseña.
    -   Seleccione el tamaño correspondiente en la lista desplegable **SIZE**.

    > [WACOM.NOTE] El tamaño de la máquina virtual se especifica durante el aprovisionamiento:
    > A2 es el tamaño mínimo recomendado para cargas de trabajo de producción.
    > El tamaño mínimo recomendado es A3 para una máquina virtual cuando se usa SQL Server Enterprise Edition.
    > Seleccione A3 o un tamaño superior cuando use SQL Server Enterprise Edition.
    > Seleccione A6 cuando use la imagen de SQL Server 2012 Enterprise para almacenamiento de datos.
    > Seleccione A7 cuando use la imagen de SQL Server 2014 Evaluation para almacenamiento de datos.
    > El tamaño seleccionado limita la cantidad de discos de datos que puede configurar. Para obtener la información más actualizada sobre los tamaños disponibles de máquina virtual y la cantidad de discos de datos que puede adjuntar a una máquina virtual, consulte [Tamaños de máquina virtual para Azure](http://msdn.microsoft.com/es-es/library/azure/dn197896.aspx).

    Haga clic en la flecha de avance en la esquina inferior derecha para continuar.

    ![Configuración de MV](./media/virtual-machines-provision-sql-server/4VM-Config.png)

5.  Proporcione la siguiente información en la página **Virtual machine mode**:

    -   Seleccione **Standalone Virtual Machine**.
    -   En el cuadro **DNS NAME**, proporcione la primera parte de un nombre DNS que elija, para que así se complete un nombre con el formato **TESTNAME.cloudapp.net**
    -   En el cuadro **REGION/AFFINITY GROUP/VIRTUAL NETWORK**, seleccione una región en la que se hospedará esta imagen virtual.

    Haga clic en la flecha de avance para continuar.

    ![Modo de VM][Image5]

6.  En la página **Virtual machine options**:

    -   En el cuadro **CONJUNTO DE DISPONIBILIDAD**, seleccione **(none)**.
    -   Lea y acepte los términos legales.

    ![Opciones de MV][Image6]

7.  Haga clic en la marca de verificación de la esquina inferior derecha para continuar.

8.  Espere a que Azure prepare la máquina virtual. Espere el estado de la máquina virtual para continuar:

    -   Inicio (aprovisionamiento)
    -   Stopped
    -   Inicio (aprovisionamiento)
    -   Ejecución (aprovisionamiento)
    -   Ejecución

## <span id="RemoteDesktop"></span>Abrir la máquina virtual con Escritorio remoto y finalizar la configuración</a>

1.  Una vez que se completa el aprovisionamiento, haga clic en el nombre de la máquina virtual para ir a la página DASHBOARD. En la parte inferior de la página, haga clic en **Conectar**.

    ![Página Seleccionar panel][Image5b]

2.  Elija abrir el archivo .rpd con el programa Escritorio remoto de Windows (`%windir%\system32\mstsc.exe`).

3.  En el cuadro de diálogo **Seguridad de Windows**, escriba la contraseña de la cuenta de administrador local que especificó en un paso anterior. (Puede que se le pida comprobar las credenciales de la máquina virtual).

4.  Es posible que, la primera vez que inicie sesión en esta máquina virtual, deban completarse varios procesos, entre los que se incluyen la configuración del escritorio, las actualizaciones de Windows y la finalización de las tareas de configuración inicial de Windows (sysprep). Una vez que Sysprep de Windows finaliza, la configuración de SQL Server completa las tareas de configuración. Estas tareas pueden causar un breve retraso mientras se completan. Es posible que `SELECT @@SERVERNAME` no devuelva el nombre correcto hasta que la configuración de SQL Server se complete.

Una vez se haya conectado a la máquina virtual con el Escritorio remoto de Windows, la máquina virtual funcionará como cualquier otro equipo. Conéctese a la instancia predeterminada de SQL Server con SQL Server Management Studio (en ejecución en la máquina virtual) de manera normal.

## <span id="SSMS"></span>Finalizar los pasos de configuración para conectarse a la máquina virtual con SQL Server Management Studio en otro equipo</a>

Antes de que pueda conectarse a la instancia de SQL Server desde Internet, debe completar las siguientes tareas descritas en las secciones que aparecen a continuación:

-   [Creación de un extremo TCP para la máquina virtual](#Endpoint)
-   [Apertura de puertos TCP en el firewall de Windows](#FW)
-   [Configuración de SQL Server para escuchar en el protocolo TCP](#TCP)
-   [Configuración de SQL Server para autenticación de modo mixto](#Mixed)
-   [Creación de inicios de sesión para la autenticación de SQL Server](#Logins)
-   [Determinación del nombre DNS de la máquina virtual](#DNS)
-   [Conexión al motor de base de datos desde otro equipo](#cde)
-   [Conexión al motor de base de datos desde su aplicación] (\#cdea)

El siguiente diagrama resume la ruta de conexión:

![Conexión a una máquina virtual de SQL Server][Image8b]

### <span id="Endpoint"></span>Creación de un extremo TCP para la máquina virtual</a>

La máquina virtual debe tener un extremo para escuchar la comunicación TCP de entrada. Este paso de la configuración de Azure dirige el tráfico del puerto TCP de entrada a un puerto TCP al que puede tener acceso la máquina virtual.

1.  En el Portal de administración de Azure, haga clic en **MÁQUINAS VIRTUALES**.

2.  Haga clic en la máquina virtual recién creada. Se muestra información acerca de su máquina virtual.

3.  Cerca de la parte superior de la página, seleccione la página **ENDPOINTS** y, a continuación, en la parte inferior de la página haga clic en **ADD ENDPOINT**.

    ![Haga clic en ADD ENDPOINT][Image28]

4.  En la página **Add Endpoint to Virtual Machine**, haga clic en **Add Endpoint** y, a continuación, en la flecha de avance para continuar.

    ![Haga clic en Add endpoint][Image29]

5.  En la página **Specify the details of the endpoint**, proporcione la siguiente información.

    -   En el cuadro **NAME**, escriba un nombre para el extremo.
    -   En el cuadro **PROTOCOL**, seleccione **TCP**. Puede escribir el puerto de escucha predeterminado de SQL Server **1433** en el cuadro **Private Port**. De manera similar, puede escribir **57500** en el cuadro **PUBLIC PORT**. Observe que muchas organizaciones seleccionan números de puerto distintos para evitar ataques malintencionados a la seguridad.

    ![Pantalla de extremo][Image30]

6.  Haga clic en la marca de verificación para continuar. Se crea el extremo.

    ![Máquina virtual con extremo][Image31]

### <span id="FW"></span>Apertura de puertos TCP en el firewall de Windows para la instancia predeterminada del motor de base de datos</a>

1.  Conéctese a la máquina virtual a través del Escritorio remoto de Windows. Una vez que ha iniciado sesión, en el menú de inicio, haga clic en **Ejecutar**, escriba **WF.msc** y, a continuación, haga clic en **Aceptar**.

    ![Iniciar el programa de firewall][Image12]]

2.  En **Firewall de Windows con seguridad avanzada**, en el panel de la izquierda, haga clic con el botón secundario en **Reglas de entrada** y, a continuación, haga clic en **Nueva regla** en el panel de acciones.

    ![Nueva regla][Image13]

3.  En el cuadro de diálogo **Tipo de regla**, seleccione **Puerto** y haga clic en **Siguiente**.

4.  En el cuadro de diálogo **Protocolo y puertos**, seleccione **TCP**. Seleccione **Puertos locales específicos** y escriba el número de puerto para la instancia del motor de base de datos (**1433** para la instancia predeterminada o la opción que haya elegido para el puerto privado en el paso referido al extremo).

    ![Puerto TCP 1433][Image14]

5.  Haga clic en **Next**.

6.  En el cuadro de diálogo **Acción**, seleccione **Permitir la conexión** y haga clic en **Siguiente**.

    **Nota de seguridad:** si selecciona **Permitir la conexión si es segura**, puede proporcionar una mayor seguridad. Seleccione esta opción si desea configurar opciones de seguridad adicionales en el entorno.

    ![Permitir conexiones][Image15]

7.  En el cuadro de diálogo **Perfil**, seleccione **Público** y haga clic en **Siguiente**.

    **Nota de seguridad:** al seleccionar **Público** se permite el acceso a través de Internet. Cuando sea posible, seleccione un perfil más restrictivo.

    ![Perfil público][Image16]

8.  En el cuadro de diálogo **Nombre**, escriba un nombre y una descripción para esta regla y, a continuación, haga clic en **Finalizar**.

    ![Nombre de la regla][Image17]

Abra puertos adicionales para otros componentes cada vez que sea necesario. Para obtener más información, consulte [Configurar Firewall de Windows para permitir el acceso a SQL Server](http://msdn.microsoft.com/es-es/library/cc646023.aspx).

### <span id="TCP"></span>Configuración de SQL Server para escuchar en el protocolo TCP</a>

1.  Mientras esté conectado a la máquina virtual con Escritorio remoto, en el menú de inicio, haga clic en **Todos los programas**, **Microsoft SQL Server** *versión*, **Herramientas de configuración** y, a continuación, haga clic en **Administrador de configuración de SQL Server**.

    ![Abrir SSCM][Image9]

2.  En el panel de la consola del Administrador de configuración de SQL Server, expanda **Configuración de red de SQL Server**.

3.  En el panel de la consola, haga clic en **Protocolos para *nombre de la instancia***. (La instancia predeterminada es **Protocolos para MSSQLSERVER**).

4.  En el panel de detalles, haga clic con el botón secundario en TCP; el valor predeterminado debe ser Habilitado para las imágenes de la galería. Para sus imágenes personalizadas, haga clic en **Habilitar** (si el estado es Deshabilitado).

    ![Habilitar TCP][Image10]

5.  En el panel de la consola, haga clic en **Servicios de SQL Server**. (Es posible posponer el reinicio del motor de base de datos hasta finalizar el próximo paso).

6.  En el panel de detalles, haga clic con el botón secundario en **SQL Server (*nombre de la instancia*)** (la instancia predeterminada es **SQL Server (MSSQLSERVER)**) y, a continuación, haga clic en **Reiniciar** para detener y reiniciar la instancia de SQL Server.

    ![Reiniciar el motor de base de datos][Image11]

7.  Cierre el Administrador de configuración de SQL Server.

Para obtener más información acerca de la habilitación de protocolos para el motor de base de datos de SQL Server, consulte [Habilitar o deshabilitar un protocolo de red de servidor](http://msdn.microsoft.com/es-es/library/ms191294.aspx).

### <span id="Mixed"></span>Configuración de SQL Server para autenticación de modo mixto</a>

El motor de base de datos de SQL Server no puede utilizar la autenticación de Windows sin un entorno de dominio. Para conectarse al motor de base de datos desde otro equipo, configure SQL Server para autenticación de modo mixto. La autenticación de modo mixto permite la autenticación de SQL Server y la autenticación de Windows. (Si ha configurado una red virtual de Azure, es posible que no sea necesario configurar la autenticación de modo mixto. Para obtener más información, consulte el tema [Consideraciones de conectividad para SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/?LinkId=294723) en el conjunto de documentación [SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/?LinkId=294719).

1.  Mientras esté conectado a la máquina virtual con Escritorio remoto, en el menú de inicio, haga clic en **Todos los programas**, **Microsoft SQL Server *versión*** y, a continuación, haga clic en **SQL Server Management Studio**.

    ![Iniciar SSMS][Image18]

    La primera vez que abra Management Studio se debe crear el entorno de Management Studio para los usuarios. Esta operación puede tardar unos minutos.

2.  Cuando se abre, Management Studio presenta el cuadro de diálogo **Conectar al servidor**. En el cuadro **Nombre del servidor**, escriba el nombre de la máquina virtual para conectar al motor de base de datos con el Explorador de objetos. (En lugar del nombre de la máquina virtual, también puede utilizar **(local)** o un punto como **Nombre del servidor**). Seleccione **Autenticación de Windows** y deje ***nombre\_de\_su\_MV*\\su\_administrador\_local** en el cuadro **Nombre del usuario**. Haga clic en **Connect**.

    ![Conectar al servidor][Image19]

3.  En el Explorador de objetos de SQL Server Management Studio, haga clic con el botón secundario en el nombre de la instancia de SQL Server (el nombre de la máquina virtual) y, a continuación, haga clic en **Propiedades**.

    ![Propiedades del servidor][Image20]

4.  En la página **Seguridad**, en **Autenticación de servidor**, seleccione **Modo de autenticación de Windows y SQL Server** y, a continuación, haga clic en **Aceptar**.

    ![Seleccionar el modo de autenticación][Image21]

5.  En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Aceptar** para aceptar el requisito de reiniciar SQL Server.

6.  En el Explorador de objetos, haga clic con el botón secundario en el servidor y, a continuación, haga clic en **Reiniciar**. (También se debe reiniciar Agente SQL Server si está en ejecución).

    ![Reinicio][Image22]

7.  En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Sí** para indicar que desea reiniciar SQL Server.

### <span id="Logins"></span>Creación de inicios de sesión para la autenticación de SQL Server</a>

Para conectarse al motor de base de datos desde otro equipo, debe crear al menos un inicio de sesión para la autenticación de SQL Server.

1.  En el Explorador de objetos de SQL Server Management Studio, expanda la carpeta de la instancia de servidor en la que desea crear el nuevo inicio de sesión.

2.  Haga clic con el botón secundario en la carpeta **Seguridad**, señale **Nuevo** y seleccione **Inicio de sesión...**.

    ![Nuevo inicio de sesión][Image23]

3.  En el cuadro **Inicio de sesión - Nuevo**, en la página **General**, escriba el nombre del usuario nuevo en el cuadro **Nombre de inicio de sesión**.

4.  Seleccione **Autenticación de SQL Server**.

5.  En el campo **Contraseña**, escriba una contraseña para el usuario nuevo. Vuelva a escribir esa contraseña en el cuadro **Confirmar contraseña**.

6.  Para exigir que las opciones de directivas de contraseña sean complejas y aplicables, seleccione **Exigir directivas de contraseña** (recomendado). Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

7.  Para exigir que las opciones de directivas de contraseña expiren, seleccione **Exigir expiración de contraseña** (recomendado). Exigir directivas de contraseña debe estar seleccionada para habilitar esta casilla. Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

8.  Para exigir al usuario que cree una contraseña nueva después de la primera vez que se use el inicio de sesión, seleccione **El usuario debe cambiar la contraseña en el siguiente inicio de sesión** (Se recomienda si este inicio de sesión lo utiliza alguien más. Si solo usted utiliza el inicio de sesión, no seleccione esta opción). Exigir expiración de contraseña debe estar seleccionada para habilitar esta casilla. Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

9.  En la lista **Base de datos predeterminada**, seleccione una base de datos predeterminada para el inicio de sesión. **master** es el valor predeterminado para esta opción. Si todavía no ha creado una base de datos de usuario, deje este valor en **master**.

10. En la lista **Idioma predeterminado**, deje el valor **predeterminado**.

    ![Propiedades de inicio de sesión][Image24]

11. Si este es el primer inicio de sesión que crea, es posible que desee designar este inicio de sesión como administrador de SQL Server. Si es así, en la página **Roles del servidor**, active **sysadmin**.

    **Nota de seguridad:** los miembros del rol del servidor fijo sysadmin tienen el control completo del motor de base de datos. Deberá restringir cuidadosamente la suscripción en este rol.

    ![sysadmin][Image25]

12. Haga clic en Aceptar.

Para ver más información acerca de los inicios de sesión de SQL Server, consulte [Crear un inicio de sesión][].

### <span id="DNS"></span>Determinación del nombre DNS de la máquina virtual</a>

Para conectarse al motor de base de datos de SQL Server desde otro equipo, debe conocer el nombre del Sistema de nombres de dominio (DNS) de la máquina virtual. (Este es el nombre que Internet utiliza para identificar la máquina virtual. Puede utilizar la dirección IP, pero esta podría cambiar cuando Azure mueva recursos por redundancia o mantenimiento. El nombre DNS será estable, porque se puede redirigir a una dirección IP nueva).

1.  En el Portal de administración de Azure (o desde el paso anterior), seleccione **MÁQUINAS VIRTUALES**.

2.  En la página **VIRTUAL MACHINE INSTANCES**, en la columna **DNS NAME**, encuentre y copie el nombre DNS para la máquina virtual que aparece antecedido por **<http://>**. (Puede que la interfaz de usuario no muestre todo el nombre, pero puede hacer clic con el botón secundario en él y seleccionar Copiar).

    ![DNS Name][Image32]

### <span id="cde"></span>Conexión al motor de base de datos desde otro equipo</a>

1.  En otro equipo conectado a Internet, abra SQL Server Management Studio.

2.  En el cuadro de diálogo **Conectar al servidor** o **Conectar al motor de base de datos**, en el cuadro **Nombre del servidor**, escriba el nombre DNS de la máquina virtual (determinado en la tarea anterior) y un número de puerto de extremo público con formato *NombreDNS,nombrepuerto*, tal como en **tutorialtestVM.cloudapp.net,57500**.

3.  En el cuadro **Autenticación**, seleccione **Autenticación de SQL Server**.

4.  En el cuadro **Inicio de sesión**, escriba el nombre de un inicio de sesión que haya creado en una tarea anterior.

5.  En el cuadro **Contraseña**, escriba la contraseña del inicio de sesión que creó en una tarea anterior.

6.  Haga clic en **Connect**.

    ![Conectar mediante SSMS][Image33]

### <span id="cdea"></span> Conexión al motor de base de datos desde su aplicación</a>

Si puede conectarse a una instancia de SQL Server en ejecución en una máquina virtual de Azure a través de Management Studio, debería poder conectarse utilizando una cadena de conexión similar a la siguiente.

    connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

Para obtener más información, consulte [How to Troubleshoot Connecting to the SQL Server Database Engine](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <span id="Optional"></span>Pasos siguientes</a>

Ha visto cómo crear y configurar SQL Server en una máquina virtual de Azure mediante el uso de la imagen de plataforma. Cuando utilice SQL Server en Máquinas virtuales de Azure, le recomendamos seguir las pautas detalladas que aparecen en la documentación [SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) en la biblioteca. Este conjunto de documentación incluye una serie de artículos y tutoriales que proporcionan pautas detalladas. La serie incluye las siguientes secciones:

[SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Introducción a SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Preparar la migración a SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294721)

-   Migrar el esquema y los archivos de la base de datos de SQL Server entre máquinas virtuales en Azure mediante discos de datos

[Implementación de SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294722)

-   Cómo copiar datos y archivos de instalación en un disco de datos de SQL Server desde el entorno local a Azure con CSUpload
-   Crear una máquina virtual base local con Hyper-V
-   Crear una máquina virtual de SQL Server en Azure con el disco local de SQL Server existente
-   Crear una máquina virtual de SQL Server en Azure con la máquina virtual local de SQL Server existente
-   Usar PowerShell para configurar una máquina virtual de SQL Server en Azure
-   Usar discos de datos conectados para almacenar archivos de base de datos

[Consideraciones de conectividad para SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294723)

-   Tutorial: Conexión a SQL Server en el mismo servicio en la nube
-   Tutorial: Conexión a SQL Server en un servicio en la nube diferente
-   Tutorial: Conexión de la aplicación ASP.NET a SQL Server en Azure a través de la red virtual

[Consideraciones de rendimiento para SQL Server en Máquinas virtuales de Azure](http://go.microsoft.com/fwlink/?LinkId=294724)

[Consideraciones de seguridad para SQL Server en Máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Solucionar problemas y supervisar SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294726)


[Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294727)

-   Tutorial: Grupos de disponibilidad de AlwaysOn en Azure (GUI)
-   Tutorial: Grupos de disponibilidad de AlwaysOn en Azure (PowerShell)
-   Tutorial: Configuración del agente de escucha de los Grupos de disponibilidad AlwaysOn
-   Tutorial: Asistente para agregar una réplica de Azure
-   Tutorial: Creación de reflejo de la base de datos para la recuperación ante desastres en Azure
-   Tutorial: Database Mirroring for Disaster Recovery in Hybrid IT
-   Tutorial: Creación de reflejo de la base de datos para alta disponibilidad en Azure
-   Tutorial: Trasvase de registros para la recuperación ante desastres en entornos informáticos híbridos
-   Solucionar problemas de agente de escucha del grupo de disponibilidad de Azure

[Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[Business Intelligence de SQL Server en Máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294729)

-   Usar PowerShell para crear una máquina virtual de Azure con SQL Server BI y SharePoint 2010
-   Usar PowerShell para crear una máquina virtual de Azure con SQL Server BI y SharePoint 2013
-   Usar PowerShell para crear una máquina virtual de Azure con un servidor de informes en modo nativo

[Almacenamiento de datos de SQL Server en Máquinas virtuales de Azure](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Artículos técnicos para SQL Server en Máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/dn248435.aspx)

-   [Artículo técnico: Estrategias de desarrollo y patrones de aplicación de SQL Server en máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/dn574746.aspx)

-   [Artículo técnico: Implementar Business Intelligence de SQL Server en Máquinas virtuales de Azure](http://msdn.microsoft.com/library/windowsazure/dn321998.aspx)

-   [Artículo técnico: Guía de rendimiento para SQL Server en Máquinas virtuales de Azure](http://msdn.microsoft.com/library/windowsazure/dn248436.aspx)

-   [Artículo técnico: Control de visor de informes de Reporting Services y servidores de informes basados en máquinas virtuales de Microsoft Azure](http://msdn.microsoft.com/library/azure/dn753698.aspx)

[Image5]: ./media/virtual-machines-provision-sql-server/5VM-Mode.png
[Image5b]: ./media/virtual-machines-provision-sql-server/5VM-Connect.png
[Image6]: ./media/virtual-machines-provision-sql-server/6VM-Options.png
[Image8b]: ./media/virtual-machines-provision-sql-server/SQLVMConnectionsOnAzure.GIF
[Image9]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
[Image10]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
[Image11]: ./media/virtual-machines-provision-sql-server/11Restart.png
[Image12]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
[Image13]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
[Image14]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
[Image15]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
[Image17]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
[Image18]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
[Image19]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
[Image20]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
[Image21]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
[Image22]: ./media/virtual-machines-provision-sql-server/22Restart2.png
[Image23]: ./media/virtual-machines-provision-sql-server/23New-Login.png
[Image24]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
[Image25]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
[Image28]: ./media/virtual-machines-provision-sql-server/28Add-Endpoint.png
[Image29]: ./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png
[Image30]: ./media/virtual-machines-provision-sql-server/30Endpoint-Details.png
[Image31]: ./media/virtual-machines-provision-sql-server/31VM-Connect.png
[Image32]: ./media/virtual-machines-provision-sql-server/32DNS-Name.png
[Image33]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png

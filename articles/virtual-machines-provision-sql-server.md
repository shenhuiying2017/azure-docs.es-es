<properties  linkid="manage-windows-commontask-install-sql-server" urlDisplayName="Install SQL Server" pageTitle="Provision a SQL Server virtual machine in Azure " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="A tutorial that teaches you how to create and configure a SQL Server virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Provisioning a SQL Server Virtual Machine on Azure" authors="selcint" solutions="" manager="clairt" editor="tyson" />

# Aprovisionamiento de una máquina virtual de SQL Server en Azure

La galería de máquinas virtuales de Azure incluye varias imágenes que contienen Microsoft SQL Server. Puede seleccionar una de las imágenes de máquina virtual en la galería y, con unos pocos clics, puede aprovisionar la máquina virtual a su entorno de Azure.

En este tutorial, aprenderá a:

* [Conectarse al Portal de administración de Azure y aprovisionar una
  máquina virtual desde la galería](#Provision)
* [Abrir la máquina virtual con Escritorio remoto y finalizar la
  configuración](#RemoteDesktop)
* [Finalizar los pasos de configuración para conectarse a la máquina
  virtual con SQL Server Management Studio en otro equipo](#SSMS)
* [Pasos siguientes](#Optional)

## <a  id="Provision">Conectarse al Portal de administración de Azure y aprovisionar una máquina virtual desde la galería</a>

1.  Inicie sesión en el [Portal de administración de Azure][1] con su cuenta. Si no tiene una cuenta de Azure, visite [Evaluación gratuita de Azure][2].

2.  En el Portal de administración de Azure, en la parte inferior izquierda de la página web, haga clic sucesivamente en **+NEW**, **PROCESO**, **MÁQUINA VIRTUAL** y, a continuación, en **FROM GALLERY**.

3.  En la página **Create a Virtual Machine**, seleccione una imagen de máquina virtual que contenga SQL Server y, a continuación, haga clic en la flecha de avance en la parte inferior de la página. Para conocer la información más actualizada sobre las imágenes compatibles de SQL Server en Azure, consulte el tema [Introducción a SQL Server en máquinas virtuales de Azure][3] en el conjunto de documentación [SQL Server en máquinas virtuales de Azure][4].
    
    > [WACOM.NOTE] Si tiene una máquina virtual creada con la edición
    > de evaluación de SQL Server 2012 de imagen de plataforma que
    > estuvo disponible durante el período de versión preliminar, no
    > puede actualizarla a una imagen de edición de pago por hora en la
    > galería. Puede elegir una de las dos siguientes opciones: - Puede
    > crear una máquina virtual nueva con la edición de SQL Server de
    > pago por hora desde la galería y migrar los archivos de base de
    > datos a esta nueva máquina virtual si sigue los pasos que se
    > indican en [Migrar el esquema y los archivos de la base de datos
    > de SQL Server entre máquinas virtuales en Azure mediante discos de
    > datos][5]. **O bien**,
    
    > * Puede actualizar una instancia existente de la edición de
    >   evaluación de SQL Server 2012 a una edición diferente de SQL
    >   Server 2012 en virtud del contrato [Movilidad de Licencias a
    >   través de Software Assurance en Azure][6] si sigue los pasos que
    >   se indican en [Actualizar a una edición diferente de SQL Server
    >   2012][7]. Para obtener información sobre cómo comprar la copia
    >   con licencia de SQL Server, consulte [Cómo comprar SQL
    >   Server][8].

4.  En la página **Virtual Machine Configuration**, proporcione la siguiente información:
    * Proporcione un **VIRTUAL MACHINE NAME**.
    * En el cuadro **NEW USER NAME**, escriba el nombre de usuario único para la cuenta de administrador local de máquina virtual.
    * En el cuadro **NEW PASSWORD**, escriba una contraseña segura. Para obtener más información, consulte [Contraseñas seguras][9].
    * En el cuadro **CONFIRM PASSWORD**, vuelva a escribir la contraseña.
    * Seleccione el tamaño correspondiente en la lista desplegable **SIZE**.
    
    > [WACOM.NOTE] El tamaño de la máquina virtual se especifica
    > durante el aprovisionamiento: >- Mediano es el tamaño más
    > pequeño recomendado para cargas de trabajo de producción. - El
    > tamaño mínimo recomendado es Grande para una máquina virtual
    > cuando se usa SQL Server Enterprise Edition. - Seleccione Grande o
    > un tamaño superior cuando use SQL Server Enterprise Edition. -
    > Seleccione A6 cuando use la imagen de SQL Server 2012 Enterprise
    > para almacenamiento de datos. - Seleccione A7 cuando use la
    > imagen de SQL Server 2014 Evaluation para almacenamiento de datos.- El tamaño seleccionado limita la cantidad de discos de datos
    > que puede configurar. Para obtener la información más actualizada
    > sobre los tamaños disponibles de máquina virtual y la cantidad de
    > discos de datos que puede adjuntar a una máquina virtual, consulte
    > [Tamaños de máquina virtual para Azure][10].
    
    Haga clic en la flecha de avance en la esquina inferior derecha para
    continuar.
    
    ![Configuración de
    MV](./media/virtual-machines-provision-sql-server/4VM-Config.png)

5.  Proporcione la siguiente información en la página **Virtual machine mode**:
    * Seleccione **Standalone Virtual Machine**.
    * En el cuadro **DNS NAME**, proporcione la primera parte de un nombre DNS que elija, para que así se complete un nombre con el formato **TESTNAME.cloudapp.net**
    * En el cuadro **REGION/AFFINITY GROUP/VIRTUAL NETWORK**, seleccione una región en la que se hospedará esta imagen virtual.
    
    Haga clic en la flecha de avance para continuar.
    
    ![Modo de
    VM](./media/virtual-machines-provision-sql-server/5VM-Mode.png)

6.  En la página **Virtual machine options**:
    * En el cuadro **CONJUNTO DE DISPONIBILIDAD**, seleccione **(none)**.
    * Lea y acepte los términos legales.
    
    ![Opciones de
    MV](./media/virtual-machines-provision-sql-server/6VM-Options.png)

7.  Haga clic en la marca de verificación de la esquina inferior derecha para continuar.

8.  Espere a que Azure prepare la máquina virtual. Espere el estado de la máquina virtual para continuar:
    
    * Inicio (aprovisionamiento)
    * Detención
    * Inicio (aprovisionamiento)
    * Ejecución (aprovisionamiento)
    * Ejecución

## <a  id="RemoteDesktop">Abrir la máquina virtual con Escritorio remoto y finalizar la configuración</a>

1.  Una vez que se completa el aprovisionamiento, haga clic en el nombre de la máquina virtual para ir a la página DASHBOARD. En la parte inferior de la página, haga clic en **Conectar**.
    
    ![Página Seleccionar
    panel](./media/virtual-machines-provision-sql-server/5VM-Connect.png)

2.  Elija abrir el archivo .rpd con el programa Escritorio remoto de Windows (`%windir%\system32\mstsc.exe`).

3.  En el cuadro de diálogo **Seguridad de Windows**, escriba la contraseña de la cuenta de administrador local que especificó en un paso anterior. (Puede que se le pida comprobar las credenciales de la máquina virtual).

4.  Es posible que, la primera vez que inicie sesión en esta máquina virtual, deban completarse varios procesos, entre los que se incluyen la configuración del escritorio, las actualizaciones de Windows y la finalización de las tareas de configuración inicial de Windows (sysprep). Una vez que Sysprep de Windows finaliza, la configuración de SQL Server completa las tareas de configuración. Estas tareas pueden causar una breve demora mientras se completan. Es posible que `SELECT @@SERVERNAME` no devuelva el nombre correcto hasta que la configuración de SQL Server se complete.

Una vez se haya conectado a la máquina virtual con el Escritorio remoto de Windows, la máquina virtual funcionará como cualquier otro equipo. Conéctese a la instancia predeterminada de SQL Server con SQL Server Management Studio (en ejecución en la máquina virtual) de manera normal.

## <a  id="SSMS">Finalizar los pasos de configuración para conectarse a la máquina virtual con SQL Server Management Studio en otro equipo</a>

Antes de que pueda conectarse a la instancia de SQL Server desde Internet, debe completar las siguientes tareas descritas en las secciones que aparecen a continuación:

* [Creación de un extremo TCP para la máquina virtual](#Endpoint)
* [Apertura de puertos TCP en el firewall de Windows](#FW)
* [Configuración de SQL Server para escuchar en el protocolo TCP](#TCP)
* [Configuración de SQL Server para autenticación de modo mixto](#Mixed)
* [Creación de inicios de sesión para la autenticación de SQL
  Server](#Logins)
* [Determinación del nombre DNS de la máquina virtual](#DNS)
* [Conexión al motor de base de datos desde otro equipo](#cde)
* [Conexión al motor de base de datos desde su aplicación] (#cdea)

El siguiente diagrama resume la ruta de conexión:

![Conexión a una máquina virtual de SQL
Server](./media/virtual-machines-provision-sql-server/SQLVMConnectionsOnAzure.GIF)

### <a  id="Endpoint">Creación de un extremo TCP para la máquina virtual</a>

La máquina virtual debe tener un extremo para escuchar la comunicación TCP de entrada. Este paso de la configuración de Azure dirige el tráfico del puerto TCP de entrada a un puerto TCP al que puede tener acceso la máquina virtual.

1.  En el Portal de administración de Azure, haga clic en **MÁQUINAS VIRTUALES**.

2.  Haga clic en la máquina virtual recién creada. Se muestra información acerca de su máquina virtual.

3.  Cerca de la parte superior de la página, seleccione la página **ENDPOINTS** y, a continuación, en la parte inferior de la página haga clic en **ADD ENDPOINT**.
    
    ![Haga clic en ADD
    ENDPOINT](./media/virtual-machines-provision-sql-server/28Add-Endpoint.png)

4.  En la página **Add Endpoint to Virtual Machine**, haga clic en **Add Endpoint** y, a continuación, en la flecha de avance para continuar.
    
    ![Haga clic en Add
    endpoint](./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png)

5.  En la página **Specify the details of the endpoint**, proporcione la siguiente información.
    
    * En el cuadro **NAME**, escriba un nombre para el extremo.
    * En el cuadro **PROTOCOL**, seleccione **TCP**. Puede escribir el  puerto de escucha predeterminado de SQL Server **1433** en el cuadro **Private Port**. De manera similar, puede escribir **57500** en el cuadro **PUBLIC PORT**. Observe que muchas organizaciones seleccionan números de puerto distintos para evitar ataques malintencionados a la seguridad.
    
    ![Pantalla de
    extremo](./media/virtual-machines-provision-sql-server/30Endpoint-Details.png)

6.  Haga clic en la marca de verificación para continuar. Se crea el extremo.
    
    ![Máquina virtual con
    extremo](./media/virtual-machines-provision-sql-server/31VM-Connect.png)

### <a  id="FW">Apertura de puertos TCP en el firewall de Windows para la instancia predeterminada del motor de base de datos</a>

1.  Conéctese a la máquina virtual a través del Escritorio remoto de Windows. Una vez que ha iniciado sesión, en el menú de inicio, haga clic en **Ejecutar**, escriba **WF.msc** y, a continuación, haga clic en **Aceptar**.
    
    ![Iniciar el programa de
    firewall](./media/virtual-machines-provision-sql-server/12Open-WF.png)

2.  En **Firewall de Windows con seguridad avanzada**, en el panel de la izquierda, haga clic con el botón secundario en **Reglas de entrada** y, a continuación, haga clic en **Nueva regla** en el panel de acciones.
    
    ![Nueva
    regla](./media/virtual-machines-provision-sql-server/13New-FW-Rule.png)

3.  En el cuadro de diálogo **Tipo de regla**, seleccione **Puerto** y haga clic en **Siguiente**.

4.  En el cuadro de diálogo **Protocolo y puertos**, seleccione **TCP**. Seleccione **Puertos locales específicos** y escriba el número de puerto para la instancia del motor de base de datos (**1433** para la instancia predeterminada o la opción que haya elegido para el puerto privado en el paso referido al extremo).
    
    ![Puerto TCP
    1433](./media/virtual-machines-provision-sql-server/14Port-1433.png)

5.  Haga clic en **Siguiente**.

6.  En el cuadro de diálogo **Acción**, seleccione **Permitir la conexión** y haga clic en **Siguiente**.
    
    **Nota de seguridad:** si selecciona **Permitir la conexión si es segura**, puede proporcionar una mayor seguridad. Seleccione esta opción si desea configurar opciones de seguridad adicionales en el entorno.
    
    ![Permitir
    conexiones](./media/virtual-machines-provision-sql-server/15Allow-Connection.png)

7.  En el cuadro de diálogo **Perfil**, seleccione **Público** y haga clic en **Siguiente**.
    
    **Nota de seguridad:** al seleccionar **Público** se permite el acceso a través de Internet. Cuando sea posible, seleccione un perfil más restrictivo.
    
    ![Perfil
    público](./media/virtual-machines-provision-sql-server/16Public-Profile.png)

8.  En el cuadro de diálogo **Nombre**, escriba un nombre y una descripción para esta regla y, a continuación, haga clic en **Finalizar**.
    
    ![Nombre de la
    regla](./media/virtual-machines-provision-sql-server/17Rule-Name.png)

Abra puertos adicionales para otros componentes cada vez que sea necesario. Para obtener más información, consulte [Configurar Firewall de Windows para permitir el acceso a SQL Server][11].

### <a  id="TCP">Configuración de SQL Server para escuchar en el protocolo TCP</a>

1.  Mientras esté conectado a la máquina virtual con Escritorio remoto, en el menú de inicio, haga clic en **Todos los programas**, **Microsoft SQL Server** *versión*, **Herramientas deconfiguración** y, a continuación, haga clic en **Administrador de configuración de SQL Server**.
    
    ![Abrir
    SSCM](./media/virtual-machines-provision-sql-server/9Click-SSCM.png)

2.  En el panel de la consola del Administrador de configuración de SQL Server, expanda **Configuración de red de SQL Server**.

3.  En el panel de la consola, haga clic en **Protocolos para *nombre de la instancia***. (La instancia predeterminada es **Protocolos para MSSQLSERVER**).

4.  En el panel de detalles, haga clic con el botón secundario en TCP; el valor predeterminado debe ser Habilitado para las imágenes de la galería. Para sus imágenes personalizadas, haga clic en **Habilitar** (si el estado es Deshabilitado).
    
    ![Habilitar
    TCP](./media/virtual-machines-provision-sql-server/10Enable-TCP.png)

5.  En el panel de la consola, haga clic en **Servicios de SQL Server**. (Es posible posponer el reinicio del motor de base de datos hasta finalizar el próximo paso).

6.  En el panel de detalles, haga clic con el botón secundario en **SQL Server (*nombre de la instancia*)** (la instancia predeterminada es **SQL Server (MSSQLSERVER)**) y, a continuación, haga clic en **Reiniciar** para detener y reiniciar la instancia de SQL Server.
    
    ![Reiniciar el motor de base de
    datos](./media/virtual-machines-provision-sql-server/11Restart.png)

7.  Cierre el Administrador de configuración de SQL Server.

Para obtener más información acerca de la habilitación de protocolos para el motor de base de datos de SQL Server, consulte [Habilitar o deshabilitar un protocolo de red de servidor][12].

### <a  id="Mixed">Configuración de SQL Server para autenticación de modo mixto</a>

El motor de base de datos de SQL Server no puede utilizar la autenticación de Windows sin un entorno de dominio. Para conectarse al motor de base de datos desde otro equipo, configure SQL Server para autenticación de modo mixto. La autenticación de modo mixto permite la autenticación de SQL Server y la autenticación de Windows. (Si ha configurado una red virtual de Azure, es posible que no sea necesario configurar la autenticación de modo mixto. Para obtener más información, consulte el tema [Consideraciones de conectividad para SQL Server en máquinas virtuales de Azure][13] en el conjunto de documentación [SQL Server en máquinas virtuales de Azure][14].

1.  Mientras esté conectado a la máquina virtual con Escritorio remoto, en el menú de inicio, haga clic en **Todos los programas**, **Microsoft SQL Server *versión*** y, a continuación, haga clic en **SQL Server Management Studio**.
    
    ![Iniciar
    SSMS](./media/virtual-machines-provision-sql-server/18Start-SSMS.png)
    
    La primera vez que abra Management Studio se debe crear el entorno de Management Studio para los usuarios. Esta operación puede tardar unos minutos.

2.  Cuando se abre, Management Studio presenta el cuadro de diálogo **Conectar al servidor**. En el cuadro **Nombre del servidor**, escriba el nombre de la máquina virtual para conectar al motor de base de datos con el Explorador de objetos. (En lugar del nombre de la máquina virtual, también puede utilizar **(local)** o un punto como **Nombre del servidor**). Seleccione **Autenticación de Windows** y deje ***nombre\_de\_su\_MV*\\su\_administrador\_local** en el cuadro **Nombre del usuario**. Haga clic en **Conectar**.
    
    ![Conectar al
    servidor](./media/virtual-machines-provision-sql-server/19Connect-to-Server.png)

3.  En el Explorador de objetos de SQL Server Management Studio, haga clic con el botón secundario en el nombre de la instancia de SQL Server (el nombre de la máquina virtual) y, a continuación, haga clic en **Propiedades**.
    
    ![Propiedades del
    servidor](./media/virtual-machines-provision-sql-server/20Server-Properties.png)

4.  En la página **Seguridad**, en **Autenticación de servidor**, seleccione **Modo de autenticación de Windows y SQL Server** y, a continuación, haga clic en **Aceptar**.
    
    ![Seleccionar el modo de
    autenticación](./media/virtual-machines-provision-sql-server/21Mixed-Mode.png)

5.  En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Aceptar** para aceptar el requisito de reiniciar SQL Server.

6.  En el Explorador de objetos, haga clic con el botón secundario en el servidor y, a continuación, haga clic en **Reiniciar**. (También se debe reiniciar Agente SQL Server si está en ejecución).
    
    ![Reiniciar](./media/virtual-machines-provision-sql-server/22Restart2.png)

7.  En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Sí** para indicar que desea reiniciar SQL Server.

### <a  id="Logins">Creación de inicios de sesión para la autenticación de SQL Server</a>

Para conectarse al motor de base de datos desde otro equipo, debe crear al menos un inicio de sesión para la autenticación de SQL Server.

1.  En el Explorador de objetos de SQL Server Management Studio, expanda la carpeta de la instancia de servidor en la que desea crear el nuevo inicio de sesión.

2.  Haga clic con el botón secundario en la carpeta **Seguridad**, señale **Nuevo** y seleccione **Inicio de sesión...**.
    
    ![Nuevo inicio de
    sesión](./media/virtual-machines-provision-sql-server/23New-Login.png)

3.  En el cuadro **Inicio de sesión - Nuevo**, en la página **General**, escriba el nombre del usuario nuevo en el cuadro **Nombre de inicio de sesión**.

4.  Seleccione **Autenticación de SQL Server**.

5.  En el campo **Contraseña**, escriba una contraseña para el usuario nuevo. Vuelva a escribir esa contraseña en el cuadro **Confirmar contraseña**.

6.  Para exigir que las opciones de directivas de contraseña sean complejas y aplicables, seleccione **Exigir directivas de contraseña** (recomendado). Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

7.  Para exigir que las opciones de directivas de contraseña expiren, seleccione **Exigir expiración de contraseña** (recomendado). Exigir directivas de contraseña debe estar seleccionada para habilitar esta casilla. Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

8.  Para exigir al usuario que cree una contraseña nueva después de la primera vez que se use el inicio de sesión, seleccione **El usuario debe cambiar la contraseña en el siguiente inicio de sesión** (Se recomienda si este inicio de sesión lo utiliza alguien más. Si solo usted utiliza el inicio de sesión, no seleccione esta opción). Exigir expiración de contraseña debe estar seleccionada para habilitar esta casilla. Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

9.  En la lista **Base de datos predeterminada**, seleccione una base de datos predeterminada para el inicio de sesión. **master** es el valor predeterminado para esta opción. Si todavía no ha creado una base de datos de usuario, deje este valor en **master**.

10. En la lista **Idioma predeterminado**, deje el valor **predeterminado**.
    
    ![Propiedades de inicio de
    sesión](./media/virtual-machines-provision-sql-server/24Test-Login.png)

11. Si este es el primer inicio de sesión que crea, es posible que desee designar este inicio de sesión como administrador de SQL Server. Si es así, en la página **Roles del servidor**, active **sysadmin**.
    
    **Nota de seguridad:** los miembros del rol del servidor fijo sysadmin tienen el control completo del motor de base de datos. Deberá restringir cuidadosamente la suscripción en este rol.
    
    ![sysadmin](./media/virtual-machines-provision-sql-server/25sysadmin.png)

12. Haga clic en Aceptar.

Para ver más información acerca de los inicios de sesión de SQL Server, consulte [Crear un inicio de sesión][15].

### <a  id="DNS">Determinación del nombre DNS de la máquina virtual</a>

Para conectarse al motor de base de datos de SQL Server desde otro equipo, debe conocer el nombre del Sistema de nombres de dominio (DNS) de la máquina virtual. (Este es el nombre que Internet utiliza para identificar la máquina virtual. Puede utilizar la dirección IP, pero esta podría cambiar cuando Azure mueva recursos por redundancia o mantenimiento. El nombre DNS será estable, porque se puede redirigir a una dirección IP nueva).

1.  En el Portal de administración de Azure (o desde el paso anterior), seleccione **MÁQUINAS VIRTUALES**.

2.  En la página **VIRTUAL MACHINE INSTANCES**, en la columna **DNS NAME**, encuentre y copie el nombre DNS para la máquina virtual que aparece antecedido por **http://**. (Puede que la interfaz de usuario no muestre todo el nombre, pero puede hacer clic con el botón secundario en él y seleccionar Copiar).
    
    ![Nombre
    DNS](./media/virtual-machines-provision-sql-server/32DNS-Name.png)

### <a  id="cde">Conexión al motor de base de datos desde otro equipo</a>

1.  En otro equipo conectado a Internet, abra SQL Server Management Studio.

2.  En el cuadro de diálogo **Conectar al servidor** o **Conectar al motor de base de datos**, en el cuadro **Nombre del servidor**, escriba el nombre DNS de la máquina virtual (determinado en la tarea anterior) y un número de puerto de extremo público con formato *NombreDNS,nombrepuerto*, tal como en **tutorialtestVM.cloudapp.net,57500**.

3.  En el cuadro **Autenticación**, seleccione **Autenticación de SQL Server**.

4.  En el cuadro **Inicio de sesión**, escriba el nombre de un inicio de sesión que haya creado en una tarea anterior.

5.  En el cuadro **Contraseña**, escriba la contraseña del inicio de sesión que creó en una tarea anterior.

6.  Haga clic en **Conectar**.
    
    ![Conectar mediante
    SSMS](./media/virtual-machines-provision-sql-server/33Connect-SSMS.png)

### <a  id="cdea"> Conexión al motor de base de datos desde su aplicación</a>

Si puede conectarse a una instancia de SQL Server en ejecución en una máquina virtual de Azure a través de Management Studio, debería poder conectarse utilizando una cadena de conexión similar a la siguiente.

    connectionString="Server=<DNS _Name>;Integrated Security=false;User ID=<login _name>;Password=<your _password>;"providerName="System.Data.SqlClient"

Para obtener más información, consulte [How to Troubleshoot Connecting to the SQL Server Database Engine][16].

## <a  id="Optional">Pasos siguientes</a>

Ha visto cómo crear y configurar SQL Server en una máquina virtual de Azure mediante el uso de la imagen de plataforma. Cuando utilice SQL Server en Máquinas virtuales de Azure, le recomendamos seguir las pautas detalladas que aparecen en la documentación [SQL Server en máquinas virtuales de Azure][4] en la biblioteca. Este conjunto de documentación incluye una serie de artículos y tutoriales que proporcionan pautas detalladas. La serie incluye las siguientes secciones:

[SQL Server en máquinas virtuales de Azure][4]

[Introducción a SQL Server en máquinas virtuales de Azure][3]

[Preparar la migración a SQL Server en máquinas virtuales de Azure][17]

* Migrar el esquema y los archivos de la base de datos de SQL Server entre máquinas virtuales en Azure mediante discos de datos

[Implementación de SQL Server en máquinas virtuales de Azure][18]

* Cómo copiar datos y archivos de instalación en un disco de datos de SQL Server desde el entorno local a Azure con CSUpload
* Crear una máquina virtual base local con Hyper-V
* Crear una máquina virtual de SQL Server en Azure con el disco local de SQL Server existente
* Crear una máquina virtual de SQL Server en Azure con la máquina virtual local de SQL Server existente
* Usar PowerShell para configurar una máquina virtual de SQL Server en Azure
* Usar discos de datos conectados para almacenar archivos de base de datos

[Consideraciones de conectividad para SQL Server en máquinas virtuales
de Azure][19]

* Tutorial: Conexión a SQL Server en el mismo servicio en la nube
* Tutorial: Conexión a SQL Server en un servicio en la nube diferente
* Tutorial: Conexión de la aplicación ASP.NET a SQL Server en Azure a
  través de la red virtual

[Artículo técnico: Guía de rendimiento para SQL Server en Máquinas
virtuales de Azure][20]

[Consideraciones de rendimiento para SQL Server en Máquinas virtuales de
Azure][21]

[Consideraciones de seguridad para SQL Server en Máquinas virtuales de
Azure][22]

[Solucionar problemas y supervisar SQL Server en máquinas virtuales de
Azure][23]

[Alta disponibilidad y recuperación ante desastres para SQL Server en
máquinas virtuales de Azure][24]

* Tutorial: Grupos de disponibilidad de AlwaysOn en Azure (GUI)
* Tutorial: Grupos de disponibilidad de AlwaysOn en Azure (PowerShell)
* Tutorial: Grupos de disponibilidad AlwaysOn en TI híbrida
* Tutorial: Creación de reflejo de la base de datos para alta disponibilidad en Azure
* Tutorial: Creación de reflejo de la base de datos para la recuperación ante desastres en Azure
* Tutorial: Database Mirroring for Disaster Recovery in Hybrid IT
* Tutorial: Trasvase de registros para la recuperación ante desastres en
  entornos informáticos híbridos

[Copias de seguridad y restauración para SQL Server en Máquinas
virtuales de Azure][25]

[Business Intelligence de SQL Server en Máquinas virtuales de Azure][26]

* Usar PowerShell para crear una Máquina virtual de Azure con SQL Server BI y SharePoint 2010
* Usar PowerShell para crear una máquina virtual de Azure con SQL Server BI y SharePoint 2013
* Usar PowerShell para crear una máquina virtual de Azure con un servidor de informes en modo nativo
* [Artículo técnico: Implementar Business Intelligence de SQL Server en Máquinas virtuales de Azure][27]

[Almacenamiento de datos de SQL Server en Máquinas virtuales de
Azure][28]



[1]: http://manage.windowsazure.com
[2]: http://www.windowsazure.com/en-us/pricing/free-trial/
[3]: http://go.microsoft.com/fwlink/p/?LinkId=294720
[4]: http://go.microsoft.com/fwlink/p/?LinkId=294719
[5]: http://go.microsoft.com/fwlink/p/?LinkId=294738
[6]: http://www.windowsazure.com/en-us/pricing/license-mobility/
[7]: http://msdn.microsoft.com/library/cc707783.aspx
[8]: http://www.microsoft.com/en-us/sqlserver/get-sql-server/how-to-buy.aspx
[9]: http://msdn.microsoft.com/library/ms161962.aspx
[10]: http://go.microsoft.com/fwlink/p/?LinkId=294819
[11]: http://msdn.microsoft.com/en-us/library/cc646023.aspx
[12]: http://msdn.microsoft.com/en-us/library/ms191294.aspx
[13]: http://go.microsoft.com/fwlink/?LinkId=294723
[14]: http://go.microsoft.com/fwlink/?LinkId=294719
[15]: http://msdn.microsoft.com/en-us/library/aa337562.aspx
[16]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx
[17]: http://go.microsoft.com/fwlink/p/?LinkId=294721
[18]: http://go.microsoft.com/fwlink/p/?LinkId=294722
[19]: http://go.microsoft.com/fwlink/p/?LinkId=294723
[20]: http://msdn.microsoft.com/library/windowsazure/dn248436.aspx
[21]: http://go.microsoft.com/fwlink/?LinkId=294724
[22]: http://go.microsoft.com/fwlink/p/?LinkId=294725
[23]: http://go.microsoft.com/fwlink/p/?LinkId=294726
[24]: http://go.microsoft.com/fwlink/p/?LinkId=294727
[25]: http://go.microsoft.com/fwlink/p/?LinkId=294728
[26]: http://go.microsoft.com/fwlink/p/?LinkId=294729
[27]: http://msdn.microsoft.com/library/windowsazure/dn321998.aspx
[28]: http://msdn.microsoft.com/library/windowsazure/dn387396.aspx
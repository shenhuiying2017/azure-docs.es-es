<properties  linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="Add a VM to virtual network" pageTitle="Add a virtual machine to a virtual network - Azure" metaKeywords="" description="A tutorial that teaches you how to create a storage account and virtual machine (VM) that you add to an Azure virtual network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Add a Virtual Machine to a Virtual Network" authors="" solutions="" manager="" editor="" />

<h1>Incorporación de una máquina virtual a una red virtual</h1>


<!--SOMEWHERE IN THIS TUTORIAL I NEED TO XREF TO THE OTHER VMACHINE TUTORIAL -->

En este tutorial se hace un recorrido por los pasos para crear una cuenta de almacenamiento de Azure (VM) y una máquina virtual (VM) que agrega a una [red virtual][1].

En este tutorial se asume que no tiene ninguna experiencia previa con Azure.

 
<div  class="dev-callout"> 
<b>Importante</b>

<p>Si piensa crear una máquina virtual para instalar un nuevo bosque de Active Directory, siga las instrucciones que se indican en <a  href="../active-directory-forest/">Instalación de un nuevo bosque de Active Directory en Azure</a>.</p>
</div>

## Objetivos

En este tutorial, aprenderá a:

* [Crear una cuenta de almacenamiento](#CreateStorageAcct)

* [Crear una máquina virtual e implementarla en una red
  virtual](#CreateVM)

## Requisitos previos

* Realice uno de los siguientes tutoriales:
  
  * [Creación de una red virtual en
    Azure](/es-es/manage/services/networking/create-a-virtual-network/)
    
    O
  
  * [Creación de una red virtual para conectividad entre
    locales](/es-es/manage/services/networking/cross-premises-connectivity/)

* Una cuenta de Windows Live con al menos una suscripción válida y
  activa.

* Nombre de los siguientes elementos en [Creación de una red virtual en
  Azure](/es-es/manage/services/networking/create-a-virtual-network/) o
  [Creación de una red virtual para conectividad entre
  locales](/es-es/manage/services/networking/cross-premises-connectivity/):
  
  * Grupo de afinidad asignado a la red virtual.
  
  * Nombre de la red virtual.
  
  * Nombres de las subredes.

## <a name="CreateStorageAcct">Crear una cuenta de almacenamiento</a>

1.  Una vez creada la red virtual en el [Portal de administración de Azure][2], en la esquina inferior izquierda de la pantalla, haga clic en **New**.
    
    ![NewStorAcct](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.  En el panel de navegación, haga clic en **SERVICIOS DE DATOS**, en **ALMACENAMIENTO** y, a continuación, en **QUICK CREATE**.
    
    ![QuickCreate](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.  Especifique la siguiente información y, a continuación, haga clic en la marca de verificación en la parte inferior derecha de la pantalla.

* **URL:** Escriba *yourstorage*.

* **REGION/GRUPO DE AFINIDAD:** En la lista desplegable, seleccione
  **YourAffinityGroup**.

* **ENABLE GEO-REPLICATION:** Deje esta casilla activada.
  
	![CreateNewAcct](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

1.  En la página **Almacenamiento**, la columna **STATUS** mostrará **Online** cuando el proceso se haya completado.
    
    ![NewStorAcctCreated](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)

## <a name="CreateVM">Crear una máquina virtual e implementarla en una red virtual</a>

**Para crear una máquina virtual e implementarla en una red virtual:**

1.  Una vez que haya creado una cuenta de almacenamiento, haga clic en **New** en la esquina inferior izquierda de la pantalla.
    
    ![NewVM](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)

2.  En el panel de navegación, haga clic en **PROCESO**, **MÁQUINA VIRTUAL** y, a continuación, en **FROM GALLERY**.
    
    ![FromGallery](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)

3.  En la pantalla **VM OS Selection**, seleccione **Windows Server 2008 R2 SP1, October 2012** (o la versión más reciente que esté disponible) y, a continuación, haga clic en la flecha de avance.
    
    ![VMOS](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)

4.  En la pantalla **Virtual machine configuration**, especifique la siguiente información y, a continuación, haga clic en la flecha de avance. 
	<!--SHOULD WE TELL USERS TO WRITE DOWN USER NAME AND PASS??-->
    
    **Sugerencia:** escriba el nombre de usuario y la contraseña, ya que
    serán las credenciales que usará para iniciar sesión en su nueva
    máquina virtual.

* **VIRTUAL MACHINE NAME:** Escriba *YourVMachine*.

* **NEW USER NAME:** Solo lectura.

* **NEW PASSWORD:** Especifique una contraseña segura.

* **CONFIRM PASSWORD:** Vuelva a especificar la contraseña.

* **SIZE:** Seleccione **Small**.
  
	![VMConfig](./media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

1.  En la pantalla **Virtual machine mode**, especifique la siguiente
    información y, a continuación, haga clic en la flecha de avance.

* **Standalone Virtual Machine:** Deje esta opción seleccionada.

* **DNS NAME:** Escriba *yourcloudapplication*.

* **CUENTA DE ALMACENAMIENTO:** Seleccione **yourstorage**.

* **REGION/GRUPO DE AFINIDAD/RED VIRTUAL:** En la lista desplegable,
  seleccione **YourVirtualNetwork**.
  
	![VMMode](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

1.  En la pantalla **Virtual machine options**, especifique la siguiente
    información y, a continuación, haga clic en el botón con la marca de
    verificación. Ya ha creado la máquina virtual. Se puede tardar hasta
    10 minutos en crear la nueva máquina.
    <!-- CONFIRM HOW LONG IT CAN TAKE ON AVG FOR VMACHINE TO BE CREATED -->

* **CONJUNTO DE DISPONIBILIDAD:** Seleccione **none**.

* **VIRTUAL NETWORK SUBNETS:** Seleccione **FrontEndSubnet**.
  
	<div  class="dev-callout" markdown="1">
	<b>NOTA:</b>
	<p>Debe seleccionar al menos una subred y NO seleccionar la subred de
	puerta de enlace.</p>
  
  
	</div>
  
	![VMOptions](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

1.  Cuando se haya creado la máquina virtual, en la pantalla de máquinas
    virtuales, en **STATUS** aparecerá **Running**.
    
    ![VMInstances](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)

2.  En el panel de navegación, haga clic en **ALL ITEMS**. Todos los
    objetos que ha creado se mostrarán con su estado actual.
    
    ![AllTab](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

## Pasos siguientes

Para instalar un controlador de dominio adicional para el dominio de Active Directory local en la máquina virtual que acaba de crear, consulte [Instalación de una réplica de controlador de dominio de Active Directory en Red virtual de Azure](/es-es/manage/services/networking/replica-domain-controller/).

## Otras referencias

* [Red virtual][1]

* [Configurar una red virtual con archivos de configuración de red][3]

<!-- LINKS -->



[1]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx
[2]: http://manage.windowsazure.com/
[3]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156097.aspx
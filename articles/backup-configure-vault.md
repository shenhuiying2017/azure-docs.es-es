<properties 
	pageTitle="Configuración de los servicios de Copia de seguridad de Azure para hacer copias de seguridad de Windows Server de manera rápida y fácil" 
	description="Use este tutorial para aprender a utilizar el servicio Copia de seguridad en la nube de Microsoft Azure para crear copias de seguridad de Windows Server en la nube." 
	services="backup" 
	documentationCenter="" 
	authors="markgalioto" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="markgal"/>



<h1><a id="configure-a-backup-vault-tutorial"></a>Configuración de Copia de seguridad de Azure para hacer copias de seguridad de Windows Server de manera rápida y fácil</h1>

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure. Este tutorial le guiará por el proceso de habilitación de la característica Copia de seguridad de Azure. Anteriormente, era necesario crear o adquirir un certificado X.509 v3 con el fin de registrar el servidor de copia de seguridad. Los certificados aún se admiten, pero ahora, para facilitar el proceso de registro de almacenes de Azure en un servidor, puede generar una credencial de almacén directamente en la página de inicio rápido. 
<ul><li>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="/pricing/free-trial/">Evaluación gratuita de Azure</a>.</li></ul>
  

<p>Para hacer una copia de seguridad de los archivos y los datos de Windows Server en Azure, debe crear un almacén de copia de seguridad en la región geográfica donde desea almacenar los datos. Este tutorial le guiará por el proceso de creación del almacén que se va a usar para las copias de seguridad, la descarga de una credencial de almacén, la instalación de un agente de copia de seguridad y una introducción a las tareas de administración de las copias de seguridad disponibles a través del portal de administración.</p>



<h2><a id="create"></a>Creación de un almacén de copia de seguridad</h2>

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Nuevo**, seleccione **Servicios de datos** y luego **Servicios de recuperación**, haga clic en **Almacén de copia de seguridad** y luego en **Creación rápida**.

3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén de copia de seguridad.

4. En **Región**, seleccione la región geográfica para el almacén de credenciales de copia de seguridad.  
![New backup vault](http://i.imgur.com/8ptgjuo.png)

5. Haga clic en **Crear almacén**.

	La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal. Una vez creado el almacén de copia de seguridad, aparecerá un mensaje que indica que el almacén se ha creado correctamente y que se mostrará en los recursos de Servicios de recuperación como **Activo**. 
![backup vault creation](http://i.imgur.com/grtLcKM.png)

3. Si tiene varias suscripciones asociadas a su cuenta profesional, elija la cuenta adecuada que se asociará al almacén de copia de seguridad.

<h2><a id="upload"></a>Descarga de una credencial de almacén</h2>

Las credenciales de almacén reemplazan los certificados como manera de registrar el servicio de Azur en el servidor. Los certificados aún se pueden usar. Sin embargo, las credenciales de almacén son más fáciles de usar porque utiliza el portal de Azure para generarlas y descargarlas.  

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Servicios de recuperación** y seleccione el almacén de copia de seguridad que desee registrar en un servidor.  Aparecerá la página de inicio rápido del almacén de copia de seguridad.
	

3. En la página de inicio rápido, haga clic en **Descargar credenciales de almacén** para solicitar al portal que genere y descargue las credenciales de almacén que usará para registrar el servidor en el almacén de copia de seguridad.

4. El portal generará una credencial de almacén mediante una combinación del nombre del almacén y la fecha actual. Haga clic en **Guardar** para descargar las credenciales de almacén en la carpeta de descargas de la cuenta local o seleccione **Guardar como** del menú **Guardar** para especificar una ubicación para las credenciales de almacén. No es posible editar las credenciales de almacén. Por lo tanto, no hay motivo para hacer clic en Abrir. Una vez que se hayan descargado las credenciales, se le solicitará que abra la carpeta. Haga clic en **x** para cerrar este menú.

<h2><a id="download"></a>Descarga e instalación de un agente de copia de seguridad</h2>
1. En el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Servicios de recuperación** y seleccione un almacén de copia de seguridad para ver su página de inicio rápido.

3. En la página de inicio rápido, seleccione el tipo de agente que desee descargar. Puede elegir **Descargar Azure Backup Agent**, **Windows Server y System Center Data Protection Manager** o **Windows Server Essentials**.  Para obtener más información, consulte:

	* [Instalación de Azure Backup Agent para Windows Server 2012 y System Center 2012 SP1 - Data Protection Manager](http://technet.microsoft.com/library/hh831761.aspx#BKMK_installagent)
	* [Instalación de Azure Backup Agent para Windows Server 2012 Essentials](http://technet.microsoft.com/library/jj884318.aspx)

Después de que se instala el agente, puede usar la interfaz de administración local adecuada (como el complemento de Microsoft Management Console, System Center Data Protection Manager Console o el Panel de Windows Server Essentials) para configurar la directiva de copia de seguridad del servidor.
	
  

<h2><a id="manage"></a>Administración de almacenes de copia de seguridad y servidores</h2>
1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Servicios de recuperación** y luego en el nombre del almacén de copia de seguridad para ver la página de inicio rápido. 

3. Haga clic en **Panel** para ver información general del uso del servidor. En la parte inferior del panel puede llevar a cabo las tareas siguientes:
	* **Administrar certificado**. Si un certificado se usó para registrar un servidor, utilícelo para actualizar el certificado. Si usa credenciales de almacén, no utilice **Administrar certificado**.
	* **Eliminar**. Elimina el almacén de credenciales de copia de seguridad actual. En caso de que ya no se use un almacén de credenciales de copia de seguridad, puede eliminarlo para liberar espacio de almacenamiento. **Eliminar** solo está habilitado después de que todos los servidores registrados se hayan eliminado del almacén.
	* **Credenciales de almacén**. Use este elemento del menú de vista rápida para configurar las credenciales de almacén. 

3. Haga clic en **Protected Items** para ver los elementos a los que se les ha hecho una copia de seguridad desde los servidores. Esta lista solo tiene propósitos informativos.  
![Protected Items][protected-itmes]

4. Haga clic en **Servers** para ver los nombres de los servidores que se registraron en este almacén. Aquí puede realizar las siguientes tareas:
	* **Permitir nuevo registro**. Cuando se selecciona esta opción para un servidor, puede usar el Asistente para registro en el agente para registrar el servidor con el almacén de credenciales de copia de seguridad por segunda vez. Es posible que necesite volver a registrarse debido a un error en el certificado o si un servidor tuvo que reconstruirse. El nuevo registro se permite solo una vez por nombre de servidor.
	* **Eliminar**. Elimina un servidor del almacén de credenciales de copias seguridad. Todos los datos almacenados asociados con el servidor se eliminan inmediatamente.

		![Deleted Server][deleted-server]

<h2><a id="next"></a>Pasos siguientes</h2>

- Para obtener más información sobre la copia de seguridad de Azure, consulte [Información general sobre Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=222425). 

- Visite el [Foro de Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

[new-backup-vault]: ./media/backup-configure-vault/RS_howtobackup1.png
[backup-vault-create]: ./media/backup-configure-vault/RS_howtobackup2.png
[manage-cert]: ./media/backup-configure-vault/RS_howtoupload1.png
[install-agent]: ./media/backup-configure-vault/RS_howtodownload1.png
[deleted-server]: ./media/backup-configure-vault/RS_deletedserver.png
[protected-itmes]: ./media/backup-configure-vault/RS_protecteditems.png

<!--HONumber=49--> 

<!--HONumber=49-->
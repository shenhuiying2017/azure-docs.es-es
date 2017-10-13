---
title: Copia de seguridad de servidores de VMware con Azure Backup Server | Microsoft Docs
description: "Use Azure Backup Server para realizar una copia de seguridad de servidores VMware vCenter y ESXi en Azure o en un disco. En este artículo se proporciona instrucciones detalladas para realizar copias de seguridad (o proteger) las cargas de trabajo de VMware."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: markgal;
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-vmware-server-to-azure"></a>Realización de una copia de seguridad del servidor de VMware en Azure

En este artículo se explica cómo configurar una instancia de Azure Backup Server para proteger la carga de trabajo del servidor de VMware. En este artículo se da por supuesto que ya tiene instalado Azure Backup Server. Si no tiene Azure Backup Server instalado, consulte [Preparación para la copia de seguridad de cargas de trabajo con Azure Backup Server](backup-azure-microsoft-azure-backup.md).

Azure Backup Server puede hacer una copia de seguridad de las versiones 6.5, 6.0 y 5.5 de VMware vCenter Server, así como ayudarle a protegerlas.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Creación de una conexión segura con vCenter Server

De forma predeterminada Azure Backup Server se comunica con cada instancia de vCenter Server a través de canal HTTPS. Para activar la comunicación segura, recomendamos que instale el certificado de entidad de certificación de VMware en Azure Backup Server. Si no necesita una comunicación segura y prefiere deshabilitar el requisito de HTTPS, consulte [Deshabilitación del protocolo de comunicación segura](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Para crear una conexión segura entre Azure Backup Server y vCenter Server, importe el certificado de confianza en Azure Backup Server.

Normalmente se utiliza un explorador en la máquina de Azure Backup Server para conectarse a vCenter Server a través de la interfaz del cliente web de vSphere. La primera vez que utilice el explorador de Azure Backup Server para conectarse a vCenter Server, la conexión no es segura. La siguiente imagen muestra la conexión no segura.

![Ejemplo de conexión no segura al servidor de VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Para corregir este problema y crear una conexión segura, descargue los certificados de entidad de certificación raíz de confianza.

1. En el explorador de Azure Backup Server, escriba la dirección URL del cliente web de vSphere. Aparecerá la página de inicio de sesión del cliente web de vSphere.

    ![Cliente web de vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    En la parte inferior de la información para los administradores y desarrolladores, busque **Download trusted root CA certificates** (Descargar certificados de entidad de certificación raíz de confianza).

    ![Vínculo para descargar los certificados de entidad de certificación raíz de confianza](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Si no ve la página de inicio de sesión del cliente web de vSphere, compruebe la configuración del proxy de su explorador.

2. Haga clic en **Download trusted root CA certificates** (Descargar certificados de entidad de certificación raíz de confianza).

    vCenter Server descarga un archivo en el equipo local. El nombre del archivo es **download**. Según el navegador, recibirá un mensaje que le pregunta si desea abrir o guardar el archivo.

    ![descargar el mensaje cuando se descargan los certificados](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Guarde el archivo en una ubicación en Azure Backup Server. Cuando guarde el archivo, agregue la extensión de nombre de archivo .zip.

    El archivo es un archivo .zip que contiene la información de los certificados. Con la extensión .zip, puede usar las herramientas de extracción.

4. Haga clic con el botón derecho en **download.zip** y seleccione **Extraer todo** para extraer el contenido.

    El archivo .zip extrae su contenido en una carpeta denominada **certs**. Se muestran dos tipos de archivos en la carpeta certs. El archivo de certificado raíz tiene una extensión que comienza con una secuencia numerada como .0 y .1.
    
    El archivo CRL tiene una extensión que comienza con una secuencia numerada como .r0 y .r1. El archivo CRL está asociado a un certificado.

    ![Descargar el archivo extraído localmente ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. En la carpeta **certs**, haga clic con el botón derecho en el archivo de certificado raíz y, después, haga clic en **Cambiar nombre**.

    ![Cambiar nombre de certificado raíz ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Cambie la extensión del certificado raíz a .crt. Cuando se le pregunte si está seguro de que desea cambiar la extensión, haga clic en **Sí** o **Aceptar**. En caso contrario, cambie la función prevista del archivo. El icono del archivo cambia a un icono que representa un certificado raíz.

6. Haga clic con el botón derecho en el certificado raíz y, en el menú emergente, seleccione **Instalar certificado**.

    Se abre el cuadro de diálogo **Asistente para importar certificados**.

7. En el cuadro de diálogo **Asistente para importar certificados**, seleccione **Máquina local** como destino del certificado y, después, haga clic en **Siguiente** para continuar.

    ![Opciones de destino de almacenamiento de certificados ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Si se le pregunta si desea permitir cambios en el equipo, haga clic en **Sí** o en **Aceptar**, para todos los cambios.

8. En la página **Almacén de certificados**, seleccione **Colocar todos los certificados en el siguiente almacén** y haga clic en **Examinar** para elegir el almacén de certificados.

    ![Colocar certificados en una zona de almacenamiento específica](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    Se muestra el cuadro de diálogo **Seleccionar almacén de certificados**.

    ![Jerarquía de carpetas de almacenamiento de certificado](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Seleccione **Entidades de certificación raíz de confianza** como carpeta de destino para los certificados y, después, haga clic en **Aceptar**.

    ![Carpeta de destino de certificado](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    La carpeta **Entidades de certificación raíz de confianza** se confirma como el almacén de certificados. Haga clic en **Siguiente**.

    ![Carpeta de almacén de certificados](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. En la página **Finalización del Asistente para importar certificados**, compruebe que el certificado se encuentra en la carpeta deseada y, después, haga clic en **Finalizar**.

    ![Comprobar que el certificado se encuentra en la carpeta correcta](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Aparece un cuadro de diálogo, que confirma que la importación del certificado se ha realizado correctamente.

11. Inicie sesión en vCenter Server para comprobar que la conexión es segura.

  Si la importación del certificado no se ha realizado correctamente y no se puede establecer una conexión segura, consulte la documentación de VMware vSphere sobre [cómo obtener certificados del servidor](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Si tiene límites de seguridad en su organización y no desea activar el protocolo HTTPS, utilice el procedimiento siguiente para deshabilitar la comunicación segura.

### <a name="disable-secure-communication-protocol"></a>Deshabilitación del protocolo de comunicación segura

Si su organización no requiere el protocolo HTTPS, siga estos pasos para deshabilitar HTTPS. Para deshabilitar el comportamiento predeterminado, cree una clave del Registro que ignora el comportamiento predeterminado.

1. Copie y pegue el texto siguiente en el archivo .txt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Guarde el archivo en su equipo de Azure Backup Server. Para el nombre de archivo, utilice DisableSecureAuthentication.reg.

3. Haga doble clic en el archivo para activar la entrada del Registro.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Creación de un rol y una cuenta de usuario en vCenter Server

En vCenter Server, un rol es un conjunto predefinido de privilegios. Un administrador de vCenter Server crea los roles. Para asignar permisos, el administrador asocia las cuentas de usuario a un rol. Para establecer las credenciales de usuario necesarias para realizar una copia de seguridad del equipo de vCenter Server, cree un rol con privilegios específicos y después asocie la cuenta de usuario al rol.

Azure Backup Server usa un nombre de usuario y una contraseña para autenticarse con vCenter Server. Azure Backup Server utiliza las credenciales como autenticación para todas las operaciones de copia de seguridad.

Para agregar un rol y privilegios de vCenter Server para un administrador de copia de seguridad:

1. Inicie sesión en vCenter Server y, después, en el panel **Navegador** de vCenter server, haga clic en **Administración**.

    ![Opción de administración en el panel Navegador de vCenter Server](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. En **Administración**, seleccione **Roles** y, en el panel **Roles**, haga clic en el icono Agregar rol (el símbolo +).

    ![Agregar rol](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    Aparece el cuadro de diálogo **Crear rol** .

    ![Crear rol](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. En el cuadro de diálogo **Crear rol**, en el campo **Nombre de rol**, escriba un *BackupAdminRole*. El nombre del rol puede ser el que desee, pero debe ser reconocible para el propósito del rol.

4. Seleccione los privilegios para la versión adecuada de vCenter y, después, haga clic en **Aceptar**. En la tabla siguiente se identifican los privilegios necesarios para vCenter 6.0 y vCenter 5.5.

  Una vez seleccionados, haga clic en el icono junto a la etiqueta principal para expandir los privilegios primarios y ver los privilegios secundarios. Para seleccionar los privilegios de la máquina virtual, debe tener varios niveles en la jerarquía primaria-secundaria. No es necesario seleccionar todos los privilegios secundarios de un privilegio principal.

  ![Jerarquía de privilegios primaria-secundaria](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Después de hacer clic en **Aceptar**, el nuevo rol aparece en la lista del panel Roles.

|Privilegios para vCenter 6.0| Privilegios para vCenter 5.5|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Creación de permisos y una cuenta de usuario de vCenter Server

Después de configurar el rol con privilegios, cree una cuenta de usuario. La cuenta de usuario tiene un nombre y una contraseña, que proporciona las credenciales usadas para la autenticación.

1. Para crear una cuenta de usuario, en panel **Navegador** de vCenter Server, haga clic en **Usuarios y grupos**.

    ![Opción Usuarios y grupos](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Aparece el panel **Usuarios y grupos** de vCenter.

    ![Panel Usuarios y grupos de vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. En el panel **Usuarios y grupos** de vCenter, seleccione la pestaña **Usuarios** y haga clic en el icono Agregar usuarios (el símbolo +).

    Aparecerá el cuadro de diálogo **Nuevo usuario**.

3. En el cuadro de diálogo **Nuevo usuario**, agregue la información del usuario y, después, haga clic en **Aceptar**. En este procedimiento, el nombre de usuario es BackupAdmin.

    ![Cuadro de diálogo Nuevo usuario](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    La nueva cuenta de usuario aparece en la lista.

4. Para asociar la cuenta de usuario con el rol, en el panel **Navegador**, haga clic en **Permisos globales**. En el panel **Permisos globales**, seleccione la pestaña **Administrar** y, después, haga clic en el icono Agregar (el símbolo +).

    ![Panel Permisos globales](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    Se abre el cuadro de diálogo **Global Permission Root - Add Permission** (Raíz de permisos globales: agregar permiso).

5. En el cuadro de diálogo **Global Permission Root - Add Permission** (Raíz de permisos globales: agregar permiso), haga clic en **Agregar** para elegir el usuario o grupo.

    ![Elegir un usuario o grupo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    Se abre el cuadro de diálogo **Seleccionar usuarios y grupos**.

6. En el cuadro de diálogo **Seleccionar usuarios o grupos**, elija **BackupAdmin** y, después, haga clic en **Agregar**.

    En **Usuarios**, se usa el formato *dominio\nombre de usuario* para la cuenta de usuario. Si desea usar otro dominio, elíjalo en la lista **Dominio**.

    ![Agregar usuario BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Haga clic en **Aceptar** para agregar los usuarios seleccionados al cuadro de diálogo **Agregar permiso**.

7. Ahora que ha identificado al usuario, asígnelo al rol. En el área **Rol asignado**, en el menú desplegable, seleccione **BackupAdminRole** y haga clic en **Aceptar**.

    ![Asignar el usuario al rol](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  En la pestaña **Administrar** del panel **Permisos globales**, la nueva cuenta de usuario y el rol asociado aparecen en la lista.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Establecimiento de las credenciales de vCenter Server en Azure Backup Server

Antes de agregar el servidor de VMware a Azure Backup Server, asegúrese de haber instalado [Update 1 para Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Para abrir Azure Backup Server, haga doble clic en el icono en el escritorio de Azure Backup Server.

    ![Icono de Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Si no encuentra el icono en el escritorio, abra Azure Backup Server en la lista de aplicaciones instaladas. El nombre de la aplicación de Azure Backup Server se denomina Microsoft Azure Backup.

2. En la consola de Azure Backup Server, haga clic sucesivamente en **Administración**, en **Servidores de producción** y, después, en la cinta de opciones de herramientas, haga clic en **Administrar VMware**.

    ![Consola de Azure Backup Server](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    Aparece el cuadro de diálogo **Administrar credenciales**.

    ![Cuadro de diálogo Administrar credenciales de Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. En el cuadro de diálogo **Administrar credenciales**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar credenciales**.

4. En el cuadro de diálogo **Agregar credenciales**, introduzca un nombre y una descripción para la nueva credencial. Después, especifique el nombre de usuario y la contraseña. El nombre *Contoso Vcenter credential* se usa para identificar la credencial en el procedimiento siguiente. Utilice el mismo nombre de usuario y contraseña que utilizó en vCenter Server. Si vCenter Server y Azure Backup Server no están en el mismo dominio, en **Nombre de usuario**, especifique el dominio.

    ![Cuadro de diálogo Agregar credenciales de Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Haga clic en **Agregar** para agregar la nueva credencial a Azure Backup Server. La nueva credencial aparece en la lista del cuadro de diálogo **Administración de credenciales**.
    
    ![Cuadro de diálogo Administrar credenciales de Azure Backup Server](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Para cerrar el cuadro de diálogo **Administrar credenciales**, haga clic en la **X** de la esquina superior derecha.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adición de vCenter Server a Azure Backup Server

El Asistente de adición del servidor de producción se utiliza para agregar vCenter Server a Azure Backup Server.

Para abrir el Asistente de adición del servidor de producción, realice el procedimiento siguiente:

1. En la consola de Azure Backup Server, haga clic en **Administración**, en **Servidor de producción** y, a continuación, en **Agregar**.

    ![Abrir el Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    Se muestra el cuadro de diálogo **Asistente para adición del servidor de producción**.

    ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. En la página **Seleccionar tipo de servidor de producción**, seleccione los **servidores de VMware** y haga clic en **Siguiente**.

3. En **Nombre de servidor o dirección IP**, especifique el nombre de dominio completo (FQDN) o la dirección IP del servidor de VMware. Si todos los servidores de ESXi están administrados por el mismo VCenter, puede usar el nombre de vCenter.

    ![Especificar el nombre de dominio completo o dirección IP del servidor de VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. En **Puerto SSL**, especifique el puerto usado para comunicarse con el servidor de VMware. Utilice el puerto 443, que es el puerto predeterminado, a menos que sepa que es necesario otro distinto.

5. En **Especificar credenciales**, seleccione la credencial que ha creado antes.

    ![Especificar credenciales](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Haga clic en **Agregar** para agregar el servidor de VMware a la lista de **Servidores VMware agregados** y, después, en **Siguiente** para ir a la pantalla siguiente del asistente.

    ![Agregar credencial y el servidor de VMWare](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. En la pantalla **Resumen**, haga clic en **Agregar** para agregar el servidor de VMware especificado a Azure Backup Server.

    ![Agregar el servidor de VMware a Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  La copia de seguridad del servidor de VMware es una copia de seguridad sin agente y el nuevo servidor se agrega inmediatamente. La pantalla **Finalizar** muestra los resultados.

  ![Página Finalizar](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Para agregar varias instancias de vCenter Server a Azure Backup Server, repita los pasos anteriores de esta sección.

Después de agregar vCenter Server a Azure Backup Server, el próximo paso es crear un grupo de protección. El grupo de protección especifica los diversos detalles de la retención a corto o largo plazo. Aquí se definen y aplican las directivas de copia de seguridad. Las directivas de copia de seguridad son la programación de cuándo se realizan las copias de seguridad y qué se incluye en la copia de seguridad.


## <a name="configure-a-protection-group"></a>Configuración de un grupo de protección

Si nunca ha usado System Center Data Protection Manager ni Azure Backup Server, consulte [Planificación de copias de seguridad en disco](https://technet.microsoft.com/library/hh758026.aspx) para preparar el entorno del hardware. Después de comprobar que tiene un almacenamiento adecuado, use el asistente Crear nuevo grupo de protección para agregar las máquinas virtuales de VMware.

1. En la consola de Azure Backup Server, haga clic en **Protección** y, en la cinta de opciones de herramientas, haga clic en **Nuevo** para abrir el asistente para crear nuevo grupo de protección.

    ![Abrir el asistente Crear nuevo grupo de protección.](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    Se abre el cuadro de diálogo del asistente **Crear nuevo grupo de protección**.

    ![Cuadro de diálogo del asistente Crear nuevo grupo de protección](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Haga clic en **Siguiente** para avanzar a la página **Seleccionar tipo de grupo de protección**.

2. En la página **Seleccionar tipo de grupo de protección**, seleccione **Servidores** y, después, haga clic en **Siguiente**. Se muestra la página **Seleccionar miembros del grupo**.

3. En la página **Seleccionar miembros del grupo**, se muestran los miembros disponibles y los miembros seleccionados. Seleccione los miembros a los que desea proteger y, después, haga clic en **Siguiente**.

    ![Seleccionar a miembros del grupo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Al seleccionar un miembro, si selecciona una carpeta que contiene otras carpetas o máquinas virtuales, estas también se seleccionarán. La inclusión de las carpetas y máquinas virtuales en la carpeta principal se denomina protección de nivel de carpeta. Para quitar una carpeta o una máquina virtual, desactive la casilla.

    Si una máquina virtual, o bien una carpeta que contiene una máquina virtual, ya está protegida en Azure, no podrá volver a seleccionar dicha máquina virtual. Es decir, cuando una máquina virtual está protegida en Azure, no puede protegerse de nuevo, lo que impide la creación de puntos de recuperación duplicados para una máquina virtual. Si desea ver qué instancia de Azure Backup Server ya protege a un miembro, apunte al miembro para ver el nombre del servidor de protección.

4. En la página **Seleccionar método de protección de datos**, introduzca un nombre para el grupo de protección. Se selecciona la protección a corto plazo (en el disco) y la protección en línea. Si desea utilizar la protección en línea (en Azure), debe usar la protección a corto plazo en el disco. Haga clic en **Siguiente** para continuar con la duración de protección a corto plazo.

    ![Seleccionar método de protección de datos](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. En la página **Especificar objetivos a corto plazo**, para **Duración de retención**, especifique el número de días que desea retener los puntos de recuperación *almacenados en el disco*. Si quiere cambiar la hora y los días en que se usan los puntos de recuperación, haga clic en **Modificar**. Los puntos de recuperación a corto plazo son copias de seguridad completas. No son copias de seguridad incrementales. Si está satisfecho con los objetivos a corto plazo, haga clic en **Siguiente**.

    ![Especificar objetivos a corto plazo](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. En la página **Revisar asignación de disco**, revise el espacio en disco para las máquinas virtuales y, en caso necesario, modifíquelo. Las asignaciones de disco recomendadas se basan en la duración de retención especificada en la página **Especificar objetivos a corto plazo**, el tipo de carga de trabajo y el tamaño de los datos protegidos (identificados en el paso 3).  

  - **Tamaño de los datos**: tamaño de los datos del grupo de protección.
  - **Espacio en disco**: la cantidad recomendada de espacio en disco para el grupo de protección. Si desea modificar esta configuración, debe asignar un espacio total que sea ligeramente mayor que la cantidad que calcula que va a crecer cada origen de datos.
  - **Ubicación compartida de datos**: si activa la ubicación compartida, varios orígenes de datos de la protección pueden asignarse a una sola réplica y a un volumen de puntos de recuperación. La ubicación compartida no es compatible con todas las cargas de trabajo.
  - **Crecimiento automático**: en caso de que active esta configuración, si los datos del grupo protegido sobrepasan la asignación inicial, System Center Data Protection Manager intenta aumentar el tamaño del disco en un 25 %.
  - **Detalles del bloque de almacenamiento**: muestra el estado actual del grupo de almacenamiento, incluido el tamaño total y restante del disco.

    ![Revisar la asignación de disco](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Cuando esté satisfecho con la asignación de espacio, haga clic en **Siguiente**.

7. En la página **Seleccionar método de creación de réplicas**, especifique cómo quiere generar la copia inicial o réplica de los datos protegidos en Azure Backup Server.

    El valor predeterminado es **Automáticamente a través de la red** y **Ahora**. Si usa el valor predeterminado, recomendamos que especifique una hora de poco tráfico. Elija **Más tarde** y especifique un día y una hora.

    Para grandes cantidades de datos o condiciones de red no del todo óptimas, considere la posibilidad de replicar los datos sin conexión con medios extraíbles.

    Una vez que haya elegido sus opciones, haga clic en **Siguiente**.

    ![Elegir método de creación de réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. En la página **Opciones de comprobación de coherencia**, seleccione cómo y cuándo automatizar las comprobaciones de coherencia. Puede ejecutar comprobaciones de coherencia si los datos de réplica no son coherentes o en una programación establecida.

    Si no desea configurar la comprobación de coherencia automática, puede ejecutar una comprobación manual. En el área Protección de la consola de Azure Backup Server, haga clic con el botón derecho en el grupo de protección y, después, seleccione **Realizar comprobación de coherencia**.

    Haga clic en **Siguiente** para ir a la página siguiente.

9. En la página **Especificar datos de protección en línea**, seleccione uno o varios de los orígenes de datos que desea proteger. Puede seleccionar los miembros individualmente o hacer clic en **Seleccionar todo** para elegir todos los miembros. Después de elegir los miembros, haga clic en **Siguiente**.

    ![Especificar datos de protección en línea](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. En la página **Especificar una programación de copia de seguridad en línea**, especifique la programación para generar puntos de recuperación desde la copia de seguridad de disco. Después de generar el punto de recuperación, se transfiere al almacén de Recovery Services de Azure. Si está satisfecho con la programación de copia de seguridad en línea, haga clic en **Siguiente**.

    ![Especificar programación de copia de seguridad en línea](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. En la pantalla **Especificar directivas de retención en línea**, indique cuánto tiempo desea retener los datos de copia de seguridad en Azure. Después de definir la directiva, haga clic en **Siguiente**.

    ![Especificar directiva de retención en línea](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Los datos pueden guardarse en Azure sin límite de tiempo. Cuando se almacenan datos del punto de recuperación en Azure, el único límite que existe es que no puede tener más de 9999 puntos de recuperación por instancia protegida. En este ejemplo, la instancia protegida es el servidor de VMware.

12. En la página **Resumen**, revise los detalles de los miembros del grupo de protección y de la configuración, y después haga clic en **Crear grupo**.

    ![Resumen de configuración y miembros del grupo de protección](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Pasos siguientes
Si usa Azure Backup Server para proteger las cargas de trabajo de VMware, es posible que esté interesado en usar Azure Backup Server para ayudar a proteger [Microsoft Exchange Server](./backup-azure-exchange-mabs.md), una [granja de Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md) o un servidor [SQL Server](./backup-azure-sql-mabs.md).

Para más información sobre problemas con el registro del agente, la configuración del grupo de protección y los trabajos de copia de seguridad, consulte [Solución de problemas de Azure Backup Server](./backup-azure-mabs-troubleshoot.md).

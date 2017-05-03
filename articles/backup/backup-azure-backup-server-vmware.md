---
title: Use Azure Backup Server to protect VMware Server (Uso de Azure Backup Server para proteger el servidor de VMware) | Microsoft Docs
description: "Realice una copia de seguridad de un servidor de VMware en Azure o en el disco, con Azure Backup Server. Use este artículo para proteger la carga de trabajo VMware."
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
ms.date: 04/20/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 34146bd110661c12c4ec1e11d34d7bdfa3cac688
ms.lasthandoff: 04/21/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Realizar una copia de seguridad del servidor de VMware en Azure

En este artículo se explica cómo configurar una instancia de Azure Backup Server para proteger la carga de trabajo del servidor de VMware. En este artículo se da por supuesto que ya tiene instalado Azure Backup Server. Si no tiene Azure Backup Server instalado, consulte el artículo [Preparación para la copia de seguridad de cargas de trabajo con Azure Backup Server](backup-azure-microsoft-azure-backup.md).

Azure Backup Server puede hacer una copia de seguridad, o proteger, las versiones 6.0 y 5.5 de VMware vCenter Server.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Creación de una conexión segura con vCenter Server

De forma predeterminada Azure Backup Server se comunica con instancias de vCenter Server a través de canal HTTPS. Para habilitar la comunicación segura, se recomienda que instale el certificado de entidad de certificación de VMware en Azure Backup Server. Si no necesita una comunicación segura y prefiere deshabilitar el requisito de HTTPS, consulte la sección [Deshabilitación del protocolo de comunicación segura](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Para crear una conexión segura entre Azure Backup Server y vCenter Server, importe el certificado de confianza en Azure Backup Server.

Normalmente se utiliza un explorador en la máquina de Azure Backup Server para conectarse a vCenter Server a través de la interfaz del cliente web de vSphere. La primera vez que utilice el explorador de Azure Backup Server para conectarse a vCenter Server, la conexión no es segura. La siguiente imagen muestra la conexión no segura.

![ejemplo de conexión no segura al servidor de VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Para corregir este problema y crear una conexión segura, descargue los certificados de entidad de certificación raíz de confianza.

1. En el explorador de Azure Backup Server, escriba la dirección URL del cliente web de vSphere.

  Aparecerá la página de inicio de sesión del cliente web de vSphere.

  ![cliente web de vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

  En la parte inferior de la información para los administradores y desarrolladores, se encuentra el vínculo a **Download trusted root CA certificates** (Descargar certificados de entidad de certificación raíz de confianza).

  ![vínculo para descargar los certificados de entidad de certificación raíz de confianza](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Si no ve la página de inicio de sesión del cliente web de vSphere, compruebe la configuración del proxy de su explorador.

2. Haga clic en **Download trusted root CA certificates** (Descargar certificados de entidad de certificación raíz de confianza).

  vCenter Server descarga un archivo en el equipo local. El nombre del archivo es **download**. Según el navegador, recibirá un mensaje que le pregunta si desea abrir o guardar el archivo.

  ![descargar el mensaje cuando se descargan los certificados](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Guarde el archivo en una ubicación en Azure Backup Server. Cuando guarde el archivo, agregue la extensión de nombre de archivo .zip.

  El archivo es un archivo .zip que contiene la información de los certificados. Agregar la extensión .zip permite usar herramientas de extracción.

4. Haga clic con el botón derecho en **download.zip** y seleccione Extraer todo para extraer el contenido.

  El archivo comprimido extrae su contenido en una carpeta denominada **certs**. En la carpeta certs hay dos tipos de archivos. El archivo de certificado raíz tiene una extensión como: .0, .1, o .*número*. El archivo CRL tiene una extensión que comienza por .r0, .r1 y así sucesivamente. El archivo CRL está asociado a un certificado.

  ![descargar el archivo extraído localmente ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. En la carpeta **certs**, haga clic con el botón derecho en el archivo de certificado raíz y haga clic en **Cambiar nombre**.

  ![cambiar nombre de certificado raíz ](./media/backup-azure-backup-server-vmware/rename-cert.png)

  Cambie la extensión del certificado raíz a .crt. Cuando se le pregunte si está seguro de que desea cambiar la extensión, porque podría cambiar la función del archivo, haga clic en Sí o en Aceptar. El icono del archivo cambia al icono de un certificado raíz.

6. Haga clic con el botón derecho en el certificado raíz y, en el menú emergente, seleccione **Instalar certificado**.

  Se abre el asistente para importación de certificados.

7. En el asistente para importación de certificados, seleccione **Máquina local** como destino del certificado y haga clic en **Siguiente** para continuar.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

  Si se le pregunta si desea permitir cambios en el equipo, haga clic en Sí o en Aceptar, para todos los cambios.

8. En la pantalla Almacén de certificados, seleccione **Colocar todos los certificados en el siguiente almacén** y haga clic en **Examinar** para elegir el almacén de certificados.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  Se abre el cuadro de diálogo Seleccionar almacén de certificados.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Seleccione **Entidades de certificación raíz de confianza** como carpeta de destino para los certificados y haga clic en **Aceptar**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

  El almacén de certificados seleccionado se muestra en el **Asistente para importar certificados**. Haga clic en **Siguiente**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. En la pantalla Finalización del Asistente para importación de certificados, compruebe que el certificado se encuentra en la carpeta deseada y haga clic en **Finalizar** para completar el asistente.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  Aparece un cuadro de diálogo que permite saber si la importación se ha realizado correctamente.

11. Inicie sesión en vCenter Server para comprobar que la conexión es segura.

  Si tiene problemas al importar el certificado y no se puede establecer una conexión segura, consulte la documentación de VMware vSphere en [Obtaining Server Certificates](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html) (Obtención de certificados de servidor).

  Si tiene límites de seguridad en su organización y no desea habilitar el protocolo HTTPs, utilice el procedimiento siguiente para deshabilitar la comunicación segura.

### <a name="disable-secure-communication-protocol"></a>Deshabilitación del protocolo de comunicación segura

Si su organización no requiere el protocolo de comunicaciones seguras (HTTPS), siga estos pasos para deshabilitar HTTPS. Para deshabilitar el comportamiento predeterminado, cree una clave del Registro que ignora el comportamiento predeterminado.

1. Copie y pegue el texto siguiente en el archivo .txt.

  ```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```
2. Guarde el archivo con el nombre **DisableSecureAuthentication.reg** en Azure Backup Server.

3. Haga doble clic en el archivo para activar la entrada del Registro.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Creación de un rol y una cuenta de usuario en vCenter Server

En vCenter Server, un rol es un conjunto predefinido de privilegios. Un administrador de servidor en vCenter Server crea los roles y las cuentas de usuario de pares con los roles a los que desea asignar permisos. Para establecer las credenciales de usuario necesarias para realizar una copia de seguridad de vCenter Server, cree un rol con privilegios específicos y asocie la cuenta de usuario al rol.

Azure Backup Server usa un nombre de usuario y una contraseña para autenticarse con vCenter Server. Azure Backup Server utiliza las credenciales como autenticación para todas las operaciones de copia de seguridad.

Para agregar un rol y privilegios de vCenter Server para un administrador de copia de seguridad:

1. Inicie sesión en vCenter Server y, en **Navegador**, haga clic en **Administración**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. En la sección **Administración**, seleccione **Roles** y, en el panel **Roles**, haga clic en el icono Agregar rol (el símbolo +).

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  Se abre el cuadro de diálogo **Crear rol**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. En el cuadro de diálogo **Crear rol**, en el campo **Nombre de rol**, escriba un *BackupAdminRole*. El nombre del rol puede ser el que desee, pero debe ser reconocible para el propósito del rol.

4. Seleccione los privilegios para la versión adecuada de vCenter y haga clic en **Aceptar**. En la tabla siguiente identifica los privilegios necesarios para vCenter 6.0 y vCenter 5.5.

  Una vez seleccionados, haga clic en el botón de contenido adicional de la etiqueta principal para expandir los privilegios principales y ver los privilegios secundarios. La selección de los privilegios necesarios de la máquina virtual necesita pasar por varios "niveles". No es necesario seleccionar todos los privilegios secundarios de un privilegio principal.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

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



## <a name="create-vcenter-server-user-account-and-permissions"></a>Creación de permisos y una cuenta de usuario de vCenter Server

Cuando exista el rol con privilegios, cree una cuenta de usuario. La cuenta de usuario tiene un nombre y una contraseña, que proporciona las credenciales usadas para la autenticación.

1. Para crear una cuenta de usuario, en el navegador de vCenter Server, haga clic en **Usuarios y grupos**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  Aparece el panel Usuarios y grupos.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. En el panel Usuarios y grupos de vCenter, seleccione la pestaña **Usuarios** y haga clic en el icono Agregar usuarios (el símbolo +).

  Se abre el cuadro de diálogo Nuevo usuario.

3. En el cuadro de diálogo Nuevo usuario, rellene los campos y haga clic en **Aceptar**. Para este ejemplo, escriba **BackupAdmin** del nombre de usuario. La contraseña debe ser una contraseña segura.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  La nueva cuenta de usuario aparece en la lista.

4. Para asociar la cuenta de usuario con el rol, en el navegador, haga clic en **Permisos globales**. En el panel Permisos globales, seleccione la pestaña **Administrar** y haga clic en el icono Agregar (el símbolo +).

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  Se abre el cuadro de diálogo Global Permission Root - Add Permission (Raíz de permisos globales: agregar permiso).

5. En el cuadro de diálogo **Global Permission Root - Add Permission** (Raíz de permisos globales: agregar permiso), haga clic en **Agregar** para elegir el usuario o grupo.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  Se abre el cuadro de diálogo Seleccionar usuarios y grupos.

6. En el cuadro de diálogo **Seleccionar usuarios o grupos**, elija **BackupAdmin** y haga clic en **Agregar**.

  El nombre de usuario del campo Usuarios tiene el formato *dominio*`\`*nombre de usuario*. Si desea usar otro dominio, elíjalo en la lista de dominios.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  Haga clic en **Aceptar** para agregar los usuarios seleccionados al cuadro de diálogo Agregar permiso.

7. Ahora que ha identificado al usuario, asígnelo al rol. En el área Rol asignado, en el menú desplegable, seleccione **BackupAdminRole** y haga clic en **Aceptar**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  En la pestaña Administrar de Permisos globales, la nueva cuenta de usuario y el rol asociado aparecen en la lista.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Establecimiento de las credenciales de vCenter Server en Azure Backup Server

Antes de agregar el servidor de VMware a Azure Backup Server, asegúrese de haber instalado [Update 1 para Microsoft Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Para abrir Azure Backup Server, haga doble clic en el icono en el escritorio de Azure Backup Server.

  ![Icono de Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  Si no encuentra el icono en el escritorio, abra Azure Backup Server en la lista de aplicaciones instaladas. El nombre de la aplicación de Azure Backup Server es Microsoft Azure Backup.

2. En la consola de Azure Backup Server, haga clic en **Administración** y en **Servidores de producción** y, a continuación, en la cinta de opciones de herramientas, haga clic en **Administrar VMware**.

  ![Consola de MABS](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  Se abre el cuadro de diálogo Administración de credenciales.

  ![Cuadro de diálogo Administración de credenciales de MABS](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. En el cuadro de diálogo Administración de credenciales, haga clic en **Agregar** para abrir el cuadro de diálogo Agregar credenciales.

4. En el cuadro de diálogo Agregar credenciales, escriba un nombre y una descripción para la nueva credencial; después, especifique el nombre de usuario y la contraseña. El nombre de la credencial, *Contoso vCenter credencial* en el ejemplo, sirve para identificar la credencial en el procedimiento siguiente. Utilice el mismo nombre de usuario y contraseña que utilizó en vCenter Server. Si vCenter Server y Azure Backup Server no están en el mismo dominio, especifique el dominio en el nombre de usuario.

  ![Cuadro de diálogo Administración de credenciales de MABS](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

  Haga clic en **Agregar** para agregar la nueva credencial a Azure Backup Server. La nueva credencial aparece en la lista del cuadro de diálogo Administración de credenciales.
  ![Cuadro de diálogo Administración de credenciales de MABS](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Para cerrar el cuadro de diálogo Administrar credenciales, haga clic en la **X** de la esquina superior derecha.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adición de vCenter Server a Azure Backup Server

Para abrir el Asistente para agregar el servidor de producción

1. En la consola de Azure Backup Server, haga clic en **Administración**, en **Servidor de producción** y, a continuación, en **Agregar**.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  Se abre el asistente para adición del servidor de producción.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. En la pantalla Seleccionar tipo de servidor de producción, seleccione los servidores de VMware y haga clic en **Siguiente**.

3. En el nombre del servidor o dirección IP, especifique el nombre de dominio completo (FQDN) o la dirección IP del servidor de VMware. Si todos los servidores de ESXi están administrados por el mismo VCenter, puede usar el nombre de vCenter.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. En el cuadro de diálogo **Puerto SSL**, escriba el puerto usado para comunicarse con el servidor de VMware. Utilice el puerto 443, que es el puerto predeterminado, a menos que sepa que es necesario otro distinto.

5. En el cuadro de diálogo **Especificar credenciales**, seleccione la credencial que ha creado.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Haga clic en **Agregar** para agregar el servidor de VMware a la lista de **Servidores VMware agregados** y en **Siguiente** para ir a la pantalla siguiente del asistente.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. En la pantalla **Tareas**, haga clic en **Agregar** para agregar el servidor de VMware especificado a Azure Backup Server.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Como la copia de seguridad del servidor de VMware es una copia de seguridad sin agente, la adición del nuevo servidor tiene lugar en cuestión de segundos. La pantalla Finalizar muestra los resultados.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Para agregar varios servidores vCenter Server a Azure Backup Server, repita los pasos anteriores en esta sección.

Después de agregar vCenter Server a Azure Backup Server, el próximo paso es crear un grupo de protección. El grupo de protección especifica los diversos detalles de la retención a corto o largo plazo. Aquí se definen y aplican las directivas de copia de seguridad. Las directivas de copia de seguridad son la programación de cuándo se realizan las copias de seguridad y qué se incluye en la copia de seguridad.


## <a name="configure-a-protection-group"></a>Configuración de un grupo de protección

Si nunca ha usado System Center Data Protection Manager ni Azure Backup Server, consulte el tema, [Planificación de copias de seguridad en disco](https://technet.microsoft.com/library/hh758026.aspx), para preparar el entorno del hardware. Una vez que compruebe que tiene un almacenamiento adecuado, use el Asistente para Crear nuevo grupo de protección para agregar las máquinas virtuales de VMware.

1. En la consola de Azure Backup Server, haga clic en **Protección** y, en la cinta de opciones de herramientas, haga clic en **Nuevo** para abrir el asistente para crear nuevo grupo de protección.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  Se abre el asistente para crear nuevo grupo de protección.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  Haga clic en **Siguiente** para avanzar a la pantalla **Seleccionar tipo de grupo de protección**.

2. En la pantalla Seleccionar tipo de grupo de protección, seleccione **Servidores** y haga clic en **Siguiente**.

3. En la pantalla Seleccionar a miembros del grupo, puede ver los miembros disponibles y aquellos que se han seleccionado. Seleccione los miembros a los que desea proteger y haga clic en **Siguiente**.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

  Al seleccionar un miembro, si selecciona una carpeta que contiene otras carpetas o máquinas virtuales, estas también se seleccionarán. La inclusión de las carpetas y máquinas virtuales en la carpeta principal se denomina protección de nivel de carpeta. Puede excluir cualquier carpeta o máquina virtual anulando la selección de la casilla.

  Si una máquina virtual, o bien una carpeta que contiene una máquina virtual, ya está protegida en Azure, no podrá volver a seleccionar dicha máquina virtual. Es decir, una vez que una máquina virtual está protegida en Azure, no puede protegerse de nuevo, lo que impide la creación de puntos de recuperación duplicados para una máquina virtual. Si desea ver qué servidor Azure Backup Server está protegiendo ya a un miembro, mantenga el mouse sobre el miembro para ver el nombre del servidor de protección.

4. En la pantalla Seleccionar método de protección de datos, escriba un nombre para el grupo de protección. Se selecciona la protección a corto plazo (en el disco) y la protección en línea. Si desea utilizar la protección en línea (en Azure), debe usar la protección a corto plazo en el disco. Haga clic en **Siguiente** para continuar con la duración de protección a corto plazo.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. En la pantalla Especificar objetivos a corto plazo, para **Duración de retención**, especifique el número de días que desea retener los puntos de recuperación *almacenados en el disco*. Si quiere cambiar la hora y los días en que se usan los puntos de recuperación, haga clic en **Modificar**. Los puntos de recuperación a corto plazo son copias de seguridad completas. No son copias de seguridad incrementales. Si está satisfecho con los objetivos a corto plazo, haga clic en **Siguiente**.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. En la pantalla Revisar asignación de disco, revise el espacio en disco para las máquinas virtuales y, en caso necesario, modifíquelo. Las asignaciones de disco recomendadas se basan en la duración de retención especificada en la pantalla anterior, el tipo de carga de trabajo y el tamaño de los datos protegidos (identificados en el paso 3).  

  - Tamaño de los datos: tamaño de los datos del grupo de protección.
  - Espacio en disco: la cantidad de espacio en disco recomendada para el grupo de protección. Si desea modificar esta configuración, debe asignar un espacio total que sea ligeramente mayor que la cantidad que calcula que crecerá cada origen de datos.
  - Ubicación compartida de datos: si habilita la ubicación compartida, varios orígenes de datos de la protección pueden asignarse a una sola réplica y a un volumen de puntos de recuperación. La ubicación compartida no es compatible con todas las cargas de trabajo.
  - Crecimiento automático: en caso de que habilite esta configuración, si los datos del grupo protegido sobrepasan la asignación inicial, DPM intenta aumentar el tamaño del disco en un 25 %.
  - Detalles del bloque de almacenamiento: muestra el estado actual del bloque de almacenamiento, incluido el tamaño total y restante del disco.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

  Cuando esté satisfecho con la asignación de espacio, haga clic en **Siguiente**.

7. En la pantalla Seleccionar método de creación de réplicas, especifique cómo quiere generar la copia inicial o réplica de los datos protegidos en Azure Backup Server.

  El valor predeterminado es **Automáticamente a través de la red** y **Ahora**. Si usa el valor predeterminado, se recomienda que especifique una hora de poco tráfico. Elija **Más tarde** y especifique un día y una hora.

  Para grandes cantidades de datos o condiciones de red no del todo óptimas, considere la posibilidad de replicar los datos sin conexión con medios extraíbles.

  Una vez que haya elegido sus opciones, haga clic en **Siguiente**.

  ![Asistente para crear nuevo grupo de protección](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. En la pantalla **Opciones de comprobación de coherencia**, seleccione cómo y cuándo automatizar las comprobaciones de coherencia. Puede ejecutar comprobaciones de coherencia si los datos de réplica no son coherentes o según una programación establecida.

  Si no desea configurar la comprobación de coherencia automática, puede ejecutar una comprobación manual. En el área Protección de la consola de Azure Backup Server, haga clic con el botón derecho en el grupo de protección y seleccione **Realizar comprobación de coherencia**.

  Haga clic en **Siguiente** para ir a la pantalla siguiente.

9. En la pantalla **Especificar datos de protección en línea**, seleccione los orígenes de datos que desea proteger. Puede seleccionar los miembros individualmente o hacer clic en **Seleccionar todo** para elegir todos los miembros. Una vez elegidos los miembros, haga clic en **Siguiente**.

  ![Asistente para crear nuevo grupo de protección](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. En la pantalla **Especificar una programación de copia de seguridad en línea**, especifique la programación para generar puntos de recuperación desde la copia de seguridad de disco. Una vez generado el punto de recuperación, se transfiere al almacén de Recovery Services de Azure. Si está satisfecho con la programación de copia de seguridad en línea, haga clic en **Siguiente**.

  ![Asistente para crear nuevo grupo de protección](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. En la pantalla Especificar directivas de retención en línea, indique cuánto tiempo desea retener los datos de copia de seguridad en Azure. Una vez definidas las directivas, haga clic en **Siguiente**.

  ![Asistente para crear nuevo grupo de protección](./media/backup-azure-backup-server-vmware/retention-policy.png)

  Los datos pueden guardarse en Azure sin límite de tiempo. Cuando se almacenan datos del punto de recuperación en Azure, el único límite que existe es que no puede tener más de 9999 puntos de recuperación por instancia protegida. En este ejemplo, la instancia protegida es el servidor de VMware.

12. En la pantalla Resumen, revise los detalles para el grupo de protección. Tenga en cuenta la configuración y a los miembros del grupo. Si está satisfecho con la configuración, haga clic en **Crear grupo**.

  ![Asistente para crear nuevo grupo de protección](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Pasos siguientes
Si usa Azure Backup Server para proteger las cargas de trabajo de VMware, es posible que esté interesado en usar Azure Backup Server para proteger [Microsoft Exchange Server](./backup-azure-exchange-mabs.md), una [granja de Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md) o un servidor [SQL Server](./backup-azure-sql-mabs.md).

Consulte [Solución de problemas de Azure Backup Server](./backup-azure-mabs-troubleshoot.md) para obtener información sobre problemas con el registro del agente, la configuración del grupo de protección y los trabajos de copia de seguridad.


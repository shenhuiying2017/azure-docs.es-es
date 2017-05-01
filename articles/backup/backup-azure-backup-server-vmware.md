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
ms.date: 03/28/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 779841483e72b42725354d85a0f593aee7db8278
ms.lasthandoff: 04/11/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Realizar una copia de seguridad del servidor de VMware en Azure

En este artículo se explica cómo conectar un servidor de VMware a Azure Backup Server de modo que pueda realizar una copia de seguridad del contenido del servidor de VMware en la nube. En este artículo se da por supuesto que ya tiene instalado Azure Backup Server.

## <a name="create-secure-connection-to-vmware-server"></a>Crear una conexión segura al servidor de VMware

Para proteger un servidor de VMware, Azure Backup Server debe poder conectarse de forma segura al servidor de VMware. Para habilitar la conexión segura, instale un certificado válido en el servidor de VMware y Azure Backup Server.

Al conectarse al servidor de VMware, si la dirección URL no es segura, debe exportar el certificado para que la conexión al sitio sea segura.
![ejemplo de conexión no segura al servidor de VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

1. Haga clic en https (con el tachado) y, a continuación, en el menú emergente, haga clic en el vínculo Detalles.

  Dependiendo del explorador, puede que tenga que hacer clic en **Configuración** > **More Tools (Más herramientas)** > **Herramientas de desarrollo** y seleccionar la pestaña Seguridad.

  ![ejemplo de mensaje de error de conexión no segura](./media/backup-azure-backup-server-vmware/security-tab.png)

2. En la información sobre detalles de la pestaña Seguridad, haga clic en **Ver certificado**.

  ![ejemplo de mensaje de error de conexión no segura](./media/backup-azure-backup-server-vmware/security-tab-view-certificate.png)

  Se abre el cuadro de diálogo Certificado.

3. En el cuadro de diálogo Certificado, haga clic en la pestaña Ruta de certificación.  

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/certificate-certification-path.png)

  El certificado resaltado no es de confianza porque, en el caso de este certificado, no se pudo encontrar el emisor. Es posible que existan otros motivos por los cuales el certificado no es de confianza.

4. Para exportar el certificado a la máquina local, haga clic en la pestaña Detalles y, a continuación, en Copiar a archivo.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/certificate-details-tab.png)

  Se abre el Asistente para exportar certificados.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/certificate-wizard1.png)

  Haga clic en **Siguiente** para desplazarse por el asistente.

5. En la pantalla Formato de archivo de exportación, especifique el formato que prefiere para el certificado. Si no tiene un formato de preferencia, acepte el formato de archivo predeterminado para el certificado y haga clic en **Siguiente**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/certificate-wizard1b.png)

6. En la pantalla Archivo que se va a exportar, asigne un nombre al certificado y, a continuación, haga clic en Examinar para elegir la ubicación para almacenar el certificado en el equipo local. Guarde el certificado donde pueda encontrarlo.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/certificate-wizard2.png)

7. Después de exportar el certificado, vaya a la ubicación donde lo guardó, haga clic con el botón derecho en el certificado y, en el menú, seleccione **Instalar certificado**.

  Se abre el asistente para importación de certificados.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-import-wizard1.png)

8. En el asistente para importación de certificados, seleccione **Máquina local** como destino del certificado y haga clic en **Siguiente** para continuar.

9. En la pantalla Almacén de certificados, seleccione **Colocar todos los certificados en el siguiente almacén** y haga clic en **Examinar**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  Se abre el cuadro de diálogo Seleccionar almacén de certificados.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-store.png)

  Elija la carpeta de destino para los certificados y haga clic en **Aceptar**. Si no sabe qué carpeta usar, elija entidades de certificación raíz de confianza. La carpeta de destino elegida aparece en el cuadro de diálogo Almacén de certificados. Haga clic en **Siguiente** para importar el certificado.

10. En la pantalla Finalización del Asistente para importación de certificados, compruebe que el certificado se encuentra en la carpeta deseada y haga clic en Finalizar para completar el asistente.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  Aparece un cuadro de diálogo que permite saber si la importación se ha realizado correctamente.

11. Inicie sesión en la máquina virtual de VMware para comprobar que tiene una conexión segura al servidor de VMware. Azure Backup Server se conecta al servidor de VMware a través de un canal HTTPs seguro. Si tiene límites de seguridad en su organización y no desea habilitar el protocolo HTTPs, deshabilite la comunicación segura a través del registro. Sin embargo, se recomienda que instale certificados en Azure Backup Server y el servidor de VMware para habilitar la comunicación segura.


## <a name="create-role-and-user-account-on-vmware-server"></a>Crear rol y cuenta de usuario en el servidor de VMware

Azure Backup Server se comunica con un servidor de VMware remoto mediante la autenticación de las credenciales de un usuario de VMware especificado. Azure Backup Server autentica las credenciales del usuario de VMware para todas las operaciones de copia de seguridad. Use el asistente para adición del servidor de producción de Azure Backup Server para que sea posible una comunicación segura entre Azure Backup Server y el servidor de VMware. Hay tres fases para configurar la relación entre Azure Backup Server y 

- Crear un rol de usuario que haya asignado privilegios
- Crear una cuenta de usuario con credenciales: un nombre de usuario y una contraseña
- Agregar la cuenta de usuario del servidor de VMware a Azure Backup Server

al analizar el asistente para adición del servidor de producción. El par de nombre de usuario y contraseña se almacena como credencial.


### <a name="create-user-role-and-add-privileges"></a>Crear un rol de usuario y agregar privilegios
La cuenta de usuario de VMware, indicada en la credencial de Azure Backup Server, debe tener determinados privilegios asociados. Sin embargo, los privilegios se asocian a un rol de usuario, por lo que primero crearemos un rol de usuario y, a continuación, agregaremos privilegios específicos a dicho rol. Los privilegios asociados al rol de usuario son para un administrador de copias de seguridad.

1. Para crear un nuevo rol de usuario de VMware, inicie sesión en el servidor de VMware y, en el panel **Navegador**, haga clic en **Administración**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Para crear un nuevo rol, en la sección **Administración**, seleccione **Roles** y, en el panel **Roles**, haga clic en el icono Agregar rol (el símbolo +).

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  Se abre el cuadro de diálogo **Crear rol**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. En el cuadro de diálogo **Crear rol**, en el campo **Nombre de rol**, escriba un nombre para el rol. En este ejemplo, usaremos el nombre, *BackupAdminRole*. El nombre del rol puede ser el que desee, pero debe ser reconocible para este.

4. Seleccione los privilegios para aplicarlos al rol de usuario. Seleccione los privilegios en la siguiente lista. Una vez seleccionados, haga clic en el botón de contenido adicional de la etiqueta principal para expandir los privilegios principales y ver los privilegios secundarios. No es necesario seleccionar todos los privilegios secundarios de un privilegio principal.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  - Privilege.Datastore.AllocateSpace.label
  - Privilege.Global.ManageCustomerFields.label
  - privilege.Network.Assign.label
  - Privilege.VirtualMachine.Config.AddNewDisk.label
  - Privilege.VirtualMachine.Config.AdvanceConfig.label
  - Privilege.VirtualMachine.Config.ChangeTracking.label
  - Privilege.VirtualMachine.Config.HostUSBDevice.label
  - Privilege.VirtualMachine.Config.SwapPlacement.label  
  - Privilege.VirtualMachine.Interact.PowerOff.label
  - Privilege.VirtualMachine.Inventory.Create.label
  - Privilege.VirtualMachine.Provisioning.DiskRandomRead.summary
  - Privilege.VirtualMachine.State.CreateSnapshot.label
  - Privilege.VirtualMachine.State.RemoveSnapshot.label
</br>

  Una vez que haya seleccionado los privilegios, haga clic en **Aceptar**. El nuevo rol aparece en la lista del panel Roles.

### <a name="create-a-user-account-and-assign-permissions"></a>Crear una cuenta de usuario y asignar permisos

Una vez que haya definido el rol de usuario con privilegios, cree una cuenta de usuario. Cuando crea la cuenta de usuario, la asigna a un rol de usuario específico, que proporciona a la cuenta los privilegios asociados. La cuenta de usuario tiene un nombre y una contraseña, que proporciona las credenciales usadas para la autenticación.

1. Para crear una nueva cuenta de usuario, en el servidor de VMware, en el panel Navegador, haga clic en **Usuarios y grupos**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  Aparece el panel Usuarios y grupos.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. En el panel Usuarios y grupos de VMware, en la pestaña Usuarios, haga clic en el icono Agregar usuarios (el símbolo +).

  Se abre el cuadro de diálogo Nuevo usuario.

3. La cuenta de usuario creada contiene el par de nombre de usuario y contraseña utilizado como credenciales. En el cuadro de diálogo Nuevo usuario, rellene los campos y haga clic en **Aceptar**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  La nueva cuenta de usuario aparece en la lista.

4. Ahora que ha creado la cuenta de usuario, asóciela con el rol de usuario (que tiene los permisos deseados). En el panel Navegador, haga clic en **Permisos globales**. En el panel Permisos globales, haga clic en la pestaña **Administrar** y, a continuación, haga clic en el icono Agregar (el símbolo +).

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  Se abre el cuadro de diálogo Global Permission Root - Add Permission (Raíz de permisos globales: agregar permiso).

5. En el cuadro de diálogo **Global Permission Root - Add Permission** (Raíz de permisos globales: agregar permiso), haga clic en **Agregar** para elegir el usuario o grupo.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  Se abre el cuadro de diálogo Seleccionar usuarios y grupos.

6. En el cuadro de diálogo **Seleccionar usuarios y grupos**, seleccione la cuenta de usuario que ha creado y haga clic en **Agregar**. La cuenta de usuario seleccionada aparece en el campo Usuarios. El nombre de usuario aparece en el campo Usuarios con el formato *dominio*`\`*nombre de usuario*.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  Haga clic en **Aceptar** para agregar los usuarios seleccionados al cuadro de diálogo Agregar permiso.

7. Ahora que ha identificado al usuario, asígnelo al rol. En el área Rol asignado, en el menú desplegable, seleccione el rol y haga clic en **Aceptar**.

  ![cuadro de diálogo Certificado con error ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  En la pestaña Administrar de Permisos globales, la nueva cuenta de usuario y el rol asociado aparecen en la lista.


### <a name="add-the-vmware-user-account-credentials-to-azure-backup-server"></a>Agregar las credenciales de la cuenta de usuario de VMware a Azure Backup Server

Antes de agregar el servidor de VMware a Azure Backup Server, asegúrese de haber instalado [Update 1 para Microsoft Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Haga clic en el siguiente icono (ubicado en el escritorio del servidor) para abrir la consola de Azure Backup Server.

  ![Icono de Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  Si no encuentra el icono en el escritorio, puede abrir Azure Backup Server en la lista de aplicaciones instaladas. En ella, la aplicación Azure Backup Server se denomina Microsoft Azure Backup.

2. En la consola de Azure Backup Server, haga clic en **Administración** y en **Servidores de producción** y, a continuación, en la cinta de opciones de herramientas, haga clic en **Administrar VMware**.

  ![Consola de MABS](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  Se abre el cuadro de diálogo Administración de credenciales.

  ![Cuadro de diálogo Administración de credenciales de MABS](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. En el cuadro de diálogo Administración de credenciales, haga clic en **Agregar** para abrir el cuadro de diálogo Agregar credenciales.

4. En el cuadro de diálogo Agregar credenciales, escriba un nombre y una descripción para la nueva credencial. El nombre de usuario y la contraseña deben ser los mismos que se usaron al crear la cuenta de usuario en el servidor de VMware.

  ![Cuadro de diálogo Administración de credenciales de MABS](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog.png)

  Haga clic en **Agregar** para agregar la nueva credencial a Azure Backup Server. La nueva credencial aparece en la lista del cuadro de diálogo Administración de credenciales.
  ![Cuadro de diálogo Administración de credenciales de MABS](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Haga clic en la **X** de la esquina superior derecha para cerrar el cuadro de diálogo Administración de credenciales.


## <a name="add-vmware-server-to-azure-backup-server"></a>Agregar el servidor de VMware a Azure Backup Server

para abrir el asistente para adición del servidor de producción

1. En la consola de Azure Backup Server, haga clic en **Administración**, en **Servidor de producción** y, a continuación, en **Agregar**.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  Se abre el asistente para adición del servidor de producción.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. En la pantalla Seleccionar tipo de servidor de producción, seleccione los servidores de VMware y haga clic en **Siguiente**.

3. En el nombre del servidor o dirección IP, especifique el nombre de dominio completo (FQDN) o la dirección IP del servidor de VMware. Puede escribir el nombre de VMware si el mismo servidor vCenter administra todos los servidores ESXi.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. En el cuadro de diálogo **Puerto SSL**, escriba el puerto usado para comunicarse con el servidor de VMware. Utilice el puerto 443, que es el puerto predeterminado, a menos que sepa que es necesario otro distinto.

5. En el cuadro de diálogo **Especificar credenciales**, puede crear nuevas credenciales, o bien seleccionar credenciales existentes. En la sección anterior, ha creado una credencial. Seleccione esta credencial.

  Si no hay ninguna credencial disponible o tiene que crear una nueva, haga clic en **Agregar nueva credencial**, cree la nueva credencial y haga clic en **Aceptar**.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/specify-new-cred.png)

6. Haga clic en **Agregar** para agregar el servidor de VMware a la lista de **Servidores VMware agregados** y en **Siguiente para ir a la pantalla siguiente del asistente.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. En la pantalla **Tareas**, haga clic en **Agregar** para agregar el servidor de VMware especificado a Azure Backup Server.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Como la copia de seguridad del servidor de VMware es una copia de seguridad sin agente, la adición del nuevo servidor tiene lugar en cuestión de segundos. Puede agregar varios servidores de VMware a Azure Backup Server repitiendo los pasos anteriores en esta sección.

Ahora que ha agregado un servidor de VMware a Azure Backup Server, el próximo paso es crear un grupo de protección. El grupo de protección especifica los diversos detalles de la retención a corto o largo plazo. Aquí se definen y aplican las directivas de copia de seguridad. Las directivas de copia de seguridad son la programación de cuándo se realizan las copias de seguridad y qué se incluye en la copia de seguridad.


## <a name="configure-a-protection-group-to-back-up-vmware-server"></a>Configurar un grupo de protección para realizar una copia de seguridad del servidor de VMware

Si nunca ha usado System Center Data Protection Manager ni Azure Backup Server, consulte el tema, [Planificación de copias de seguridad en disco](https://technet.microsoft.com/library/hh758026.aspx), para preparar el entorno del hardware. Una vez que haya comprobado que tiene un almacenamiento adecuado, use el asistente para crear nuevo grupo de protección para agregar las máquinas virtuales específicas.

1. En la consola de Azure Backup Server, haga clic en **Protección** y, en la cinta de opciones de herramientas, haga clic en **Nuevo** para abrir el asistente para crear nuevo grupo de protección.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  Se abre el asistente para crear nuevo grupo de protección.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  Haga clic en **Siguiente** para avanzar a la pantalla **Seleccionar tipo de grupo de protección**.

2. En la pantalla Seleccionar tipo de grupo de protección, seleccione **Servidores** y haga clic en **Siguiente**.

3. En la pantalla Seleccionar a miembros del grupo, puede ver los miembros disponibles y aquellos que se han seleccionado. Seleccione los miembros a los que desea proteger y haga clic en **Siguiente**.

  ![Asistente para adición del servidor de producción](./media/backup-azure-backup-server-vmware/server-add-to-selected-members.png)

  Al seleccionar un miembro, si selecciona una carpeta que contiene otras carpetas o máquinas virtuales, estas también se seleccionarán. La inclusión de las carpetas y máquinas virtuales en la carpeta principal se denomina protección de nivel de carpeta. Puede excluir cualquier carpeta o máquina virtual anulando la selección de la casilla.

  Si una máquina virtual, o bien una carpeta que contiene una máquina virtual, ya está protegida en Azure, no podrá volver a seleccionar dicha máquina virtual. Es decir, una vez que una máquina virtual está protegida en Azure, no puede protegerse de nuevo, lo que impide la creación de puntos de recuperación duplicados para una máquina virtual. Si desea ver qué servidor Azure Backup Server está protegiendo ya a un miembro, mantenga el mouse sobre el miembro para ver el nombre del servidor de protección.

4. En la pantalla Seleccionar método de protección de datos, escriba un nombre para el grupo de protección. A continuación, para **Método de protección**, seleccione dónde quiere realizar una copia de seguridad de los datos. Se realiza una copia de seguridad de los datos en el disco para lograr una protección a corto plazo. Se realiza una copia de seguridad de los datos en la Nube (Azure) para lograr una protección a largo plazo. Haga clic en **Siguiente** para continuar con la duración de protección a corto plazo.

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

  Cuando haya terminado, haga clic en **Siguiente**.

7. En la pantalla Seleccionar método de creación de réplicas, especifique cómo quiere generar la copia inicial o réplica de los datos protegidos en Azure Backup Server.

  El valor predeterminado es **Automáticamente a través de la red** y **Ahora**. Si usa el valor predeterminado, se recomienda que especifique una hora de poco tráfico. Elija **Más tarde** y especifique un día y una hora.

  Para grandes cantidades de datos o condiciones de red no del todo óptimas, considere la posibilidad de replicar los datos sin conexión con medios extraíbles.

  Una vez que haya elegido sus opciones, haga clic en **Siguiente**.

  ![Asistente para crear nuevo grupo de protección](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. En la pantalla **Opciones de comprobación de coherencia**, seleccione cómo y cuándo automatizar las comprobaciones de coherencia. Puede ejecutar comprobaciones de coherencia si los datos de réplica no son coherentes o según una programación establecida.

  Si no desea configurar una comprobación de coherencia automática, puede ejecutar una comprobación manual en cualquier momento haciendo clic con el botón derecho en el grupo de protección del área Protección de la consola de Azure Backup Server y seleccionando Realizar comprobación de coherencia.

  Haga clic en **Siguiente** para ir a la pantalla siguiente.

9. En la pantalla **Especificar datos de protección en línea**, seleccione los orígenes de datos que desea proteger. Puede seleccionar los miembros individualmente o hacer clic en **Seleccionar todo** para elegir todos los miembros. Una vez elegidos los miembros, haga clic en **Siguiente*.

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


---
title: "Solución de problemas de Azure Site Recovery de VMware a Azure | Microsoft Docs"
description: "Solución de problemas y errores al replicar máquinas virtuales de Azure"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d4d6d273001642ebb8c960333a1c01977e863dbc
ms.contentlocale: es-es
ms.lasthandoff: 09/07/2017

---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Solución de problemas de instalación de inserción de Mobility Service

Este artículo describe problemas comunes a los que puede enfrentarse al intentar instalar Mobility Service de Azure Site Recovery en el servidor de origen para habilitar la protección.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(Código de error 95107) No se pudo habilitar la protección
**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95107 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0858**. <br> Las credenciales proporcionadas para instalar Mobility Service son incorrectas o la cuenta de usuario no tiene privilegios suficientes. | Las credenciales de usuario que se proporcionaron para instalar Mobility Service en la máquina de origen no son correctas. | Asegúrese de que las credenciales de usuario proporcionadas para la máquina de origen en el servidor de configuración son correctas. <br> Para agregar o modificar las credenciales del usuario, vaya al servidor de configuración y seleccione **Cspsconfigtool** > **Administrar cuenta**. </br> Además, compruebe los siguientes requisitos previos para finalizar correctamente la instalación de inserción.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(Código de error 95015) No se pudo habilitar la protección
**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95105 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0856**. <br> La opción **Compartir archivos e impresoras** no se permite en la máquina de origen o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen.| **Compartir archivos e impresoras** no está habilitado. | Permita **Compartir archivos e impresoras** en el equipo de origen en el firewall de Windows. En el equipo de origen, en **Firewall de Windows** > **Permitir una aplicación o una característica a través de Firewall**, seleccione **Compartir archivos e impresoras para todos los perfiles**. </br> Además, compruebe los siguientes requisitos previos para finalizar correctamente la instalación de inserción.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(Código de error 95117) No se pudo habilitar la protección
**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95117 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0865**. <br> La máquina de origen no está en ejecución o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen. | Problemas de conectividad de red entre el servidor de procesos y el servidor de origen. | Compruebe la conectividad entre el servidor de procesos y el servidor de origen. </br> Además, compruebe los siguientes requisitos previos para finalizar correctamente la instalación de inserción.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(Código de error 95103) No se pudo habilitar la protección
**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95103 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0854**. <br> La opción Instrumental de administración de Windows (WMI) no se permite en la máquina de origen o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen.| WMI está bloqueado en Firewall de Windows. | Permita WMI en Firewall de Windows. En **Firewall de Windows** > **Permitir una aplicación o una característica a través del Firewall**, seleccione **WMI para todos los perfiles**. </br> Además, compruebe los siguientes requisitos previos para finalizar correctamente la instalación de inserción.

## <a name="check-push-installation-logs-for-errors"></a>Búsqueda de errores en los registros de la instalación de inserción

En el servidor de configuración o procesos, vaya al archivo PushinstallService que se encuentra en <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ para obtener información sobre el origen del problema. Utilice los siguientes pasos de solución de problemas para resolver el problema.</br>

![Registros de la instalación de inserción](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-installation-prerequisites-for-windows"></a>Requisitos previos de la instalación de inserción para Windows
### <a name="ensure-that-file-and-printer-sharing-is-enabled"></a>Asegúrese de que **Compartir archivos e impresoras** está habilitado
Permita **Compartir archivos e impresoras** e **Instrumental de administración de Windows** en la máquina de origen en el Firewall de Windows. </br>
#### <a name="if-the-source-machine-is-domain-joined-br"></a>Si la máquina de origen está unida a un dominio </br>
Configure el firewall mediante la Consola de administración de directivas de grupo.

1. Inicie sesión como administrador en el equipo de dominio de Azure Active Directory. Abra la Consola de administración de directivas de grupo (GPMC. MSC, ejecute desde **Inicio** > **Ejecutar**).</br>

2. Si no está instalada la Consola de administración de directivas de grupo, consulte [Instalación de la Consola de administración de directivas de grupo](https://technet.microsoft.com/library/cc725932.aspx). </br>

3. En el árbol de la Consola de administración de directivas de grupo, haga doble clic en **Objetos de directiva de grupo** del bosque y el dominio. Vaya a **Directiva de dominio predeterminada**. </br>

    ![Directiva de dominio predeterminada](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
4. Haga clic con el botón derecho en **Directiva de dominio predeterminada** > **Editar**. Se abre una nueva ventana del **Editor de administración de directivas de grupo**. </br>

    ![Editor de administración de directivas de grupo](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
5. En el **Editor de administración de directivas de grupo**, vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Red** > **Conexiones de red** > **Firewall de Windows**. </br>

    ![Firewall de Windows](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
6. Habilite las opciones siguientes para **Perfil de dominio** y **Perfil estándar**: </br>

    a. Haga doble clic en **Firewall de Windows: permitir excepción de compartir impresoras y archivos entrante**. Seleccione **Habilitado** y seleccione **Aceptar**. </br>

    b. Haga doble clic en **Firewall de Windows: permitir excepción de administración remota entrante**. Seleccione **Habilitado** y seleccione **Aceptar**. </br>

    ![Perfil de dominio](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

#### <a name="if-the-source-machine-isnt-domain-joined-and-part-of-a-workgroup-br"></a>Si la máquina de origen no está unida a un dominio ni es parte de un grupo de trabajo </br>
Configure el firewall en la máquina remota (para el grupo de trabajo).

1. Vaya a la máquina de origen.</br>

2. En **Firewall de Windows** > **Permitir una aplicación o una característica a través del Firewall**, seleccione **Compartir archivos e impresoras** para todos los perfiles. </br>

3. En **Firewall de Windows** > **Permitir una aplicación o una característica a través del Firewall**, seleccione **WMI** para todos los perfiles. </br>

#### <a name="disable-remote-user-account-control"></a>Deshabilitación del Control de cuentas de usuario remoto
Deshabilite el Control de cuentas de usuario mediante el uso de la clave del registro para insertar Mobility Service.

1. Seleccione **Inicio** > **Ejecutar**, escriba *regedit* y seleccione **ENTRAR**.

2. Localice y seleccione la subclave del registro siguiente: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System

3. Si la entrada del registro LocalAccountTokenFilterPolicy no existe, siga estos pasos:

    a. En el menú **Editar**, seleccione **Nuevo** y, a continuación, seleccione **Valor DWORD**.

    b. Escriba *LocalAccountTokenFilterPolicy* y, a continuación, seleccione **ENTRAR**.

    c. Haga clic en **LocalAccountTokenFilterPolicy** y, a continuación, seleccione **Modificar**.

    d. En el cuadro de datos **Valor**, escriba *1* y, a continuación, seleccione **Aceptar**.

4. Cierre el editor del registro.


## <a name="push-installation-prerequisites-for-linux"></a>Requisitos previos de la instalación de inserción para Linux

1. Cree un usuario raíz en el servidor Linux de origen (use esta cuenta solo para la instalación de inserción y las actualizaciones.)</br>

2. Compruebe que el archivo /etc/hosts del servidor Linux de origen tiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red. </br>

3. Asegúrese de que los paquetes openssh, openssh-server y openssl más recientes estén instalados en el servidor Linux de origen. Compruebe si el puerto SSH 22 está habilitado y en ejecución. </br>

4. Compruebe si ya hay entradas obsoletas de los agentes en el servidor de origen, desinstale los agentes anteriores y reinicie el servidor. Vuelva a instalar al agente. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>Habilitación de la autenticación de la contraseña y el subsistema SFTP en el archivo sshd_config
1. En el servidor de origen, inicie sesión como root. </br>

2. En el archivo /etc/ssh/sshd_config, encuentre la línea que comienza con "PasswordAuthentication." </br>

3. Quite la marca de comentario de la línea y cambie el valor de "no" a "yes". </br>

    ![PasswordAuthentication](./media/site-recovery-protection-common-errors/linux1.png)

4. Busque la línea que comienza por "Subsystem" y quite la marca de comentario. </br>

    ![Subsystem](./media/site-recovery-protection-common-errors/linux2.png)

5. Guarde los cambios y reinicie el servicio sshd. </br>


## <a name="push-installation-checks-on-the-configurationprocess-server"></a>Comprobaciones de la instalación de inserción en el servidor de configuración o procesos
#### <a name="validate-credentials-for-discovery-and-installation"></a>Validación de las credenciales para la detección y la instalación

1. En el servidor de configuración, inicie Cspsconfigtool.</br>

    ![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. Asegúrese de que la cuenta que se usa para protección tiene derechos de administrador en la máquina de origen. </br>

#### <a name="check-connectivity-between-the-process-server-and-the-source-server"></a>Comprobación de la conectividad entre el servidor de procesos y el servidor de origen
1. Asegúrese de que el servidor de procesos tiene conexión a Internet.

2. Use wbemtest.exe para comprobar la conexión de WMI. </br>

    a. En el servidor de procesos, seleccione **Inicio** > **Ejecutar** > **wbemtest.exe**. Se abre la ventana **Herramienta de comprobación del Instrumental de administración de Windows** tal como se muestra:</br>

      ![Herramienta de comprobación WMI](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
    b. Seleccione **Conectar** y escriba la dirección IP del servidor de origen en **Espacio de nombres**.

    c. Escriba el nombre de **Usuario** y la **Contraseña**. (Si la máquina de origen está unida a un dominio, proporcione el nombre de dominio junto con el nombre de usuario, como *NombreDominio\NombreUsuario*. Si la máquina de origen está en un grupo de trabajo, solo debe escribir el nombre de usuario.)

    d. Seleccione el nivel de **Autenticación** como **Privacidad de paquete**. </br>

      ![Privacidad de paquete](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
    e. Seleccione **Conectar**. La conexión de WMI ahora muestra los datos proporcionados y aparece la ventana **Herramienta de comprobación del Instrumental de administración de Windows** como se muestra a continuación: </br>

      ![Datos de la Herramienta de comprobación WMI](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
   </br>
      Si la conexión de WMI no se realiza correctamente, aparece un mensaje de error. En la captura de pantalla siguiente se muestra un intento incorrecto si **WMI/Administración remota** no está habilitado en las aplicaciones permitidas en el Firewall de Windows: </br>

    ![Mensaje de error de la Herramienta de comprobación de WMI](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
   </br>

3. Compruebe el estado y la conectividad de WMI.</br>

    a. En el servidor de proceso o de configuración, seleccione **Inicio** > **Ejecutar** > **wmimgmt.msc** > **Acciones** >  **Más acciones** > **Conectarse a otro equipo (máquina de origen)**. </br>

    b. Escriba las credenciales de la cuenta que se usa para la protección y compruebe si la conectividad funciona correctamente. </br>

#### <a name="verify-that-network-shared-folders-of-the-source-machine-are-accessible-from-the-process-server-remotely-by-using-specified-credentials"></a>Compruebe si las carpetas compartidas en la red de la máquina de origen son accesibles desde el servidor de procesos de forma remota con las credenciales especificadas

  1. Inicie sesión en el equipo del servidor de proceso y abra el Explorador de archivos. En la barra de direcciones, escriba *"\\\dirección-IP-de-la-máquina-de-origen\C$"*. Seleccione **Entrar**. </br>

      ![Explorador de archivos](./media/site-recovery-protection-common-errors/fileshare1.png) </br>

  2. El Explorador de archivos pedirá las credenciales. Escriba el nombre de usuario y la contraseña y seleccione **Aceptar**.</br>

      * Si la máquina de origen está unida a un dominio, proporcione el nombre de dominio junto con el nombre de usuario, como *NombreDominio\NombreUsuario*.</br>

      * Si la máquina de origen está en un grupo de trabajo, solo debe escribir el nombre de usuario. </br>

      ![Cuadro de diálogo Credenciales](./media/site-recovery-protection-common-errors/fileshare2.png) </br>

  3. Si la conexión se establece correctamente, puede ver las carpetas de la máquina de origen de manera remota desde el servidor de procesos. </br>

      ![Vista de carpetas](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> Si la conexión no se establece correctamente, compruebe si se cumplen todos los requisitos previos.
>

Si no desea abrir **Instrumental de administración de Windows**, también puede instalar Mobility Service manualmente en la máquina de origen.</br> 

Para más información, consulte [Instalación manual de Mobility Service a través de la interfaz gráfica de usuario](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) y la [Guía de instalación a través del administrador de configuración](site-recovery-install-mobility-service-using-sccm.md). </br>

## <a name="next-steps"></a>Pasos siguientes
- [Habilitación de la replicación de máquinas virtuales VMware](vmware-walkthrough-enable-replication.md)


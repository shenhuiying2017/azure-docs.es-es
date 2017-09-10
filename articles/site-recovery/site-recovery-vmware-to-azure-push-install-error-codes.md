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
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1e2452ccc6d3f1859a39e1ee09cdde32f53767ba
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---
# <a name="troubleshooting-mobility-service-push-install-issues"></a>Solución de problemas de instalación de inserción del servicio de movilidad

En este artículo se detallan los problemas comunes que se enfrentan al intentar instalar el servicio de movilidad en el servidor de origen para habilitar la protección.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(Código de error 95107) No se pudo habilitar la protección.
**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95107 </br>***Mensaje: ***Error en la instalación de inserción del servicio de movilidad en la máquina de origen con el código de error ***EP0858***. <br> Las credenciales proporcionadas para instalar el servicio de movilidad son incorrectas o la cuenta de usuario no tiene privilegios suficientes | Las credenciales de usuario que se proporcionaron para instalar el servicio de movilidad en la máquina de origen no son correctas | Asegúrese de que las credenciales de usuario proporcionadas para la máquina de origen en el servidor de configuración son correctas. <br> Para agregar o editar credenciales de usuario: vaya al servidor de configuración > icono Cspsconfigtool > Administrar cuenta. </br> Además, asegúrese de comprobar los requisitos previos siguientes para completar correctamente la instalación de inserción.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(Código de error 95015) No se pudo habilitar la protección.
**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95105 </br>***Mensaje: ***Error en la instalación de inserción del servicio de movilidad en la máquina de origen con el código de error ***EP0856***. <br> La opción "Compartir archivos e impresoras" no se permite en la máquina de origen o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen| Compartir archivos e impresoras no está habilitado | Permita la opción "Compartir archivos e impresoras" en la máquina de origen en el Firewall de Windows. Vaya a la máquina de origen > en la configuración Firewall de Windows > "Permitir una aplicación o una características a través de Firewall" > seleccione "Compartir archivos e impresoras para todos los perfiles". </br> Además, asegúrese de comprobar los requisitos previos siguientes para completar correctamente la instalación de inserción.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(Código de error 95117) No se pudo habilitar la protección.
**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95117 </br>***Mensaje: ***Error en la instalación de inserción del servicio de movilidad en la máquina de origen con el código de error ***EP0865***. <br> La máquina de origen no está en ejecución o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen | Conectividad de red entre el servidor de procesos y el servidor de origen | Compruebe la conectividad entre el servidor de procesos y el servidor de origen. </br> Además, asegúrese de comprobar los requisitos previos siguientes para completar correctamente la instalación de inserción.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(Código de error 95103) No se pudo habilitar la protección.
**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95103 </br>***Mensaje: ***Error en la instalación de inserción del servicio de movilidad en la máquina de origen con el código de error ***EP0854***. <br> La opción "Instrumental de administración de Windows (WMI)" no se permite en la máquina de origen o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen| Instrumental de administración de Windows (WMI) bloqueado en Firewall de Windows | Permita Instrumental de administración de Windows (WMI) en el Firewall de Windows. En la configuración de Firewall de Windows > "Permitir una aplicación o una característica a través de Firewall" > seleccione VMI para todos los perfiles. </br> Además, asegúrese de comprobar los requisitos previos siguientes para completar correctamente la instalación de inserción.

## <a name="check-push-install-logs-for-errors"></a>Busque errores en los registros de la instalación de inserción

En el servidor de configuración o procesos, vaya al archivo "PushinstallService" que se encuentra en <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ para entender el origen del problema y use los pasos de solución de problemas siguiente para resolverlo.</br>
![pushiinstalllogs](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-install-pre-requisites-for-windows"></a>Requisitos previos de instalación de inserción para Windows
### <a name="ensure-file-and-printer-sharing-is-enabled"></a>Asegúrese de que "Compartir archivos e impresoras" esté habilitado
Permita "Compartir archivos e impresoras" y "Instrumental de administración de Windows" en la máquina de origen en el Firewall de Windows </br>
#### <a name="if-source-machine-is-domain-joined-br"></a>Si la máquina de origen está unida a un dominio: </br>
Configure el firewall mediante la Consola de administración de directivas de grupo (GPMC).
1. Inicie sesión en la máquina del dominio de Active Directory como administrador y abra la Consola de administración de directivas de grupo (GPMC.MSC, ejecútelo desde Inicio > Ejecutar).</br>
3. Si la GPMC no está instalada, siga el vínculo para [instalarla](https://technet.microsoft.com/library/cc725932.aspx) </br>
4. En el árbol de la consola GPMC, haga doble clic en los objetos de directiva de grupo del bosque y el dominio y vaya a "Directiva de dominio predeterminada". </br>
![gpmc1](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
5. Haga clic con el botón derecho en "Directiva de dominio predeterminada" > Editar > se abrirá una ventana nueva del "Editor de administración de directivas de grupo". </br>
![gpmc2](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
6. En el Editor de administración de directivas de grupo, vaya a Configuración del equipo > Directivas > Plantillas administrativas > Red > Conexiones de red > Firewall de Windows. </br>
![gpmc3](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
7. Habilite la configuración siguiente para el perfil de dominio y el perfil estándar </br>
a) Haga doble clic en la excepción "Firewall de Windows: permitir excepción Compartir archivos e impresoras entrantes". Seleccione Habilitado y haga clic en Aceptar. </br>
b) Haga doble clic en la excepción "Firewall de Windows: permitir excepción de administración remota entrante". Seleccione Habilitado y haga clic en Aceptar. </br>
![gpmc4](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

###### <a name="if-source-machine-is-not-domain-joined-and-part-of-workgroup-br"></a>Si la máquina de origen no está unida a un dominio ni es parte de un grupo de trabajo </br>
Configure el firewall en la máquina remota (para el grupo de trabajo):
1. Vaya a la máquina de origen.</br>
2. En la configuración de Firewall de Windows > "Permitir una aplicación o una característica a través de Firewall" > seleccione "Compartir archivos e impresoras para todos los perfiles". </br>
3. En la configuración de Firewall de Windows > "Permitir una aplicación o una característica a través de Firewall" > seleccione VMI para todos los perfiles. </br>

#### <a name="disable-remote-user-account-control-uac"></a>Deshabilitación del Control de cuentas de usuario (UAC)
Deshabilite UAC con la clave del Registro para insertar el servicio de movilidad.
1. Haga clic en Inicio > Ejecutar > escriba regedit > Entrar
2. Localice y haga clic en la subclave del Registro siguiente: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. Si la entrada del Registro LocalAccountTokenFilterPolicy no existe, siga estos pasos:
4. En el menú Edición > Nuevo > haga clic en Valor DWORD.
5. Escriba LocalAccountTokenFilterPolicy y presione Entrar.
6. Haga clic con el botón derecho en LocalAccountTokenFilterPolicy y, luego, haga clic en Modificar.
7. En el cuadro de datos Valor, escriba 1 y haga clic en Aceptar.
8. Salga del Editor del Registro.


## <a name="push-install-pre-requisites-for-linux"></a>Requisitos previos de instalación de inserción para Linux:

1. Cree un usuario raíz en el servidor Linux de origen (esta cuenta solo se usa para la instalación de inserción y las actualizaciones).</br>
2. Compruebe que el archivo /etc/hosts del servidor Linux de origen tiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red. </br>
3. Asegúrese de que los paquetes openssh, openssh-server y openssl más recientes estén instalados en el servidor Linux de origen. </br>
Compruebe si el puerto SSH 22 está habilitado y en ejecución. </br>
4. Compruebe si ya hay entradas obsoletas de los agentes en el servidor de origen, desinstale los agentes anteriores y reinicie el servidor para reinstalar el agente. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>Habilitación de la autenticación de la contraseña y el subsistema SFTP en el archivo sshd_config
1. En el servidor de origen, inicie sesión como raíz. </br>
2. En el archivo /etc/ssh/sshd_config,</br> encuentre la línea que comienza con PasswordAuthentication. </br>
3. d.   Quite la marca de comentario de la línea y cambie el valor de "no" a "yes". </br>
![Linux1](./media/site-recovery-protection-common-errors/linux1.png)
4. Busque la línea que comienza por "Subsystem" y quite la marca de comentario. </br>
![Linux2](./media/site-recovery-protection-common-errors/linux2.png)
5. Guarde los cambios y reinicie el servicio SSHD. </br>

## <a name="push-installation-checks-on-configurationprocess-server"></a>Comprobaciones de la instalación de inserción en el servidor de configuración o procesos
#### <a name="validate-credentials-for-discovery-and-installation"></a>Validación de las credenciales para la detección y la instalación

1. En el servidor de configuración, inicie Cspsconfigtool</br>
![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. Asegúrese de que la cuenta que se usa para protección tiene derechos de administrador en la máquina de origen. </br>

#### <a name="check-connectivity-between-process-server-and-source-server"></a>Comprobación de la conectividad entre el servidor de procesos y el servidor de origen
1. Asegúrese de que el servidor de procesos tiene conexión a Internet.
2. Use wbemtest.exe para comprobar la conexión de WMI. </br>
En el servidor de procesos, haga clic en Inicio > Ejecutar > wbemtest.exe > la ventana Herramienta de comprobación del instrumental de administración de Windows se abrirá como se muestra a continuación.</br>
   ![WMItestConnect1](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
Haga clic en Conectar > escriba la dirección IP del servidor de origen en el espacio de nombres, escriba el nombre de usuario y la contraseña (si la máquina de origen está unida a un dominio, escriba el nombre de dominio junto con el nombre de usuario, como "NombreDominio\NombreUsuario". Si la máquina de origen está en un grupo de trabajo, solo debe escribir el nombre de usuario). Seleccione el nivel de autenticación como Privacidad de paquete. </br>
![WMItestConnect2](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
   Haga clic en Conectar. La conexión de WMI ahora debería establecerse correctamente con los datos proporcionados y la ventana Herramienta de comprobación del instrumental de administración de Windows debería aparecer como se muestra a continuación: </br>
   ![WMItestConnect3](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
</br>
   Si la conexión de WMI no se establece correctamente, aparecerá un mensaje de error emergente. En la captura de pantalla siguiente se muestra un intento incorrecto si WMI/administración remota no está habilitado en las aplicaciones permitidas en Firewall de Windows. </br>
   ![WMItestConnect4](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
</br>

3. Compruebe el estado y la conectividad de WMI.</br>
En el servidor de configuración o procesos, </br>
haga clic en Inicio > Ejecutar > wmimgmt.msc > Acciones > Más acciones > Conectarse a otro equipo (máquina de origen). </br>
Escriba las credenciales de la cuenta que se usa para la protección y compruebe si la conectividad funciona correctamente. </br>

#### <a name="verify-network-shared-folders-of-source-machine-is-accessible-from-process-server-ps-remotely-using-specified-credentials"></a>Compruebe si las carpetas compartidas en la red de la máquina de origen son accesibles desde el servidor de procesos (PS) de forma remota con credenciales especificadas.
  1. Inicie sesión en la máquina del servidor de procesos, abra el Explorador de archivos > en la barra de direcciones, escriba > "\\\source-machine-ip\C$" > haga clic en Entrar. </br>
  ![Fileshare1](./media/site-recovery-protection-common-errors/fileshare1.png) </br>
  2. El Explorador de archivos pedirá las credenciales. Escriba el nombre de usuario y la contraseña > haga clic en Aceptar.</br>
   Si la máquina de origen está unida a un dominio, proporcione el nombre de dominio junto con el nombre de usuario, como "NombreDominio\NombreUsuario".</br>
   Si la máquina de origen está en un grupo de trabajo, solo debe escribir el nombre de usuario. </br>
  ![Fileshare2](./media/site-recovery-protection-common-errors/fileshare2.png) </br>
  3. Si la conexión se establece correctamente, puede ver las carpetas de la máquina de origen de manera remota desde el servidor de procesos </br>
  ![Fileshare3](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> Si la conexión no se establece correctamente, compruebe si se cumplen todos los requisitos previos.
>

Si no desea abrir "Instrumental de administración de Windows", también puede instalar manualmente el servicio de movilidad en la máquina de origen.</br> [Instalación manual del servicio de movilidad mediante la GUI](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) </br>
[Instalación a través del administrador de configuración](site-recovery-install-mobility-service-using-sccm.md) </br>

## <a name="next-steps"></a>Pasos siguientes
- [Habilitación de la replicación de máquinas virtuales VMware](vmware-walkthrough-enable-replication.md)


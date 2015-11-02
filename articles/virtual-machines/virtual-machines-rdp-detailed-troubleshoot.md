<properties
	pageTitle="Solución de problemas detallada de Escritorio remoto | Microsoft Azure"
	description="Pasos de solución de problemas detallada para las conexiones de RDP a una máquina virtual de Azure con Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# Solución de problemas detallada de conexiones de Escritorio remoto a máquinas virtuales de Azure basadas en Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

En este artículo se proporcionan pasos de solución de problemas detallada de errores complejos de Escritorio remoto.

> [AZURE.IMPORTANT]Para eliminar los errores más comunes de Escritorio remoto, asegúrese de leer [la solución de problemas básicos de Escritorio remoto](virtual-machines-troubleshoot-remote-desktop-connections.md) antes de continuar.

## Póngase en contacto con el servicio de atención al cliente de Azure

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](http://azure.microsoft.com/support/forums/).

Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Mensaje de error genérico de Escritorio remoto

A veces puede que vea este mensaje de error en la ventana de mensajes de conexión a Escritorio remoto: _El Escritorio remoto no se puede conectar al equipo remoto por una de estas razones..._.

Este error se produce cuando el cliente de Escritorio remoto no puede ponerse en contacto con el servicio Servicios de Escritorio remoto en la máquina virtual. Puede haber varias razones para este error.

Presentamos a continuación los componentes implicados.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_0.png)

Antes de iniciar un proceso paso a paso de solución de problemas, es útil repasar mentalmente qué cambió desde que creó las conexiones a Escritorio remoto y tomar ese cambio como base para intentar corregir el problema. Por ejemplo:

- Si pudo crear conexiones a Escritorio remoto y cambió la dirección IP pública de la máquina virtual o del servicio en la nube que contiene la máquina virtual (también denominada dirección IP virtual [VIP]), la memoria caché del cliente DNS podría tener una entrada para el nombre DNS y la *dirección IP antigua*. Vacíe la caché del cliente DNS y vuelva a intentarlo. También puede intentar establecer la conexión usando la VIP nueva.
- Si pasó de usar el Portal de Azure o el Portal de vista previa de Azure a usar una aplicación para administrar las conexiones a Escritorio remoto, asegúrese de que la configuración de la aplicación incluye el puerto TCP determinado al azar para el tráfico de Escritorio remoto.

Las secciones siguientes indican los pasos para aislar y determinar las distintas causas de este problema y proporcionan soluciones y alternativas.


## Pasos previos

Realice estos pasos antes de continuar con la solución de problemas detallada.

- Comprobar el estado de la máquina virtual en el Portal de Azure o el Portal de vista previa de Azure
- Reiniciar la máquina virtual
- [Cambiar el tamaño de la máquina virtual](virtual-machines-size-specs.md)

Después de realizar estos pasos, vuelva a intentar la conexión a Escritorio remoto.


## Solución de problemas detallada

Puede que el cliente de Escritorio remoto no pueda ponerse en contacto con el servicio Servicios de Escritorio remoto en la máquina virtual de Azure debido a problemas o errores de configuración en los orígenes siguientes:

- Equipo cliente de Escritorio remoto
- Dispositivo perimetral de intranet de la organización
- Extremo de servicio en la nube y lista de control de acceso (ACL)
- Grupos de seguridad de red
- Máquina virtual de Azure basada en Windows

### Causa 1: equipo cliente de Escritorio remoto

Para descartar el equipo como causa de los problemas o errores de configuración, compruebe que el equipo puede establecer conexiones a Escritorio remoto con otro equipo local basado en Windows.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_1.png)

Si no es posible, compruebe si el equipo tiene lo siguiente:

- Una configuración del firewall local que bloquea el tráfico de Escritorio remoto
- Software de proxy de cliente instalado localmente que impide las conexiones a Escritorio remoto
- Software de supervisión de red instalado localmente que impide las conexiones a Escritorio remoto
- Otro tipo de software de seguridad, por ejemplo para supervisar el tráfico o para permitir o impedir ciertos tipos de tráfico, que imposibilita las conexiones a Escritorio remoto

En todos estos casos, intente deshabilitar temporalmente el software y pruebe a establecer una conexión a Escritorio remoto con un equipo local para averiguar la causa principal. A continuación, trabaje con el administrador de red para corregir la configuración del software para permitir conexiones a Escritorio remoto.

### Causa 2: dispositivo perimetral de intranet de la organización

Para descartar el dispositivo perimetral de intranet de la organización como causa de los problemas o errores de configuración, compruebe que un equipo conectado directamente a Internet puede establecer conexiones a Escritorio remoto con la máquina virtual de Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_2.png)

Si no tiene un equipo conectado directamente a Internet, puede crear fácilmente una nueva máquina virtual de Azure en su propio grupo de recursos o servicio en la nube y usarla. Para obtener más información, vea [Crear una máquina virtual que ejecuta Windows en Azure](virtual-machines-windows-tutorial.md). Cuando acabe de realizar las pruebas, elimine el grupo de recursos o la máquina virtual y el servicio en la nube.

Si puede crear una conexión a Escritorio remoto con un equipo conectado directamente a Internet, compruebe si el dispositivo perimetral de intranet de la organización tiene lo siguiente:

- Un firewall interno que bloquea las conexiones HTTPS a Internet
- Un servidor proxy que impide las conexiones a Escritorio remoto
- Software de detección de intrusiones o supervisión de red en ejecución en los dispositivos de la red perimetral que impide las conexiones a Escritorio remoto

Trabaje con el administrador de red para corregir la configuración del dispositivo perimetral de intranet de la organización para permitir conexiones a Escritorio remoto a Internet basadas en HTTPS.

### Causa 3: extremo de servicio en la nube y ACL

Para eliminar el extremo de servicio en la nube y la ACL como causas de los problemas o errores de configuración de las máquinas virtuales creadas con la API de administración de servicios, compruebe que otra máquina virtual de Azure que se encuentre en el mismo servicio en la nube o la misma red virtual pueda establecer conexiones con Escritorio remoto en la máquina virtual de Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_3.png)

> [AZURE.NOTE]En el caso de máquinas virtuales creadas en el Administrador de recursos, vaya a [Causa 4: Grupos de seguridad de red](#nsgs).

Si no tiene otra máquina virtual en el mismo servicio en la nube o red virtual, puede crear una fácilmente. Para obtener más información, vea [Crear una máquina virtual que ejecuta Windows en Azure](virtual-machines-windows-tutorial.md). Cuando acabe de realizar las pruebas, elimine la máquina virtual que creó.

Si se puede establecer una conexión a Escritorio remoto con una máquina virtual en el mismo servicio en la nube o red virtual, compruebe lo siguiente:

- La configuración del extremo para el tráfico de Escritorio remoto en la máquina virtual de destino. El puerto TCP privado del extremo debe coincidir con el puerto TCP en el que escucha el servicio Servicios de Escritorio remoto en la máquina virtual, que de forma predeterminada es 3389.
- La ACL del extremo para el tráfico de Escritorio remoto en la máquina virtual de destino. Las ACL permiten especificar el tráfico entrante de Internet que se permite o se deniega en función de la dirección IP de origen. Las ACL mal configuradas pueden impedir el tráfico entrante de Escritorio remoto al extremo. Examine las ACL para asegurarse de que está permitido el tráfico entrante desde las direcciones IP públicas del proxy o de otro servidor perimetral. Para obtener más información, consulte [¿Qué es una lista de control de acceso (ACL) de red?](../virtual-network/virtual-networks-acl.md).

Para descartar el extremo como causa del problema, quite el extremo actual y cree un nuevo extremo. Para ello, elija un puerto aleatorio en el intervalo que va entre 49152 y 65535 para el número de puerto externo. Para obtener más información, consulte [Configuración de puntos de conexión en una máquina virtual](virtual-machines-set-up-endpoints.md).

### <a id="nsgs"></a>Causa 4: Grupos de seguridad de red

Los grupos de seguridad de red permiten un control pormenorizado del tráfico entrante y saliente permitido. Puede crear reglas que abarquen subredes y servicios en la nube en una red virtual de Azure. Examine las reglas del grupo de seguridad de red para asegurarse de que se permite el tráfico de Escritorio remoto desde Internet.

Para obtener más información, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md).

### Causa 5: máquina virtual de Azure basada en Windows

Por último, los problemas pueden residir en la propia máquina virtual de Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_5.png)

En el [artículo Solución de problemas básicos de Escritorio remoto](virtual-machines-troubleshoot-remote-desktop-connections.md) se describe cómo usar el [paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864). Si este paquete de diagnóstico no pudo resolver el problema **Conectividad de RDP a una máquina virtual de Azure (se requiere reinicio)**, siga las instrucciones de [este artículo](virtual-machines-windows-reset-password.md) para restablecer el servicio Servicios de Escritorio remoto en la máquina virtual. De este modo:

- Se habilitará la regla predeterminada «Escritorio remoto» del firewall de Windows (puerto TCP 3389).
- Se habilitarán las conexiones a Escritorio remoto estableciendo en 0 el valor HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections del Registro.

Pruebe de nuevo la conexión desde su equipo. Si no funciona correctamente, pueden darse algunos de estos problemas:

- El servicio Servicios de Escritorio remoto no se está ejecutando en la máquina virtual de destino.
- El servicio Servicios de Escritorio remoto no está escuchando en el puerto TCP 3389.
- El firewall de Windows u otro firewall local tiene una regla de salida que impide el tráfico de Escritorio remoto.
- El software de detección de intrusiones o supervisión de red que se ejecuta en la máquina virtual de Azure impide las conexiones a Escritorio remoto.

Para corregir estos posibles problemas para máquinas virtuales creadas con la API de administración de servicios, puede usar una sesión remota de Azure PowerShell en la máquina virtual de Azure. En primer lugar, deberá instalar un certificado para el servicio en la nube de hospedaje de la máquina virtual. Vaya a [Configurar el acceso remoto seguro de PowerShell a máquinas virtuales de Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) y descargue el archivo de script **InstallWinRMCertAzureVM.ps1** en una carpeta del equipo local.

A continuación, instale Azure PowerShell si todavía no lo ha hecho. Consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md).

Después, abra un símbolo del sistema de Azure PowerShell y cambie la carpeta actual a la ubicación del archivo de script **InstallWinRMCertAzureVM.ps1**. Para ejecutar un script de Azure PowerShell, debe establecer la directiva de ejecución correcta. Ejecute el comando **Get-ExecutionPolicy** para determinar el nivel de directiva actual. Para obtener información sobre cómo establecer el nivel adecuado, vea [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

A continuación, rellene el nombre de su suscripción de Azure, el nombre del servicio en la nube y el nombre de la máquina virtual (quitando los caracteres < and >) y ejecute estos comandos.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Puede obtener el nombre de suscripción correcto en la propiedad _SubscriptionName_ de la pantalla del comando **Get-AzureSubscription**. Puede obtener el nombre del servicio en la nube de la máquina virtual en la columna _ServiceName_ de la pantalla del comando **Get-AzureVM**.

Para demostrar que tiene este nuevo certificado, abra un complemento Certificados para el usuario actual y vaya a la carpeta **Entidades de certificación raíz de confianza\\Certificados**. Debería ver un certificado con el nombre DNS del servicio en la nube en la columna Emitido para (ejemplo: cloudservice4testing.cloudapp.net).

A continuación, inicie una sesión remota de Azure PowerShell mediante el uso de estos comandos.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Después de escribir las credenciales de administrador válidas, debería ver algo parecido a lo siguiente como símbolo del sistema de PowerShell de Azure:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La primera parte del símbolo del sistema indica que ahora está emitiendo comandos de Azure PowerShell para el servicio en la nube que contiene la máquina virtual de destino. El nombre del servicio en la nube será algo distinto de "cloudservice4testing.cloudapp.net".

Ahora puede emitir comandos de Azure PowerShell para investigar los problemas adicionales que se citaron anteriormente y corregir la configuración.

### Para corregir manualmente el puerto TCP de escucha de Servicios de Escritorio remoto

Si no pudo ejecutar el [paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para solucionar el problema **Conectividad de RDP a una máquina virtual de Azure (se requiere reinicio)**, en el símbolo del sistema de la sesión remota de Azure PowerShell, ejecute este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La propiedad PortNumber muestra el número de puerto actual. Si es necesario, vuelva a poner el número de puerto del Escritorio remoto en su valor predeterminado (3389) con este comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Compruebe que el puerto cambió a 3389 con este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Salga de la sesión remota de Azure PowerShell con este comando.

	Exit-PSSession

Compruebe que el extremo de Escritorio remoto para la máquina virtual de Azure también usa el puerto TCP 3398 como puerto interno. A continuación, reinicie la máquina virtual de Azure y vuelva a probar su conexión a Escritorio remoto.


## Recursos adicionales

[Paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

[Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO4-->
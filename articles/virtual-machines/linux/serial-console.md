---
title: Consola serie de máquina virtual de Azure | Microsoft Docs
description: Consola serie bidireccional para máquinas virtuales de Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 69f5e29be77f25d649ce357dae6e3905ab2bf6b8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Consola serie de máquina virtual (versión preliminar) 


La consola serie de máquina virtual en Azure ofrece acceso a una consola basada en texto para máquinas virtuales Linux y Windows. Esta conexión de serie es al puerto de serie COM1 de la máquina virtual y ofrece acceso a la misma, sin estar relacionada con el estado del sistema operativo o la red de la máquina virtual. En este momento, el acceso a la consola serie para una máquina virtual solo es posible mediante Azure Portal, y se permite únicamente a los usuarios que tengan acceso de colaborador o superior a la máquina virtual. 

> [!Note] 
> Las versiones preliminares están a su disposición con la condición de que acepte los términos de uso. Para más información, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Actualmente este servicio está en **versión preliminar pública** y el acceso a la consola serie para las máquinas virtuales está disponible para las regiones globales de Azure. En este momento, la consola serie no está disponible en las nubes Azure Government, Azure Alemania y Azure China.


## <a name="prerequisites"></a>requisitos previos 

* La máquina virtual TIENE QUE tener los [diagnósticos de arranque](boot-diagnostics.md) habilitados 
* La cuenta que utilice la de serie debe tener el [rol Colaborador](../../role-based-access-control/built-in-roles.md) para la máquina virtual y la cuenta de almacenamiento de [diagnósticos de arranque](boot-diagnostics.md). 
* Para conocer valores específicos de distribución de Linux, consulte [Accessing the serial console for Linux](#accessing-serial-console-for-linux) (Acceso a la consola de serie para Linux).


## <a name="open-the-serial-console"></a>Apertura de la consola de serie
La consola de serie para las máquinas virtuales solo es accesible a través de [Azure Portal](https://portal.azure.com). A continuación se muestran los pasos requeridos para acceder a la consola de serie para las máquinas virtuales a través del portal. 

  1. Abra Azure Portal.
  2. En el menú de la izquierda, seleccione Máquinas virtuales.
  3. Haga clic en la máquina virtual en la lista. Se abrirá la página de información general para la VM.
  4. Desplácese hacia abajo hasta la sección Soporte y solución de problemas y haga clic en la opción de la consola de serie (versión preliminar). Se abrirá un panel nuevo con la consola de serie y se iniciará la conexión.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> La consola de serie requiere un usuario local con una contraseña configurada. En este momento, las máquinas virtuales configuradas solo con la clave pública SSH no tendrán acceso a la consola de serie. Para crear un usuario local con contraseña, siga [Extensión VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) y cree un usuario local con contraseña.

### <a name="disable-feature"></a>Deshabilitación de la característica
La funcionalidad de la consola serie puede desactivarse para determinadas máquinas virtuales deshabilitando el valor de ese parámetro de diagnósticos de arranque de la máquina virtual específica.

## <a name="serial-console-security"></a>Seguridad de la consola serie 

### <a name="access-security"></a>Seguridad de acceso 
El acceso a la consola serie está limitado a los usuarios que tienen un acceso de [colaborador de la máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) o superior en relación con la máquina virtual. Si su inquilino de AAD requiere Multi-Factor Authentication, el acceso a la consola serie también necesitará MFA para acceder mediante [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Seguridad del canal
Todos los datos enviados y recibidos se cifran en la conexión.

### <a name="audit-logs"></a>Registros de auditoría
Todo el acceso a la consola serie queda registrado en los registros de los [diagnósticos de arranque](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la máquina virtual. El administrador de la máquina virtual de Azure es el propietario y el que controla el acceso a estos registros.  

>[!CAUTION] 
Si bien no se registra ninguna contraseña de acceso para la consola, si los comandos ejecutados en la consola contienen o producen contraseñas, secretos, nombres de usuario o cualquier otra forma de información de identificación personal, estos se escribirán en los registros de diagnósticos de arranque de la máquina virtual, junto con todo el demás texto visible, como parte de la aplicación de la funcionalidad de desplazamiento de la consola de serie. Estos registros son circulares y solo aquellas personas con permisos de lectura para la cuenta de almacenamiento de diagnósticos tienen acceso a ellos; sin embargo, se recomienda seguir la práctica recomendada de usar la consola SSH para cualquier elemento que pueda implicar secretos o información de identificación personal. 

### <a name="concurrent-usage"></a>Uso simultáneo
Si un usuario se conecta a la consola serie y otro usuario solicita correctamente el acceso a esa misma máquina virtual, se desconectará al primer usuario y se conectará al segundo de una forma similar a la acción del primer usuario levantándose y abandonando la consola física y el nuevo usuario sentándose a ocupar su sitio.

>[!CAUTION] 
Esto quiere decir que no se cerrará la sesión del usuario que se desconecta. La posibilidad de forzar el cierre de sesión con la desconexión (mediante SIGHUP u otro mecanismo similar) sigue en proceso de valoración. Para Windows hay un tiempo de espera automático habilitado en SAC, sin embargo para Linux puede configurar el parámetro de tiempo de espera terminal. Basta con agregar `export TMOUT=600` en .bash_profile o .profile para el usuario con el que inicia sesión en la consola para que el tiempo de espera de la sesión finalice una vez transcurridos 10 minutos.

### <a name="disable-feature"></a>Deshabilitación de la característica
La funcionalidad de la consola de serie puede desactivarse para determinadas máquinas virtuales con la deshabilitación de ese parámetro de diagnósticos de arranque de la VM.

## <a name="common-scenarios-for-accessing-serial-console"></a>Escenarios comunes para acceder a la consola de serie 
Escenario          | Acciones en la consola de serie                |  Aplicabilidad de SO 
:------------------|:-----------------------------------------|:------------------
Archivo FSTAB roto | Escriba la clave para continuar y corrija el archivo fstab con un editor de texto. Vea [Azure VM Linux no se puede iniciar debido a errores de fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors). | Linux 
Reglas de firewall incorrectas | Acceda a la consola de serie y corrija iptables o las reglas de firewall de Windows. | Linux y Windows 
Comprobación o daños en el sistema de archivos | Acceda a la consola de serie y recupere el sistema de archivos. | Linux y Windows 
Problemas de configuración de SSH/RDP | Acceda a la consola de serie y cambie la configuración. | Linux y Windows 
Sistema de bloqueo de red| Acceda a la consola de serie a través del portal para administrar el sistema. | Linux y Windows 
Interacción con el cargador de arranque | Acceda a GRUB/BCD mediante la consola de serie. | Linux y Windows 

## <a name="accessing-serial-console-for-linux"></a>Acceso a la consola de serie para Linux
Para que la consola de serie funcione correctamente, el sistema operativo invitado debe configurarse para leer y escribir mensajes de la consola en el puerto de serie. La mayoría de [distribuciones de Linux aprobadas por Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) tienen la consola de serie configurada de manera predeterminada. Con solo hacer clic en la sección Consola de serie del portal se accederá a la consola. 

### <a name="access-for-redhat"></a>Acceso para Red Hat 
Las imágenes de Red Hat disponibles en Azure tienen habilitado el acceso a la consola de forma predeterminada. El modo de usuario único en Red Hat requiere que el usuario raíz esté habilitado (está deshabilitado de manera predeterminada). Si tiene que habilitar el modo de usuario único, siga estas instrucciones:

1. Inicie sesión en el sistema de Red Hat a través de SSH.
2. Habilite la contraseña de usuario raíz. 
 * `passwd root` (establezca una contraseña de raíz segura)
3. Asegúrese de que el usuario raíz solo puede iniciar sesión a través de ttyS0.
 * `edit /etc/ssh/sshd_config` y asegúrese de que PermitRootLog en está establecido en no.
 * `edit /etc/securetty file` para permitir solo el inicio de sesión a través de ttyS0. 

Ahora si el sistema arranca en modo de usuario único puede iniciar sesión a través de la contraseña raíz.

En RHEL 7.4+ o 6.9+, alternativamente, puede habilitar el modo de usuario único en las solicitudes de GRUB (vea [aquí](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single) las instrucciones).

### <a name="access-for-ubuntu"></a>Acceso para Ubuntu 
Las imágenes de Ubuntu disponibles en Azure tienen habilitado el acceso a la consola de forma predeterminada. Si el sistema arranca en modo de usuario único, puede acceder sin necesidad de otras credenciales. 

### <a name="access-for-coreos"></a>Acceso para CoreOS
Las imágenes de CoreOS disponibles en Azure tienen habilitado el acceso a la consola de forma predeterminada. Si es necesario, se puede arrancar el sistema en modo de usuario único mediante la modificación de los parámetros de GRUB, y la adición de `coreos.autologin=ttyS0` permitiría que el usuario principal inicie sesión y esté disponible en la consola de serie. 

### <a name="access-for-suse"></a>Acceso para SUSE
Las imágenes de SLES disponibles en Azure tienen habilitado el acceso a la consola de forma predeterminada. Si está utilizando versiones anteriores de SLES en Azure, siga el [artículo de KB](https://www.novell.com/support/kb/doc.php?id=3456486) para habilitar la consola de serie. Las imágenes más recientes de SLES 12 SP3+ también permiten el acceso a través de la consola de serie en caso de que el sistema arranque en modo de emergencia.

### <a name="access-for-centos"></a>Acceso para CentOS
Las imágenes de CentOS disponibles en Azure tienen habilitado el acceso a la consola de forma predeterminada. Para el modo de usuario único, siga unas instrucciones similares a las detalladas anteriormente para Red Hat. 

### <a name="access-for-oracle-linux"></a>Acceso para Oracle Linux
Las imágenes de Oracle Linux disponibles en Azure tienen habilitado el acceso a la consola de forma predeterminada. Para el modo de usuario único, siga unas instrucciones similares a las detalladas anteriormente para Red Hat.

### <a name="access-for-custom-linux-image"></a>Acceso para imagen personalizada de Linux
Para habilitar la consola de serie para la imagen de VM de Linux personalizada, habilite el acceso de la consola en /etc/inittab para ejecutar un terminal en ttyS0. A continuación se muestra un ejemplo de cómo se añade esto al archivo inittab. 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Errors
La mayoría de errores son transitorios por naturaleza, y basta con intentar establecer la conexión de nuevo para solucionarlos. La tabla siguiente muestra una lista de errores y su solución 

Error                            |   Mitigación 
:---------------------------------|:--------------------------------------------|
No se puede recuperar la configuración de diagnóstico de arranque para "<VMNAME>". Para usar la consola serie, asegúrese de que el diagnóstico de arranque está habilitado para esta máquina virtual. | Asegúrese de que la máquina virtual tiene los [diagnósticos de arranque](boot-diagnostics.md) habilitados. 
La máquina virtual se encuentra en un estado desasignado detenido. Inicie la máquina virtual y vuelva a intentar la conexión de la consola serie. | La máquina virtual debe estar en un estado iniciado para tener acceso a la consola de serie.
No tiene los permisos necesarios para usar esta máquina virtual como consola de serie. Asegúrese de tener al menos permisos del rol de colaborador de la VM.| El acceso a la consola serie requiere cierto nivel de permiso. Consulte los [requisitos de acceso](#prerequisites) para más información
No se puede determinar el grupo de recursos para la cuenta de almacenamiento de diagnósticos de arranque "<STORAGEACCOUNTNAME>". Compruebe que los diagnósticos de arranque están habilitados para esta máquina virtual y acceda a esta cuenta de almacenamiento. | El acceso a la consola de serie requiere cierto nivel de permiso. Consulte los [requisitos de acceso](#prerequisites) para obtener más información.

## <a name="known-issues"></a>Problemas conocidos 
Como nos encontramos aún en las etapas de versión preliminar para el acceso a la consola serie, estamos trabajando en algunos problemas conocidos. Presentamos aquí la lista con algunas posibles soluciones 

Problema                           |   Mitigación 
:---------------------------------|:--------------------------------------------|
No hay opción para la consola serie con la instancia de conjunto de escalado de máquinas virtuales |  Con esta versión preliminar, no se admite el acceso a la consola serie para las instancias del conjunto de escalado de máquinas virtuales.
Al pulsar Entrar tras un banner de conexión no aparece la solicitud de inicio de sesión | [Pulsar Entrar no hace nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 
**P. ¿Cómo puedo enviar comentarios?**

A. Envíe comentarios como problemas que tenga acudiendo a https://aka.ms/serialconsolefeedback. También tiene la posibilidad (aunque se prefiere menos) de enviar comentarios a través de azserialhelp@microsoft.com o en la categoría de máquinas virtuales en http://feedback.azure.com.

**P. Aparece el error "La consola existente tiene el tipo de SO en conflicto 'Windows' con el tipo de SO solicitado de Linux"**.

A. Se trata de un problema conocido; para solucionarlo, basta con abrir [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) en modo Bash e intentarlo de nuevo.

**P. No puedo acceder a la consola serie, ¿dónde puedo presentar un caso de soporte técnico?**

A. Esta característica de versión preliminar viene recogida en los términos de las versiones preliminares de Azure. La mejor manera de obtener soporte técnico es recurrir a los canales mencionados anteriormente. 

## <a name="next-steps"></a>Pasos siguientes
* La consola de serie también está disponible para máquinas virtuales con [Windows](../windows/serial-console.md).
* Obtenga más información sobre los [diagnósticos de arranque](boot-diagnostics.md).
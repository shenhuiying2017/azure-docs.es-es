---
title: Consola serie de máquina virtual de Azure | Microsoft Docs
description: Consola serie bidireccional para máquinas virtuales de Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Consola serie de máquina virtual (versión preliminar) 


La consola serie de máquina virtual en Azure ofrece acceso a una consola basada en texto para máquinas virtuales Linux y Windows. Esta conexión de serie es al puerto de serie COM1 de la máquina virtual y ofrece acceso a la misma, sin estar relacionada con el estado del sistema operativo o la red de la máquina virtual. En este momento, el acceso a la consola serie para una máquina virtual solo es posible mediante Azure Portal, y se permite únicamente a los usuarios que tengan acceso de colaborador o superior a la máquina virtual. 

> [!Note] 
> Las versiones preliminares están a su disposición con la condición de que acepte los términos de uso. Para más información, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Actualmente este servicio está en **versión preliminar pública** y el acceso a la consola serie para las máquinas virtuales está disponible para las regiones globales de Azure. En este momento, la consola serie no está disponible en las nubes Azure Government, Azure Alemania y Azure China.

 

## <a name="prerequisites"></a>requisitos previos 

* La máquina virtual TIENE QUE tener los [diagnósticos de arranque](boot-diagnostics.md) habilitados 
* La cuenta que utilice la consola serie tiene que tener el [rol Colaborador](../../active-directory/role-based-access-built-in-roles.md) para la máquina virtual y la cuenta de almacenamiento de [diagnósticos de arranque](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Apertura de la consola serie
La consola serie para las máquinas virtuales solo es accesible mediante [Azure Portal](https://portal.azure.com). A continuación se muestran los pasos requeridos para acceder a la consola serie para las máquinas virtuales en el portal 

  1. Abra Azure Portal.
  2. En el menú de la izquierda, seleccione Máquinas virtuales.
  3. Haga clic en la máquina virtual en la lista. Se abrirá la página de información general de la máquina virtual.
  4. Desplácese hacia abajo hasta la sección de Soporte técnico y solución de problemas y haga clic en la opción de la consola serie (versión preliminar). Se abrirá un panel nuevo con la consola serie y se iniciará la conexión.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Deshabilitación de la característica
La funcionalidad de la consola serie puede desactivarse para determinadas máquinas virtuales deshabilitando el valor de ese parámetro de diagnósticos de arranque de la máquina virtual específica.

## <a name="serial-console-security"></a>Seguridad de la consola serie 

### <a name="access-security"></a>Seguridad de acceso 
El acceso a la consola serie está limitado a los usuarios que tienen un acceso de [colaborador de la máquina virtual](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) o superior en relación con la máquina virtual. Si su inquilino de AAD requiere Multi-Factor Authentication, el acceso a la consola serie también necesitará MFA para acceder mediante [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Seguridad del canal
Todos los datos enviados y recibidos se cifran en la conexión.

### <a name="audit-logs"></a>Registros de auditoría
Todo el acceso a la consola serie queda registrado en los registros de los [diagnósticos de arranque](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la máquina virtual. El administrador de la máquina virtual de Azure es el propietario y el que controla el acceso a estos registros.  

>[!CAUTION] 
Si bien no se registra ninguna contraseña de acceso para la consola, si los comandos ejecutados en la consola contienen o producen contraseñas, secretos, nombres de usuario o cualquier otra forma de información de identificación personal (PII), estos se escribirán en los registros de los diagnósticos de arranque de la máquina virtual, junto con todo el demás texto visible, como parte de la implementación de la funcionalidad de desplazamiento de la consola serie. Estos registros son circulares y solo aquellas personas con permisos de lectura para la cuenta de almacenamiento de diagnósticos tienen acceso a ellos; de todas formas, se recomienda seguir la práctica recomendada de usar el Escritorio remoto para cualquier elemento que pueda implicar secretos o información de identificación personal. 

### <a name="concurrent-usage"></a>Uso simultáneo
Si un usuario se conecta a la consola serie y otro usuario solicita correctamente el acceso a esa misma máquina virtual, se desconectará al primer usuario y se conectará al segundo de una forma similar a la acción del primer usuario levantándose y abandonando la consola física y el nuevo usuario sentándose a ocupar su sitio.

>[!CAUTION] 
Esto quiere decir que no se cerrará la sesión del usuario que se desconecta. La posibilidad de forzar el cierre de sesión con la desconexión (mediante SIGHUP u otro mecanismo similar) sigue en proceso de valoración. Para Windows hay un tiempo de espera automático habilitado en SAC, sin embargo para Linux puede configurar el valor de tiempo de espera del terminal. 


## <a name="accessing-serial-console-for-windows"></a>Acceso a la consola serie para Windows 
Las imágenes más nuevas de Windows Server en Azure tendrán la [consola de administración especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitada de forma predeterminada. SAC se admite en versiones de servidor de Windows, pero no está disponible en versiones de cliente (p. ej. Windows 10, Windows 8 o Windows 7). Para habilitar la consola serie para las máquinas virtuales Windows creadas usando imágenes de Feb2018 o inferiores siga los pasos a continuación: 

1. Conéctese a la máquina virtual Windows mediante Escritorio remoto
2. Ejecute los siguientes comandos como administrador en un símbolo del sistema 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Reinicie el sistema para que la consola de SAC se habilite

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Si es necesario SAC también puede habilitarse sin conexión, 

1. Conecte el disco de Windows para el que desee Configurar SAC como disco de datos a la máquina virtual existente. 
2. Ejecute los siguientes comandos como administrador en un símbolo del sistema 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>¿Cómo se puede saber si SAC está habilitada o no? 

Si [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) no está habilitada la consola serie no mostrará el símbolo del sistema de SAC. Puede mostrar una información de mantenimiento de la máquina virtual en algunos casos, o puede estar en blanco.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Habilitación del menú de arranque para mostrar la consola serie 

Si tiene que habilitar los mensajes del cargador de arranque de Windows para mostrar en la consola serie, puede agregar las siguientes opciones adicionales al cargador de arranque de Windows.

1. Conéctese a la máquina virtual Windows mediante Escritorio remoto
2. Ejecute los siguientes comandos como administrador en un símbolo del sistema 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Reinicie el sistema para que el menú de arranque se habilite

> [!NOTE] 
> En este momento la compatibilidad para las teclas de función no está habilitada, si necesita opciones de arranque avanzadas use bcdedit /set {current} onetimeadvancedoptions on, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) para más información

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>Escenarios comunes para acceder a la consola serie de Windows 
Escenario          | Acciones en la consola serie                
:------------------|:----------------------------------------
Reglas de firewall incorrectas | Acceso a la consola serie y corrección de iptables o reglas de firewall de Windows 
Comprobación o daños en el sistema de archivos | Acceso a la consola serie y recuperación del sistema de archivos después de iniciar sesión en SAC CMD
Problemas de configuración de RDP | Acceso a la consola serie e inicio de sesión al canal de cmd. Comprobación del mantenimiento de Terminal Services y reinicio si es necesario.
Sistema de bloqueo de red| Acceso a la consola serie e inicio de sesión al canal de cmd. Comprobar el estado de firewall con la línea de comandos [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts). 

## <a name="errors"></a>Errors
La mayoría de errores son transitorios por naturaleza, y basta con intentar la conexión de nuevo para solucionarlos. La tabla siguiente muestra una lista de errores y su solución 

Error                            |   Mitigación 
:---------------------------------|:--------------------------------------------|
No se puede recuperar la configuración de diagnóstico de arranque para "<VMNAME>". Para usar la consola serie, asegúrese de que el diagnóstico de arranque está habilitado para esta máquina virtual. | Asegúrese de que la máquina virtual tiene los [diagnósticos de arranque](boot-diagnostics.md) habilitados. 
La máquina virtual se encuentra en un estado desasignado detenido. Inicie la máquina virtual y vuelva a intentar la conexión de la consola serie. | La máquina virtual tiene que estar en un estado iniciado para tener acceso a la consola serie
No tiene los permisos necesarios para usar la consola serie de esta máquina virtual. Asegúrese de tener al menos los permisos del rol de colaborador de máquina virtual.| El acceso a la consola serie requiere cierto nivel de permiso. Consulte los [requisitos de acceso](#prerequisites) para más información
No se puede determinar el grupo de recursos para la cuenta de almacenamiento de diagnósticos de arranque "<STORAGEACCOUNTNAME>". Compruebe que el diagnóstico de arranque está habilitado para esta máquina virtual y que tiene acceso a esta cuenta de almacenamiento. | El acceso a la consola serie requiere cierto nivel de permiso. Consulte los [requisitos de acceso](#prerequisites) para más información

## <a name="known-issues"></a>Problemas conocidos 
Como nos encontramos aún en las etapas de versión preliminar para el acceso a la consola serie, estamos trabajando en algunos problemas conocidos. Presentamos aquí la lista con algunas posibles soluciones 

Problema                           |   Mitigación 
:---------------------------------|:--------------------------------------------|
No hay opción para la consola serie con la instancia de conjunto de escalado de máquinas virtuales | Con esta versión preliminar, no se admite el acceso a la consola serie para las instancias del conjunto de escalado de máquinas virtuales.
Al pulsar Entrar tras un banner de conexión no aparece la solicitud de inicio de sesión | [Pulsar Entrar no hace nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Se muestra solamente información de mantenimiento cuando se conecta a una máquina virtual Windows| [Señales de mantenimiento de Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 
**P. ¿Cómo puedo enviar comentarios?**

A. Envíe comentarios como problemas que tenga acudiendo a https://aka.ms/serialconsolefeedback. También tiene la posibilidad (aunque se prefiere menos) de enviar comentarios a azserialhelp@microsoft.com, o en la categoría de máquinas virtuales en http://feedback.azure.com 

**P. Aparece el error "Existing console has conflicting OS type "Windows" with the requested OS type of Linux?" (¿Tiene la consola existente un tipo de SO "Windows" en conflicto con el tipo de SO solicitado de Linux?)**

A. Se trata de un problema conocido; para solucionarlo, basta con abrir [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) en modo Bash e intentarlo de nuevo.

**P. No puedo acceder a la consola serie, ¿dónde puedo presentar un caso de soporte técnico?**

A. Esta característica de versión preliminar viene recogida en los términos de las versiones preliminares de Azure. La mejor manera de obtener soporte técnico es recurrir a los canales mencionados anteriormente. 

## <a name="next-steps"></a>Pasos siguientes
* La consola serie también está disponible para máquinas virtuales [Linux](../linux/serial-console.md)
* Más información acerca de los [diagnósticos de arranque](boot-diagnostics.md)

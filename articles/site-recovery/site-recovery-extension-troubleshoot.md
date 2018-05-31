---
title: 'Solución de errores del agente de Azure Site Recovery: no está disponible el estado del agente invitado | Microsoft Docs'
description: Síntomas, causas y soluciones de errores de Azure Site Recovery relacionados con el agente y la extensión
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: asgang
ms.openlocfilehash: 9bfe181b2271f4e8af6f43e1728167712dade8ee
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777610"
---
# <a name="troubleshoot-azure-site-recovery-extension-failures-issues-with-the-agent-or-extension"></a>Solución de errores de extensión de Azure Site Recovery: problemas con el agente o la extensión

En este artículo se proporcionan pasos de solución de problemas que pueden ayudar a resolver errores de Azure Site Recovery relacionados con el agente y la extensión de máquina virtual.


## <a name="azure-site-recovery-extension-time-out"></a>Tiempo de espera de la extensión de Azure Site Recovery  

Mensaje de error: "se ha superado el tiempo de espera de ejecución de la tarea al realizar el seguimiento del inicio de la operación de extensión"<br>
Código de error: "151076"

 Azure Site Recovery instala una extensión en la máquina virtual como parte de la habilitación del trabajo de protección. Cualquiera de las condiciones siguientes puede evitar que se desencadene la protección y producir un error del trabajo. Siga los pasos de solución de problemas siguientes y luego vuelva a intentar la operación:

**Causa 1: [El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [El agente instalado en la máquina virtual está obsoleto (en máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [La extensión de Site Recovery no se actualiza ni se carga](#the-site-recovery-extension-fails-to-update-or-load)**  

Mensaje de error: "la operación de extensión de Site Recovery anterior está tardando más tiempo del esperado".<br>
Código de error: "150066"<br>

**Causa 1: [El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [El agente instalado en la máquina virtual está obsoleto (en máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [El estado de la extensión de Site Recovery es incorrecto](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Error de protección porque el agente de máquina virtual no responde

Mensaje de error: "se ha superado el tiempo de espera de ejecución de la tarea al realizar el seguimiento del inicio de la operación de extensión"<br>
Código de error: "151099"<br>

Este error puede producirse si el agente invitado de Azure en la máquina virtual no está listo.
Puede comprobar el estado del agente invitado de Azure en [Azure Portal](https://portal.azure.com/). Vaya a la máquina virtual que está intentando proteger y compruebe el estado en "VM > Configuración > Propiedades > Estado del agente". La mayoría de las veces el estado del agente pasa a ser Listo después de reiniciar la máquina virtual. Pero si el reinicio no es una opción viable o todavía se enfrenta al problema, siga los siguientes pasos de solución de problemas.

**Causa 1: [El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [El agente instalado en la máquina virtual está obsoleto (en máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Mensaje de error: "se ha superado el tiempo de espera de ejecución de la tarea al realizar el seguimiento del inicio de la operación de extensión"<br>
Código de error: "151095"<br>

Se produce cuando la versión del agente en el equipo Linux es antigua. Siga estos pasos de solución de problemas.<br>
  **Causa 1: [El agente instalado en la máquina virtual está obsoleto (en máquinas virtuales Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Causas y soluciones

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>El agente está instalado en la máquina virtual, pero no responde (en máquinas virtuales Windows)

#### <a name="solution"></a>Solución
Es posible que el agente de máquina virtual se haya dañado o que el servicio se haya detenido. Volver a instalar el agente de máquina virtual ayuda a obtener la versión más reciente. O bien, intente restablecer la comunicación con el servicio.

1. Determine si el "servicio Windows Azure Guest Agent" se está ejecutando en los servicios de máquina virtual (services.msc). Intente reiniciar el "servicio Windows Azure Guest Agent".    
2. Si el servicio Windows Azure Guest Agent no está visible en los servicios, en el Panel de control, vaya a **Programas y características** para determinar si está instalado.
4. Si Windows Azure Guest Agent aparece en **Programas y características**, desinstálelo.
5. Descargue e instale la [versión más reciente del MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Debe tener derechos de administrador para completar la instalación.
6. Compruebe que el servicio Windows Azure Guest Agent aparece en los servicios.
7. Reinicie el trabajo de protección.

Además, compruebe que [Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) está instalado en la máquina virtual, ya que se requiere para que el agente de máquina virtual se comunique con el servicio.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>El agente instalado en la máquina virtual está obsoleto (en el caso de máquinas virtuales Linux)

#### <a name="solution"></a>Solución
La mayoría de los errores relacionados con el agente o la extensión de máquinas virtuales de Linux están provocados por problemas que afectan a un agente VM obsoleto. Para solucionar este problema, siga estas directrices generales:

1. Siga las instrucciones para [actualizar el agente de máquina virtual Linux ](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Se *recomienda encarecidamente* actualizar el agente solo a través de un repositorio de distribución. No recomendamos descargar el código de agente desde GitHub directamente y actualizarlo. Si el último agente no está disponible para su distribución, póngase en contacto con el soporte técnico de distribución para obtener instrucciones sobre cómo instalarlo. Para buscar el agente más reciente, vaya a la página del [agente Linux de Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) en el repositorio de GitHub.

2. Asegúrese de que el agente de Azure se ejecuta en la máquina virtual mediante la ejecución del comando siguiente: `ps -e`.

 Si el proceso no se está ejecutando, reinícielo mediante los siguientes comandos:

 * Para Ubuntu: `service walinuxagent start`
 * Para otras distribuciones: `service waagent start`

3. [Configure el agente de reinicio automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Habilite la protección de la máquina virtual.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>La extensión de Site Recovery no se actualiza ni se carga
Si el estado de las extensiones es "Vacío", "No está listo" o En transición.

#### <a name="solution"></a>Solución

Desinstale la extensión y reinicie la operación.

Para desinstalar la extensión, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), vaya a la máquina virtual que experimenta los errores de copia de seguridad.
2. Seleccione **Configuración**.
3. Seleccione **Extensiones**.
4. Seleccione **Extensión de Site Recovery**.
5. Seleccione **Desinstalar**.

En máquinas virtuales Linux, si la extensión VMSnapshot no aparece en Azure Portal, [actualice el Agente de Linux de Azure](../virtual-machines/linux/update-agent.md) y ejecute la protección. 

La realización de estos pasos hace que se vuelva a instalar la extensión durante la protección.



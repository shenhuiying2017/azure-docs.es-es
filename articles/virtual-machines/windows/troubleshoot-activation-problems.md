---
title: "Solución de problemas de activación de máquinas virtuales de Windows en Azure | Microsoft Docs"
description: "Ofrece los pasos de solución de problemas para resolver problemas de activación de máquinas virtuales de Windows en Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: f6095d98ada2974bce03ec8f5527367837daafd3
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Solución de problemas de activación de máquinas virtuales Windows de Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Si tiene algún problema al activar una máquina virtual (VM) Windows de Azure que se crea a partir de una imagen personalizada, puede usar la información proporcionada en este documento para solucionar el problema. 

## <a name="symptom"></a>Síntoma

Al intentar activar una VM Windows de Azure, recibe un mensaje de error similar al del ejemplo siguiente:

**Error: 0xC004F074 The Software LicensingService reported that the computer could not be activated. No Key ManagementService (KMS) could be contacted. Please see the Application Event Log for additional information.** (Error: 0xC004F074 El Servicio de licencias de software ha notificado que el servicio no se ha podido activar. No se ha podido establecer contacto con ningún Servicio de administración de claves (KMS). Vea el registro de eventos de la aplicación para obtener información adicional).

## <a name="cause"></a>Causa
Por lo general, los problemas de activación de máquinas virtuales de Azure se producen si la VM de Windows no está configurada con la clave de instalación de cliente KMS apropiada o la tiene un problema de conectividad con el servicio de KMS de Azure (kms.core.windows.net, puerto 1668). 

## <a name="solution"></a>Solución

>[!NOTE]
>Si usa una VPN de sitio a sitio y tunelización forzada, vea [Use Azure custom routes to enable KMS activation with forced tunneling](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) (Uso de rutas personalizadas de Azure para permitir la activación de KMS con tunelización forzada). 
>
>Si usa ExpressRoute y tiene una ruta predeterminada publica, vea [Azure VM may fail to activate over ExpressRoute](https://blogs.technet.microsoft.com/jpaztech/2016/05/16/azure-vm-may-fail-to-activate-over-expressroute/) (Es posible que la máquina virtual de Azure no se pueda activar por ExpressRoute).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Paso 1 Configuración de la clave de instalación de cliente KMS adecuada (para Windows Server 2016 y Windows Server 2012 R2)

En el caso de máquinas creadas a partir de una imagen personalizada de Windows Server 2016 o Windows Server 2012 R2, hay que configurar la clave de instalación de cliente KMS adecuada para la VM.

Este paso no se aplica a Windows 2012 o Windows 2008 R2. Usa la característica Automation Virtual Machine Activation (AVMA), que solo es compatible con Windows Server 2016 y Windows Server 2012 R2.

1. Ejecute **slmgr.vbs /dlv** en un símbolo del sistema con privilegios elevados. Compruebe el valor de Descripción en la salida y determine si se ha creado desde el canal comercial (RETAIL) o desde soportes de licencia por volumen (VOLUME_KMSCLIENT):
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Si **slmgr.vbs /dlv** muestra el canal RETAIL, ejecute los siguientes comandos para establecer la [clave de instalación de cliente KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) de la versión de Windows Server que se usa y haga que vuelva a intentar la activación: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Por ejemplo, para Windows Server 2016 Datacenter, debe ejecutar el siguiente comando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Paso 2 Comprobación de la conectividad entre la VM y el servicio KMS de Azure

1. Descargue la herramienta [Psping](http:/technet.microsoft.com/en-us/sysinternals/jj729731.aspx) y extráigala en una carpeta local en la VM que no se activa. 

2. Vaya a Inicio, busque en Windows PowerShell, haga clic con el botón derecho en Windows PowerShell y seleccione Ejecutar como administrador.

3. Asegúrese de que la VM está configurada para usar el servidor de KMS de Azure correcto. Para ello, ejecute el siguiente comando:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    El comando debe devolver: Key Management Service machine name set to kms.core.windows.net:1688 successfully (El nombre de máquina del servicio de administración de claves se estableció correctamente en kms.core.windows.net:1688).

4. Compruebe con Psping que dispone de conectividad con el servidor de KMS. Vaya a la carpeta en la que extrajo la descarga de Pstools.zip y ejecute lo siguiente:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  En la penúltima línea, asegúrese de que aparece: Enviados = 4, Recibidos = 4, Perdidos = 0 (0 % de pérdida).

  Si el valor de perdidos es mayor que 0 (cero), la máquina virtual no tiene conectividad con el servidor de KMS server. En este caso, si la VM está en una red virtual y tiene especificado un servidor DNS personalizado, debe asegurarse de que el servidor DNS es capaz de resolver kms.core.windows.net. También puede cambiar el servidor DNS a uno que pueda resolver kms.core.windows.net.

  Tenga en cuenta que si quita todos los servidores DNS de una red virtual, las máquinas virtuales usan el servicio DNS interno de Azure. Este servicio pude resolver kms.core.windows.net.
  
Compruebe también que firewall de invitado no está configurado de manera que bloquee los intentos de activación.

5. Después de comprobar la conectividad correcta con kms.core.windows.net, ejecute el siguiente comando en un símbolo del sistema de Windows PowerShell con privilegios elevados. Este comando intenta la activación varias veces.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Una activación correcta devuelve información similar a la siguiente:

**Activación de Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678) … El producto se activó correctamente.**

## <a name="faq"></a>Preguntas más frecuentes 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>He creado Windows Server 2016 desde Azure Marketplace. ¿Tengo que configurar la clave KMS para activar Windows Server 2016? 
 
Nº La imagen de Azure Marketplace ya tiene configurada la clave de instalación de cliente KMS apropiada. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>¿La activación de Windows funciona siempre igual sin importar que la VM use la Ventaja de uso híbrido (HUB) de Azure? 
 
Sí. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>¿Qué sucede si el período de activación de Windows expira? 
 
Cuando el período de gracia ha expirado y Windows no está activado todavía, Windows Server 2008 R2 y las versiones posteriores de Windows mostrarán notificaciones adicionales sobre la activación. El fondo de pantalla del escritorio permanece negro y Windows Update solo instalará las actualizaciones críticas y de seguridad, pero no las actualizaciones opcionales. Vea la sección de notificaciones en la parte inferior de la página [Licensing Conditions](http://technet.microsoft.com/en-us/library/ff793403.aspx) (Condiciones de licencias).   

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

---
title: "Solución de problemas de implementación de la serie StorSimple 8000 | Microsoft Docs"
description: "Se describe cómo diagnosticar y corregir los errores que se producen al implementar StorSimple por primera vez."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: d6642231bf9d012b0015902c82d2bfde3b86285c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Solución de problemas de implementación de dispositivos de StorSimple
## <a name="overview"></a>Información general
Este artículo proporciona instrucciones útiles para la solución de problemas relacionados con la implementación de Microsoft Azure StorSimple. Se describen los problemas comunes, las causas posibles y los pasos recomendados, como ayuda para resolver los problemas que pueden producirse al configurar StorSimple. 

Esta información se aplica al dispositivo físico de la serie StorSimple 8000 y al dispositivo StorSimple Cloud Appliance.

> [!NOTE]
> Los problemas relacionados con la configuración del dispositivo pueden producirse al implementar el dispositivo por primera vez o bien más adelante, cuando el dispositivo está operativo. Este artículo se centra en la solución de problemas relacionados con la implementación por primera vez. Para solucionar los problemas de un dispositivo operativo, vaya a [Uso la herramienta de diagnóstico para solucionar los problemas de un dispositivo operativo](storsimple-8000-diagnostics.md).

En este artículo también se describen las herramientas para solucionar problemas relacionados con implementaciones de StorSimple y se incluye un ejemplo de solución de problemas paso a paso.

## <a name="first-time-deployment-issues"></a>Problemas de implementación por primera vez
Si surge un problema al implementar el dispositivo por primera vez, tenga en cuenta lo siguiente:

* Si se trata de problemas de un dispositivo físico, asegúrese de que el hardware está instalado y configurado como se describe en [Desempaquetar, montar en bastidor y colocar los cables del dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [Desempaquetar, montar en bastidor y colocar los cables del dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
* Compruebe los requisitos previos para la implementación. Asegúrese de que dispone de toda la información que se describe en la [lista de comprobación de la configuración de la implementación](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Revise las notas de la versión de StorSimple para ver si se describe el problema. Las notas de la versión incluyen soluciones alternativas a problemas de instalación conocidos. 

Durante la implementación del dispositivo, los problemas más habituales a los que se enfrentan los usuarios se producen cuando se ejecuta el Asistente para instalación y cuando se registra el dispositivo a través de Windows PowerShell para StorSimple. (Windows PowerShell para StorSimple se utiliza para registrar y configurar el dispositivo de StorSimple. Para obtener más información sobre el registro de dispositivos, consulte [Paso 3: Configurar y registrar el dispositivo mediante Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Las siguientes secciones le ayudarán a resolver los problemas que aparecen al configurar el dispositivo de StorSimple por primera vez.

## <a name="first-time-setup-wizard-process"></a>Proceso del Asistente para instalación por primera vez
Los pasos siguientes resumen el proceso del Asistente para instalación. Para obtener información detallada de la configuración, consulte cómo [Implementar el dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).

1. Ejecute el cmdlet [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) para iniciar el Asistente para instalación, que le guiará por el resto de los pasos. 
2. Configure la red: el Asistente para instalación permite configurar la red para la interfaz de red DATA 0 del dispositivo de StorSimple. Entre los ajustes se incluyen los siguientes:
   * IP virtual (VIP), máscara de subred y puerta de enlace: el cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) se ejecuta en segundo plano. Configura la dirección IP, la máscara de subred y la puerta de enlace para la interfaz de red DATA 0 del dispositivo de StorSimple.
   * Servidor DNS principal: el cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) se ejecuta en segundo plano. Establece la configuración de DNS para la solución de StorSimple.
   * Servidor NTP: el cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) se ejecuta en segundo plano. Establece la configuración del servidor NTP para la solución de StorSimple.
   * Proxy web opcional: el cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) se ejecuta en segundo plano. Establece y habilita la configuración de proxy web para la solución de StorSimple.
3. Configure la contraseña: el paso siguiente consiste en configurar la contraseña del administrador de dispositivos.
   La contraseña del administrador de dispositivos se usa para iniciar sesión en el dispositivo. La contraseña predeterminada del dispositivo es **Password1**.
        
     > [!IMPORTANT]
     > Las contraseñas se recopilan antes del registro, pero solo se aplican después de registrar correctamente el dispositivo. Si hay un error al aplicar una contraseña, se le pedirá que proporcione la contraseña de nuevo hasta que se recopilen las contraseñas necesarias (que cumplan los requisitos de complejidad).
     
4. Registre el dispositivo: el último paso consiste en registrar el dispositivo con el servicio StorSimple Device Manager en ejecución en Microsoft Azure. Para ello, debe [obtener la clave de registro del servicio](storsimple-8000-manage-service.md#get-the-service-registration-key) desde Azure Portal e indicarla en el Asistente para instalación. **Una vez registrado correctamente el dispositivo, se le indicará una clave de cifrado de datos del servicio. Asegúrese de mantener esta clave de cifrado en una ubicación segura, ya que será necesaria para registrar todos los dispositivos posteriores en el servicio.**

## <a name="common-errors-during-device-deployment"></a>Errores comunes durante la implementación de dispositivos
En las tablas siguientes se enumeran los errores comunes que pueden aparecer en los siguientes casos:

* Al realizar la configuración de red necesaria.
* Al realizar la configuración de proxy web opcional.
* Configure la contraseña del administrador de dispositivos.
* Al registrar el dispositivo.

## <a name="errors-during-the-required-network-settings"></a>Errores durante la configuración de red necesaria
| Nº | Mensaje de error | Causas posibles | Acción recomendada |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Este comando solo se puede ejecutar en el controlador activo. |La configuración se estaba realizando en el controlador pasivo. |Ejecute este comando desde el controlador activo. Para obtener más información, consulte [Identificación de un controlador activo en el dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: El dispositivo no está listo. |Hay problemas con la conectividad de red en DATA 0. |Compruebe la conectividad de red física en DATA 0. |
| 3 |Invoke-HcsSetupWizard: Hay un conflicto de dirección IP con otro sistema en la red (excepción de HRESULT: 0x80070263). |Otro sistema estaba usando la dirección IP indicada para DATA 0. |Indique una dirección IP nueva que no esté en uso. |
| 4 |Invoke-HcsSetupWizard: Error en un recurso de clúster (excepción de HRESULT: 0x800713AE). |VIP duplicada. La dirección IP proporcionada ya está en uso. |Indique una dirección IP nueva que no esté en uso. |
| 5 |Invoke-HcsSetupWizard: Dirección IPv4 no válida. |La dirección IP se ha indicado con un formato incorrecto. |Compruebe el formato e indique de nuevo la dirección IP. Para obtener más información, consulte [Direcciones Ipv4][1]. |
| 6 |Invoke-HcsSetupWizard: Dirección IPv6 no válida. |La dirección IP se ha indicado con un formato incorrecto. |Compruebe el formato e indique de nuevo la dirección IP. Para obtener más información, consulte [Direcciones Ipv6][2]. |
| 7 |Invoke-HcsSetupWizard: No hay más extremos disponibles desde el asignador de extremos. (Excepción de HRESULT: 0x800706D9). |La funcionalidad de clúster no funciona. |[Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para conocer los pasos siguientes. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Errores durante la configuración del proxy web opcional
| Nº | Mensaje de error | Causas posibles | Acción recomendada |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Parámetro no válido (excepción de HRESULT: 0x80070057) |Uno de los parámetros proporcionados para la configuración del proxy no es válido. |El URI no se indica con el formato correcto. Utilice el siguiente formato: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: Servidor RPC no disponible (excepción de HRESULT: 0x800706ba) |La causa principal es una de las siguientes:<ol><li>El clúster no está activo.</li><li>El controlador pasivo no puede comunicarse con el controlador activo, y el comando se ejecuta desde el controlador pasivo.</li></ol> |Dependiendo de la causa principal:<ol><li>[Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para asegurarse de que el clúster está activo.</li><li>Ejecute el comando desde el controlador activo. Si desea ejecutar el comando desde el controlador pasivo, deberá asegurarse de que el controlador pasivo puede comunicarse con el controlador activo. Tendrá que [ponerse en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) si se interrumpe la conectividad.</li></ol> |
| 3 |Invoke-HcsSetupWizard: Error en la llamada RPC (excepción de HRESULT: 0x800706be) |El clúster está inactivo. |[Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para asegurarse de que el clúster está activo. |
| 4 |Invoke-HcsSetupWizard: El recurso de clúster no se encontró (excepción de HRESULT: 0x8007138f). |No se encuentra el recurso de clúster. Esto puede ocurrir si la instalación no se realizó correctamente. |Puede que necesite restablecer la configuración predeterminada de fábrica del dispositivo. [Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para crear un recurso de clúster. |
| 5 |Invoke-HcsSetupWizard: El recurso de clúster no está en línea (excepción de HRESULT: 0x8007138c). |Los recursos de clúster no están en línea. |[Póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para conocer los pasos siguientes. |

## <a name="errors-related-to-device-administrator-password"></a>Errores relacionados con la contraseña del administrador de dispositivos
La contraseña predeterminada del administrador de dispositivos es **Password1**. Esta contraseña expira tras el primer inicio de sesión; por lo tanto, deberá utilizar el Asistente para instalación para cambiarla. Debe proporcionar una nueva contraseña de administrador de dispositivos al registrar el dispositivo por primera vez. 

Asegúrese de que las contraseñas cumplen los requisitos siguientes:

* La contraseña del administrador de dispositivos debe tener entre 8 y 15 caracteres.
* Las contraseñas deben contener tres de los cuatro siguientes tipos de caracteres: minúsculas, mayúsculas, números y caracteres especiales. 
* La contraseña no puede ser la misma que ninguna de las 24 últimas contraseñas.

Además, tenga en cuenta que las contraseñas expiran cada año y solo se pueden cambiar después de registrar correctamente el dispositivo. Si se produce un error en el registro por cualquier motivo, las contraseñas no se cambiarán.

Para más información sobre la contraseña del administrador de dispositivos, vaya a [Use the StorSimple Device Manager service to change your StorSimple password](storsimple-8000-change-passwords.md) (Uso del servicio StorSimple Device Manager para cambiar la contraseña de StorSimple).

Puede encontrar uno o varios de los siguientes errores al configurar las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager.

| Nº | Mensaje de error | Acción recomendada |
| --- | --- | --- |
| 1 |La contraseña supera la longitud máxima. |La contraseña de administrador del dispositivo debe tener entre 8 y 15 caracteres. |
| 2 |La contraseña no cumple la longitud necesaria. |La contraseña de administrador del dispositivo debe tener entre 8 y 15 caracteres.|
| 3 |La contraseña debe contener caracteres en minúsculas. |Las contraseñas deben contener tres de los siguientes cuatro tipos de caracteres: minúsculas, mayúsculas, números y caracteres especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 4 |La contraseña debe contener caracteres numéricos. |Las contraseñas deben contener tres de los siguientes cuatro tipos de caracteres: minúsculas, mayúsculas, números y caracteres especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 5 |La contraseña debe contener caracteres especiales. |Las contraseñas deben contener tres de los siguientes cuatro tipos de caracteres: minúsculas, mayúsculas, números y caracteres especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 6 |La contraseña debe contener tres de los siguientes cuatro tipos de caracteres: mayúsculas, minúsculas, números y caracteres especiales. |La contraseña no contiene los tipos de caracteres necesarios. Asegúrese de que la contraseña cumple estos requisitos. |
| 7 |El parámetro no coincide con la confirmación. |Asegúrese de que la contraseña cumple todos los requisitos y de que la ha escrito correctamente. |
| 8 |La contraseña no coincide con el valor predeterminado. |La contraseña predeterminada es *Password1*. Debe cambiar esta contraseña después de iniciar sesión por primera vez. |
| 9 |La contraseña que ha escrito no coincide con la contraseña del dispositivo. Vuelva a escribir la contraseña. |Compruebe la contraseña y escríbala de nuevo. |

Las contraseñas se recopilan antes de que el dispositivo se registre, pero solo se aplican después de un registro correcto. El flujo de trabajo de recuperación de contraseñas requiere que el dispositivo esté registrado.

> [!IMPORTANT]
> En general, si se produce un error al intentar aplicar una contraseña, el software intenta recopilar repetidamente la contraseña hasta que lo consigue. En raras ocasiones, no se puede aplicar la contraseña. En este caso, puede registrar el dispositivo y continuar; sin embargo, las contraseñas no se modificarán. Puede cambiar la contraseña del administrador de dispositivos después del registro desde Azure Portal.


La contraseña se puede restablecer en Azure Portal mediante el servicio StorSimple Device Manager. Para más información, consulte [Change the device administrator password](storsimple-8000-change-passwords.md#change-the-device-administrator-password) (Cambio de la contraseña del administrador de dispositivos).

## <a name="errors-during-device-registration"></a>Errores durante el registro de dispositivos
Para registrar el dispositivo se usa el servicio StorSimple Device Manager que se ejecuta en Microsoft Azure. Pueden surgir uno o varios de los siguientes problemas durante el registro del dispositivo.

| Nº | Mensaje de error | Causas posibles | Acción recomendada |
| --- | --- | --- | --- |
| 1 |Error 350027: No se pudo registrar el dispositivo en StorSimple Device Manager. | |Espere unos minutos y vuelva a intentar la operación. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 2 |Error 350013: Se ha producido un error al registrar el dispositivo. Esto puede deberse a que la clave de registro del servicio no es correcta. | |Vuelva a registrar el dispositivo con la clave de registro del servicio correcta. Para obtener más información, consulte [Obtención de la clave de registro de servicio](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Error 350063: La autenticación en el servicio StorSimple Device Manager se realizó correctamente pero hubo un error en el registro. Vuelva a intentar la operación más tarde. |Este error indica que la autenticación con ACS se ha realizado correctamente pero se ha producido un error en la llamada de registro realizada al servicio. Esto puede deberse a un problema de red esporádico. |Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 4 |Error 350049: No se pudo alcanzar el servicio durante el registro. |Cuando se realiza la llamada al servicio, se recibe una excepción de web. En algunos casos, este problema se puede corregir volviendo a intentar la operación más adelante. |Compruebe la dirección IP y el nombre DNS y, a continuación, vuelva a intentar la operación. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) |
| 5 |Error 350031: El dispositivo ya se ha registrado. | |No es necesaria ninguna acción. |
| 6 |Error 350016: Error de registro de dispositivo. | |Asegúrese de que la clave de registro es correcta. |
| 7 |Invoke-HcsSetupWizard: Se ha producido un error al registrar el dispositivo; se puede deber a una dirección IP o un nombre DNS incorrectos. Compruebe la configuración de red y vuelva a intentarlo. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md). (Error 350050) |Asegúrese de que el dispositivo puede hacer ping a la red externa. Si no tiene conectividad con la red externa, se puede producir este error en el registro. Este error puede ser una combinación de uno o varios de los siguientes elementos:<ul><li>IP incorrecta</li><li>Subred incorrecta</li><li>Puerta de enlace incorrecta</li><li>Configuración DNS incorrecta</li></ul> |Consulte los pasos del [Ejemplo de solución de problemas paso a paso](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: no se pudo realizar la operación actual debido a un error de servicio interno [0x1FBE2]. Vuelva a intentar la operación más tarde. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. |Se trata de un error genérico que se produce para todos los errores invisibles de usuario del servicio o del agente. La razón más habitual es un error en la autenticación de ACS. El error puede deberse a problemas con la configuración del servidor NTP y a una configuración incorrecta de la hora en el dispositivo. |Corrija la hora (si hay problemas) y, a continuación, vuelva a intentar la operación de registro. Si utiliza el comando Set-HcsSystem -Timezone para ajustar la zona horaria, ponga en mayúsculas cada palabra de la zona horaria (por ejemplo "Hora Estándar del Pacífico").  Si persiste este problema, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para conocer los pasos siguientes. |
| 9 |Advertencia: no se pudo activar el dispositivo. Las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager no han cambiado. |Si se produce un error en el registro, las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager no cambian. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Herramientas para solucionar problemas en implementaciones de StorSimple
StorSimple incluye varias herramientas que puede utilizar para solucionar problemas de la solución StorSimple. Entre ellos se incluyen los siguientes:

* Paquetes de soporte técnico y registros de dispositivo
* Cmdlets diseñados específicamente para la solución de problemas

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Paquetes de soporte y registros de dispositivo disponibles para la solución de problemas
Un paquete de soporte contiene todos los registros relevantes que pueden ayudar al equipo de soporte técnico de Microsoft a solucionar los problemas del dispositivo. Puede usar Windows PowerShell para StorSimple para generar un paquete de soporte cifrado que posteriormente puede compartir con el personal de soporte técnico.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Para ver los registros o el contenido del paquete de soporte
1. Use Windows PowerShell para StorSimple para generar un paquete de soporte, tal y como se describe en [Crear y administrar paquetes de soporte técnico](storsimple-8000-create-manage-support-package.md).
2. Descargue el [script de descifrado](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente en el equipo cliente.
3. Utilice este [procedimiento detallado](storsimple-8000-create-manage-support-package.md#edit-a-support-package) para abrir y descifrar el paquete de soporte.
4. Los registros del paquete de soporte descifrado están en formato etw/etvx. Para ver estos archivos en el Visor de eventos de Windows, realice lo siguiente:
   
   1. Ejecute el comando **eventvwr** en el cliente Windows. Se iniciará el Visor de eventos.
   2. En el panel **Acciones**, haga clic en **Abrir registro guardado** y elija los archivos de registro con formato etvx/etw (el paquete de soporte). Ahora puede ver el archivo. Después de abrirlo, puede hacer clic con el botón derecho y guardarlo como texto.
      
      > [!IMPORTANT]
      > También puede usar el cmdlet **Get-WinEvent** para abrir estos archivos en Windows PowerShell. Para obtener más información, consulte [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) en la documentación de referencia de cmdlets de Windows PowerShell.
     
5. Cuando los registros se abran en el Visor de eventos, busque los siguientes registros, que contienen problemas relacionados con la configuración del dispositivo:
   
   * hcs_pfconfig/registro operativo
   * hcs_pfconfig/registro de configuración
6. En los archivos de registro, busque cadenas relacionadas con los cmdlets a los que llama el Asistente para instalación. Consulte [Proceso del Asistente para instalación por primera vez](#first-time-setup-wizard-process) para obtener una lista de estos cmdlets.
7. Si no puede averiguar la causa del problema, puede [ponerse en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para conocer los pasos siguientes. Siga los pasos de [Crear una solicitud de soporte](storsimple-8000-contact-microsoft-support.md#create-a-support-request) cuando se ponga en contacto con el servicio de soporte técnico de Microsoft para obtener ayuda.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets disponibles para solucionar problemas
Use los siguientes cmdlets de Windows PowerShell para detectar errores de conectividad.

* `Get-NetAdapter`: use este cmdlet para detectar el mantenimiento de las interfaces de red.
* `Test-Connection`: use este cmdlet para comprobar la conectividad de red, dentro y fuera de la red.
* `Test-HcsmConnection`: use este cmdlet para comprobar la conectividad de un dispositivo registrado correctamente.
* `Sync-HcsTime`: use este cmdlet para mostrar la hora del dispositivo y forzar una sincronización de hora con el servidor NTP.
* `Enable-HcsPing` y `Disable-HcsPing`: use estos cmdlets para permitir a los hosts hacer ping a las interfaces de red del dispositivo StorSimple. De manera predeterminada, las interfaces de red de StorSimple no responden a las solicitudes de ping.
* `Trace-HcsRoute`: use este cmdlet como una herramienta de seguimiento de ruta. Este envía paquetes a cada enrutador de la ruta hasta el destino final durante un período de tiempo y, a continuación, calcula los resultados basándose en los paquetes devueltos en cada salto. Puesto que `Trace-HcsRoute` muestra el grado de pérdida de paquetes en un determinado vínculo o enrutador, puede determinar qué enrutadores o vínculos podrían estar causando problemas de red.
* `Get-HcsRoutingTable`: use este cmdlet para mostrar la tabla de enrutamiento de la IP local.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Solución de problemas con el cmdlet Get-NetAdapter
Al configurar las interfaces de red para una implementación de dispositivo por primera vez, el estado del hardware no está disponible en la interfaz de usuario del servicio StorSimple Device Manager porque el dispositivo todavía no está registrado en el servicio. Además, es posible que la hoja **Hardware health** (Mantenimiento de hardware) no siempre refleje correctamente el estado del dispositivo, en especial si hay problemas que afectan a la sincronización del servicio. En estos casos, puede usar el cmdlet `Get-NetAdapter` para determinar el mantenimiento y el estado de las interfaces de red.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Para ver una lista de todos los adaptadores de red del dispositivo
1. Inicie Windows PowerShell para StorSimple y escriba `Get-NetAdapter`. 
2. Use la salida del cmdlet `Get-NetAdapter` y las instrucciones siguientes para conocer el estado de la interfaz de red.
   
   * Si el mantenimiento es correcto y la interfaz está habilitada, el estado de **ifIndex** se muestra como **Up**.
   * Si el mantenimiento es correcto pero la interfaz no está conectada físicamente (mediante un cable de red), el estado de **ifIndex** se muestra como **Disabled**.
   * Si el mantenimiento es correcto pero la interfaz no está habilitada, el estado de **ifIndex** se muestra como **NotPresent**.
   * Si la interfaz no existe, no aparece en la lista. La interfaz de usuario del servicio StorSimple Device Manager seguirá mostrando esta interfaz con un estado de error.

Para obtener más información sobre cómo usar este cmdlet, vaya a [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) en la referencia de cmdlets de Windows PowerShell.

En las secciones siguientes se muestran ejemplos de salida del cmdlet `Get-NetAdapter` .

 En estos ejemplos, el controlador 0 actuó como controlador pasivo, con la siguiente configuración:

* Existían las interfaces de red DATA 0, DATA 1, DATA 2 y DATA 3 en el dispositivo.
* No había tarjetas de interfaz de red DATA 4 y DATA 5, por lo que no aparecen en la salida.
* La interfaz DATA 0 estaba habilitada.

El controlador 1 actuó como controlador activo, con la siguiente configuración:

* Existían las interfaces de red DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 y DATA 5 en el dispositivo.
* La interfaz DATA 0 estaba habilitada.

**Ejemplo de salida: controlador 0**

A continuación se puede ver la salida del controlador 0 (el controlador pasivo). DATA 1, DATA 2 y DATA 3 no están conectadas. DATA 4 y DATA 5 no se muestran porque no están presentes en el dispositivo.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Ejemplo de salida: controlador 1**

A continuación se puede ver la salida del controlador 1 (el controlador activo). Solo está configurada y en funcionamiento la interfaz de red DATA 0 del dispositivo.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Solución de problemas con el cmdlet Test-Connection
Puede usar el cmdlet `Test-Connection` para determinar si el dispositivo StorSimple puede conectarse a la red externa. Si todos los parámetros de redes, incluidos los de DNS, están configurados correctamente en el Asistente para instalación, puede usar el cmdlet `Test-Connection` para hacer ping a una dirección conocida fuera de la red (por ejemplo, outlook.com).

Debe habilitar ping para solucionar problemas de conectividad con este cmdlet si ping se deshabilita.

Consulte los siguientes ejemplos de salida del cmdlet `Test-Connection` .

> [!NOTE]
> En el primer ejemplo, el dispositivo está configurado con un DNS incorrecto. En el segundo ejemplo, el DNS es correcto.

**Ejemplo de salida: DNS incorrecto**

En el ejemplo siguiente, no hay ninguna salida para las direcciones IPV4 e IPV6, lo que indica que no se ha resuelto el DNS. Esto significa que no hay conectividad con la red externa y que se debe indicar un DNS correcto.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Ejemplo de salida: DNS correcto**

En el ejemplo siguiente, el DNS devuelve la dirección IPV4, lo que indica que está configurado correctamente. Así se confirma que hay conectividad con la red externa.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Solución de problemas con el cmdlet Test-HcsmConnection
Use el cmdlet `Test-HcsmConnection` con un dispositivo que ya esté conectado al servicio StorSimple Device Manager y esté registrado en él. Este cmdlet permite comprobar la conectividad entre un dispositivo registrado y el servicio StorSimple Device Manager correspondiente. Puede ejecutar este comando en Windows PowerShell para StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Para ejecutar el cmdlet Test-HcsmConnection
1. Asegúrese de que el dispositivo está registrado.
2. Compruebe el estado del dispositivo. Si el dispositivo está desactivado, en modo de mantenimiento o sin conexión, puede aparecer uno de los errores siguientes:
   
   * ErrorCode.CiSDeviceDecommissioned: indica que el dispositivo está desactivado.
   * ErrorCode.DeviceNotReady: indica que el dispositivo se encuentra en modo de mantenimiento.
   * ErrorCode.DeviceNotReady: indica que el dispositivo no está en línea.
3. Compruebe que el servicio StorSimple Device Manager se esté ejecutando (use el cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx)). Si no se está ejecutando el servicio, pueden aparecer los errores siguientes:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError: indica que se produjo una excepción al ejecutar Get-ClusterResource.
4. Compruebe el token del servicio de control de acceso (ACS). Si se produce una excepción de web, puede deberse a un problema de puerta de enlace, a la falta de una autenticación de proxy, a un DNS incorrecto o a un error de autenticación. Pueden aparecer los errores siguientes:
   
   * ErrorCode.CiSApplianceGateway: indica una excepción de HttpStatusCode.BadGateway (el servicio de resolución de nombres no pudo resolver el nombre de host).
   * ErrorCode.CiSApplianceProxy: indica una excepción de HttpStatusCode.ProxyAuthenticationRequired con código de estado HTTP 407 (el cliente no se pudo autenticar con el servidor proxy).
   * ErrorCode.CiSApplianceDNSError: indica una excepción de WebExceptionStatus.NameResolutionFailure (el servicio de resolución de nombres no pudo resolver el nombre de host).
   * ErrorCode.CiSApplianceACSError: indica que el servicio ha devuelto un error de autenticación, aunque existe conectividad.
     
     Si no se produce una excepción web, compruebe el elemento ErrorCode.CiSApplianceFailure. Esto indica que el aparato ha fallado.
5. Compruebe la conectividad del servicio en la nube. Si el servicio inicia una excepción de web, pueden aparecer los errores siguientes:
   
   * ErrorCode.CiSApplianceGateway: indica una excepción de HttpStatusCode.BadGateway (un servidor proxy intermedio recibió una solicitud incorrecta de otro proxy o del servidor original).
   * ErrorCode.CiSApplianceProxy: indica una excepción de HttpStatusCode.ProxyAuthenticationRequired con código de estado HTTP 407 (el cliente no se pudo autenticar con el servidor proxy).
   * ErrorCode.CiSApplianceDNSError: indica una excepción de WebExceptionStatus.NameResolutionFailure (el servicio de resolución de nombres no pudo resolver el nombre de host).
   * ErrorCode.CiSApplianceACSError: indica que el servicio ha devuelto un error de autenticación, aunque existe conectividad.
     
     Si no se produce una excepción web, compruebe el elemento ErrorCode.CiSApplianceSaasServiceError. Esto indica un problema con el servicio StorSimple Device Manager.
6. Compruebe la conectividad del Bus de servicio de Azure. ErrorCode.CiSApplianceServiceBusError: indica que el dispositivo no se puede conectar al Bus de servicio.

En los archivos de registro CiSCommandletLog0Curr.errlog y CiSAgentsvc0Curr.errlog encontrará más información como, por ejemplo, los detalles de la excepción.

Para obtener más información sobre cómo usar el cmdlet, vaya a [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) en la documentación de referencia de Windows PowerShell.

> [!IMPORTANT]
> Puede ejecutar este cmdlet para ambos controladores, activo y pasivo.

Consulte los siguientes ejemplos de salida del cmdlet `Test-HcsmConnection` .

**Ejemplo de salida: dispositivo registrado correctamente mediante la actualización número 3 de StorSimple**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Ejemplo de salida: dispositivo sin conexión** 

Este ejemplo corresponde a un dispositivo que muestra el estado **Sin conexión** en Azure Portal.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

El dispositivo no se pudo conectar con la configuración del proxy web actual. Puede tratarse de un problema con la configuración del proxy web o con la conectividad de red. En este caso, debe asegurarse de que la configuración del proxy web es correcta y de que los servidores proxy web están en línea y accesibles.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Solucionar problemas con el cmdlet Sync-HcsTime
Use este cmdlet para mostrar la hora del dispositivo. Si la hora del dispositivo tiene un desfase con respecto al servidor NTP, puede usar este cmdlet para forzar la sincronización de la hora con el servidor NTP.
- Si el desfase entre el dispositivo y el servidor NTP supera los 5 minutos, aparecerá una advertencia. 
- Si el desfase es superior a 15 minutos, el dispositivo se desconectará. Todavía puede usar este cmdlet para forzar una sincronización de hora. 
- Sin embargo, si el desfase es superior a 15 horas, no podrá para forzar la sincronización de hora y aparecerá un mensaje de error.

**Salida de ejemplo: sincronización de hora forzada mediante Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Solucionar problemas con los cmdlets Enable-HcsPing y Disable-HcsPing
Use estos cmdlets para asegurarse de que las interfaces de red en el dispositivo responden a las solicitudes de ping ICMP. De manera predeterminada, las interfaces de red de StorSimple no responden a las solicitudes de ping. Con este cmdlet sabrá con facilidad si el dispositivo está en línea y accesible.

**Salida de ejemplo: Enable-HcsPing y Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Solucionar problemas con el cmdlet Trace-HcsRoute
Use este cmdlet como una herramienta de seguimiento de ruta. Este envía paquetes a cada enrutador de la ruta hasta el destino final durante un período de tiempo y, a continuación, calcula los resultados basándose en los paquetes devueltos en cada salto. Puesto que este cmdlet muestra el grado de pérdida de paquetes en un vínculo o enrutador determinados, puede determinar qué enrutadores o vínculos podrían estar causando problemas de red.

**Salida de ejemplo que muestra cómo realizar un seguimiento de la ruta de un paquete con Trace-HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Solucionar problemas con el cmdlet Get-HcsRoutingTable
Use este cmdlet para ver la tabla de enrutamiento del dispositivo StorSimple. Una tabla de enrutamiento es un conjunto de reglas que le pueden ayudar a determinar hacia dónde se dirigen los paquetes de datos transmitidos a través de una red de protocolo de Internet (IP).

La tabla de enrutamiento muestra las interfaces y la puerta de enlace que enruta los datos hacia las redes especificadas. Asimismo, le ofrece la métrica de enrutamiento encargada de decidir qué ruta de acceso tomar para llegar a un destino determinado. Cuanto más bajo sea el valor de la métrica de enrutamiento, mayor será la preferencia.

Por ejemplo, si tiene dos interfaces de red, DATA 2 y DATA 3, conectadas a Internet. Si las métricas de enrutamiento de DATA 2 y DATA 3 son 15 y 261 respectivamente, DATA 2 será la interfaz seleccionada para conectar a Internet ya que su métrica de enrutamiento es menor.

Si ejecuta la actualización número 1 en el dispositivo StorSimple, la interfaz de red DATA 0 tendrá mayor preferencia para el tráfico de la nube. Esto implica que, incluso si hay otras interfaces habilitadas en la nube, el tráfico de nube se enrutaría a través de DATA 0.

Si ejecuta el cmdlet `Get-HcsRoutingTable` sin especificar ningún parámetro (tal y como se muestra en el ejemplo siguiente), el cmdlet dará como resultado las tablas de enrutamiento de IPv4 e IPv6. Como alternativa, puede especificar `Get-HcsRoutingTable -IPv4` o `Get-HcsRoutingTable -IPv6` para obtener una tabla de enrutamiento pertinente.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Ejemplo de solución de problemas de StorSimple paso a paso
En el ejemplo siguiente se muestra la solución de problemas paso a paso de una implementación de StorSimple. En el escenario de ejemplo, se produce un error en el registro del dispositivo; el mensaje de error indica que la configuración de red o el nombre DNS son incorrectos.

El mensaje de error devuelto es:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

El error puede deberse a alguna de las siguientes causas:

* Instalación incorrecta de hardware
* Interfaces de red defectuosas
* Dirección IP, máscara de subred, puerta de enlace, servidor DNS principal o proxy web incorrectos
* Clave de registro incorrecta
* Configuración de firewall incorrecta

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Para buscar y corregir el problema con el registro del dispositivo
1. Compruebe la configuración del dispositivo: en el controlador activo, ejecute `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > El Asistente para instalación se debe ejecutar en el controlador activo. Para comprobar que está conectado al controlador activo, observe el banner que aparece en la consola serie. Este banner indica si está conectado al controlador 0 o al 1, y si es el controlador activo o pasivo. Para más información, vaya a [Identificación de un controlador activo en el dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Asegúrese de que los cables del dispositivo están conectados correctamente: compruebe el cableado de red en la parte posterior del dispositivo. Los cables son específicos del modelo de dispositivo. Para obtener más información, consulte [Desempaquetar, montar en bastidor y colocar los cables del dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [Desempaquetar, montar en bastidor y colocar los cables del dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Si utiliza puertos de red de 10 GbE, deberá usar los adaptadores QSFP-SFP y los cables SFP suministrados. Para obtener más información, consulte la [lista de cables, conmutadores y transceptores que recomienda para los puertos de 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Compruebe el mantenimiento de la interfaz de red:
   
   * Utilice el cmdlet Get-NetAdapter para detectar el estado de mantenimiento de las interfaces de red para DATA 0. 
   * Si el vínculo no funciona, el estado de **ifindex** indicará que la interfaz no está funcionando. Deberá comprobar entonces la conexión de red del puerto al dispositivo y al conmutador. También deberá descartar los cables defectuosos. 
   * Si sospecha que se ha producido un error en el puerto de DATA 0 del controlador activo y desea confirmarlo, conéctese al puerto de DATA 0 del controlador 1. Para ello, desconecte el cable de red de la parte posterior del dispositivo del controlador 0, conecte el cable al controlador 1 y, a continuación, vuelva a ejecutar el cmdlet Get-NetAdapter.
     Si se produce un error en el puerto de DATA 0 de un controlador, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para conocer los pasos siguientes. Es posible que deba reemplazar el controlador del sistema.
4. Compruebe la conectividad con el conmutador:
   
   * Asegúrese de que las interfaces de red DATA 0 de los controladores 0 y 1 del revestimiento de hardware principal están en la misma subred. 
   * Compruebe el concentrador o el enrutador. Por lo general debe conectar ambos controladores al mismo concentrador o enrutador. 
   * Asegúrese de que los conmutadores que usa para la conexión tienen DATA 0 para ambos controladores en la misma vLAN.
5. Elimine los errores de usuario:
   
   * Vuelva a ejecutar el Asistente para instalación (ejecute **Invoke-HcsSetupWizard**) y escriba de nuevo los valores para asegurarse de que no hay ningún error. 
   * Compruebe la clave de registro utilizada. Se puede usar la misma clave del registro para conectar varios dispositivos a un servicio StorSimple Device Manager. Siga el procedimiento de [Obtención de la clave de registro de servicio](storsimple-8000-manage-service.md#get-the-service-registration-key) para asegurarse de que está utilizando la clave de registro correcta.
     
     > [!IMPORTANT]
     > Si está ejecutando varios servicios, asegúrese de que se usa la clave de registro correspondiente al servicio adecuado para registrar el dispositivo. Si ha registrado un dispositivo con el servicio StorSimple Device Manager incorrecto, deberá [ponerse en contacto con el soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para conocer los pasos siguientes. Tal vez sea necesario realizar un restablecimiento de fábrica del dispositivo (que puede provocar la pérdida de datos) para, a continuación, conectarlo al servicio deseado.
     > 
     > 
6. Use el cmdlet Test-Connection para comprobar que dispone de conectividad con la red externa. Para obtener más información, vaya a [Solución de problemas con el cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Compruebe si existen interferencias de firewall. Si ha comprobado que la configuración de IP virtual (VIP), subred, puerta de enlace y DNS es correcta y todavía existen problemas de conectividad, es posible que el firewall esté bloqueando la comunicación entre el dispositivo y la red externa. Debe asegurarse de que los puertos 80 y 443 están disponibles en el dispositivo de StorSimple para la comunicación saliente. Para obtener más información, consulte [Requisitos de red para el dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Revise los registros. Vaya a [Paquetes de soporte y registros de dispositivo disponibles para la solución de problemas](#support-packages-and-device-logs-available-for-troubleshooting).
9. Si los pasos anteriores no resuelven el problema, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md) para obtener ayuda.

## <a name="next-steps"></a>Pasos siguientes
[Aprenda a usar la herramienta Diagnostics para solucionar los problemas de un dispositivo StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 

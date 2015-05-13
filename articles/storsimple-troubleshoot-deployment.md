<properties 
   pageTitle="Solución de problemas de implementación de dispositivos de StorSimple"
   description="Se describe cómo diagnosticar y corregir los errores que se producen al implementar StorSimple por primera vez."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/06/2015"
   ms.author="v-sharos" />

# Solución de problemas de implementación de dispositivos de StorSimple

## Información general

Este artículo proporciona instrucciones útiles para la solución de problemas relacionados con la implementación de Microsoft Azure StorSimple. Se describen los problemas comunes, las causas posibles y los pasos recomendados, como ayuda para resolver los problemas que pueden producirse al configurar StorSimple. Esta información se aplica tanto al dispositivo físico local de StorSimple como al dispositivo virtual.

> [AZURE.NOTE]Los problemas relacionados con la configuración del dispositivo pueden producirse al implementar el dispositivo por primera vez o bien más adelante, cuando el dispositivo está operativo. Este artículo se centra en la solución de problemas relacionados con la implementación por primera vez. Para solucionar problemas de un dispositivo operativo, vaya a [Solución de problemas de un dispositivo StorSimple operativo](storsimple-troubleshoot-operational-device.md).

En este artículo también se describen las herramientas para solucionar problemas relacionados con implementaciones de StorSimple y se incluye un ejemplo de solución de problemas paso a paso.

## Problemas de implementación por primera vez

Si surge un problema al implementar el dispositivo por primera vez, tenga en cuenta lo siguiente:

- Si se trata de problemas de un dispositivo físico, asegúrese de que el hardware está instalado y configurado como se describe en [Instalación de hardware de su dispositivo](https://msdn.microsoft.com/library/azure/dn772375.aspx).
- Compruebe los requisitos previos para la implementación. Asegúrese de que dispone de toda la información que se describe en la [lista de comprobación de implementación](storsimple-deployment-walkthrough.md\#pre-installation checklist\).
- Revise las notas de la versión de StorSimple para ver si se describe el problema. Las notas de la versión incluyen soluciones alternativas a problemas de instalación conocidos. 

Durante la implementación del dispositivo, los problemas más habituales a los que se enfrentan los usuarios se producen cuando se ejecuta el Asistente para instalación y cuando se registra el dispositivo a través de Windows PowerShell para StorSimple. \(Windows PowerShell para StorSimple se utiliza para registrar y configurar el dispositivo de StorSimple. Para obtener más información sobre el registro de dispositivos, consulte [Registro del dispositivo](https://msdn.microsoft.com/library/azure/dn757742.aspx)\).

Las siguientes secciones le ayudarán a resolver los problemas que aparecen al configurar el dispositivo de StorSimple por primera vez.

## Proceso del Asistente para instalación por primera vez

Los pasos siguientes resumen el proceso del Asistente para instalación. Para obtener información de configuración detallada, consulte el [Tutorial de implementación de StorSimple](storsimple-deployment-walkthrough.md).

1. Ejecute el cmdlet [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) para iniciar el Asistente para instalación, que le guiará por el resto de los pasos. 
2. Configure la red: el Asistente para instalación permite configurar la red para la interfaz de red DATA 0 del dispositivo de StorSimple. Entre los ajustes se incluyen los siguientes:
  - IP virtual \(VIP\), máscara de subred y puerta de enlace: el cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) se ejecuta en segundo plano. Configura la dirección IP, la máscara de subred y la puerta de enlace para la interfaz de red DATA 0 del dispositivo de StorSimple.
  - Servidor DNS principal: el cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) se ejecuta en segundo plano. Establece la configuración de DNS para la solución de StorSimple.
  - Servidor NTP: el cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) se ejecuta en segundo plano. Establece la configuración del servidor NTP para la solución de StorSimple.
  - Proxy web opcional: el cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) se ejecuta en segundo plano. Establece y habilita la configuración de proxy web para la solución de StorSimple.
3. Configure las contraseñas: el paso siguiente consiste en configurar las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager.
  - La contraseña del administrador de dispositivos se usa para iniciar sesión en el dispositivo. La contraseña predeterminada del dispositivo es *Password1*.
  - La contraseña de StorSimple Snapshot Manager se requiere cuando se configura un dispositivo para utilizar este complemento. Debe establecer primero la contraseña en el Asistente para instalación y, a continuación, puede establecerla y cambiarla desde el servicio StorSimple Manager. Esta contraseña autentica el dispositivo con StorSimple Snapshot Manager.
 
    > [AZURE.IMPORTANT]Las contraseñas se recopilan antes del registro, pero solo se aplican después de registrar correctamente el dispositivo. Si hay un error al aplicar una contraseña, se le pedirá que proporcione la contraseña de nuevo hasta que se recopilen las contraseñas necesarias \(que cumplan los requisitos de complejidad\).

4. Registre el dispositivo: el último paso consiste en registrar el dispositivo con el servicio StorSimple Manager en ejecución en Microsoft Azure. Para ello, debe [obtener la clave de registro del servicio](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03) desde el Portal de administración de Azure e indicarla en el Asistente para instalación. Una vez registrado correctamente el dispositivo, se le indicará una clave de cifrado de datos del servicio. Asegúrese de mantener esta clave de cifrado en una ubicación segura, ya que será necesaria para registrar todos los dispositivos posteriores con el servicio.

## Errores comunes durante la implementación de dispositivos

En las tablas siguientes se enumeran los errores comunes que pueden aparecer en los siguientes casos:

- Al realizar la configuración de red necesaria.
- Al realizar la configuración de proxy web opcional.
- Al configurar las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager. 
- Al registrar el dispositivo. 

### Errores que se producen al realizar la configuración de red necesaria

| Nº| Mensaje de error | Causas posibles | Acción recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1 | Invoke-HcsSetupWizard: Este comando solo se puede ejecutar en el controlador activo. | La configuración se estaba realizando en el controlador pasivo.| Ejecute este comando desde el controlador activo. Para obtener más información, consulte [Identificación de un controlador activo en el dispositivo](https://msdn.microsoft.com/library/azure/dn790262.aspx).|
| 2 | Invoke-HcsSetupWizard: El dispositivo no está listo. | Hay problemas con la conectividad de red en DATA 0.| Compruebe la conectividad de red física en DATA 0.|
| 3 | Invoke-HcsSetupWizard: Hay un conflicto de dirección IP con otro sistema en la red \(excepción de HRESULT: 0x80070263\). | Otro sistema estaba usando la dirección IP indicada para DATA 0. | Indique una dirección IP nueva que no esté en uso.|
| 4 | Invoke-HcsSetupWizard: Error en un recurso de clúster \(excepción de HRESULT:0x800713AE\). | VIP duplicada. La dirección IP proporcionada ya está en uso.| Indique una dirección IP nueva que no esté en uso.|
| 5 | Invoke-HcsSetupWizard: Dirección IPv4 no válida. | La dirección IP se ha indicado con un formato incorrecto.| Compruebe el formato e indique de nuevo la dirección IP. Para obtener más información, consulte [Direcciones Ipv4][1]. |
| 6 | Invoke-HcsSetupWizard: Dirección IPv6 no válida. | La dirección IP se ha indicado con un formato incorrecto.| Compruebe el formato e indique de nuevo la dirección IP. Para obtener más información, consulte [Direcciones Ipv6][2].|
| 7 | Invoke-HcsSetupWizard: No hay más extremos disponibles desde el asignador de extremos \(excepción de HRESULT: 0x800706d9\). | La funcionalidad de clúster no funciona. | [Póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para conocer los pasos siguientes.

### Errores que se producen al realizar la configuración de proxy web opcional

| Nº| Mensaje de error | Causas posibles | Acción recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1 | Invoke-HcsSetupWizard: Parámetro no válido \(excepción de HRESULT: 0x80070057\) | Uno de los parámetros proporcionados para la configuración del proxy no es válido.| El URI no se indica con el formato correcto. Utilice el siguiente formato: http://*<IP address or FQDN of the web proxy server>\*: \*<TCP port number>\* |
| 2 | Invoke-HcsSetupWizard: Servidor RPC no disponible \(excepción de HRESULT: 0x800706ba\) | La causa principal es una de las siguientes:<ol><li>El clúster no está activo.</li><li>El controlador pasivo no puede comunicarse con el controlador activo y el comando se ejecuta desde el controlador pasivo.</li></ol> | Dependiendo de la causa principal:<ol><li>[Póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para asegurarse de que el clúster está activo.</li><li>Ejecute el comando desde el controlador activo. Si desea ejecutar el comando desde el controlador pasivo, deberá asegurarse de que el controlador pasivo puede comunicarse con el controlador activo. Tendrá que [ponerse en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) si se interrumpe la conectividad.</li></ol> |
| 3 | Invoke-HcsSetupWizard: Error en la llamada RPC \(excepción de HRESULT: 0x800706be\) | El clúster está inactivo. | [Póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para asegurarse de que el clúster está activo.|
| 4 | Invoke-HcsSetupWizard: El recurso de clúster no se encontró \(excepción de HRESULT: 0x8007138f\). | No se encuentra el recurso de clúster. Esto puede ocurrir si la instalación no se realizó correctamente. | Puede que necesite restablecer la configuración predeterminada de fábrica del dispositivo. [Póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para crear un recurso de clúster.|
| 5 | Invoke-HcsSetupWizard: El recurso de clúster no está en línea \(excepción de HRESULT: 0x8007138c\).| Los recursos de clúster no están en línea. | [Póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para conocer los pasos siguientes.|

### Errores que se producen al configurar las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager.

La contraseña predeterminada del administrador de dispositivos es *Password1*. Esta contraseña expira tras el primer inicio de sesión; por lo tanto, deberá utilizar el Asistente para instalación para cambiarla. Debe proporcionar una nueva contraseña de administrador de dispositivos al registrar el dispositivo por primera vez.

Si utiliza el software de StorSimple Snapshot Manager que se ejecuta en el host de Windows Server para administrar el dispositivo, también debe proporcionar una contraseña de StorSimple Snapshot Manager durante el registro por primera vez.

Asegúrese de que las contraseñas cumplen los requisitos siguientes:

- La contraseña del administrador de dispositivos debe tener entre 8 y 15 caracteres.
- La contraseña de StorSimple Snapshot Manager debe tener 14 o 15 caracteres.
- Las contraseñas deben contener tres de los cuatro siguientes tipos de caracteres: minúsculas, mayúsculas, números y caracteres especiales. 
- La contraseña no puede ser la misma que ninguna de las 24 últimas contraseñas.

Además, tenga en cuenta que las contraseñas expiran cada año y solo se pueden cambiar después de registrar correctamente el dispositivo. Si se produce un error en el registro por cualquier motivo, las contraseñas no se cambiarán. Para obtener más información sobre las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager, vaya a [Contraseñas](https://msdn.microsoft.com/library/azure/00fb2b46-e29e-4c4d-90a8-1dbb7db3672e#sec_02).

Puede encontrar uno o varios de los siguientes errores al configurar las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager.

| Nº| Mensaje de error | Acción recomendada |
| ---| ------------- | ------------------ | 
| 1 | La contraseña supera la longitud máxima. |Utilice una contraseña que cumpla estos requisitos:<ul><li>La contraseña del administrador de dispositivos debe tener entre 8 y 15 caracteres.</li><li>La contraseña de StorSimple Snapshot Manager debe tener 14 o 15 caracteres.</li></ul> | 
| 2 | La contraseña no cumple la longitud necesaria. | Utilice una contraseña que cumpla estos requisitos:<ul><li>La contraseña del administrador de dispositivos debe tener entre 8 y 15 caracteres.</li><li>La contraseña de StorSimple Snapshot Manager debe tener 14 o 15 caracteres.</lu></ul> |
| 3 | La contraseña debe contener caracteres en minúsculas. | Las contraseñas deben contener tres de los siguientes cuatro tipos de caracteres: minúsculas, mayúsculas, números y caracteres especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 4 | La contraseña debe contener caracteres numéricos. | Las contraseñas deben contener tres de los siguientes cuatro tipos de caracteres: minúsculas, mayúsculas, números y caracteres especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 5 | La contraseña debe contener caracteres especiales. | Las contraseñas deben contener tres de los siguientes cuatro tipos de caracteres: minúsculas, mayúsculas, números y caracteres especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 6 | La contraseña debe contener tres de los siguientes cuatro tipos de caracteres: mayúsculas, minúsculas, números y caracteres especiales. | La contraseña no contiene los tipos de caracteres necesarios. Asegúrese de que la contraseña cumple estos requisitos. |
| 7 | El parámetro no coincide con la confirmación. | Asegúrese de que la contraseña cumple todos los requisitos y de que la ha escrito correctamente. |
| 8 | La contraseña no coincide con el valor predeterminado. | La contraseña predeterminada es *Password1*. Debe cambiar esta contraseña después de iniciar sesión por primera vez. |
| 9 | La contraseña que ha escrito no coincide con la contraseña del dispositivo. Vuelva a escribir la contraseña. | Compruebe la contraseña y escríbala de nuevo. |

Las contraseñas se recopilan antes de que el dispositivo se registre, pero solo se aplican después de un registro correcto. El flujo de trabajo de recuperación de contraseñas requiere que el dispositivo esté registrado.

> [AZURE.IMPORTANT]En general, si se produce un error al intentar aplicar una contraseña, el software intenta recopilar repetidamente la contraseña hasta que lo consigue. En raras ocasiones, no se puede aplicar la contraseña. En este caso, puede registrar el dispositivo y continuar; sin embargo, las contraseñas no se modificarán. No recibirá ninguna indicación acerca de cuál de las contraseñas no se modificó: la contraseña del administrador de dispositivos o la contraseña de StorSimple Snapshot Manager. Si se produce esta situación, se recomienda que cambie ambas contraseñas.

Puede restablecer las contraseñas desde el Portal de administración del servicio StorSimple Manager. Para obtener más información, consulte:

- [Configuración de la contraseña del administrador de dispositivos](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec09)
- [Configuración de la contraseña de StorSimple Snapshot Manager](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)

### Errores que se producen al registrar el dispositivo

Para registrar el dispositivo se utiliza el servicio StorSimple Manager que se ejecuta en Microsoft Azure. Pueden surgir uno o varios de los siguientes problemas durante el registro del dispositivo.

| Nº| Mensaje de error | Causas posibles | Acción recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1 | Error 350027: No se pudo registrar el dispositivo con StorSimple Manager. | | Espere unos minutos y vuelva a intentar la operación. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).|
| 2 | Error 350013: Se ha producido un error al registrar el dispositivo. Esto puede deberse a que la clave de registro del servicio no es correcta. | | Vuelva a registrar el dispositivo con la clave de registro del servicio correcta. Para obtener más información, consulte [Obtención de la clave de registro de servicio](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03). |
| 3 | Error 350063: La autenticación en el servicio StorSimple Manager se ha realizado correctamente pero se ha producido un error en el registro. Vuelva a intentar la operación más tarde. | Este error indica que la autenticación con ACS se ha realizado correctamente pero se ha producido un error en la llamada de registro realizada al servicio. Esto puede deberse a un problema de red esporádico. | Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx). |
| 4 | Error 350049: No se pudo alcanzar el servicio durante el registro. | Cuando se realiza la llamada al servicio, se recibe una excepción de web. En algunos casos, esto se puede corregir volviendo a intentar realizar la operación más adelante. | Compruebe la dirección IP y el nombre DNS y, a continuación, vuelva a intentar la operación. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx). | 
| 5 | Error 350031: El dispositivo ya se ha registrado. | | No es necesaria ninguna acción. |
| 6 | Error 350016: Error de registro de dispositivo. | |Asegúrese de que la clave de registro es correcta. |
| 7 | Invoke-HcsSetupWizard: Se ha producido un error al registrar el dispositivo; se puede deber a una dirección IP o un nombre DNS incorrectos. Compruebe la configuración de red y vuelva a intentarlo. Si el problema persiste, [póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) \(error 350050\). | Asegúrese de que el dispositivo puede hacer ping a la red externa. Si no tiene conectividad con la red externa, se puede producir este error en el registro. Este error puede ser una combinación de uno o varios de los siguientes elementos:<ul><li>IP incorrecta</li><li>Subred incorrecta</li><li>Puerta de enlace incorrecta</li><li>Configuración de DNS incorrecta</li></ul> | Consulte los pasos del [Ejemplo de solución de problemas paso a paso](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Invoke-HcsSetupWizard: No se pudo realizar la operación actual debido a un error de servicio interno [0x1FBE2]. Vuelva a intentar la operación más tarde. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. | Se trata de un error genérico que se produce para todos los errores invisibles de usuario del servicio o del agente. La razón más habitual es un error en la autenticación de ACS. El error puede deberse a problemas con la configuración del servidor NTP y a una configuración incorrecta de la hora en el dispositivo. | Corrija la hora \(si hay problemas\) y, a continuación, vuelva a intentar la operación de registro. Si persiste este problema, [póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para conocer los pasos siguientes. |
| 9 | Advertencia: no se pudo activar el dispositivo. Las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager no han cambiado. | Si se produce un error en el registro, las contraseñas del administrador de dispositivos y de StorSimple Snapshot Manager no cambian. |

## Herramientas para solucionar problemas en implementaciones de StorSimple

StorSimple incluye varias herramientas que puede utilizar para solucionar problemas de la solución StorSimple. Entre ellas se incluyen las siguientes:

- Paquetes de soporte y registros de dispositivo 
- Cmdlets diseñados específicamente para la solución de problemas 

## Paquetes de soporte y registros de dispositivo disponibles para la solución de problemas

Un paquete de soporte contiene todos los registros relevantes que pueden ayudar al equipo de soporte técnico de Microsoft a solucionar los problemas del dispositivo. Puede usar Windows PowerShell para StorSimple para generar un paquete de soporte cifrado que posteriormente puede compartir con el personal de soporte técnico.

### Para ver los registros o el contenido del paquete de soporte

1. Use Windows PowerShell para StorSimple para generar un paquete de soporte, tal como se describe en [Generación de un paquete de soporte](https://msdn.microsoft.com/library/azure/dn772348.aspx).

2. Descargue el [script de descifrado](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente en el equipo cliente.

3. Utilice este [procedimiento detallado](https://msdn.microsoft.com/library/azure/dn762117.aspx) para abrir y descifrar el paquete de soporte.

4. Los registros del paquete de soporte descifrado están en formato etw/etvx. Para ver estos archivos en el Visor de eventos de Windows, realice lo siguiente:
  1. Ejecute el comando **eventvwr** en el cliente Windows. Se iniciará el Visor de eventos.
  2. En el panel **Acciones**, haga clic en **Abrir registro guardado** y elija los archivos de registro con formato etvx/etw \(el paquete de soporte\). Ahora puede ver el archivo. Después de abrirlo, puede hacer clic con el botón secundario y guardarlo como texto.
   
    > [AZURE.IMPORTANT]También puede utilizar el cmdlet **Get-WinEvent** para abrir este archivo en Windows PowerShell. Para obtener más información, consulte [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) en la documentación de referencia de cmdlets de Windows PowerShell.

5. Cuando los registros se abran en el Visor de eventos, busque los siguientes registros, que contienen problemas relacionados con la configuración del dispositivo:

  - hcs\_pfconfig/registro operativo
  - hcs\_pfconfig/registro de configuración

6. En los archivos de registro, busque cadenas relacionadas con los cmdlets a los que llama el Asistente para instalación. Consulte [Proceso del Asistente para instalación por primera vez](#first-time-setup-wizard-process) para obtener una lista de estos cmdlets. 

7. Si no puede averiguar la causa del problema, puede [ponerse en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para conocer los pasos siguientes. Siga los pasos de [Inicio de una sesión de soporte técnico](https://msdn.microsoft.com/library/azure/dn757804.aspx) cuando se ponga en contacto con el servicio de soporte técnico de Microsoft para obtener ayuda.

## Cmdlets disponibles para solucionar problemas

Use los siguientes cmdlets de Windows PowerShell para detectar errores de conectividad.

- Get-NetAdapter: use este cmdlet para detectar el mantenimiento de las interfaces de red. 

- Test-Connection: use este cmdlet para comprobar la conectividad de red, dentro y fuera de la red.

- Test-HcsmConnection: use este cmdlet para comprobar la conectividad de un dispositivo registrado correctamente.

## Solución de problemas con el cmdlet Get-NetAdapter

Al configurar las interfaces de red para una implementación de dispositivo por primera vez, el estado del hardware no está disponible en la interfaz de usuario del servicio StorSimple Manager porque el dispositivo todavía no está registrado con el servicio. Además, es posible que la página Estado de hardware no siempre refleje correctamente el estado del dispositivo, en especial si hay problemas que afectan a la sincronización del servicio. En estos casos, puede usar el cmdlet Get-NetAdapter para determinar el mantenimiento y el estado de las interfaces de red.

### Para ver una lista de todos los adaptadores de red del dispositivo

1. Inicie Windows PowerShell para StorSimple y, a continuación, escriba **Get-NetAdapter**. 

2. Utilice la salida del cmdlet Get-NetAdapter y las instrucciones siguientes para conocer el estado de la interfaz de red.
  - Si el mantenimiento es correcto y la interfaz está habilitada, el estado de **ifIndex** se muestra como **Up**.
  - Si el mantenimiento es correcto pero la interfaz no está conectada físicamente \(mediante un cable de red\), el estado de **ifIndex** se muestra como **Disabled**.
  - Si el mantenimiento es correcto pero la interfaz no está habilitada, el estado de **ifIndex** se muestra como **NotPresent**.
  - Si la interfaz no existe, no aparece en la lista. La interfaz de usuario del servicio StorSimple Manager seguirá mostrando esta interfaz con un estado de error.

Para obtener más información sobre cómo usar este cmdlet, vaya a [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) en la referencia de cmdlets de Windows PowerShell.

En las secciones siguientes se muestran ejemplos de salida del cmdlet Get-NetAdapter.

 En estos ejemplos, el controlador 0 actuó como controlador pasivo, con la siguiente configuración:

- Existían las interfaces de red DATA 0, DATA 1, DATA 2 y DATA 3 en el dispositivo.
- No había tarjetas de interfaz de red DATA 4 y DATA 5, por lo que no aparecen en la salida.
- La interfaz DATA 0 estaba habilitada.

El controlador 1 actuó como controlador activo, con la siguiente configuración:

- Existían las interfaces de red DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 y DATA 5 en el dispositivo.
- La interfaz DATA 0 estaba habilitada.

**Ejemplo de salida: controlador 0**

A continuación se puede ver la salida del controlador 0 \(el controlador pasivo\). DATA 1, DATA 2 y DATA 3 no están conectadas. DATA 4 y DATA 5 no se muestran porque no están presentes en el dispositivo.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Ejemplo de salida: controlador 1**

A continuación se puede ver la salida del controlador 1 \(el controlador activo\). Solo está configurada y en funcionamiento la interfaz de red DATA 0 del dispositivo.

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

 
## Solución de problemas con el cmdlet Test-Connection

Puede usar el cmdlet Test-Connection para determinar si el dispositivo de StorSimple puede conectarse a la red externa. Si todos los parámetros de redes, incluidos los de DNS, están configurados correctamente en el Asistente para instalación, puede usar el cmdlet Test-Connection para hacer ping a una dirección conocida fuera de la red \(por ejemplo, outlook.com\).

Consulte los siguientes ejemplos de salida del cmdlet Test-Connection.

> [AZURE.NOTE]En el primer ejemplo, el dispositivo está configurado con un DNS incorrecto. En el segundo ejemplo, el DNS es correcto.
 
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

## Solución de problemas con el cmdlet Test-HcsmConnection

Use el cmdlet Test-HcsmConnection para un dispositivo que ya está conectado al servicio StorSimple Manager y registrado con él. Este cmdlet permite comprobar la conectividad entre un dispositivo registrado y el servicio StorSimple Manager correspondiente. Puede ejecutar este comando en Windows PowerShell para StorSimple.

### Para ejecutar el cmdlet Test-HcsmConnection

1. Asegúrese de que el dispositivo está registrado.

2. Compruebe el estado del dispositivo. Si el dispositivo está desactivado, en modo de mantenimiento o sin conexión, pueden aparecer los errores siguientes:

   - ErrorCode.CiSDeviceDecommissioned: indica que el dispositivo está desactivado.
   - ErrorCode.DeviceNotReady: indica que el dispositivo se encuentra en modo de mantenimiento.
   - ErrorCode.DeviceNotReady: indica que el dispositivo no está en línea.

3. Compruebe que el servicio StorSimple Manager se está ejecutando \(utilice el cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx)\). Si no se está ejecutando el servicio, pueden aparecer los errores siguientes:

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError: indica que se produjo una excepción al ejecutar Get-ClusterResource.

4. Compruebe el token del servicio de control de acceso \(ACS\). Si se produce una excepción de web, puede deberse a un problema de puerta de enlace, a la falta de una autenticación de proxy, a un DNS incorrecto o a un error de autenticación. Pueden aparecer los errores siguientes:

   - ErrorCode.CiSApplianceGateway: indica una excepción de HttpStatusCode.BadGateway \(el servicio de resolución de nombres no pudo resolver el nombre de host\). 
   - ErrorCode.CiSApplianceProxy: indica una excepción de HttpStatusCode.ProxyAuthenticationRequired con código de estado HTTP 407 \(el cliente no se pudo autenticar con el servidor proxy\). 
   - ErrorCode.CiSApplianceDNSError: indica una excepción de WebExceptionStatus.NameResolutionFailure \(el servicio de resolución de nombres no pudo resolver el nombre de host\).
   - ErrorCode.CiSApplianceACSError: indica que el servicio ha devuelto un error de autenticación, aunque existe conectividad.
   
   Si no se produce una excepción de web, compruebe lo siguiente:

   - ErrorCode.CiSApplianceFailure: indica que se produjo un error en el dispositivo.

5. Compruebe la conectividad del servicio en la nube. Si el servicio inicia una excepción de web, pueden aparecer los errores siguientes:

  - ErrorCode.CiSApplianceGateway: indica una excepción de HttpStatusCode.BadGateway \(un servidor proxy intermedio recibió una solicitud incorrecta de otro proxy o del servidor original\).
  - ErrorCode.CiSApplianceProxy: indica una excepción de HttpStatusCode.ProxyAuthenticationRequired con código de estado HTTP 407 \(el cliente no se pudo autenticar con el servidor proxy\). 
  - ErrorCode.CiSApplianceDNSError: indica una excepción de WebExceptionStatus.NameResolutionFailure \(el servicio de resolución de nombres no pudo resolver el nombre de host\).
  - ErrorCode.CiSApplianceACSError: indica que el servicio ha devuelto un error de autenticación, aunque existe conectividad.
  
  Si no se produce una excepción de web, compruebe lo siguiente: - ErrorCode.CiSApplianceSaasServiceError: indica un problema con el servicio StorSimple Manager.
 
6. Compruebe la conectividad del Bus de servicio de Azure. ErrorCode.CiSApplianceServiceBusError: indica que el dispositivo no se puede conectar al Bus de servicio.
 
En los archivos de registro CiSCommandletLog0Curr.errlog y CiSAgentsvc0Curr.errlog encontrará más información como, por ejemplo, los detalles de la excepción.

Para obtener más información sobre cómo usar el cmdlet, vaya a [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) en la documentación de referencia de Windows PowerShell.

> [AZURE.IMPORTANT]Puede ejecutar este cmdlet para ambos controladores, activo y pasivo.
 
Consulte los siguientes ejemplos de salida del cmdlet Test-HcsmConnection.

**Ejemplo de salida: dispositivo registrado correctamente**

El primer ejemplo corresponde a un dispositivo que se ha registrado correctamente con el servicio StorSimple Manager y no presenta problemas de conectividad.

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Ejemplo de salida: dispositivo sin conexión**

Este ejemplo corresponde a un dispositivo que muestra el estado **Sin conexión** en el Portal de administración.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

El dispositivo no se pudo conectar con la configuración del proxy web actual. Puede tratarse de un problema con la configuración del proxy web o con la conectividad de red. En este caso, debe asegurarse de que la configuración del proxy web es correcta y de que los servidores proxy web están en línea y accesibles.

## Ejemplo de solución de problemas de StorSimple paso a paso

En el ejemplo siguiente se muestra la solución de problemas paso a paso de una implementación de StorSimple. En el escenario de ejemplo, se produce un error en el registro del dispositivo; el mensaje de error indica que la configuración de red o el nombre DNS son incorrectos.

El mensaje de error devuelto es:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

El error puede deberse a alguna de las siguientes causas:

- Instalación incorrecta de hardware
- Interfaces de red defectuosas
- Dirección IP, máscara de subred, puerta de enlace, servidor DNS principal o proxy web incorrectos
- Clave de registro incorrecta
- Configuración de firewall incorrecta

### Para buscar y corregir el problema con el registro del dispositivo

1. Compruebe la configuración del dispositivo: en el controlador activo, ejecute **Invoke-HcsSetupWizard**.

     \>[AZURE.NOTE]El Asistente para instalación se debe ejecutar en el controlador activo. Para comprobar que está conectado al controlador activo, observe el banner que aparece en la consola serie. Este banner indica si está conectado al controlador 0 o al 1, y si es el controlador activo o pasivo. Para más información, vaya a [Identificación de un controlador activo en el dispositivo](https://msdn.microsoft.com/library/azure/dn790262.aspx).
 
2. Asegúrese de que los cables del dispositivo están conectados correctamente: compruebe el cableado de red en la parte posterior del dispositivo. Los cables son específicos del modelo de dispositivo. Para obtener más información, vaya a [Cableado del dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) o a [Cableado del dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

     \>[AZURE.NOTE]Si utiliza puertos de red de 10 GbE, deberá usar los adaptadores QSFP-SFP y los cables SFP suministrados. Para obtener más información, consulte la [lista de cables, conmutadores y transceptores recomendados por el proveedor OEM para puertos Mellanox](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Compruebe el mantenimiento de la interfaz de red:

   - Utilice el cmdlet Get-NetAdapter para detectar el estado de mantenimiento de las interfaces de red para DATA 0. 
   - Si el vínculo no funciona, el estado de **ifindex** indicará que la interfaz no está funcionando. Deberá comprobar entonces la conexión de red del puerto al dispositivo y al conmutador. También deberá descartar los cables defectuosos. 
   - Si sospecha que se ha producido un error en el puerto de DATA 0 del controlador activo y desea confirmarlo, conéctese al puerto de DATA 0 del controlador 1. Para ello, desconecte el cable de red de la parte posterior del dispositivo del controlador 0, conecte el cable al controlador 1 y, a continuación, vuelva a ejecutar el cmdlet Get-NetAdapter. Si se produce un error en el puerto de DATA 0 de un controlador, [póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para conocer los pasos siguientes. Es posible que deba reemplazar el controlador del sistema.
 
4. Compruebe la conectividad con el conmutador:
   - Asegúrese de que las interfaces de red DATA 0 de los controladores 0 y 1 del revestimiento de hardware principal están en la misma subred. 
   - Compruebe el concentrador o el enrutador. Por lo general debe conectar ambos controladores al mismo concentrador o enrutador. 
   - Asegúrese de que los conmutadores que usa para la conexión tienen DATA 0 para ambos controladores en la misma vLAN.
   
5. Elimine los errores de usuario:

  - Vuelva a ejecutar el Asistente para instalación \(ejecute **Invoke-HcsSetupWizard**\) y escriba de nuevo los valores para asegurarse de que no hay ningún error. 
  - Compruebe la clave de registro utilizada. Se puede usar la misma clave del registro para conectar varios dispositivos a un servicio StorSimple Manager. Siga el procedimiento de [Obtención de la clave de registro de servicio](https://msdn.microsoft.com/library/azure/cd4dee49-6ae8-4ff0-b79b-74b2027cb694#sec03) para asegurarse de que está utilizando la clave de registro correcta.

    > [AZURE.IMPORTANT]Si está ejecutando varios servicios, asegúrese de que se usa la clave de registro correspondiente al servicio adecuado para registrar el dispositivo. Si ha registrado un dispositivo con el servicio StorSimple Manager incorrecto, deberá [ponerse en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para conocer los pasos siguientes. Tal vez sea necesario realizar un restablecimiento de fábrica del dispositivo \(que puede provocar la pérdida de datos\) para, a continuación, conectarlo al servicio deseado.

6. Use el cmdlet Test-Connection para comprobar que dispone de conectividad con la red externa. Para obtener más información, vaya a [Solución de problemas con el cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).

7. Compruebe si existen interferencias de firewall. Si ha comprobado que la configuración de IP virtual \(VIP\), subred, puerta de enlace y DNS es correcta y todavía existen problemas de conectividad, es posible que el firewall esté bloqueando la comunicación entre el dispositivo y la red externa. Debe asegurarse de que los puertos 80 y 443 están disponibles en el dispositivo de StorSimple para la comunicación saliente. Para obtener más información, consulte [Requisitos de red para el dispositivo de StorSimple](https://msdn.microsoft.com/library/azure/dn772371.aspx).

8. Revise los registros. Vaya a [Paquetes de soporte y registros de dispositivo disponibles para solucionar problemas ](#support-packages-and-device logs-for-troubleshooting).

9. Si los pasos anteriores no resuelven el problema, [póngase en contacto con el servicio de soporte técnico de Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) para obtener ayuda.

## Pasos siguientes
[Solución de problemas de un dispositivo de StorSimple operativo](storsimple-troubleshoot-an-operational-device.md)

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx
<!--HONumber=52-->

<properties
   pageTitle="Actualización del dispositivo de StorSimple"
   description="Se explica cómo utilizar la característica de actualización de StorSimple para instalar actualizaciones y revisiones, tanto normales como en modo de mantenimiento."
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
   ms.date="07/30/2015"
   ms.author="v-sharos" />

# Actualización del dispositivo de StorSimple

## Información general

Las características de actualización de StorSimple permiten mantener fácilmente actualizado el dispositivo de StorSimple. Según el tipo de actualización, puede aplicar las actualizaciones al dispositivo a través del Portal de administración de Microsoft Azure o mediante la interfaz de Windows PowerShell. En este tutorial se describen los tipos de actualización y cómo instalar cada uno de ellos.

Puede aplicar dos tipos de actualizaciones de dispositivo:

- Actualizaciones normales (o en modo normal)
- Actualizaciones en modo de mantenimiento

Puede instalar las actualizaciones normales a través del Portal de administración o de Windows PowerShell; sin embargo, debe usar Windows PowerShell para instalar las actualizaciones en modo de mantenimiento.

A continuación se describe cada tipo de actualización.

### Actualizaciones normales

Se trata de actualizaciones no perturbadoras que se pueden instalar cuando el dispositivo está en modo normal. Estas actualizaciones se aplican a través del sitio web Microsoft Update a cada controlador de dispositivo.

> [AZURE.IMPORTANT]Durante el proceso de actualización puede producirse una conmutación por error del controlador. Sin embargo, esto no afectará a la disponibilidad ni al funcionamiento del sistema.

- Para obtener más información acerca de cómo instalar actualizaciones normales a través del Portal de administración, consulte [Instalación de actualizaciones normales a través del Portal de administración](#install-regular-updates-via-the-management-portal).

- También puede instalar actualizaciones normales a través de Windows PowerShell para StorSimple. Para obtener más información, consulte [Instalación de actualizaciones normales a través de Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### Actualizaciones en modo de mantenimiento

Se trata de actualizaciones perturbadoras, como las actualizaciones de firmware de disco o de firmware de USM. En estas actualizaciones es necesario que el dispositivo esté en modo de mantenimiento. Para obtener más información, consulte [Paso 2: activar el modo de mantenimiento](#to-enter-maintenance-mode). No puede usar el Portal de administración para instalar las actualizaciones en modo de mantenimiento. En su lugar, debe usar Windows PowerShell para StorSimple.

Para obtener más información sobre cómo instalar las actualizaciones en modo de mantenimiento, consulte [Instalación de actualizaciones en modo de mantenimiento a través de Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT]Las actualizaciones en modo de mantenimiento deben aplicarse por separado en cada controlador.

## Instalación de actualizaciones normales a través del Portal de administración

Puede usar el Portal de administración para aplicar actualizaciones en el dispositivo de StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## Instalación de actualizaciones normales a través de Windows PowerShell para StorSimple

También puede usar Windows PowerShell para StorSimple para aplicar las actualizaciones normales (en modo normal).

> [AZURE.IMPORTANT]Aunque puede instalar actualizaciones periódicas con Windows PowerShell para StorSimple, le recomendamos encarecidamente que las instale a través del Portal de administración. A partir de la actualización 1, se realizarán comprobaciones previas antes de instalar actualizaciones desde el Portal. Estas comprobaciones previas previenen los errores y garantizan una experiencia con menos complicaciones.

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## Instalación de actualizaciones en modo de mantenimiento a través de Windows PowerShell para StorSimple

Para aplicar las actualizaciones en modo de mantenimiento en el dispositivo de StorSimple se usa Windows PowerShell para StorSimple. En este modo, todas las solicitudes de E/S se interrumpen. También se detienen servicios como la memoria de acceso aleatorio no volátil (NVRAM) o el servicio de Cluster Server. Al entrar o salir de este modo, se reinician ambos controladores. Al salir de este modo, los servicios se reanudan y deben estar correctos. Esto puede tardar unos minutos.

Si debe aplicar actualizaciones en modo de mantenimiento, recibirá una alerta a través del Portal de administración para indicarle que existen actualizaciones que se deben instalar. Esta alerta incluye instrucciones para usar Windows PowerShell para StorSimple con el fin de instalar las actualizaciones. Después de actualizar el dispositivo, utilice el mismo procedimiento para cambiarlo a modo normal. Para obtener instrucciones detalladas, consulte [Paso 4: salir del modo de mantenimiento](#to-exit-maintenance-mode).

> [AZURE.IMPORTANT]
> 
> - Antes de entrar en modo de mantenimiento, asegúrese de que los controladores de dispositivos son correctos; para ello, compruebe el **Estado de hardware** en la página **Mantenimiento** del Portal de administración. Si el controlador no es correcto, póngase en contacto con el servicio de soporte técnico de Microsoft para conocer los pasos siguientes. Para obtener más información, póngase en contacto con el servicio de soporte técnico de Microsoft. 
> - Si está en modo de mantenimiento, debe aplicar primero la actualización en un controlador y, a continuación, en el otro.

### Paso 1: conéctese a la consola serie del dispositivo.

En primer lugar, utilice una aplicación como PuTTY para tener acceso a la consola serie. El siguiente procedimiento explica cómo usar PuTTY para conectarse a la consola serie.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### Paso 2: acceda al modo de mantenimiento

Una vez esté conectado a la consola, mire a ver si hay actualizaciones que se deban instalar y active el modo de mantenimiento para hacerlo.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### Paso 3: instale las actualizaciones

A continuación, instale las actualizaciones.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### Paso 4: salir del modo de mantenimiento

Por último, salga del modo de mantenimiento.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## Instale las reparaciones mediante Windows PowerShell para StorSimple

A diferencia de las actualizaciones para Microsoft Azure StorSimple, las revisiones se instalan desde una carpeta compartida. Al igual que en el caso de las actualizaciones, hay dos tipos de revisiones:

- Revisiones normales 
- Revisiones en modo de mantenimiento  

Los siguientes procedimientos explican cómo usar Windows PowerShell para StorSimple con el fin de instalar revisiones normales y en modo de mantenimiento.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## ¿Qué ocurre con las actualizaciones si se realiza un restablecimiento de fábrica del dispositivo?

Si se restablece la configuración de fábrica de un dispositivo, se pierden todas las actualizaciones. Después de registrar y configurar el dispositivo en el que se ha efectuado un restablecimiento de fábrica, deberá instalar manualmente las actualizaciones a través del Portal de administración o de Windows PowerShell para StorSimple. Para obtener más información acerca de los restablecimientos de fábrica, consulte [Restablecer el dispositivo a los valores de fábrica](https://msdn.microsoft.com/library/azure/dn772373.aspx).

## Pasos siguientes

Obtenga más información sobre [Windows PowerShell para StorSimple](https://msdn.microsoft.com/library/azure/dn772425.aspx).
 

<!---HONumber=August15_HO6-->
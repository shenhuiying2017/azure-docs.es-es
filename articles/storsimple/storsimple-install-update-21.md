<properties
   pageTitle="Instalación de Update 2.1 en el dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo instalar Update 2.1 de la serie StorSimple 8000 en un dispositivo de dicha serie."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/25/2016"
   ms.author="alkohli" />

# Instalación de Update 2.1 en el dispositivo StorSimple

## Información general

En este tutorial se explica cómo instalar Update 2.1 en un dispositivo StorSimple ejecutando una versión anterior del software mediante el Portal de Azure clásico y usando el método de revisión. El método de revisión se utiliza cuando se configura una puerta de enlace en una interfaz de red que no sea DATA 0 del dispositivo StorSimple y está intentando actualizar desde una versión del software anterior a Update 1.

Update 2.1 incluye software del dispositivo, WMI y actualizaciones de iSCSI. Si actualiza desde una versión anterior a Update 2, también deberá aplicar un controlador LSI, Spaceport, Storport y las actualizaciones de firmware del disco. El software del dispositivo, WMI, iSCSI, el controlador LSI y las correcciones de Storport y Spaceport son actualizaciones que no provocan interrupciones y se pueden aplicar mediante el Portal de Azure clásico. Las actualizaciones del firmware del disco son actualizaciones perturbadoras y solo pueden aplicarse mediante la interfaz de Windows PowerShell del dispositivo.

> [AZURE.IMPORTANT]

> -  Es posible que no vea Update 2.1 de inmediato porque llevamos a cabo un lanzamiento por fases de las actualizaciones. Busque actualizaciones de nuevo en unos días ya que estarán disponibles pronto.
> - En esta actualización se incluye un conjunto de comprobaciones previas que se hace antes de la instalación para determinar el estado del dispositivo en cuanto a la conectividad de red y el estado del hardware. Estas comprobaciones previas se realizan solo si aplica las actualizaciones desde el Portal de Azure clásico.
> - Se recomienda instalar las actualizaciones de software y de los controladores mediante el Portal de Azure clásico. Solo debe ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si, en el Portal, se produce un error en las comprobaciones de la puerta de enlace anteriores a la actualización. Dependiendo de la versión de origen, las actualizaciones pueden tardar 1,5 y 2,5 horas en instalarse. Las actualizaciones en modo de mantenimiento deben instalarse mediante la interfaz de Windows PowerShell del dispositivo. Como las actualizaciones en modo de mantenimiento son perturbadoras, generarán un tiempo de inactividad para el dispositivo.
> - Si ejecuta la opción de StorSimple Snapshot Manager, antes de actualizar el dispositivo, asegúrese de haber actualizado la versión de Snapshot Manager a Update 2.1.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Instalación de Update 2.1 a través del Portal de Azure clásico

Realice los pasos siguientes para actualizar el dispositivo a [Update 2.1](storsimple-update21-release-notes.md).


> [AZURE.NOTE]
Si va a aplicar Update 2 o una versión posterior (incluida Update 2.1), Microsoft podrá extraer información de diagnóstico adicional del dispositivo. Como consecuencia, cuando nuestro equipo de operaciones identifica dispositivos que están teniendo problemas, estamos mejor equipados para recopilar información del dispositivo y diagnosticar problemas. Al aceptar Update 2 o una versión posterior, nos permite ofrecer este soporte técnico proactivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series Update 2.1 (6.3.9600.17705)**. También se debe modificar **Fecha de última actualización:**. 

	Si está realizando la actualización desde una versión anterior a Update 2, también verá que están disponibles las actualizaciones del modo de mantenimiento (este mensaje podría seguir apareciendo hasta 24 horas después de instalar las actualizaciones).

    Las actualizaciones del modo de mantenimiento provocan interrupciones con tiempos de inactividad del dispositivo y solo pueden aplicarse a través de la interfaz de Windows PowerShell del dispositivo. En algunos casos, cuando se ejecuta Update 1.2, es posible que el firmware del disco ya esté actualizado, en cuyo caso no es necesario instalar las actualizaciones en modo de mantenimiento.

	Si va a actualizar desde Update 2, el dispositivo debería estar ahora actualizado. Puede omitir los demás pasos.

13. Descargue las actualizaciones del modo de mantenimiento mediante los pasos enumerados en [Descargar revisiones](#to-download-hotfixes) para buscar y descargar KB3121899, que instala las actualizaciones de firmware del disco (el resto de las actualizaciones ya deben estar instaladas).

13. Siga los pasos enumerados en [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones del modo de mantenimiento.

  

## Instalación de Update 2.1 como una revisión

Debe usar este procedimiento si la comprobación de la puerta de enlace produce un error al intentar instalar las actualizaciones a través del Portal de Azure clásico. Se produce un error en la comprobación porque tiene una puerta de enlace asignada a una interfaz de red que no es DATA 0 y el dispositivo está ejecutando una versión de software antes de la actualización 1.

Las versiones de software que se pueden actualizar mediante el método de revisión son las siguientes:

- Update 0.1, 0.2, 0.3
- Update 1, 1.1, 1.2
- Actualización 2 

> [AZURE.IMPORTANT]
>
> - Si el dispositivo está ejecutando la versión de lanzamiento (GA), [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para que le ayude con la actualización.

El método de revisión implica los tres pasos siguientes:

- Descargar las revisiones desde el catálogo de Microsoft Update.
- Instalar y comprobar las revisiones de modo normal.
- Instalar y comprobar la revisión del modo de mantenimiento (solo cuando se actualiza desde versiones de software anteriores a Update 2).

#### Descarga de actualizaciones para un dispositivo que ejecuta el software Update 2

**Si el dispositivo ejecuta Update 2**, debe descargar e instalar las siguientes revisiones en el orden indicado:

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |
|--------|-----------|-------------------------|------------- |-------------|
| 1\. | KB3162954 | Actualización de software &#42; | Normal | ~45 min |
| 2\. | KB3146621 | Paquete iSCSI | Normal | ~20 min |
| 3\. | KB3103616 | Paquete WMI | Normal | ~12 min |


 & #42; *Tenga en cuenta que la actualización de software consta de dos archivos binarios: `all-hcsmdssoftwareupdate_d5db7c7a86fc0fffd7fd7e8a1b58584ca4850936.exe` y `all-cismdsagentupdatebundle_a3b6e721045c9229f62ffe3374fb5715bf3699e3.exe`. La actualización de software de dispositivo `all-hcsmdssoftwareupdate_d5db7c7a86fc0fffd7fd7e8a1b58584ca4850936.exe` debe instalarse antes que el agente de Cis y Mds `all-cismdsagentupdatebundle_a3b6e721045c9229f62ffe3374fb5715bf3699e3.exe`. También debe reiniciar el controlador activo mediante el cmdlet `Restart-HcsController` después de aplicar la actualización del agente de Cis y MDS (y antes de aplicar el resto de actualizaciones).*

#### Descarga de actualizaciones para un dispositivo que ejecuta un software anterior a Update 2

**Si el dispositivo está ejecutando las versiones 0.2, 0.3, 1.0 y 1.1**, debe descargar e instalar la actualización del controlador LSI y de firmware, además de las actualizaciones de software, iSCSI y WMI. Esta actualización ya está instalada si ejecuta Update 1.2 o 2.
 
| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |
|--------|-----------|-------------------------|------------- |-------------|
| 4\. | KB3121900 | Controlador LSI y firmware | Normal | ~20 min |


<br></br> **Si el dispositivo está ejecutando las versiones 0.2, 0.3, 1.0, 1.1 o 1.2**, debe descargar e instalar la revisión de Storport y Spaceport. Ya estarán instaladas si ejecuta Update 2.

| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |
|--------|-----------|-------------------------|------------- |-------------|
| 5\. | KB3090322 | Revisión de Spaceport </br> Windows Server 2012 R2 | Normal | ~20 min |
| 6\. | KB3080728 | Revisión de Storport </br> Windows Server 2012 R2 | Normal | ~20 min |



<br></br> Es posible que también tenga que instalar actualizaciones de firmware del disco. Puede comprobar si las necesita ejecutando el cmdlet `Get-HcsFirmwareVersion`. Si ejecuta las versiones de firmware `XMGG`, `XGEG`, `KZ50`, `F6C2` o `VR08`, no necesita instalar estas actualizaciones.


| Orden | KB | Descripción | Tipo de actualización | Hora de instalación |
|--------|-----------|-------------------------|------------- |-------------|
| 7\. | KB3121899 | Firmware del disco | Mantenimiento | ~30 min |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Este procedimiento debe realizarse una sola vez para aplicar Update 2.1. Puede usar el Portal de Azure clásico para aplicar las actualizaciones posteriores.
> - Si actualiza desde Update 2, el tiempo total de instalación es de 1,5 horas aproximadamente.
> - Antes de usar este procedimiento para aplicar la actualización, asegúrese de que ambos controladores de dispositivo estén en línea y todos los componentes de hardware estén en buen estado.

Realice los siguientes pasos para descargar e instalar las revisiones.

[AZURE.INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## Pasos siguientes

Más información sobre el [lanzamiento de la actualización 2.1](storsimple-update21-release-notes.md).

<!---HONumber=AcomDC_0601_2016-->
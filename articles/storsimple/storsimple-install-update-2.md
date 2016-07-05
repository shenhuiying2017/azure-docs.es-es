<properties
   pageTitle="Instalar Update 2 en el dispositivo de StorSimple | Microsoft Azure"
   description="Se explica cómo instalar Update 2 de la serie StorSimple 8000 en un dispositivo de la serie StorSimple 8000."
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
   ms.date="06/21/2016"
   ms.author="alkohli" />

# Instalar Update 2 en el dispositivo de StorSimple

## Información general

En este tutorial se explica cómo instalar Update 2 en un dispositivo de StorSimple ejecutando una versión anterior del software mediante el Portal de Azure clásico. En él también se tratan los pasos necesarios para la actualización cuando se configura una puerta de enlace en una interfaz de red que no sea DATA 0 del dispositivo de StorSimple y está intentando actualizar desde una versión de software anterior a Update 1.

En Update 2 se incluyen actualizaciones de software del dispositivo, actualizaciones de controladores LSI y actualizaciones del firmware del disco. Las actualizaciones del software y de los controladores LSI no provocan interrupciones y se pueden aplicar a través del Portal de Azure clásico. Las actualizaciones del firmware del disco son actualizaciones perturbadoras y solo pueden aplicarse mediante la interfaz de Windows PowerShell del dispositivo.

> [AZURE.IMPORTANT]

> -  Es posible que no vea Update 2 de inmediato porque hacemos una implementación por fases de las actualizaciones. Busque actualizaciones de nuevo en unos días ya que estarán disponibles pronto.
> - En esta actualización se incluye un conjunto de comprobaciones previas que se hace antes de la instalación para determinar el estado del dispositivo en cuanto a la conectividad de red y el estado del hardware. Estas comprobaciones previas se realizan solo si aplica las actualizaciones desde el Portal de Azure clásico.
> - Se recomienda instalar las actualizaciones de software y de los controladores mediante el Portal de Azure clásico. Solo debe ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si, en el Portal, se produce un error en las comprobaciones de la puerta de enlace anteriores a la actualización. La instalación de las actualizaciones puede tardar de 4 a 7 horas (incluidas las actualizaciones de Windows Update). Las actualizaciones en modo de mantenimiento deben instalarse mediante la interfaz de Windows PowerShell del dispositivo. Como las actualizaciones en modo de mantenimiento son perturbadoras, generarán un tiempo de inactividad para el dispositivo.
> - Si ejecuta la opción de StorSimple Snapshot Manager, antes de actualizar el dispositivo, asegúrese de haber actualizado la versión de Snapshot Manager a Update 2.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Instalación de Update 2 a través del Portal de Azure clásico

Realice los pasos siguientes para actualizar el dispositivo a [Update 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
Update 2 permite a Microsoft extraer información de diagnóstico adicional del dispositivo. Como consecuencia, cuando nuestro equipo de operaciones identifica dispositivos que están teniendo problemas, estamos mejor equipados para recopilar información del dispositivo y diagnosticar problemas. Al aceptar Update 2, nos permite proporcionar este soporte técnico proactivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Compruebe que el dispositivo ejecuta **StorSimple 8000 Series Update 2 (6.3.9600.17673)**. También se debe modificar **Fecha de última actualización:**. También verá que hay disponibles actualizaciones en modo de mantenimiento (este mensaje podría seguir apareciendo hasta 24 horas después de instalar las actualizaciones).

    Las actualizaciones del modo de mantenimiento provocan interrupciones con tiempos de inactividad del dispositivo y solo pueden aplicarse a través de la interfaz de Windows PowerShell del dispositivo. En algunos casos, cuando se ejecuta Update 1.2, es posible que el firmware del disco ya esté actualizado, en cuyo caso no es necesario instalar las actualizaciones en modo de mantenimiento.

13. Descargue las actualizaciones en modo de mantenimiento mediante los pasos enumerados en [Descargar revisiones](#to-download-hotfixes) para buscar y descargar KB3121899, que instala las actualizaciones de firmware del disco (el resto de actualizaciones ya deben estar instaladas).

13. Siga los pasos enumerados en [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones del modo de mantenimiento.


## Instalar Update 2 como una revisión

Debe usar este procedimiento si la comprobación de la puerta de enlace produce un error al intentar instalar las actualizaciones a través del Portal de Azure clásico. Se produce un error en la comprobación porque tiene una puerta de enlace asignada a una interfaz de red que no es DATA 0 y el dispositivo está ejecutando una versión de software antes de la actualización 1.

Las versiones de software que se pueden actualizar con el método de revisión son Update 0.1, Update 0.2, Update 0.3, Update 1, Update 1.1 y Update 1.2. El método de revisión implica los tres pasos siguientes:

- Descargar las revisiones desde el catálogo de Microsoft Update.
- Instalar y comprobar las revisiones de modo normal.
- Instalar y comprobar la revisión del modo de mantenimiento.

Para instalar Update 2 como una revisión, debe descargar e instalar las revisiones siguientes:

| Orden | KB | Descripción | Tipo de actualización |
|--------|-----------|-------------------------|------------- |
| 1 | KB3121901 | Actualización de software | Normal |
| 2 | KB3121900 | Controlador LSI | Normal |
| 3 | KB3080728 | Corrección de Storport </br> Windows Server 2012 R2 | Normal |
| 4 | KB3090322 | Corrección de Spaceport </br> Windows Server 2012 R2 | Normal |
| 5 | KB3121899 | Firmware del disco | Mantenimiento |


> [AZURE.IMPORTANT]
>
> - Si el dispositivo está ejecutando la versión de lanzamiento (GA), [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para que le ayude con la actualización.
> - Este procedimiento debe realizarse una sola vez para aplicar Update 2. Puede usar el Portal de Azure clásico para aplicar las actualizaciones posteriores.
> - Cada instalación de la revisión puede tardar unos 20 minutos en completarse. El tiempo total de instalación es casi 2 horas.
> - Antes de usar este procedimiento para aplicar la actualización, asegúrese de que ambos controladores de dispositivo estén en línea y todos los componentes de hardware estén en buen estado.

Realice los pasos siguientes para aplicar esta actualización como una revisión.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## Pasos siguientes

Obtenga más información sobre la [versión Update 2](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0622_2016-->
---
title: "Notas de la versión de la actualización 3 de la serie StorSimple 8000 | Microsoft Docs"
description: Describe las nuevas funciones, problemas y soluciones alternativas de Update 3 de la serie StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22e3859fad55a8632d62aaf5f656081d6d662bd
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Actualización de las notas de la versión Update 3 del dispositivo StorSimple serie 8000
> [!NOTE]
> El portal clásico para StorSimple está en desuso. Los administradores de dispositivos StorSimple realizarán la transición automáticamente al nuevo Azure Portal según la programación de puesta en desuso. Recibirá un correo electrónico y una notificación del portal en los que se avisa de este paso. Este documento también se retirará pronto. Si tiene alguna pregunta sobre este paso, consulte las [preguntas frecuentes de la migración a Azure Portal](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Información general
Las siguientes notas de la versión describen las funciones nuevas e identifican los problemas críticos abiertos de Update 3 de la serie StorSimple 8000. También contienen una lista de las actualizaciones de software de StorSimple incluidas en esta versión. 

Update 3 puede aplicarse a cualquier dispositivo de StorSimple que ejecute software de versión (GA) o las actualizaciones de la 0.1 a la 2.2. La versión de dispositivo asociada a Update 3 es 6.3.9600.17759.

Revise la información contenida en las notas de la versión antes de implementar la actualización de la solución de StorSimple.

> [!IMPORTANT]
> * Update 3 incluye actualizaciones de software de dispositivo, controlador LSI y firmware, Storport y Spaceport. Para instalar esta actualización, se necesitan en torno a una hora y media o dos. 
> * Para las nuevas versiones, no podrán ver las actualizaciones de inmediato porque hacemos una implementación por fases de las actualizaciones. Espere unos días y luego busque actualizaciones de nuevo ya que estas estarán disponibles pronto.
> 
> 

## <a name="whats-new-in-update-3"></a>Novedades de Update 3
Se realizaron las siguientes mejoras clave y correcciones de errores en Update 3.

* **Cambios en la recuperación de espacio automatizada** : A partir de Update 3, los algoritmos de recuperación de espacio se ejecutan en el controlador en espera del sistema, lo que provoca una ejecución más rápida. Para obtener más información sobre los puertos necesarios para trabajar con la recuperación de espacio, consulte los [Requisitos de red de StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Mejoras de rendimiento** : Update 3 ha mejorado el rendimiento de lectura y escritura en la nube.
* **Mejoras relacionadas con la migración** : En esta versión, se realizaron varias correcciones de errores y mejoras para la función de migración de los dispositivos de la serie 5000/7000 a dispositivos de la serie 8000. Para más información sobre cómo usar la función de migración, vaya a [Migración de dispositivo de la serie 5000/7000 a dispositivo de la serie 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Supervisión de correcciones relacionadas** : En esta versión, se corrigieron los errores relacionados con la supervisión de los gráficos, el panel de servicio y el panel del dispositivo.

## <a name="issues-fixed-in-update-3"></a>Problemas corregidos en Update 3
En las tablas siguientes se ofrece un resumen de los problemas corregidos en Update 3.    

| Sin  | Característica | Problema | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Migración de datos en el lado host |En la versión anterior, StorSimple Cloud Appliance permanecía sin conexión durante una migración de datos en el lado host. Este problema está corregido en esta versión. |Sin  |Sí |
| 2 |Volúmenes anclados localmente |En la versión anterior, hubo problemas relacionados con errores de E/S, errores de conversión de volumen y errores de ruta de acceso de datos para volúmenes anclados localmente. Estos problemas eran la causa raíz y se han solucionado en esta versión. |Sí |Sin  |
| 3 |Supervisión |Hubo varios problemas relacionados con unidades de informes y supervisión, así como gráficos del panel de dispositivos donde se mostraba información incorrecta de los volúmenes anclados localmente. Estos problemas se han corregido en esta versión. |Sí |Sin  |
| 4 |E/S de escrituras pesadas |Al usar StorSimple para cargas de trabajo que implican escrituras pesadas, el usuario ejecutaba un error poco frecuente donde el espacio de trabajo se colocaba en la nube. Este error se ha corregido en esta versión. |Sí |Sí |
| 5 |Backup |En determinados casos excepcionales, en las versiones anteriores de software, cuando el usuario realizaba una copia de seguridad de un clon remoto, ejecutaba errores de la nube y la operación sufría un error. En esta versión, se ha corrido el problema y la operación se realiza correctamente. |Sí |Sí |
| 6 |Directiva de copia de seguridad |En determinados casos excepcionales, en las versiones anteriores del software, había un error relacionado con la eliminación de la directiva de copia de seguridad. Este problema está corregido en esta versión. |Sí |Sí |

## <a name="known-issues-in-update-3"></a>Problemas conocidos de Update 3
En la tabla siguiente se proporciona un resumen de los problemas conocidos de esta versión.

| Nº | Característica | Problema | Comentarios / solución alternativa | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Cuórum de disco |En raras ocasiones, si se desconecta la mayoría de los discos en el revestimiento de EBOD de un dispositivo 8600 y no se produce un cuórum de disco, el grupo de almacenamiento se desconectará. Seguirá desconectado incluso si se vuelven a conectar los discos. |Necesitará reiniciar el dispositivo. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes. |Sí |Sin  |
| 2 |Identificador de controlador incorrecto |Cuando se realiza un reemplazo de controlador, el controlador 0 puede aparecer como controlador 1. Durante el reemplazo de controlador, cuando se carga la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el identificador del controlador del mismo nivel. En raras ocasiones, este comportamiento también puede aparecer después del reinicio del sistema. |No se requiere ninguna acción del usuario. La situación se solucionará una vez completado el reemplazo del controlador. |Sí |Sin  |
| 3 |Cuentas de almacenamiento |El uso del servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no admitido. Esto provocará una situación en la que no se pueden recuperar los datos de usuario. | |Sí |Sí |
| 4 |Conmutación por error del dispositivo |No se admiten varias conmutaciones por error de un contenedor de volúmenes del mismo dispositivo de origen a diferentes dispositivos de destino. La conmutación por error de un único dispositivo inactivo a varios dispositivos hará que los contenedores de volúmenes del primer dispositivo conmutado por error pierdan la propiedad de los datos. Después de este tipo de conmutación por error, estos contenedores de volúmenes aparecerán o se comportarán de forma diferente cuando se visualicen en el Portal de Azure clásico. | |Sí |Sin  |
| 5 |Instalación |Durante la instalación del adaptador de StorSimple para SharePoint, deberá proporcionar una dirección IP de dispositivo para que la instalación finalice correctamente. | |Sí |Sin  |
| 6 |Proxy web |Si la configuración de proxy web tiene HTTPS como protocolo especificado, la comunicación de dispositivo a servicio se verá afectada y el dispositivo se desconectará. También se generarán paquetes de compatibilidad en el proceso, que consumen muchos recursos en el dispositivo. |Asegúrese de que la dirección URL del proxy web tiene HTTP como protocolo especificado. Para obtener más información, vaya a [Configurar el proxy web para el dispositivo](storsimple-8000-configure-web-proxy.md). |Sí |Sin  |
| 7 |Proxy web |Si configura y habilita el proxy web en un dispositivo registrado, será necesario reiniciar el controlador activo en el dispositivo. | |Sí |Sin  |
| 8 |Latencia alta de la nube y alta carga de trabajo de E/S |Cuando el dispositivo StorSimple encuentra una combinación de latencias muy altas de la nube (del orden de segundos) y alta carga de trabajo de E/S, los volúmenes del dispositivo pasan a un estado degradado y las operaciones de E/S pueden fallar con el error «el dispositivo no está listo». |Necesitará reiniciar los controladores de dispositivo de forma manual o realizar una conmutación por error del dispositivo para recuperarse de esta situación. |Sí |Sin  |
| 9 |Azure PowerShell |Cuando se usa el cmdlet de StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** para seleccionar el primer objeto y crear un nuevo objeto **VolumeContainer**, el cmdlet devuelve todos los objetos. |Escriba el cmdlet entre paréntesis, como se indica a continuación: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait**. |Sí |Sí |
| 10 |Migración |Cuando se pasan varios contenedores de volúmenes para la migración, el ETA de la copia de seguridad más reciente solo es preciso en el primer contenedor de volúmenes. Además, la migración paralela se iniciará después de que se hayan migrado las cuatro primeras copias de seguridad del primer contenedor de volúmenes. |Se recomienda migrar los contenedores de volúmenes de uno en uno. |Sí |Sin  |
| 11 |Migración |Después de la restauración, los volúmenes no se agregan a la directiva de copia de seguridad ni al grupo de discos virtuales. |Para crear copias de seguridad, será preciso agregar estos volúmenes a una directiva de copia de seguridad. |Sí |Sí |
| 12 |Migración |Una vez completada la migración, el dispositivo de las series 5000/7000 no debe tener acceso a los contenedores de datos migrados. |Cuando la migración finaliza y se envía, se recomienda eliminar los contenedores de datos migrados. |Sí |Sin  |
| 13 |Clonación y recuperación ante desastres |Los dispositivos StorSimple con la actualización 1 no se pueden clonar. Tampoco se puede realizar la recuperación ante desastres en un dispositivo que ejecute el software anterior a la actualización 1. |Para que se puedan realizar estas operaciones, tendrá que implementar la actualización 1 en el dispositivo de destino |Sí |Sí |
| 14 |Migración |En los dispositivos de las series 5000-7000, se puede producir un error en la copia de seguridad de la configuración para la migración cuando haya grupos de volúmenes sin volúmenes asociados. |Elimine todos los grupos de volúmenes vacíos sin volúmenes asociados y vuelva a intentar realizar la copia de seguridad de la configuración. |Sí |Sin  |
| 15 |Cmdlets de Azure PowerShell y volúmenes anclados localmente |No se puede crear un volumen anclado localmente mediante los cmdlets de Azure PowerShell. (Se puede almacenar en capas cualquier volumen creado con Azure PowerShell). |Utilice siempre el servicio del administrador de StorSimple para configurar volúmenes anclados localmente. |Sí |Sin  |
| 16 |Espacio disponible para los volúmenes anclados localmente |Si elimina un volumen anclado localmente, puede que el espacio disponible para los volúmenes nuevos no se actualice inmediatamente. El servicio del administrador de StorSimple actualiza el espacio local disponible aproximadamente cada hora. |Espere una hora antes de intentar crear el nuevo volumen. |Sí |Sin  |
| 17 |Volúmenes anclados localmente |El trabajo de restauración expone la copia de seguridad de instantánea temporal en el catálogo de copia de seguridad, pero solo para la duración de la tarea de restauración. Además, expone un grupo de discos virtuales con el prefijo **tmpCollection** en la página **Directivas de copia de seguridad**, pero solo para la duración del trabajo de restauración. |Este comportamiento puede producirse si el trabajo de restauración ha anclado solo localmente volúmenes o una mezcla de volúmenes nivelados y anclados localmente. Si el trabajo de restauración incluye solo volúmenes nivelados, este comportamiento no se producirá. No se requiere ninguna intervención del usuario. |Sí |Sin  |
| 18 |Volúmenes anclados localmente |Si cancela un trabajo de restauración y se produce una conmutación por error de controlador inmediatamente después, el trabajo de restauración mostrará **Cancelado** en lugar de **Erróneo**. Si se produce un error en un trabajo de restauración y se produce una conmutación por error de controlador inmediatamente después, el trabajo de restauración mostrará **Cancelado** en lugar de **Erróneo**. |Este comportamiento puede producirse si el trabajo de restauración ha anclado solo localmente volúmenes o una mezcla de volúmenes nivelados y anclados localmente. Si el trabajo de restauración incluye solo volúmenes nivelados, este comportamiento no se producirá. No se requiere ninguna intervención del usuario. |Sí |Sin  |
| 19 |Volúmenes anclados localmente |Si se cancela un trabajo de restauración o si se produce un error en una restauración y, a continuación, se produce una conmutación por error de controlador, aparece un trabajo de restauración adicional en la página **Trabajos** . |Este comportamiento puede producirse si el trabajo de restauración ha anclado solo localmente volúmenes o una mezcla de volúmenes nivelados y anclados localmente. Si el trabajo de restauración incluye solo volúmenes nivelados, este comportamiento no se producirá. No se requiere ninguna intervención del usuario. |Sí |Sin  |
| 20  |Volúmenes anclados localmente |Si intenta convertir un volumen en niveles (creado y clonado con la actualización 1.2 o anterior) a un volumen anclado localmente y el dispositivo ya no tiene espacio o si hay una interrupción en la nube, es posible que los clones resulten dañados. |Este problema solo se produce con los volúmenes que se crearon y clonaron con software anterior a la actualización 2.1. Este debería ser un escenario poco frecuente. | | |
| 21 |Conversión de volumen |No actualice los ACR anexados a un volumen mientras se realiza una conversión de volumen (de un volumen en niveles a un volumen anclado localmente o viceversa). Actualizar los ACR podría dañar los datos. |En caso de ser necesario, actualice los ACR antes de realizar la conversión de volumen y no realice ninguna actualización ACR adicional mientras la conversión esté en progreso. | | |
| 22 |Actualizaciones |Al aplicar Update 3, la página **Mantenimiento** del Portal de Azure clásico mostrará el siguiente mensaje relacionado con Update 2: "La serie StorSimple 8000 Update 2 permite que Microsoft recopile proactivamente información de registro del dispositivo cuando se detecten posibles problemas". Esto puede resultar confuso ya que indica que el dispositivo se está actualizando a Update 2. Después de que el dispositivo se ha actualizado correctamente a Update 3, este mensaje desaparece. |Este comportamiento se solucionará en futuras versiones. |Sí |Sin  |

## <a name="controller-and-firmware-updates-in-update-3"></a>Actualizaciones de firmware y de controlador en Update 3
Esta versión tiene actualizaciones de firmware y controlador LSI. Para más información sobre cómo instalar el controlador LSI y las actualizaciones de firmware del disco, consulte [Instalación de Update 3](storsimple-install-update-3.md) en el dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Actualizaciones de dispositivo virtual en Update 3
Esta actualización no se puede aplicar a StorSimple Cloud Appliance (también conocido como el dispositivo virtual). Deben crearse nuevos dispositivos virtuales. 

## <a name="next-step"></a>Paso siguiente
Obtenga información sobre cómo [instalar Update 3](storsimple-install-update-3.md) en el dispositivo StorSimple.


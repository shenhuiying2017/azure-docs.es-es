---
title: "Notas de la versión de la actualización 2.2 de la serie StorSimple 8000 | Microsoft Docs"
description: "Se describen las nuevas características, problemas y soluciones alternativas de la serie StorSimple 8000 Update 2.2."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 06c14bdd24dd24a98b3838a2ba73b657ce56785a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Notas de la versión de la serie StorSimple 8000 Update 2.2
> [!NOTE]
> El portal clásico para StorSimple está en desuso. Los administradores de dispositivos StorSimple realizarán la transición automáticamente al nuevo Azure Portal según la programación para puesta en desuso. Recibirá un correo electrónico y una notificación del portal en los que se avisa de este paso. Este documento también se retirará pronto. Si tiene alguna pregunta sobre este paso, consulte las [preguntas frecuentes de la migración a Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Información general
Las siguientes notas de la versión describen las características nuevas e identifican los problemas críticos abiertos de la serie StorSimple 8000 Update 2.2. También contienen una lista de las actualizaciones de software de StorSimple incluidas en esta versión.

Update 2.2 puede aplicarse a cualquier dispositivo de StorSimple que ejecute software de versión (GA) o las actualizaciones de la 0.1 a la 2.1. La versión de dispositivo asociada a Update 2.2 es 6.3.9600.17708.

Revise la información contenida en las notas de la versión antes de implementar la actualización de la solución de StorSimple.

> [!IMPORTANT]
> * Update 2.2 incluye únicamente actualizaciones del software. Para instalar esta actualización, se necesitan en torno a una hora y media o dos. 
> * Si ejecuta Update 2.1, recomendamos que aplique Update 2.2 tan pronto como sea posible.
> * Para las nuevas versiones, no podrán ver las actualizaciones de inmediato porque hacemos una implementación por fases de las actualizaciones. Espere unos días y luego busque actualizaciones de nuevo ya que estas estarán disponibles pronto.
> 
> 

## <a name="whats-new-in-update-22"></a>Novedades de Update 2.2
Se realizaron las siguientes mejoras clave en Update 2.2.

* **Optimización de la recuperación de espacio automatizada** : cuando se eliminan datos en volúmenes con aprovisionamiento fino, se deben recuperar los bloques de almacenamiento no utilizados. Con esta versión, se ha mejorado el proceso de recuperación de espacio de la nube, con lo que el espacio no utilizado que se encontrará disponible antes que en versiones anteriores.
* **Mejoras de rendimiento de instantáneas** : en Update 2.2, se ha mejorado el tiempo que se tarda en procesar una instantánea de nube en determinados escenarios donde se utilizan grandes volúmenes y existe una actividad de datos mínima o nula. Los volúmenes de archivado, por ejemplo, sería un escenario donde se observarían ventajas gracias a esta mejora.
* **Protección de la recopilación del paquete de soporte** : en esta versión, se ha mejorado el modo en que se recopila y carga el paquete de soporte. 
* **Mejoras de la confiabilidad de Update** : esta versión contiene correcciones de errores que conllevan una mayor confiabilidad de Update.

## <a name="issues-fixed-in-update-22"></a>Problemas corregidos en Update 2.2
En las tablas siguientes se ofrece un resumen de los problemas corregidos en Update 2.2 y 2.1.    

| No | Característica | Problema | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Rendimiento del host |En la versión anterior, se observaban problemas de rendimiento del host durante la creación de un volumen anclado localmente y durante la conversión de un volumen en capas en uno anclado localmente. Estos problemas se han corregido en esta versión, lo que conlleva una mejora de rendimiento del host durante los procedimientos de creación y conversión de volúmenes. |Sí |No |
| 2 |Volúmenes anclados localmente |En escasas ocasiones, el sistema podría bloquearse al crear un volumen anclado localmente. Este error se ha corregido en esta versión. |Sí |No |
| 3 |Organización en niveles |Se produjeron bloqueos esporádicos cuando los metadatos para los dispositivos de la nube de StorSimple (8010 y 8020) se organizaban en niveles en la nube. Este problema está corregido en esta versión. |No |Sí |
| 4 |Creación de instantáneas |Se han producido problemas relacionados con la creación de instantáneas incrementales en escenarios con grandes volúmenes y una actividad de datos mínima o nula. Estos problemas se han corregido en esta versión. |Sí |Sí |
| 5 |Autenticación de Openstack |Al usar OpenStack como el proveedor de servicios en la nube, el usuario se encontraba con un error poco frecuente relacionado con la autenticación que terminaba con el bloqueo del analizador JSON. Este error se ha corregido en esta versión. |Sí |No |
| 6 |Copia del lado del host |En versiones anteriores del software, se ha visto un error poco frecuente relacionado con la temporización de ODX al copiar los datos de un volumen a otro. El resultado sería una conmutación por error del controlador y el sistema podría entrar en modo de recuperación. Este error se ha corregido en esta versión. |yes |No |
| 7 |Instrumental de administración de Windows (WMI) |En las versiones anteriores del software, había varias instancias de un error de proxy web con la excepción "<ManagementException> Error en la carga del proveedor". Este error se atribuye a una fuga de memoria WMI y se ha corregido. |Sí |No |
| 8 |Actualizar |En determinados casos excepcionales, en las versiones anteriores del software, el usuario recibía un error "CisPowershellHcsscripterror" al intentar analizar o instalar actualizaciones. Este problema está corregido en esta versión. |Sí |Sí |
| 9 |Paquete de soporte |En esta versión, se han realizado mejoras en la forma en que se recopila y carga el paquete de soporte. |Sí |Sí |

## <a name="known-issues-in-update-22"></a>Problemas conocidos de Update 2.2
En la tabla siguiente se proporciona un resumen de los problemas conocidos de esta versión.

| Nº | Característica | Problema | Comentarios / solución alternativa | Se aplica a un dispositivo físico | Se aplica a un dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Cuórum de disco |En raras ocasiones, si se desconecta la mayoría de los discos en el revestimiento de EBOD de un dispositivo 8600 y no se produce un cuórum de disco, el grupo de almacenamiento se desconectará. Seguirá desconectado incluso si se vuelven a conectar los discos. |Necesitará reiniciar el dispositivo. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft para conocer los pasos siguientes. |Sí |No |
| 2 |Identificador de controlador incorrecto |Cuando se realiza un reemplazo de controlador, el controlador 0 puede aparecer como controlador 1. Durante el reemplazo de controlador, cuando se carga la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el identificador del controlador del mismo nivel. En raras ocasiones, este comportamiento también puede aparecer después del reinicio del sistema. |No se requiere ninguna acción del usuario. La situación se solucionará una vez completado el reemplazo del controlador. |Sí |No |
| 3 |Cuentas de almacenamiento |El uso del servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no admitido. Esto provocará una situación en la que no se pueden recuperar los datos de usuario. | |Sí |Sí |
| 4 |Conmutación por error del dispositivo |No se admiten varias conmutaciones por error de un contenedor de volúmenes del mismo dispositivo de origen a diferentes dispositivos de destino. La conmutación por error de un único dispositivo inactivo a varios dispositivos hará que los contenedores de volúmenes del primer dispositivo conmutado por error pierdan la propiedad de los datos. Después de este tipo de conmutación por error, estos contenedores de volúmenes aparecerán o se comportarán de forma diferente cuando se visualicen en el Portal de Azure clásico. | |Sí |No |
| 5 |Instalación |Durante la instalación del adaptador de StorSimple para SharePoint, deberá proporcionar una dirección IP de dispositivo para que la instalación finalice correctamente. | |Sí |No |
| 6 |Proxy web |Si la configuración de proxy web tiene HTTPS como protocolo especificado, la comunicación de dispositivo a servicio se verá afectada y el dispositivo se desconectará. También se generarán paquetes de compatibilidad en el proceso, que consumen muchos recursos en el dispositivo. |Asegúrese de que la dirección URL del proxy web tiene HTTP como protocolo especificado. Para obtener más información, vaya a [Configurar el proxy web para el dispositivo](storsimple-configure-web-proxy.md). |Sí |No |
| 7 |Proxy web |Si configura y habilita el proxy web en un dispositivo registrado, será necesario reiniciar el controlador activo en el dispositivo. | |Sí |No |
| 8 |Latencia alta de la nube y alta carga de trabajo de E/S |Cuando el dispositivo StorSimple encuentra una combinación de latencias muy altas de la nube (del orden de segundos) y alta carga de trabajo de E/S, los volúmenes del dispositivo pasan a un estado degradado y las operaciones de E/S pueden fallar con el error «el dispositivo no está listo». |Necesitará reiniciar los controladores de dispositivo de forma manual o realizar una conmutación por error del dispositivo para recuperarse de esta situación. |Sí |No |
| 9 |Azure PowerShell |Cuando se usa el cmdlet de StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** para seleccionar el primer objeto y crear un nuevo objeto **VolumeContainer**, el cmdlet devuelve todos los objetos. |Escriba el cmdlet entre paréntesis, como se indica a continuación: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait**. |yes |Sí |
| 10 |Migración |Cuando se pasan varios contenedores de volúmenes para la migración, el ETA de la copia de seguridad más reciente solo es preciso en el primer contenedor de volúmenes. Además, la migración paralela se iniciará después de que se hayan migrado las cuatro primeras copias de seguridad del primer contenedor de volúmenes. |Se recomienda migrar los contenedores de volúmenes de uno en uno. |Sí |No |
| 11 |Migración |Después de la restauración, los volúmenes no se agregan a la directiva de copia de seguridad ni al grupo de discos virtuales. |Para crear copias de seguridad, será preciso agregar estos volúmenes a una directiva de copia de seguridad. |Sí |Sí |
| 12 |Migración |Una vez completada la migración, el dispositivo de las series 5000/7000 no debe tener acceso a los contenedores de datos migrados. |Cuando la migración finaliza y se envía, se recomienda eliminar los contenedores de datos migrados. |Sí |No |
| 13 |Clonación y recuperación ante desastres |Los dispositivos StorSimple con la actualización 1 no se pueden clonar. Tampoco se puede realizar la recuperación ante desastres en un dispositivo que ejecute el software anterior a la actualización 1. |Para que se puedan realizar estas operaciones, tendrá que implementar la actualización 1 en el dispositivo de destino |Sí |Sí |
| 14 |Migración |En los dispositivos de las series 5000-7000, se puede producir un error en la copia de seguridad de la configuración para la migración cuando haya grupos de volúmenes sin volúmenes asociados. |Elimine todos los grupos de volúmenes vacíos sin volúmenes asociados y vuelva a intentar realizar la copia de seguridad de la configuración. |Sí |No |
| 15 |Cmdlets de Azure PowerShell y volúmenes anclados localmente |No se puede crear un volumen anclado localmente mediante los cmdlets de Azure PowerShell. (Se puede almacenar en capas cualquier volumen creado con Azure PowerShell). |Utilice siempre el servicio del administrador de StorSimple para configurar volúmenes anclados localmente. |Sí |No |
| 16 |Espacio disponible para los volúmenes anclados localmente |Si elimina un volumen anclado localmente, puede que el espacio disponible para los volúmenes nuevos no se actualice inmediatamente. El servicio del administrador de StorSimple actualiza el espacio local disponible aproximadamente cada hora. |Espere una hora antes de intentar crear el nuevo volumen. |Sí |No |
| 17 |Volúmenes anclados localmente |El trabajo de restauración expone la copia de seguridad de instantánea temporal en el catálogo de copia de seguridad, pero solo para la duración de la tarea de restauración. Además, expone un grupo de discos virtuales con el prefijo **tmpCollection** en la página **Directivas de copia de seguridad**, pero solo para la duración del trabajo de restauración. |Este comportamiento puede producirse si el trabajo de restauración ha anclado solo localmente volúmenes o una mezcla de volúmenes nivelados y anclados localmente. Si el trabajo de restauración incluye solo volúmenes nivelados, este comportamiento no se producirá. No se requiere ninguna intervención del usuario. |Sí |No |
| 18 |Volúmenes anclados localmente |Si cancela un trabajo de restauración y se produce una conmutación por error de controlador inmediatamente después, el trabajo de restauración mostrará **Cancelado** en lugar de **Erróneo**. Si se produce un error en un trabajo de restauración y se produce una conmutación por error de controlador inmediatamente después, el trabajo de restauración mostrará **Cancelado** en lugar de **Erróneo**. |Este comportamiento puede producirse si el trabajo de restauración ha anclado solo localmente volúmenes o una mezcla de volúmenes nivelados y anclados localmente. Si el trabajo de restauración incluye solo volúmenes nivelados, este comportamiento no se producirá. No se requiere ninguna intervención del usuario. |Sí |No |
| 19 |Volúmenes anclados localmente |Si se cancela un trabajo de restauración o si se produce un error en una restauración y, a continuación, se produce una conmutación por error de controlador, aparece un trabajo de restauración adicional en la página **Trabajos** . |Este comportamiento puede producirse si el trabajo de restauración ha anclado solo localmente volúmenes o una mezcla de volúmenes nivelados y anclados localmente. Si el trabajo de restauración incluye solo volúmenes nivelados, este comportamiento no se producirá. No se requiere ninguna intervención del usuario. |Sí |No |
| 20 | |Volúmenes anclados localmente |Si intenta convertir un volumen en niveles (creado y clonado con la actualización 1.2 o anterior) a un volumen anclado localmente y el dispositivo ya no tiene espacio o si hay una interrupción en la nube, es posible que los clones resulten dañados. |Este problema solo se produce con los volúmenes que se crearon y clonaron con software anterior a la actualización 2.1. Este debería ser un escenario poco frecuente. | | |
| 21 |Conversión de volumen |No actualice los ACR anexados a un volumen mientras se realiza una conversión de volumen (de un volumen en niveles a un volumen anclado localmente o viceversa). Actualizar los ACR podría dañar los datos. |En caso de ser necesario, actualice los ACR antes de realizar la conversión de volumen y no realice ninguna actualización ACR adicional mientras la conversión esté en progreso. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Actualizaciones de firmware y de controlador en Update 2.2
Esta versión incluye únicamente actualizaciones del software. Sin embargo, si está actualizando desde una versión anterior de la actualización 2, tendrá que instalar el controlador, Storport, Spaceport y, en algunos casos las actualizaciones de firmware del disco en el dispositivo.

Para más información sobre cómo instalar el controlador, Storport, Spaceport y las actualizaciones de firmware del disco, consulte [Instalación de Update 2.2](storsimple-install-update-21.md) en el dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Actualizaciones de dispositivo virtual en Update 2.2
No se puede aplicar esta actualización al dispositivo virtual. Deben crearse nuevos dispositivos virtuales. 

## <a name="next-step"></a>Paso siguiente
Obtenga información sobre cómo [instalar Update 2.2](storsimple-install-update-21.md) en el dispositivo StorSimple.


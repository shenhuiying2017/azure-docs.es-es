---
title: "Administración del servicio Administrador de dispositivos de StorSimple | Microsoft Docs"
description: "Obtenga información sobre cómo administrar el dispositivo StorSimple mediante el servicio Administrador de dispositivos de StorSimple en Azure Portal."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 0e7d7f44a70278a7777ba6c32c8e546074953fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Uso del servicio Administrador de dispositivos de StorSimple para administrar el dispositivo StorSimple

## <a name="overview"></a>Información general

En este artículo se describe la interfaz del servicio Administrador de dispositivos de StorSimple, que incluye cómo conectarse a la misma, las diversas opciones disponibles y los vínculos a los flujos de trabajo específicos que se pueden realizar a través de esta interfaz de usuario. Esta guía es aplicable tanto al dispositivo físico de StorSimple como al dispositivo de nube.

Después de leer este artículo, aprenderá a:

* Conexión al servicio Administrador de dispositivos de StorSimple
* Administración del dispositivo StorSimple a través del servicio Administrador de dispositivos de StorSimple

## <a name="connect-to-storsimple-device-manager-service"></a>Conexión al servicio Administrador de dispositivos de StorSimple

El servicio StorSimple Device Manager se ejecuta en Microsoft Azure y se conecta a varios dispositivos de StorSimple. Use una instancia de Azure Portal central que se ejecute en un navegador para administrar estos dispositivos. Para conectarse al servicio StorSimple Device Manager, haga lo siguiente.

#### <a name="to-connect-to-the-service"></a>Para conectarse al servicio
1. Vaya a [https://portal.azure.com/](https://portal.azure.com/).
2. Con las credenciales de su cuenta Microsoft, inicie sesión en el Portal de Microsoft Azure (situado en la parte superior derecha del panel).
3. Desplácese hacia abajo en el panel de navegación izquierdo para obtener acceso al servicio de StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administración del dispositivo StorSimple mediante el servicio Administrador de dispositivos de StorSimple

En la siguiente tabla se muestra un resumen de todas las tareas comunes de administración y flujos de trabajo complejos que pueden llevarse a cabo en la interfaz de usuario del servicio StorSimple Device Manager. Estas tareas se organizan en función de las hojas de la interfaz de usuario en que se inician.

Para obtener más información sobre cada flujo de trabajo, haga clic en el procedimiento adecuado en la tabla.

#### <a name="storsimple-device-manager-workflows"></a>Flujos de trabajo de StorSimple Device Manager

| Si desea hacer esto... | Utilice este procedimiento. |
| --- | --- |
| Crear un servicio</br>Eliminar un servicio</br>Obtener la clave de registro del servicio</br>Regenerar la clave de registro del servicio regenerar |[Implementación de un servicio Administrador de dispositivos de StorSimple](storsimple-8000-manage-service.md) |
| Visualizar los registros de actividad |[Resumen de uso del servicio Administrador de dispositivos de StorSimple](storsimple-8000-service-dashboard.md) |
| Cambiar la clave de cifrado de datos de servicio</br>Ver los registros de operaciones |[Panel de uso del servicio Administrador de dispositivos de StorSimple](storsimple-8000-service-dashboard.md) |
| Desactivación de un dispositivo</br>Eliminar un dispositivo |[Desactivar o eliminar un dispositivo](storsimple-8000-deactivate-and-delete-device.md) |
| Obtener información sobre recuperación ante desastres y conmutación por error</br>Conmutación por error a un dispositivo físico</br>Conmutación por error en un dispositivo virtual</br>Recuperación ante desastres y continuidad empresarial (BCDR) |[Conmutación por error y recuperación ante desastres para el dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Enumerar copias de seguridad para un volumen</br>Seleccionar un conjunto de copia de seguridad</br>Eliminación de un conjunto de copia de seguridad |[Administrar copias de seguridad](storsimple-8000-manage-backup-catalog.md) |
| Clonar un volumen |[Clonación de un volumen](storsimple-8000-clone-volume-u2.md) |
| Restaurar un conjunto de copias de seguridad |[Restaurar un conjunto de copias de seguridad](storsimple-8000-restore-from-backup-set-u2.md) |
| Acerca de las cuentas de almacenamiento</br>Agregar una cuenta de almacenamiento</br>Editar una cuenta de almacenamiento</br>Eliminar una cuenta de almacenamiento</br>Rotación de claves de cuentas de almacenamiento |[Administrar cuentas de almacenamiento](storsimple-8000-manage-storage-accounts.md) |
| Acerca de las plantillas de ancho de banda</br>Agregar una plantilla de ancho de banda</br>Editar una plantilla de ancho de banda</br>Eliminar una plantilla de ancho de banda</br>Usar una plantilla de ancho de banda predeterminada</br>Crear una plantilla de ancho de banda para todo el día que comience a una hora especificada |[Administrar plantillas de ancho de banda](storsimple-8000-manage-bandwidth-templates.md) |
| Acerca de los registros de control de acceso</br>Crear un registro de control de acceso</br>Editar un registro de control de acceso</br>Eliminar un registro de control de acceso |[Administrar registros de control de acceso](storsimple-8000-manage-acrs.md) |
| Ver detalles del trabajo</br>Cancelación de un trabajo |[Trabajos de administración](storsimple-8000-manage-jobs-u2.md) |
| Recibir notificaciones de alerta</br>Administrar alertas</br>Revisar alertas |[Ver y administrar alertas de StorSimple](storsimple-8000-manage-alerts.md) |
| Gráficos de supervisión de dispositivos |[Supervisar su dispositivo StorSimple](storsimple-monitor-device.md) |
| Agregar un contenedor de volúmenes</br>Modificar un contenedor de volúmenes</br>Eliminar un contenedor de volúmenes |[Administrar contenedores de volúmenes](storsimple-8000-manage-volume-containers.md) |
| Agregar un volumen</br>Modificar un volumen</br>Desconectar un volumen</br>Eliminar un volumen</br>Supervisar un volumen |[Administración de volúmenes](storsimple-8000-manage-volumes-u2.md) |
| Modificar la configuración del dispositivo</br>Modificar la configuración del tiempo</br>Modificar la configuración de DNS.md</br>Configurar interfaces de red |[Modificar la configuración de dispositivo de su dispositivo StorSimple device](storsimple-8000-modify-device-config.md) |
| Ver la configuración de proxy web |[Configurar el proxy web para el dispositivo](storsimple-8000-configure-web-proxy.md) |
| Modificar la contraseña del administrador de dispositivos</br>Modificar la contraseña de Snapshot Manager de StorSimple |[Cambiar las contraseñas de StorSimple](storsimple-8000-change-passwords.md) |
| Configuración de la administración remota |[Conectarse de forma remota al dispositivo StorSimple](storsimple-8000-remote-connect.md) |
| Configurar alertas |[Ver y administrar alertas de StorSimple](storsimple-8000-manage-alerts.md) |
| Configurar CHAP para el dispositivo StorSimple |[Configurar CHAP para el dispositivo StorSimple](storsimple-configure-chap.md) |
| Agregar una directiva de copia de seguridad</br>Incorporación o modificación de una programación</br>Eliminación de una directiva de copia de seguridad</br>Creación de una copia de seguridad manual</br>Creación de una directiva de copia de seguridad personalizada con varios volúmenes y programaciones |[Administrar directivas de copia de seguridad](storsimple-8000-manage-backup-policies-u2.md) |
| Detener los controladores de dispositivo</br>Reiniciar los controladores de dispositivo</br>Apagar los controladores de dispositivo</br>Restablecer el dispositivo a los valores predeterminados de fábrica</br>(Lo anterior es solo para dispositivos locales) |[Administrar controladores de dispositivo StorSimple](storsimple-8000-manage-device-controller.md) |
| Obtener información sobre los componentes de hardware de StorSimple</br>Supervisar el estado del hardware</br>(Lo anterior es solo para dispositivos locales) |[Supervisar componentes de hardware](storsimple-8000-monitor-hardware-status.md) |
| Crear un paquete de soporte |[Crear y administrar paquetes de soporte técnico](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalación de actualizaciones de software |[Actualizar su dispositivo](storsimple-update-device.md) |

## <a name="next-steps"></a>Pasos siguientes

Si tiene algún problema con la operación diaria de su dispositivo StorSimple o con cualquiera de sus componentes de hardware, consulte:

* [Solución de problemas mediante la herramienta de diagnóstico](storsimple-8000-diagnostics.md)
* [Utilizar los LED Indicadores de supervisión de StorSimple](storsimple-monitoring-indicators.md)

Si no puede resolver los problemas y necesita crear una solicitud de servicio, póngase en contacto con el [Servicio de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md).


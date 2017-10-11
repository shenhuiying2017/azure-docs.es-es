---
title: "Administración de la interfaz de usuario web de la matriz virtual de StorSimple | Microsoft Docs"
description: "Describe cómo realizar tareas de administración básicas en los dispositivos mediante la interfaz de usuario web de la matriz virtual de StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 989e7b697f9b527df549fb32be18edd1d3c8d224
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Usar la interfaz de usuario web para administrar la matriz virtual de StorSimple
![flujo del proceso de instalación](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Información general
Los tutoriales de este artículo se aplican a la matriz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple) que se ejecuta en la versión de disponibilidad general de marzo de 2016. En este artículo se describen algunos de los flujos de trabajo complejos y tareas de administración que se pueden realizar en la matriz virtual de StorSimple. Puede administrar la matriz virtual de StorSimple mediante la interfaz de usuario del servicio StorSimple Manager (denominada portal de interfaz de usuario) y la interfaz de usuario web local del dispositivo. En este artículo nos centraremos en las tareas que puede realizar mediante la interfaz de usuario web.

Este artículo incluye los siguientes tutoriales:

* Obtener la clave de cifrado de los datos del servicio
* Solucionar los problemas de instalación de la interfaz de usuario web
* Crear un paquete de registro
* Apagar o reiniciar el dispositivo

## <a name="get-the-service-data-encryption-key"></a>Obtener la clave de cifrado de los datos del servicio
Una clave de cifrado de los datos del servicio se genera cuando se registra el primer dispositivo mediante el servicio StorSimple Manager. Esta clave y la clave de registro del servicio son necesarias para registrar dispositivos adicionales en el servicio StorSimple Manager.

Si ha perdido su clave de cifrado de los datos del servicio y necesita recuperarla, siga los siguientes pasos en la interfaz de usuario web local del dispositivo que haya registrado con el servicio.

#### <a name="to-get-the-service-data-encryption-key"></a>Cómo obtener la clave de cifrado de los datos del servicio
1. Conéctese a la interfaz de usuario web local Vaya a **Configuración** > **Configuración de la nube**.
2. En la parte inferior de la página, haga clic en **Obtener clave de cifrado de datos del servicio**. Aparecerá una clave. Cópiela y guárdela.
   
    ![obtener la clave de cifrado de los datos del servicio 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Solucionar los problemas de instalación de la interfaz de usuario web
Es posible que vea algún error cuando configure el dispositivo a través de la interfaz de usuario web local. Para diagnosticar y solucionar estos errores, puede ejecutar las pruebas de diagnóstico.

#### <a name="to-run-the-diagnostic-tests"></a>Ejecutar las pruebas de diagnóstico
1. En la interfaz de usuario web local, vaya a **Solución de problemas** > **Pruebas de diagnóstico**.
   
    ![ejecutar diagnósticos 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. En la parte inferior de la página, haga clic en **Ejecutar pruebas de diagnóstico**. Con esto, iniciará las pruebas para diagnosticar los posibles problemas con la red, el dispositivo, el proxy web, la hora o la configuración de la nube. Verá una notificación que le indica que el dispositivo está ejecutando algunas pruebas.
3. Cuando hayan terminado estas pruebas, podrá ver los resultados. En el siguiente ejemplo se muestra el resultado de las pruebas de diagnóstico. Tenga en cuenta que la configuración del proxy web no se ha realizado en este dispositivo y que, por lo tanto, no se ejecutó la prueba del proxy web. El resto de las pruebas de la configuración de red, el servidor DNS y la hora se realizaron correctamente.
   
    ![ejecutar diagnósticos 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Crear un paquete de registro
Un paquete de registro contiene todos los registros relevantes que pueden ayudar al equipo de soporte técnico de Microsoft a solucionar los problemas del dispositivo. En esta versión, se puede generar un paquete de registro a través de la interfaz de usuario web local.

#### <a name="to-generate-the-log-package"></a>Generar el paquete de registro
1. En la interfaz de usuario web local, vaya a **Solución de problemas** > **Registros del sistema**.
   
    ![crear un paquete de registro 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. En la parte inferior de la página, haga clic en **Crear paquete de registro**. Se creará un paquete de los registros del sistema. Este proceso tardará unos minutos.
   
    ![crear un paquete de registro 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Verá una notificación una vez el paquete se cree correctamente y la página se actualizará para indicar la hora y la fecha durante las cuales se creó el mismo.
   
    ![crear un paquete de registro 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Haga clic en **Descargar paquete de registro**. Se descargará un paquete comprimido en su sistema.
   
    ![crear un paquete de registro 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Puede descomprimir el paquete de registro que haya descargado y ver los archivos de registro del sistema.

## <a name="shut-down-and-restart-your-device"></a>Apagar y reiniciar el dispositivo
Puede apagar o reiniciar el dispositivo virtual mediante la interfaz de usuario web local. Se recomienda que antes de reiniciar, desconecte los volúmenes o recursos compartidos en el host y, luego, el dispositivo. Esto minimizará la posibilidad de daños en los datos. 

#### <a name="to-shut-down-your-virtual-device"></a>Apagar el dispositivo virtual
1. En la interfaz de usuario web local, vaya a **Mantenimiento** > **Configuración de energía**.
2. En la parte inferior de la página, haga clic en **Apagar**.
   
    ![apagar el dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Aparecerá una advertencia que le indicará que si apaga el dispositivo se interrumpirá cualquier operación de E/S que estuviera en curso, lo que producirá un tiempo de inactividad. Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![advertencia de apagado del dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Cuando se haya iniciado el apagado verá una notificación.
   
    ![proceso de apagado del dispositivo iniciado](./media/storsimple-ova-web-ui-admin/image38.png)
   
    El dispositivo se apagará. Si quiere iniciar el dispositivo, debe hacerlo a través del Administrador de Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Reiniciar el dispositivo virtual
1. En la interfaz de usuario web local, vaya a **Mantenimiento** > **Configuración de energía**.
2. En la parte inferior de la página, haga clic en **Reiniciar**.
   
    ![proceso de reinicio del dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)
3. Aparecerá una advertencia que le indicará que si reinicia el dispositivo se interrumpirá cualquier operación de E/S que estuviera en curso, lo que resultará en un tiempo de inactividad. Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![advertencia de reinicio](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Verá una notificación cuando comience el reinicio.
   
    ![proceso de reinicio comenzado](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Mientras el proceso de reinicio esté en curso, perderá la conexión a la interfaz de usuario. De todos modos, puede supervisar el proceso de reinicio si actualiza la interfaz de usuario de forma regular. Como alternativa, puede supervisar el estado del proceso de reinicio del dispositivo a través del Administrador de Hyper-V.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre cómo [usar el servicio StorSimple Manager para administrar su dispositivo](storsimple-virtual-array-manager-service-administration.md).


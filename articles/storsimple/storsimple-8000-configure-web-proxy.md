---
title: "Configuración de proxy web para dispositivos de la serie StorSimple 8000 | Microsoft Docs"
description: "Obtenga información acerca de cómo usar Windows PowerShell para StorSimple para configurar opciones de proxy web para el dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: 
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 1109e44ed9c6aa8a0f7305b8a50410316711589c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurar el proxy web para el dispositivo StorSimple

## <a name="overview"></a>Información general

Este tutorial describe cómo utilizar Windows PowerShell para StorSimple para configurar y ver la configuración de proxy web de su dispositivo StorSimple. El dispositivo StorSimple utiliza la configuración de proxy web cuando se comunica con la nube. Un servidor proxy web se utiliza para agregar otra capa de seguridad, filtrar contenido, como memoria caché para facilitar el cumplimiento de los requisitos de ancho de banda o incluso para ayudar en los análisis.

Las instrucciones de este artículo se aplican solo a los dispositivos físicos de la serie StorSimple 8000. La configuración del proxy web no se admite en StorSimple Cloud Appliance (8010 y 8020).

El proxy web es una configuración _opcional_ de su dispositivo StorSimple. Puede configurar el proxy web solo a través de Windows PowerShell para StorSimple. La configuración es un proceso de dos pasos, como sigue:

1. En primer lugar, configure los valores de proxy web mediante el asistente de instalación o los cmdlets de Windows PowerShell para StorSimple.
2. A continuación, habilite los valores de proxy web configurados a través de los cmdlets de Windows PowerShell para StorSimple.

Una vez completada la configuración de proxy web, puede ver los valores configurados en el servicio Microsoft Azure StorSimple Device Manager y en Windows PowerShell para StorSimple.

Después de leer este tutorial, podrá:

* Configurar el proxy web mediante el asistente para instalación y los cmdlets.
* Habilitar el proxy web mediante cmdlets.
* Ver la configuración del proxy web en Azure Portal.
* Solución de errores durante la configuración del proxy web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurar el proxy web a través de Windows PowerShell para StorSimple

Puede utilizar cualquiera de las siguientes opciones para configurar el proxy web:

* El asistente de instalación que le guiará a través de los pasos de configuración.
* Los cmdlets de Windows PowerShell para StorSimple

En las siguientes secciones se detallan cada uno de estos métodos.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configuración del proxy web mediante el asistente de instalación

Use al asistente para configuración para que le guíe a través de los pasos de configuración del proxy web. Lleve a cabo los siguientes pasos para configurar el proxy web en su dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar el proxy web mediante el asistente de instalación

1. En el menú de la consola serie, elija la opción 1, **Iniciar sesión con acceso total** y escriba la **contraseña de administrador de dispositivo**. Escriba el siguiente comando para iniciar la sesión del asistente de configuración:
   
    `Invoke-HcsSetupWizard`
2. Si es la primera vez que utiliza el asistente de configuración para el registro de dispositivos, es preciso que configure todos los valores de red requeridos hasta que llegue a la configuración del proxy web. Si el dispositivo ya está registrado, acepte todos los valores de red configurados hasta que llegue a la configuración del proxy web. En el asistente para instalación, cuando se le solicite configurar los valores del proxy web, escriba **Sí**.
3. Para la **URL del Proxy Web**especifique la dirección IP o el nombre de dominio completo (FQDN) del servidor proxy web y el número de puerto TCP que desea que el dispositivo utilice cuando se comunique con la nube. Utilice el siguiente formato:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    De manera predeterminada, se especifica el número de puerto TCP 8080.
4. Elija el tipo de autenticación entre **NTLM**, **Básica** o **Ninguna**. La autenticación Básica es la menos segura para la configuración del servidor proxy. NT LAN Manager (NTLM) es un protocolo de autenticación complejo y muy seguro que utiliza un sistema de mensajería de tres vías (a veces cuatro si se requiere integridad adicional) para autenticar a un usuario. La autenticación predeterminada es NTLM. Para obtener más información, consulte autenticación [Básica](http://hc.apache.org/httpclient-3.x/authentication.html) y [NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **En el servicio StorSimple Device Manager, los gráficos de supervisión de dispositivos no funcionan cuando la autenticación básica o NTLM está habilitadas en la configuración del servidor proxy del dispositivo. Para que los gráficos de supervisión funcionen, debe asegurarse de que la autenticación esté establecida en NINGUNA.**
  
5. Si ha habilitado la autenticación, escriba un **nombre de usuario del proxy web** y una **contraseña del proxy web**. También tiene que confirmar la contraseña.
   
    ![Configurar el proxy web en el dispositivo StorSimple 1](./media/storsimple-configure-web-proxy/IC751830.png)

Si va a registrar su dispositivo por primera vez, continúe con el registro. Si su dispositivo ya está registrado, el asistente se cierra y se guardan los valores configurados.

El proxy web ya está habilitado. Puede omitir el paso en el que se [habilita el proxy web](#enable-web-proxy) e ir directamente a [ver la configuración del proxy web en Azure Portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurar el proxy web mediante Windows PowerShell para StorSimple

Una forma alternativa de configurar los valores de proxy web es mediante los cmdlets de Windows PowerShell para StorSimple. Lleve a cabo los siguientes pasos para configurar el proxy web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Para configurar el proxy web mediante los cmdlets
1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador del dispositivo**. La contraseña predeterminada es `Password1`.
2. En el símbolo del sistema, escriba:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Escriba y confirme la contraseña cuando se le pida.
   
    ![Configurar el proxy web en el dispositivo StorSimple 3](./media/storsimple-configure-web-proxy/IC751831.png)

El proxy web ya está configurado y debe habilitarse.

## <a name="enable-web-proxy"></a>Habilitar el proxy web

El proxy web está deshabilitado de manera predeterminada. Después de configurar los valores del proxy web en el dispositivo StorSimple, use Windows PowerShell para StorSimple para habilitar la configuración del proxy web.

> [!NOTE]
> **Este paso no se requiere si se ha usado el asistente para configurar el proxy web. El proxy web se habilita automáticamente de manera predeterminada después de una sesión del asistente de configuración.**


Lleve a cabo los siguientes pasos en Windows PowerShell para StorSimple para habilitar el proxy web en el dispositivo:

#### <a name="to-enable-web-proxy"></a>Para habilitar el proxy web
1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador del dispositivo**. La contraseña predeterminada es `Password1`.
2. En el símbolo del sistema, escriba:
   
    `Enable-HcsWebProxy`
   
    La configuración de proxy web ya está habilitada en su dispositivo StorSimple.
   
    ![Configurar el proxy web en el dispositivo StorSimple 4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Visualización de la configuración del proxy web en Azure Portal

Los valores del proxy web se configuran mediante la interfaz de Windows PowerShell y no se pueden modificar desde el portal, pero se pueden ver desde él. Lleve a cabo los siguientes pasos para ver el proxy web:

#### <a name="to-view-web-proxy-settings"></a>Para ver la configuración de proxy web:
1. Navegue hasta **Servicio StorSimple Device Manager > Dispositivos**. Seleccione un dispositivo y haga clic en él y, luego, vaya a **Configuración del dispositivo > Red**.

    ![Haga clic en Red](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. En la hoja **Configuración de red**, haga clic en el icono **Proxy web**.

    ![Haga clic en Proxy web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. En la hoja **Proxy web**, revise los valores configurados del proxy web en el dispositivo StorSimple.
   
    ![Ver la configuración de proxy web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Errores durante la configuración del proxy web

Si el proxy web se configuró de manera incorrecta, se muestran mensajes de error al usuario en el Windows PowerShell para StorSimple. En la siguiente tabla se detallan algunos mensajes de error, sus causas probables y las acciones recomendadas.

| Nº de serie | Código de error HRESULT | Posible causa principal | Acción recomendada |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |El comando se ejecuta desde el controlador pasivo y no puede comunicarse con el controlador activo. |Ejecute el comando en el controlador activo. Para ejecutar el comando desde el controlador pasivo, es preciso que arregle la conectividad del controlador pasivo al activo. Si no hay conectividad, debe ponerse en contacto con el soporte técnico de Microsoft. |
| 2. |0x800710dd - El identificador de la operación no es válido |StorSimple Cloud Appliance no admite la configuración de proxy. |StorSimple Cloud Appliance no admite la configuración de proxy. La misma solo puede establecerse en un dispositivo StorSimple físico. |
| 3. |0x80070057 - Parámetro no válido |Uno de los parámetros proporcionados para la configuración del proxy no es válido. |El URI no se proporciona con el formato correcto. Utilice el siguiente formato: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - Servidor RPC no disponible |La causa principal es una de las siguientes:</br></br>El clúster no está activo. </br></br>El servicio de ruta de datos no se está ejecutando.</br></br>El comando se ejecuta desde el controlador pasivo y no puede comunicarse con el controlador activo. |Póngase en contacto con el soporte técnico de Microsoft para asegurarse de que el clúster está activo y que el servicio de ruta de datos se ejecuta.</br></br>Ejecute el comando desde el controlador activo. Si desea ejecutar el comando desde el controlador pasivo, debe asegurarse de que el controlador pasivo puede comunicarse con el activo. Si no hay conectividad, debe ponerse en contacto con el soporte técnico de Microsoft. |
| 5. |0X800706be - Llamada RPC con errores |El clúster está inactivo. |Póngase en contacto con el soporte técnico de Microsoft para asegurarse de que el clúster está activo. |
| 6. |0x8007138f - Recurso de clúster no encontrado |El recurso de clúster del servicio de plataforma no se encuentra. Esto puede ocurrir cuando la instalación no se ha realizado correctamente. |Puede que sea necesario realizar un restablecimiento de fábrica en el dispositivo. Puede que sea necesario crear un recurso de plataforma. Póngase en contacto con el servicio de soporte técnico de Microsoft para conocer los pasos siguientes. |
| 7. |0x8007138c - El recurso de clúster no está en línea |Los recursos de clúster de ruta de datos o plataforma no están en línea. |Póngase en contacto con el soporte técnico de Microsoft para asegurarse de que tanto la ruta de datos como el recurso del servicio de plataforma están en línea. |

> [!NOTE]
> * La lista anterior de mensajes de error no es exhaustiva.
> * Los errores relacionados con la configuración del proxy web no se muestran en Azure Portal en el servicio StorSimple Device Manager. Si hay un problema con el proxy web una vez completada la configuración, el estado del dispositivo cambiará a **Sin conexión** en el Portal clásico.

## <a name="next-steps"></a>Pasos siguientes
* Si experimenta problemas durante la implementación del dispositivo o la configuración de los valores de proxy web, vea [Solución de problemas de implementación del dispositivo StorSimple](storsimple-troubleshoot-deployment.md).
* Para aprender a usar el servicio StorSimple Device Manager, vaya a ///[Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md) (Uso del servicio StorSimple Device Manager para administrar un dispositivo de StorSimple).


---
title: Conectarse de forma remota al dispositivo StorSimple | Microsoft Docs
description: "Explica cómo configurar el dispositivo para la administración remota y, a continuación, cómo conectarse a Windows PowerShell para StorSimple a través de HTTP o HTTPS."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff76884f020a0fb8a1b48bd371c419bd65e85fd3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Conexión de forma remota al dispositivo StorSimple serie 8000

## <a name="overview"></a>Información general

Puede conectarse de forma remota al dispositivo a través de Windows PowerShell. Cuando se conecta de este modo, no ve un menú. (Verá un menú solo si utiliza la consola en serie del dispositivo para conectarse). Con la conexión remota de Windows PowerShell, se conecta a un espacio de ejecución específico. También puede especificar el idioma de visualización.

Para obtener más información acerca del uso de la conexión remota de Windows PowerShell para administrar el dispositivo, vaya a [Uso de Windows PowerShell para StorSimple para administrar su dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).

Este tutorial explica cómo configurar el dispositivo para la administración remota y, a continuación, cómo conectarse a Windows PowerShell para StorSimple. Puede utilizar HTTP o HTTPS para conectarse de forma remota mediante Windows PowerShell. Sin embargo, cuando decide cómo conectarse a Windows PowerShell para StorSimple, tenga en cuenta la siguiente información:

* Es seguro conectarse directamente a la consola en serie del dispositivo, pero la conexión a la consola en serie a través conmutadores de red no lo es. Tenga presente los riesgos de seguridad al conectarse a la consola en serie del dispositivo a través de los conmutadores de red.
* Conectarse a través de una sesión HTTP puede ofrecer más seguridad que la conexión a través de la consola en serie a través de la red. Aunque este no es el método más seguro, es aceptable en redes de confianza.
* La conexión a través de una sesión HTTPS con un certificado autofirmado es la opción más segura y la recomendada.

Puede conectarse de forma remota a la interfaz de Windows PowerShell. No obstante, el acceso remoto a su dispositivo StorSimple mediante la interfaz de Windows PowerShell no está habilitado de forma predeterminada. Deberá habilitar primero la administración remota en el dispositivo y, a continuación, habilitarlo en el cliente que se utilizará para acceder al dispositivo.

Los pasos descritos en este artículo se realizaron en un sistema host con Windows Server 2012 R2.

## <a name="connect-through-http"></a>Conectarse a través de HTTP

Conectarse a Windows PowerShell para StorSimple a través de una sesión HTTP proporciona una mayor seguridad que la conexión a través de la consola en serie del dispositivo StorSimple. Aunque este no es el método más seguro, es aceptable en redes de confianza.

Puede usar Azure Portal o la consola en serie para configurar la administración remota. Seleccione entre los siguientes procedimientos:

* [Uso de Azure Portal para habilitar la administración remota a través de HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)
* [Utilice la consola en serie para habilitar la administración remota a través de HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Después de habilitar la administración remota, utilice el procedimiento siguiente para preparar al cliente para una conexión remota.

* [Preparar al cliente para la conexión remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Uso de Azure Portal para habilitar la administración remota a través de HTTP

Siga estos pasos en Azure Portal para habilitar la administración remota a través de HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Para habilitar la administración remota a través de Azure Portal

1. Vaya al servicio StorSimple Device Manager. Seleccione **Dispositivos** y, a continuación, seleccione y haga clic en el dispositivo que desea configurar para la administración remota. Vaya a **Configuración del dispositivo > Seguridad**.
2. En la hoja **Configuración de seguridad**, haga clic en **Administración remota**.
3. En la hoja **Administración remota**, establezca **Habilitar administración remota** en **Sí**.
4. Ahora puede elegir conectarse con HTTP. (La configuración predeterminada es conectarse a través de HTTPS). Asegúrese de que se selecciona HTTP.
   
   > [!NOTE]
   > La conexión a través de HTTP solo es aceptable en redes de confianza.
   
5. Haga clic en **Guardar** y, cuando se le pida confirmación, seleccione **Sí**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utilice la consola en serie para habilitar la administración remota a través de HTTP
Realice los pasos siguientes en la consola en serie del dispositivo para habilitar la administración remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar la administración remota a través de la consola en serie del dispositivo
1. En el menú de la consola serie, seleccione la opción 1. Para obtener más información sobre el uso de la consola serie en el dispositivo, vaya a [Conéctese a Windows PowerShell para StorSimple mediante la consola serie del dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. En el símbolo del sistema, escriba: `Enable-HcsRemoteManagement –AllowHttp`
3. Se le notificará acerca de las vulnerabilidades de seguridad del uso de HTTP para conectarse al dispositivo. Cuando se le solicite, confirme escribiendo **S**.
4. Compruebe que está habilitado HTTP escribiendo: `Get-HcsSystem`
5. Compruebe que el campo **RemoteManagementMode** muestre **HttpsAndHttpEnabled**. La ilustración siguiente muestra esta configuración en PuTTY.
   
     ![Serie HTTPS y HTTP habilitada](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar al cliente para la conexión remota
Realice los pasos siguientes en el cliente para habilitar la administración remota.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar al cliente para la conexión remota
1. Iniciar una sesión de Windows PowerShell como administrador.
2. Escriba el siguiente comando para agregar la dirección IP del dispositivo StorSimple a la lista de hosts de confianza del cliente:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Reemplace <*device_ip*> por la dirección IP del dispositivo; por ejemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Escriba el siguiente comando para guardar las credenciales del dispositivo en una variable: 
   
    ```
    $cred = Get-Credential
    ```
    
4. En el cuadro de diálogo que aparece:
   
   1. Escriba el nombre de usuario en este formato: *device_ip\SSAdmin*.
   2. Escriba la contraseña del administrador de dispositivos que se estableció cuando configuró el dispositivo con el asistente para la instalación. La contraseña predeterminada es *Password1*.
5. Inicie una sesión de Windows PowerShell en el dispositivo mediante este comando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Para crear una sesión de Windows PowerShell para su uso con el dispositivo virtual StorSimple, anexe el parámetro `–Port` y especifique el puerto público que configuró en Conexión remota para dispositivo virtual StorSimple.
   
   
En este momento, debe tener una sesión remota activa de Windows PowerShell en el dispositivo.
   
![Conexión remota de PowerShell mediante HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Conectarse a través de HTTPS

Conectarse a Windows PowerShell para StorSimple a través de una sesión HTTPS es el método más seguro y recomendado de conexión remota a su dispositivo StorSimple de Microsoft Azure. Los procedimientos siguientes explican cómo configurar los equipos cliente y consola en serie de modo que pueda usar HTTPS para conectarse a Windows PowerShell para StorSimple.

Puede usar Azure Portal o la consola en serie para configurar la administración remota. Seleccione entre los siguientes procedimientos:

* [Uso de Azure Portal para habilitar la administración remota a través de HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)
* [Utilice la consola en serie para habilitar la administración remota a través de HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Después de habilitar la administración remota, utilice los procedimientos siguientes para preparar el host para la administración remota y conectarse con el dispositivo desde el host remoto.

* [Preparar el host para la administración remota](#prepare-the-host-for-remote-management)
* [Conectarse al dispositivo desde el host remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Uso de Azure Portal para habilitar la administración remota a través de HTTPS

Siga estos pasos en Azure Portal para habilitar la administración remota a través de HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Para habilitar la administración remota a través de HTTPS desde Azure Portal

1. Vaya al servicio StorSimple Device Manager. Seleccione **Dispositivos** y, a continuación, seleccione y haga clic en el dispositivo que desea configurar para la administración remota. Vaya a **Configuración del dispositivo > Seguridad**.
2. En la hoja **Configuración de seguridad**, haga clic en **Administración remota**.
3. Establezca **Habilitar administración remota** en **Sí**.
4. Ahora puede elegir conectarse mediante HTTPS. (La configuración predeterminada es conectarse a través de HTTPS). Asegúrese de que se ha seleccionado HTTPS.
5. Haga clic en ... y, después, en **Descargar certificado de administración remota**. Especifique una ubicación para guardar este archivo. Deberá instalar este certificado en el equipo cliente o host que usará para conectarse al dispositivo.
6. Haga clic en **Guardar** y, cuando se le pida confirmación, haga clic en **Sí**.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utilice la consola en serie para habilitar la administración remota a través de HTTPS

Realice los pasos siguientes en la consola en serie del dispositivo para habilitar la administración remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar la administración remota a través de la consola en serie del dispositivo
1. En el menú de la consola serie, seleccione la opción 1. Para obtener más información sobre el uso de la consola serie en el dispositivo, vaya a [Conéctese a Windows PowerShell para StorSimple mediante la consola serie del dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. En el símbolo del sistema, escriba: 
   
     `Enable-HcsRemoteManagement`
   
    Esto debe habilitar HTTPS en el dispositivo.
3. Compruebe que se ha habilitado HTTPS escribiendo: 
   
     `Get-HcsSystem`
   
    Asegúrese de que el campo **RemoteManagementMode** muestre **HttpsEnabled**. La ilustración siguiente muestra esta configuración en PuTTY.
   
     ![Serie HTTPS habilitada](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Desde la salida de `Get-HcsSystem`, copie el número de serie del dispositivo y guárdelo para usarlo más adelante.
   
   > [!NOTE]
   > El número de serie se asigna al nombre CN en el certificado.
   
5. Obtenga un certificado de administración remota, escriba: 
   
     `Get-HcsRemoteManagementCert`
   
    Aparecerá un certificado similar al siguiente.
   
    ![Obtener certificado de administración remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Copie la información en el certificado desde **---BEGIN CERTIFICATE---** hasta **---END CERTIFICATE---** en un editor de texto, como Bloc de notas, y guárdelo como un archivo .cer. (Copiará este archivo en el host remoto al preparar el host.)
   
   > [!NOTE]
   > Para generar un nuevo certificado, utilice cmdlet `Set-HcsRemoteManagementCert`.
   
### <a name="prepare-the-host-for-remote-management"></a>Preparar el host para la administración remota

Para preparar el equipo host para una conexión remota que utiliza una sesión HTTPS, realice los siguientes procedimientos:

* [Importación del archivo .cer en el almacén raíz del cliente o un host remoto](#to-import-the-certificate-on-the-remote-host).
* [Incorporación de los números de serie del dispositivo al archivo de hosts en el host remoto](#to-add-device-serial-numbers-to-the-remote-host).

A continuación se describe cada uno de los procedimientos anteriores.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar el certificado en el host remoto
1. Haga clic con el botón secundario en el archivo .cer y seleccione **Instalar certificado**. Esto iniciará el asistente para importar certificados.
   
    ![Asistente para importación de certificados 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Para **Ubicación de almacén**, seleccione **Equipo local** y, después, haga clic en **Siguiente**.
3. Seleccione **Colocar todos los certificados en el siguiente almacén** y, luego, haga clic en **Examinar**. Navegue al almacén raíz del host remoto y, a continuación, haga clic en **Siguiente**.
   
    ![Asistente para importación de certificados 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Haga clic en **Finalizar** Aparece un mensaje que indica que la importación se ha realizado correctamente.
   
    ![Asistente para importación de certificados 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para agregar números de serie del dispositivo con el host remoto
1. Inicie el Bloc de notas como administrador y, a continuación, abra el archivo de hosts que se encuentra en \Windows\System32\Drivers\etc.
2. Agregue las siguientes tres entradas al archivo de hosts: **Dirección IP de DATA 0**, **Dirección IP fija del Controlador 0** y **Dirección IP fija del Controlador 1**.
3. Escriba el número de serie del dispositivo que guardó anteriormente. Asigne esto a la dirección IP como se muestra en la siguiente imagen. Para Controlador 0 y 1, anexe **Controller0** y **Controller1** al final del número de serie (nombre CN).
   
    ![Adición de nombre CN a archivo de hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Guarde el archivo de hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Conectarse al dispositivo desde el host remoto

Use Windows PowerShell y SSL para iniciar una sesión de SSAdmin en el dispositivo desde un host remoto o cliente. La sesión de SSAdmin se asigna a la opción 1 en el menú de la [consola serie](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) del dispositivo.

Realice el procedimiento siguiente en el equipo desde el que desea realizar la conexión remota de Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para iniciar una sesión de SSAdmin en el dispositivo mediante el uso de Windows PowerShell y SSL
1. Iniciar una sesión de Windows PowerShell como administrador.
2. Agregue la dirección IP del dispositivo a los hosts de confianza del cliente, escriba:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Donde *<device_ip>* es la dirección IP del dispositivo; por ejemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Para crear una nueva credencial, escriba:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Donde <*dirección IP del dispositivo de destino*> es la dirección IP de DATA 0 para el dispositivo; por ejemplo, **10.126.173.90** tal como se muestra en la imagen anterior del archivo de hosts. Además, proporcione la contraseña de administrador para el dispositivo.
4. Cree una sesión, escriba:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Para el parámetro -ComputerName del cmdlet, escriba el <*número de serie del dispositivo de destino*>. Este número de serie se asigna a la dirección IP de DATA 0 en el archivo de hosts del host remoto; Por ejemplo, **SHX0991003G44MT** tal como se muestra en la siguiente imagen.
5. Escriba:
   
     `Enter-PSSession $session`
6. Deberá esperar unos minutos y, a continuación, se conectará al dispositivo a través de HTTPS con SSL. Verá un mensaje que indica que está conectado al dispositivo.
   
    ![Conexión remota de PowerShell mediante HTTP y SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información acerca del [uso de Windows PowerShell para administrar el dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).
* Más información sobre el [uso del servicio StorSimple Device Manager para administrar su dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


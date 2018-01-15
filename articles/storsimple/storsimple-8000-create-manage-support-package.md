---
title: Crear un paquete de soporte de la serie StorSimple 8000 | Microsoft Docs
description: "Obtenga información sobre cómo crear, descifrar y editar un paquete de soporte para el dispositivo de la serie StorSimple 8000."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c83caae58e73d6f2d0fe086938e0bbc5cfdf80c4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Crear y administrar un paquete de soporte para la serie StorSimple 8000

## <a name="overview"></a>Información general

Un paquete de soporte de StorSimple es un mecanismo fácil de usar que recopila todos los registros relevantes para ayudar al servicio de soporte técnico de Microsoft a solucionar los problemas del dispositivo StorSimple. Los registros recopilados están cifrados y comprimidos.

En este tutorial se incluyen instrucciones paso a paso para crear y administrar el paquete de soporte para el dispositivo de la serie StorSimple 8000. Si está trabajando con una matriz virtual de StorSimple, vaya a [generar un paquete de registros](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Crear un paquete de soporte

En algunos casos, debe crear manualmente el paquete de soporte mediante Windows PowerShell para StorSimple. Por ejemplo: 

* Si necesita quitar información confidencial de los archivos de registro antes de compartirlos con el servicio de soporte técnico de Microsoft.
* Si tiene dificultades para cargar el paquete debido a problemas de conectividad.

Puede compartir su paquete de soporte generado manualmente con el servicio de soporte técnico de Microsoft mediante el correo electrónico. Lleve a cabo los siguientes pasos para crear un paquete de soporte en Windows PowerShell para StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Para crear un paquete de soporte en Windows PowerShell para StorSimple

1. Para iniciar una sesión de Windows PowerShell como administrador en el equipo remoto que se use para conectarse a su dispositivo StorSimple, escriba el siguiente comando:
   
    `Start PowerShell`
2. En la sesión de Windows PowerShell, conéctese a la consola de SSAdmin del dispositivo:
   
   1. En el símbolo del sistema, escriba:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. En el cuadro de diálogo que se abre, escriba la contraseña de administrador del dispositivo. La contraseña predeterminada es _Password1_.
     
      ![Cuadro de diálogo de credenciales de PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Seleccione **Aceptar**.
   4. En el símbolo del sistema, escriba:
     
      `Enter-PSSession $MS`
3. En la sesión que se abre, escriba el comando correspondiente.
   
   * Para los recursos compartidos de red que están protegidos por contraseña, escriba:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Se le solicitará una contraseña y una frase de contraseña de cifrado (ya que el paquete de soporte está cifrado). A continuación, se crea un paquete de soporte en la carpeta predeterminada (nombre de dispositivo anexado con la fecha y hora actuales).
   * Para o recursos compartidos que no están protegidos por contraseña, no es necesario el parámetro `-Credential` . Escriba lo siguiente:
     
       `Export-HcsSupportPackage`
     
       El paquete de soporte se crea para ambos controladores en la carpeta predeterminada. El paquete es un archivo comprimido y cifrado que puede enviarse al Soporte técnico de Microsoft para solucionar problemas. Para obtener más información, consulte [Ponerse en contacto con el soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parámetros del cmdlet Export-HcsSupportPackage

Puede usar los siguientes parámetros con el cmdlet Export-HcsSupportPackage.

| . | Obligatorio/opcional | DESCRIPCIÓN |
| --- | --- | --- |
| `-Path` |Requerido |Se usa para proporcionar la ubicación de la carpeta compartida de red en la que se coloca el paquete de soporte. |
| `-EncryptionPassphrase` |Requerido |Se utiliza para proporcionar una frase de contraseña que ayuda a cifrar el paquete de soporte. |
| `-Credential` |Opcional |Se usa para suministrar credenciales de acceso a la carpeta compartida de red. |
| `-Force` |Opcional |Se utiliza para omitir el paso de confirmación de la frase de contraseña de cifrado. |
| `-PackageTag` |Opcional |Se usa para especificar un directorio en *Path* en que se coloca el paquete de soporte. El valor predeterminado es [nombre de dispositivo]-[fecha y hora actuales:aaaa-MM-dd-HH-mm-ss]. |
| `-Scope` |Opcional |Especifique como **Clúster** (predeterminado) para crear un paquete de soporte para ambos controladores. Si desea crear un paquete únicamente para el controlador actual, especifique **Controlador**. |

## <a name="edit-a-support-package"></a>Editar un paquete de soporte

Después de haber generado un paquete de soporte, debe modificar el paquete para quitar la información confidencial. Esto puede incluir los nombres de los volúmenes, las direcciones IP de los dispositivos y los nombres de las copias de seguridad de los archivos de registro.

> [!IMPORTANT]
> Solo pueden editarse los paquetes de soporte generados a través de Windows PowerShell para StorSimple. No es posible editar los paquetes creados en Azure Portal con el servicio Administrador de dispositivos de StorSimple.

Para editar un paquete de soporte antes de cargarlo en el sitio de soporte técnico de Microsoft, primero descifre el paquete de soporte, edite los archivos y vuelva a cifrarlo. Lleve a cabo los siguiente pasos.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar un paquete de soporte en Windows PowerShell para StorSimple

1. Genere un paquete de soporte como se describe en [Para crear un paquete de soporte en Windows PowerShell para StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Descargue el script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente en su cliente.
3. Importe el módulo de Windows PowerShell. Deberá especificar la ruta de acceso a la carpeta local en la que descargó el script. Para importar el módulo, escriba:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Todos los archivos tienen la extensión *.aes* , es decir, archivos comprimidos y cifrados. Para descomprimir y descifrar archivos, escriba:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Observe que las extensiones de archivo reales se muestran ahora para todos los archivos.
   
    ![Edición del paquete de soporte](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Cuando se le solicite la frase de contraseña de cifrado, escriba la frase de contraseña utilizada al crear el paquete de soporte.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Vaya a la carpeta que contiene los archivos de registro. Puesto que los archivos de registro ahora están descomprimidos y descifrados, tendrán sus extensiones de archivo originales. Modifique estos archivos para quitar cualquier información específica del cliente, como los nombres de los volúmenes y las direcciones IP de los dispositivos, y guarde los archivos.
7. Al cerrar los archivos para comprimirlos con Gzip y cifrarlos con AES-256. Esto es por seguridad y para acelerar la transferencia del paquete de soporte por la red. Para comprimir y cifrar los archivos, escriba lo siguiente:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Edición del paquete de soporte](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Cuando se le solicite, proporcione una frase de contraseña de cifrado para el paquete de soporte modificado.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Anote la nueva frase de contraseña para poder compartirla con el servicio de soporte técnico de Microsoft cuando se le solicite.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Ejemplo: Edición de archivos de un paquete de soporte en un recurso compartido protegido por contraseña

A continuación se muestra un ejemplo que muestra cómo descifrar, editar y volver a cifrar un paquete de soporte.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>pasos siguientes

* Obtenga información sobre la [información recopilada en el paquete de soporte](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Aprenda cómo [usar paquetes de soporte y registros de dispositivos para solucionar los problemas de implementación de su dispositivo](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Obtenga información sobre cómo [usar el servicio Administrador de dispositivos de StorSimple para administrar el dispositivo de StorSimple](storsimple-8000-manager-service-administration.md).


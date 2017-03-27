---
title: "Creación de certificados autofirmados para conexiones de punto a sitio: Azure PowerShell |Microsoft Docs"
description: "Este artículo contiene pasos para crear un certificado raíz autofirmado, exportar la clave pública y generar los certificados de cliente mediante PowerShell en Windows 10."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: cb66edd0c0ff1f0b78232233719dd44329584c78
ms.lasthandoff: 03/15/2017


---
# <a name="create-a-self-signed-root-certificate-for-point-to-site-connections-using-powershell"></a>Creación de un certificado raíz autofirmado para conexiones de punto a sitio mediante PowerShell

Las conexiones de punto a sitio utilizan certificados para realizar la autenticación. Cuando se configura una conexión de punto a sitio, debe cargar la clave pública (archivo .cer) de un certificado raíz en Azure. Este artículo lo ayudará a crear un certificado raíz autofirmado, exportar la clave pública, y generar e instalar los certificados de cliente.

> [!NOTE]
> Anteriormente, makecert era el método recomendado para crear certificados raíz autofirmados y generar certificados de cliente para conexiones de punto a sitio. Ahora puede usar PowerShell para crear estos certificados. Una ventaja del uso de PowerShell es la posibilidad de crear certificados SHA-2. 
>
>

## <a name="rootcert"></a>Creación de un certificado raíz autofirmado

Los siguientes pasos lo guiarán en la creación de un certificado raíz autofirmado mediante PowerShell. Se requiere Windows 10 para completarlos siguientes pasos. Los cmdlets y parámetros que se usan en estos pasos forman parte del sistema operativo Windows 10, no de una versión de PowerShell.

1. En un equipo con Windows 10, abra una consola de Windows PowerShell con privilegios elevados.
2. Utilice el ejemplo siguiente para crear el certificado raíz autofirmado. En el ejemplo siguiente se crea un certificado raíz firmado automáticamente con el nombre "P2SRootCert" que se instala automáticamente en "Certificates-Current User\Personal\Certificates". Puede ver el certificado abriendo *certmgr.msc*.

        $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

### <a name="cer"></a>Para obtener la clave pública

Las conexiones de punto a sitio requieren la clave pública (.cer) para cargarse en Azure. Los pasos siguientes ayudan a exportar el archivo .cer para el certificado raíz autofirmado.

1. Para obtener un archivo .cer del certificado, abra **certmgr.msc**. Busque el certificado raíz autofirmado; normalmente se encuentra en Certificados - Usuario actual\Personal\Certificados y haga clic en el botón derecho. Haga clic en **Todas las tareas** y, luego, en **Exportar**. Se abre el **Asistente para exportar certificados**.
2. En el asistente, haga clic en **Siguiente**. Seleccione **No exportar la clave privada** y, después, haga clic en **Siguiente**.
3. En la página **Formato de archivo de exportación**, seleccione **X.509 codificado base&64; (.CER)** y, luego, haga clic en **Siguiente**. 
4. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
5. Haga clic en **Finalizar** para exportar el certificado. Verá **La exportación se realizó correctamente**. Haga clic en **Aceptar** para cerrar el asistente.

### <a name="to-export-a-self-signed-root-certificate-optional"></a>Pasos para exportar un certificado raíz autofirmado (opcional)
Puede que desee exportar el certificado autofirmado y almacenarlo de manera segura. Si es necesario, más adelante puede instalarlo en otro equipo y generar más certificados de cliente o exportar otro archivo .cer.

Para exportar el certificado raíz autofirmado como un archivo .pfx, seleccione el certificado raíz y use los mismos pasos descritos en [Exportar un certificado de cliente](#clientexport) para realizar la exportación.

## <a name="clientcert"></a>Generación de un certificado de cliente

Cada equipo cliente que se conecta a una red virtual con una conexión de punto a sitio debe tener instalado un certificado de cliente. Puede generarlo desde un certificado raíz autofirmado y, luego, exportar e instalar el certificado de cliente. Si no está instalado el certificado de cliente, se produce un error de autenticación. 

Los pasos siguientes lo llevan por el proceso de generación de un certificado de cliente a partir de un certificado autofirmado. Puede generar varios certificados de cliente desde el mismo certificado raíz. Al generar certificados de cliente mediante los pasos siguientes, el certificado de cliente se instala automáticamente en el equipo que se usó para generar el certificado. Si desea instalar un certificado de cliente en otro equipo cliente, puede exportar el certificado.

Se requiere Windows 10 para completarlos siguientes pasos. Los cmdlets y parámetros que se usan en estos pasos forman parte del sistema operativo Windows 10, no de una versión de PowerShell.

### <a name="example-1"></a>Ejemplo 1

Este ejemplo utiliza la variable "$cert" declarada de la sección anterior. Si se cierra la consola de PowerShell después de crear el certificado raíz autofirmado o está creando más certificados de cliente en una nueva sesión de consola de PowerShell, siga los pasos del ejemplo 2.

Modifique y ejecute el ejemplo para generar un certificado de cliente. Si ejecuta el ejemplo siguiente sin modificación alguna, el resultado es un certificado de cliente con el nombre "P2SChildCert".  Si quiere agregar algo más al nombre del certificado secundario, modifique el valor CN. No cambie el valor de TextExtension cuando ejecute este ejemplo. El certificado de cliente que genera se instala automáticamente en la ruta del equipo "Certificates - Current User\Personal\Certificates".

    New-SelfSignedCertificate -Type Custom -KeySpec Signature `
    -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 -KeyLength 2048 `
    -CertStoreLocation "Cert:\CurrentUser\My" `
    -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

### <a name="example-2"></a>Ejemplo 2

Si va a crear más certificados de cliente, o bien no está usando la misma sesión de PowerShell que empleó para crear el certificado raíz autofirmado, siga estos pasos:

1. Identifique el certificado raíz autofirmado que se instaló en el equipo. Este cmdlet devuelve una lista de certificados que están instalados en el equipo.

        Get-ChildItem -Path “Cert:\CurrentUser\My”

2. Busque el nombre del firmante de la lista devuelta y, luego, copie la huella digital que se encuentra junto a él en un archivo de texto. En el ejemplo siguiente, hay dos certificados. El nombre CN es el nombre del certificado raíz autofirmado a partir del que va a generar un certificado secundario. En este caso, "P2SRootCert".

        Thumbprint                                Subject
        ----------                                -------
        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. Declare una variable para el certificado raíz con la huella digital del paso anterior. Reemplace la huella digital con la del certificado raíz a partir del que va a generar un certificado secundario.

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"

    Por ejemplo, al usar la huella digital de P2SRootCert del paso anterior, la variable tendría el aspecto similar al siguiente:

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
        

4.  Modifique y ejecute el ejemplo para generar un certificado de cliente. Si ejecuta el ejemplo siguiente sin modificación alguna, el resultado es un certificado de cliente con el nombre "P2SChildCert". Si quiere agregar algo más al nombre del certificado secundario, modifique el valor CN. No cambie el valor de TextExtension cuando ejecute este ejemplo. El certificado de cliente que genera se instala automáticamente en la ruta del equipo "Certificates - Current User\Personal\Certificates".

        New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" `
        -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

## <a name="clientexport"></a>Exportación de un certificado de cliente   

Al generar un certificado de cliente, se instala automáticamente en el equipo que usó para generarlo. Si desea instalar el certificado de cliente en otro equipo cliente, debe exportar el certificado de cliente que ha generado.                              

1. Para exportar un certificado de cliente, abra **certmgr.msc**. De forma predeterminada, los certificados de cliente que ha generado se encuentran en "Certificates - Current User\Personal\Certificates". Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**. Se abre el **Asistente para exportar certificados**.
2. En el asistente, haga clic en **Siguiente**, seleccione **Exportar la clave privada** y, luego, haga clic en **Siguiente**.
3. En la página **Formato de archivo de exportación** , puede dejar seleccionados los valores predeterminados. A continuación, haga clic en **Siguiente**. 
4. En la página **Seguridad** , debe proteger la clave privada. Si decide usar una contraseña, asegúrese de anotarla o de recordar la contraseña que estableció para este certificado. A continuación, haga clic en **Siguiente**.
5. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
6. Haga clic en **Finalizar** para exportar el certificado.    

## <a name="install"></a>Instalación de un certificado de cliente exportado

Si desea crear una conexión P2S desde un equipo cliente distinto del utilizado para generar los certificados de cliente, debe instalar un certificado de cliente. Al instalar el certificado de cliente, necesitará la contraseña que se creó cuando se exportó el certificado de cliente.

1. Busque el archivo *.pfx* y cópielo en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Deje **Ubicación de almacén** como **Usuario actual** y, luego, haga clic en **Siguiente**.
2. En la página **File to import** (Archivo para importar), no haga ningún cambio. Haga clic en **Next**.
3. En la página **Protección de clave privada**, escriba la contraseña del certificado, si ha usado una, o compruebe que la entidad de seguridad que instala el certificado sea correcta y, luego, haga clic en **Siguiente**.
4. En la página **Almacén de certificados**, deje la ubicación predeterminada y, luego, haga clic en **Siguiente**.
5. Haga clic en **Finalizar** En la **Advertencia de seguridad** para la instalación de certificados, haga clic en **Sí**. Al haberse generado el certificado, puede hacer clic en "Sí" de forma segura. El certificado se importó correctamente.

## <a name="next-steps"></a>Pasos siguientes
Continúe con la configuración de punto a sitio. 

* Para ver los pasos del modelo de implementación de **Resource Manager**, consulte [Configuración de una conexión de punto a sitio a una red virtual mediante una red virtual](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Para ver los pasos del modelo de implementación **clásica**, consulte el artículo [Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md).



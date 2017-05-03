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
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 72fc6eb93c77dd5a0a7ce55897f4c06fb98c0721
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-self-signed-root-certificate-for-point-to-site-connections-using-powershell"></a>Creación de un certificado raíz autofirmado para conexiones de punto a sitio mediante PowerShell

Las conexiones de punto a sitio utilizan certificados para realizar la autenticación. Cuando se configura una conexión de punto a sitio, debe cargar la clave pública (archivo .cer) de un certificado raíz en Azure. Los certificados de cliente deben generarse a partir del certificado raíz e instalarse en todos los equipos cliente que se conecten a la red virtual. El certificado de cliente permite al cliente autenticarse. En este artículo, se muestra cómo crear un certificado raíz autofirmado, exportar la clave pública y generar certificados de cliente. Este artículo no contiene instrucciones de configuración de punto a sitio ni preguntas más frecuentes sobre este tipo de configuración. Puede encontrar esa información en uno de los artículos de la lista siguiente:

> [!div class="op_single_selector"]
> * [Creación de certificados autofirmados](vpn-gateway-certificates-point-to-site.md)
> * [Configuración de una conexión de punto a sitio - Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Configuración de una conexión de punto a sitio - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Configuración de una conexión de punto a sitio - Clásico - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

## <a name="rootcert"></a>Creación de un certificado raíz autofirmado

Los siguientes pasos le guiarán por la creación de un certificado raíz autofirmado mediante PowerShell en Windows 10. Los cmdlets y parámetros que se usan en estos pasos forman parte del sistema operativo Windows 10, no de una versión de PowerShell. Esto no significa que los certificados que cree solo se puedan instalar en Windows 10. Para información acerca de los clientes compatibles, consulte [Preguntas más frecuentes sobre la conexión de punto a sitio](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

1. En un equipo con Windows 10, abra una consola de Windows PowerShell con privilegios elevados.
2. Utilice el ejemplo siguiente para crear el certificado raíz autofirmado. En el ejemplo siguiente se crea un certificado raíz firmado automáticamente con el nombre "P2SRootCert" que se instala automáticamente en "Certificates-Current User\Personal\Certificates". Puede ver el certificado abriendo *certmgr.msc*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>Para obtener la clave pública

Las conexiones de punto a sitio requieren la clave pública (.cer) para cargarse en Azure. Los pasos siguientes le ayudan a exportar el archivo .cer para el certificado raíz autofirmado:

1. Para obtener un archivo .cer del certificado, abra **Administrar certificados de usuario**. Busque el certificado raíz autofirmado; normalmente se encuentra en Certificados - Usuario actual\Personal\Certificados y haga clic en el botón derecho. Haga clic en **Todas las tareas** y, luego, en **Exportar**. Se abre el **Asistente para exportar certificados**.
2. En el asistente, haga clic en **Siguiente**. Seleccione **No exportar la clave privada** y, después, haga clic en **Siguiente**.
3. En la página **Formato de archivo de exportación**, seleccione **X.509 codificado base 64 (.CER)** y, luego, haga clic en **Siguiente**. 
4. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
5. Haga clic en **Finalizar** para exportar el certificado. Verá el mensaje **La exportación se realizó correctamente**. Haga clic en **Aceptar** para cerrar el asistente.

### <a name="to-export-a-self-signed-root-certificate-optional"></a>Pasos para exportar un certificado raíz autofirmado (opcional)

Puede que desee exportar el certificado autofirmado y almacenarlo de manera segura. Si es necesario, más adelante puede instalarlo en otro equipo y generar más certificados de cliente o exportar otro archivo .cer. Para exportar el certificado raíz autofirmado como archivo .pfx, seleccione el certificado raíz y use los mismos pasos descritos en [Exportación de un certificado de cliente](#clientexport).

## <a name="clientcert"></a>Generación de un certificado de cliente

Cada equipo cliente que se conecta a una red virtual con una conexión de punto a sitio debe tener instalado un certificado de cliente. Puede generarlo desde un certificado raíz autofirmado y, luego, exportar e instalar el certificado de cliente. Si no está instalado el certificado de cliente, se produce un error de autenticación. 

Los pasos siguientes lo llevan por el proceso de generación de un certificado de cliente a partir de un certificado autofirmado. Puede generar varios certificados de cliente desde el mismo certificado raíz. Al generar certificados de cliente mediante los pasos siguientes, el certificado de cliente se instala automáticamente en el equipo que se usó para generar el certificado. Si desea instalar un certificado de cliente en otro equipo cliente, puede exportar el certificado.

Se necesita Windows 10 para generar certificados de cliente mediante los siguientes pasos en PowerShell. Los cmdlets y parámetros que se usan en estos pasos forman parte del sistema operativo Windows 10, no de una versión de PowerShell. Esto no significa que los certificados que cree solo se puedan instalar en Windows 10. Para información acerca de los clientes compatibles, consulte [Preguntas más frecuentes sobre la conexión de punto a sitio](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

### <a name="example-1"></a>Ejemplo 1

Este ejemplo utiliza la variable "$cert" declarada de la sección anterior. Si cerró la consola de PowerShell después de crear el certificado raíz autofirmado o va a crear más certificados de cliente en una nueva sesión de consola de PowerShell, siga los pasos del [Ejemplo 2](#ex2).

Modifique y ejecute el ejemplo para generar un certificado de cliente. Si ejecuta el ejemplo siguiente sin modificación alguna, el resultado es un certificado de cliente con el nombre "P2SChildCert".  Si quiere agregar algo más al nombre del certificado secundario, modifique el valor CN. No cambie el valor de TextExtension cuando ejecute este ejemplo. El certificado de cliente que genera se instala automáticamente en la ruta del equipo "Certificates - Current User\Personal\Certificates".

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Ejemplo 2

Si va a crear más certificados de cliente, o bien no está usando la misma sesión de PowerShell que empleó para crear el certificado raíz autofirmado, siga estos pasos:

1. Identifique el certificado raíz autofirmado que se instaló en el equipo. Este cmdlet devuelve una lista de certificados que están instalados en el equipo.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Busque el nombre del firmante de la lista devuelta y, luego, copie la huella digital que se encuentra junto a él en un archivo de texto. En el ejemplo siguiente, hay dos certificados. El nombre CN es el nombre del certificado raíz autofirmado a partir del que va a generar un certificado secundario. En este caso, "P2SRootCert".

        Thumbprint                                Subject

        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. Declare una variable para el certificado raíz con la huella digital del paso anterior. Reemplace la huella digital con la del certificado raíz a partir del que va a generar un certificado secundario.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Por ejemplo, al usar la huella digital de P2SRootCert del paso anterior, la variable tendrá el aspecto similar al siguiente:

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```    

4.  Modifique y ejecute el ejemplo para generar un certificado de cliente. Si ejecuta el ejemplo siguiente sin modificación alguna, el resultado es un certificado de cliente con el nombre "P2SChildCert". Si quiere agregar algo más al nombre del certificado secundario, modifique el valor CN. No cambie el valor de TextExtension cuando ejecute este ejemplo. El certificado de cliente que genera se instala automáticamente en la ruta del equipo "Certificates - Current User\Personal\Certificates".

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>Exportación de un certificado de cliente   

Al generar un certificado de cliente, se instala automáticamente en el equipo que usó para generarlo. Si desea instalar el certificado de cliente en otro equipo cliente, debe exportar el certificado de cliente que ha generado.                              

1. Para exportar un certificado de cliente, abra **Administrar certificados de usuario**. De forma predeterminada, los certificados de cliente que ha generado se encuentran en "Certificates - Current User\Personal\Certificates". Haga clic con el botón derecho en el certificado de cliente que desee exportar y haga clic en **Todas las tareas** y en **Exportar** para abrir el **Asistente para exportar certificados**.
2. En el asistente, haga clic en **Siguiente**, seleccione **Exportar la clave privada** y, luego, haga clic en **Siguiente**.
3. En la página **Formato de archivo de exportación**, deje seleccionados los valores predeterminados. Asegúrese de que **Incluir todos los certificados en la ruta de certificación si es posible** esté seleccionada. Al seleccionar esta opción, también se exporta la información del certificado raíz que se requiere para una autenticación correcta. A continuación, haga clic en **Siguiente**.
4. En la página **Seguridad** , debe proteger la clave privada. Si decide usar una contraseña, asegúrese de anotarla o de recordar la contraseña que estableció para este certificado. A continuación, haga clic en **Siguiente**.
5. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
6. Haga clic en **Finalizar** para exportar el certificado.    

## <a name="install"></a>Instalación de un certificado de cliente exportado

Si desea crear una conexión P2S desde un equipo cliente distinto del que usó para generar los certificados de cliente, debe instalar un certificado de cliente. Al instalar un certificado de cliente, necesita la contraseña que se creó cuando se exportó el certificado de cliente.

1. Busque el archivo *.pfx* y cópielo en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Deje **Ubicación del almacén** como **Usuario actual** y haga clic en **Siguiente**.
2. En la página **File to import** (Archivo para importar), no haga ningún cambio. Haga clic en **Siguiente**.
3. En la página **Protección de clave privada**, escriba la contraseña del certificado o compruebe que la entidad de seguridad sea correcta y haga clic en **Siguiente**.
4. En la página **Almacén de certificados**, deje la ubicación predeterminada y, luego, haga clic en **Siguiente**.
5. Haga clic en **Finalizar** En la **Advertencia de seguridad** para la instalación de certificados, haga clic en **Sí**. Al haberse generado el certificado, puede hacer clic en "Sí" de forma segura. El certificado se importó correctamente.

## <a name="next-steps"></a>Pasos siguientes

Continúe con la configuración de punto a sitio. 

* Para ver los pasos del modelo de implementación de **Resource Manager**, consulte [Configuración de una conexión de punto a sitio a una red virtual mediante una red virtual](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Para ver los pasos del modelo de implementación **clásica**, consulte el artículo [Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

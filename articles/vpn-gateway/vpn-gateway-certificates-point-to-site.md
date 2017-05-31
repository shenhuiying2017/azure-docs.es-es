---
title: "Creación y exportación de certificados para conexiones de punto a sitio: PowerShell: Azure | Microsoft Docs"
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
ms.date: 05/04/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 1bfcb8683669770d6dd927cbde53a683bbc1d56e
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell"></a>Generación y exportación de certificados para conexiones de punto a sitio con PowerShell

En este artículo, se muestra cómo crear un certificado raíz autofirmado y generar certificados de cliente. Este artículo no contiene instrucciones de configuración de punto a sitio ni preguntas más frecuentes sobre este tipo de configuración. Puede encontrar esa información en uno de los artículos «Configuración de punto a sitio» de la lista siguiente:

> [!div class="op_single_selector"]
> * [Creación de certificados autofirmados: PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Creación de certificados autofirmados: Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Configuración de una conexión de punto a sitio: Resource Manager: Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Configuración de una conexión de punto a sitio - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Configuración de una conexión de punto a sitio: Clásico: Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Las conexiones de punto a sitio utilizan certificados para realizar la autenticación. Cuando se configura una conexión de punto a sitio, debe cargar la clave pública (archivo .cer) de un certificado raíz en Azure. Además, los certificados de cliente deben generarse a partir del certificado raíz e instalarse en todos los equipos cliente que se conecten a la red virtual. El certificado de cliente permite al cliente autenticarse.


> [!NOTE]
> Debe realizar los pasos de este artículo en un equipo que ejecute Windows 10. Los cmdlets de PowerShell que se necesitan para generar certificados forman parte del sistema operativo Windows 10 y no funcionan en otras versiones de Windows. Solo necesita estos cmdlets para generar los certificados. Una vez que se generan los certificados, puede instalarlos en cualquier sistema operativo cliente compatible. Si no tiene acceso a un equipo Windows 10, puede usar makecert para generar certificados. Sin embargo, los certificados generados mediante makecert, mientras sean compatibles con una conexión de punto a sitio, no son SHA-2. Para obtener instrucciones sobre makecert, vea [Creación de certificados con makecert](vpn-gateway-certificates-point-to-site-makecert.md). Los certificados creados por usted, con PowerShell o makecert, pueden instalarse en cualquier [sistema operativo cliente admitido](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq), no solo en el sistema operativo que usó para crearlos.
> 
>

## <a name="rootcert"></a>Creación de un certificado raíz autofirmado

Los siguientes pasos le indican cómo crear un certificado raíz autofirmado mediante PowerShell en Windows 10. Los cmdlets y parámetros que se usan en estos pasos forman parte del sistema operativo Windows 10, no de una versión de PowerShell. Esto no significa que los certificados que cree solo se puedan instalar en Windows 10. Se pueden instalar en cualquier cliente compatible. Para información acerca de los clientes compatibles, consulte [Preguntas más frecuentes sobre la conexión de punto a sitio](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

1. En un equipo con Windows 10, abra una consola de Windows PowerShell con privilegios elevados.
2. Utilice el ejemplo siguiente para crear el certificado raíz autofirmado. En el ejemplo siguiente se crea un certificado raíz firmado automáticamente con el nombre "P2SRootCert" que se instala automáticamente en "Certificates-Current User\Personal\Certificates". Puede ver el certificado si abre *certmgr.msc*, o bien *Administrar certificados de usuario*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>Exportación de la clave pública (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

El archivo exported.cer debe cargarse en Azure. Para ver las instrucciones, consulte [Configuración de una conexión de punto a sitio](vpn-gateway-howto-point-to-site-rm-ps.md#upload).

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>Exportación del certificado raíz autofirmado y la clave pública para almacenarlo (opcional)

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

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]
    

## <a name="install"></a>Instalación de un certificado de cliente exportado

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Continúe con la configuración de punto a sitio. 

* Para ver los pasos del modelo de implementación de **Resource Manager**, consulte [Configuración de una conexión de punto a sitio a una red virtual mediante una red virtual](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Para ver los pasos del modelo de implementación **clásica**, consulte el artículo [Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

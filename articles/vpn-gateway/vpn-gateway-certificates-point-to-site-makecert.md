---
title: "Creación y exportación de certificados para conexiones de punto a sitio: makecert : Azure | Microsoft Docs"
description: "Este artículo contiene pasos para crear un certificado raíz autofirmado, exportar la clave pública y generar los certificados de cliente mediante makecert."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 0800a7754241eb409dbd86db82b586a3e19a29fc
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generación y exportación de certificados para conexiones de punto a sitio con makecert

> [!NOTE]
> Siga estas instrucciones solo cuando no tenga acceso a un equipo Windows 10 para generar certificados autofirmados para conexiones de punto a sitio. Makecert está en desuso. Además, makecert no puede crear un certificado de SHA-2, solo SHA-1 (que sigue siendo válido para P2S). Por estos motivos, se recomienda que siga los [pasos de PowerShell](vpn-gateway-certificates-point-to-site.md), si es posible. Los certificados creados por usted, con PowerShell o makecert, pueden instalarse en cualquier [sistema operativo cliente admitido](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq), no solo en el sistema operativo que usó para crearlos.
>
>


En este artículo, se muestra cómo crear un certificado raíz autofirmado, exportar la clave pública y generar certificados de cliente. Este artículo no contiene instrucciones de configuración de punto a sitio ni preguntas más frecuentes sobre este tipo de configuración. Puede encontrar esa información en uno de los artículos «Configuración de punto a sitio» de la lista siguiente:

> [!div class="op_single_selector"]
> * [Creación de certificados autofirmados: PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Creación de certificados autofirmados: Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Configuración de una conexión de punto a sitio: Resource Manager: Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Configuración de una conexión de punto a sitio - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Configuración de una conexión de punto a sitio: Clásico: Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Las conexiones de punto a sitio utilizan certificados para realizar la autenticación. Cuando se configura una conexión de punto a sitio, debe cargar la clave pública (archivo .cer) de un certificado raíz en Azure. Además, los certificados de cliente deben generarse a partir del certificado raíz e instalarse en todos los equipos cliente que se conecten a la red virtual. El certificado de cliente permite al cliente autenticarse.

## <a name="rootcert"></a>Creación de un certificado raíz autofirmado

Los siguientes pasos le mostrarán cómo crear un certificado autofirmado mediante makecert. Estos pasos no son específicos del modelo de implementación. Son válidos para el Administrador de recursos y la versión clásica.

1. Descargue e instale [makecert](https://msdn.microsoft.com/en-us/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Después de la instalación, la utilidad makecert.exe se encuentra en esta ruta de acceso: C:\Archivos de programa (x86)\Windows Kits\10\bin\<arch>. Abra un símbolo del sistema como administrador y navegue hasta la ubicación de la herramienta makecert. Puede usar el siguiente ejemplo:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Después, cree e instale un certificado en el almacén de certificados personal de su equipo. En el ejemplo siguiente se crea un archivo *.cer* correspondiente que se carga en Azure al configurar P2S. Reemplace P2SRootCert y P2SRootCert.cer por el nombre que quiera usar para el certificado. El certificado estará en sus certificados, en Usuario actual\Personal\Certificados.

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha1 -len 2048 -ss My "P2SRootCert.cer"
  ```

## <a name="cer"></a>Exportación de la clave pública (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

El archivo exported.cer debe cargarse en Azure. Para ver las instrucciones, consulte [Configuración de una conexión de punto a sitio](vpn-gateway-howto-point-to-site-rm-ps.md#upload).

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportación del certificado autofirmado y clave privada para almacenarlo (opcional)

Puede que desee exportar el certificado autofirmado y almacenarlo de manera segura. Si es necesario, más adelante puede instalarlo en otro equipo y generar más certificados de cliente o exportar otro archivo .cer. Para exportar el certificado raíz autofirmado como archivo .pfx, seleccione el certificado raíz y use los mismos pasos descritos en [Exportación de un certificado de cliente](#clientexport).

## <a name="create-and-install-client-certificates"></a>Creación e instalación de certificados de cliente

No instale el certificado autofirmado directamente en el equipo cliente. Debe generar un certificado de cliente a partir del certificado autofirmado. Luego, el certificado de cliente se exporta e instala en el equipo cliente. Los siguientes pasos no son específicos del modelo de implementación. Son válidos para el Administrador de recursos y la versión clásica.

### <a name="clientcert"></a>Generación de un certificado de cliente

Cada equipo cliente que se conecta a una red virtual con una conexión de punto a sitio debe tener instalado un certificado de cliente. Puede generarlo desde un certificado raíz autofirmado y, luego, exportar e instalar el certificado de cliente. Si no está instalado el certificado de cliente, se produce un error de autenticación. 

Los pasos siguientes lo llevan por el proceso de generación de un certificado de cliente a partir de un certificado autofirmado. Puede generar varios certificados de cliente desde el mismo certificado raíz. Al generar certificados de cliente mediante los pasos siguientes, el certificado de cliente se instala automáticamente en el equipo que se usó para generar el certificado. Si desea instalar un certificado de cliente en otro equipo cliente, puede exportar el certificado.
 
1. En el mismo equipo que usó para crear el certificado autofirmado, abra un símbolo del sistema como administrador.
2. Modifique y ejecute el ejemplo para generar un certificado de cliente.
  * Cambie *"P2SRootCert"* por el nombre del certificado raíz autofirmado desde el que genera el certificado de cliente. Asegúrese de que esté usando el nombre del certificado raíz, que será el valor de CN= que especificó cuando creó el certificado raíz autofirmado.
  * Cambie *P2SChildCert* por el nombre con el cual desea generar un certificado de cliente.

  Si ejecuta el mismo ejemplo sin modificarlo, el resultado es un certificado de cliente denominado P2SChildcert en el almacén de certificados personal que se generó a partir del certificado raíz P2SRootCert.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha1
  ```

### <a name="clientexport"></a>Exportación de un certificado de cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Instalación de un certificado de cliente exportado

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Continúe con la configuración de punto a sitio. 

* Para ver los pasos del modelo de implementación de **Resource Manager**, consulte [Configuración de una conexión de punto a sitio a una red virtual mediante una red virtual](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para ver los pasos del modelo de implementación **clásica**, consulte el artículo [Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

---
title: "Solución de problemas de conexión de punto a sitio de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo solucionar problemas de conexión de punto a sitio."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: de37c8ffd47a2b8e201d18e3a20b5325d528ad59
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Solución de problemas: conexión de punto a sitio de Azure

En este artículo se enumeran problemas comunes de conexión de punto a sitio que puede experimentar. También se tratan las posibles causas de estos problemas y sus soluciones.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Error de cliente de VPN: no se encontró un certificado

### <a name="symptom"></a>Síntoma

Al intentar conectar a una red virtual de Azure mediante el cliente de VPN, aparece el mensaje de error siguiente:

**No se encuentra un certificado que se puede usar con este protocolo de autenticación extendido. (Error 798)**

### <a name="cause"></a>Causa

Este problema se produce si el certificado de cliente no está en **Certificados - Usuario actual\Personal\Certificados**.

### <a name="solution"></a>Solución

Asegúrese de que el certificado de cliente está instalado en la siguiente ubicación del almacén de certificados (Certmgr.msc):
 
**Certificados - Usuario actual\Personal\Certificados**

Para más información sobre cómo instalar el certificado de cliente, consulte [Generación y exportación de certificados para conexiones de punto a sitio](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Al importar el certificado de cliente, no seleccione la opción **Habilitar la protección de clave privada de alta seguridad**.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Error de cliente de VPN: no se esperaba el mensaje recibido o tiene un formato incorrecto

### <a name="symptom"></a>Síntoma

Al intentar conectar a una red virtual de Azure mediante el cliente de VPN, aparece el mensaje de error siguiente:

**Mensaje recibido inesperado o con formato incorrecto. (Error 0x80090326)**

### <a name="cause"></a>Causa

Este problema se produce si la clave pública del certificado raíz no se carga a la puerta de enlace VPN de Azure. También se puede producir si la clave está dañada o expiró.

### <a name="solution"></a>Solución

Para resolver este problema, compruebe el estado del certificado raíz en Azure Portal para ver si se revocó. Si no se ha revocado, pruebe a eliminar el certificado raíz y a volver a cargarlo. Para más información, vea [Crear certificados](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Error de cliente de VPN: una cadena de certificados se ha procesado pero ha finalizado 

### <a name="symptom"></a>Síntoma 

Al intentar conectar a una red virtual de Azure mediante el cliente de VPN, aparece el mensaje de error siguiente:

**Se ha procesado una cadena de certificados, pero termina en un certificado raíz en el que el proveedor de confianza no confía**

### <a name="solution"></a>Solución

1. Asegúrese de que los certificados siguientes están en la ubicación correcta:

    | Certificate | La ubicación |
    | ------------- | ------------- |
    | AzureClient.pfx  | Usuario actual\Personal\Certificados |
    | Azuregateway-*GUID*.cloudapp.net  | Usuario actual\Entidades de certificación raíz de confianza|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Equipo local\Entidades de certificación raíz de confianza|

2. Si los certificados ya están en la ubicación, pruebe a eliminarlos y a volver a instalarlos. El certificado **azuregateway-*GUID*.cloudapp.net** se encuentra en el paquete de configuración del cliente de VPN descargado de Azure Portal. Puede usar archivadores de archivos para extraer los archivos del paquete.

## <a name="file-download-error-target-uri-is-not-specified"></a>Error en la descarga del archivo. No se ha especificado el URI de destino

### <a name="symptom"></a>Síntoma

Aparece el siguiente mensaje de error:

**Error en la descarga del archivo. No se ha especificado el URI de destino.**

### <a name="cause"></a>Causa 

Este problema se produce debido a un tipo de puerta de enlace incorrecto. 

### <a name="solution"></a>Solución

El tipo de puerta de enlace de la VPN debe ser **VPN** y el tipo de VPN **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Error de cliente de VPN: error de script personalizado de VPN de Azure 

### <a name="symptom"></a>Síntoma

Al intentar conectar a una red virtual de Azure mediante el cliente de VPN, aparece el mensaje de error siguiente:

**Error de script personalizado (para actualizar la tabla de enrutamiento). (Error 8007026f)**

### <a name="cause"></a>Causa

Este problema puede producirse si está intentando abrir la conexión VPN de sitio a punto mediante un acceso directo.

### <a name="solution"></a>Solución 

Abra el paquete VPN directamente en lugar de hacerlo desde el acceso directo.

## <a name="cannot-install-the-vpn-client"></a>No se puede instalar el cliente de VPN

### <a name="cause"></a>Causa 

Es necesario que un certificado adicional confíe en la puerta de enlace de la VPN de la red virtual. El certificado está incluido en el paquete de configuración del cliente de VPN que se genera desde Azure Portal.

### <a name="solution"></a>Solución

Extraiga el paquete de configuración del cliente de VPN y busque el archivo .cer. Para instalar el certificado, siga estos pasos:

1. Abra mmc.exe.
2. Agregue el complemento **Certificados**.
3. Seleccione la cuenta **Equipo** del equipo local.
4. Haga clic con el botón derecho en el nodo **Entidades de certificación raíz de confianza**. Haga clic en **All-Task** > **Import** (Importar) y vaya al archivo .cer que extrajo del paquete de configuración del cliente de VPN.
5. Reinicie el equipo. 
6. Intente instalar el cliente de VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Error de Azure Portal: error al guardar la puerta de enlace de la VPN y los datos no son válidos

### <a name="symptom"></a>Síntoma

Al intentar guardar los cambios de la puerta de enlace de la VPN en Azure Portal, aparece el mensaje de error siguiente:

**Error al guardar la puerta de enlace de red &lt;*nombre de puerta de enlace*&gt;. Los datos del certificado &lt;*identificador de certificado*&gt; no son válidos.**

### <a name="cause"></a>Causa 

Este problema puede producirse si la clave pública del certificado raíz que se ha cargado contiene un carácter no válido, por ejemplo, un espacio.

### <a name="solution"></a>Solución

Asegúrese de que los datos del certificado no contienen caracteres no válidos como saltos de línea (retornos de carro). El valor completo debe estar en una línea larga. El siguiente texto es un ejemplo del certificado:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Error de Azure Portal: error al guardar la puerta de enlace de la VPN y el nombre del recurso no es válido

### <a name="symptom"></a>Síntoma

Al intentar guardar los cambios de la puerta de enlace de la VPN en Azure Portal, aparece el mensaje de error siguiente: 

**Error al guardar la puerta de enlace de red &lt;*nombre de puerta de enlace*&gt;. El nombre del recurso &lt;*nombre del certificado que intenta cargar*&gt; no es válido**.

### <a name="cause"></a>Causa

Este problema se produce porque el nombre del certificado contiene un carácter no válido, como un espacio. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Error de Azure Portal: error de descarga de archivo de paquete de VPN 503

### <a name="symptom"></a>Síntoma

Al intentar descargar el paquete de configuración del cliente de VPN, aparece el mensaje de error siguiente:

**No se pudo descargar el archivo. Detalles del error: error 503. El servidor está ocupado.**
 
### <a name="solution"></a>Solución

Este error puede deberse a un problema de red temporal. Vuelva a intentar descargar el paquete VPN pasados unos minutos.

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Actualización de puerta de enlace de la VPN de Azure: todos los clientes de punto a sitio no se pueden conectar

### <a name="cause"></a>Causa

Si el certificado cubre más del 50 por ciento durante su vigencia, se deshace.

### <a name="solution"></a>Solución

Para resolver este problema, cree y redistribuya los nuevos certificados a los clientes de VPN. 

## <a name="too-many-vpn-clients-connected-at-once"></a>Demasiados clientes de VPN conectados a la vez

Para cada puerta de enlace de la VPN, el número máximo de conexiones permitidas es 128. Puede ver el número total de clientes conectados en Azure Portal.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>La VPN de punto a sitio agrega de forma incorrecta una ruta para 10.0.0.0/8 a la tabla de enrutamiento

### <a name="symptom"></a>Síntoma

Al marcar la conexión VPN en el cliente de punto a sitio, el cliente de VPN no debe agregar una ruta hacia la red virtual de Azure. La aplicación auxiliar de IP debe agregar una ruta para la subred de los clientes de VPN. 

El intervalo de cliente de VPN pertenece a una subred más pequeña de 10.0.0.0/8, como 10.0.12.0/24. En lugar de una ruta para 10.0.12.0/24, se agrega una ruta para 10.0.0.0/8 con una prioridad más alta. 

Esta ruta incorrecta interrumpe la conectividad con otras redes locales que pueden pertenecer a otra subred del intervalo 10.0.0.0/8, por ejemplo, 10.50.0.0/24, que no tienen una ruta concreta definida. 

### <a name="cause"></a>Causa

Este comportamiento es así por diseño para los clientes Windows. Cuando el cliente usa el protocolo PPP IPCP, obtiene la dirección IP de la interfaz de túnel del servidor (la puerta de enlace de la VPN en este caso). Pero debido a una limitación del protocolo, el cliente no tiene la máscara de subred. Dado que no hay ninguna otra manera de obtenerla, el cliente intenta adivinar la máscara de subred en función de la clase de la dirección IP de la interfaz de túnel. 

Por lo tanto, se agrega una ruta en función de la siguiente asignación estática: 

Si la dirección pertenece a la clase A --> se aplica /8

Si la dirección pertenece a la clase B --> se aplica /16

Si la dirección pertenece a la clase C --> se aplica /24

## <a name="vpn-client-cannot-access-network-file-shares"></a>El cliente de VPN no puede acceder a recursos compartidos de archivos de red

### <a name="symptom"></a>Síntoma

El cliente de VPN se ha conectado a la red virtual de Azure, pero no puede acceder a recursos compartidos de red.

### <a name="cause"></a>Causa

El protocolo SMB se usa para el acceso a recursos compartidos de archivos. Cuando se inicia la conexión, el cliente de VPN agrega las credenciales de sesión y se produce el error. Una vez establecida la conexión, el cliente se ve obligado a usar las credenciales almacenadas en caché para la autenticación Kerberos. Este proceso inicia consultas al Centro de distribución de claves (un controlador de dominio) para obtener un token. Como el cliente se conecta desde Internet, es posible que no pueda alcanzar el controlador de dominio. Por lo tanto, el cliente no pueden conmutar por error desde Kerberos a NTLM. 

El único momento en que el cliente debe escribir una credencial es cuando tiene un certificado válido (con SAN=UPN) emitido por el dominio al que está unido. El cliente también debe estar conectado físicamente a la red de dominios. En este caso, el cliente intenta usar el certificado y llega al controlador de dominio. Luego, el Centro de distribución de claves devuelve un error "KDC_ERR_C_PRINCIPAL_UNKNOWN". El cliente se ve forzado a conmutar por error a NTLM. 

### <a name="solution"></a>Solución

Para solucionar el problema, deshabilite el almacenamiento en caché de credenciales de dominio desde la subclave del Registro siguiente: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>No se puede encontrar la conexión VPN de punto a sitio en Windows después de volver a instalar el cliente de VPN

### <a name="symptom"></a>Síntoma

Se quita la conexión VPN de punto a sitio y luego se vuelve a instalar el cliente de VPN. En esta situación, la conexión VPN no se configura correctamente. No se ve la conexión VPN en el valor **Conexiones de red** de Windows.

### <a name="solution"></a>Solución

Para resolver el problema, elimine los archivos de configuración antiguos del cliente de VPN de **C:\Usuarios\Nombre de usuario\AppData\Roaming\Microsoft\Network\Connections** y luego vuelva a ejecutar el instalador del cliente de VPN.


---
title: Guía de solución de problemas del Explorador de Azure Storage | Microsoft Docs
description: Información general sobre las dos funciones de depuración de Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: c409788ef68ab41a23e2991ea0ea1effce841a82
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guía de solución de problemas del Explorador de Azure Storage

Explorador de Microsoft Azure Storage es una aplicación independiente que permite trabajar fácilmente con los datos de Azure Storage en Windows, macOS y Linux. La aplicación puede conectarse a las cuentas de Storage hospedadas en Azure, nubes nacionales y Azure Stack.

Esta guía resume las soluciones para problemas comunes en el Explorador de Storage.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Error: Certificado autofirmado de cadena de certificados (y errores similares)

Los errores de certificado están causados por una de las dos situaciones siguientes:

1. La aplicación se conecta a través de un "proxy transparente", lo que significa que un servidor (por ejemplo, el servidor de la compañía) intercepta el tráfico HTTPS, lo descifra y, luego, lo cifrar mediante un certificado autofirmado.
2. Ejecuta una aplicación que inserta un certificado SSL autofirmado en los mensajes HTTPS que recibe. Ejemplos de aplicaciones que insertan certificados incluyen software antivirus y de inspección de tráfico de red.

Cuando el Explorador de Storage ve un certificado autofirmado o que no es de confianza, ya no podrá saber si se ha modificado el mensaje HTTPS recibido. Si tiene una copia del certificado autofirmado, puede indicar que el Explorador de Storage confíe en él con los pasos siguientes:

1. Obtención de una copia X.509 (.cer) codificada en Base-64 del certificado
2. Haga clic en **Editar** > **Certificados SSL** > **Importar certificados** y, luego, utilice el selector de archivos para encontrar, seleccionar y abrir el archivo .cer.

Este problema también puede ser el resultado de varios certificados (raíz e intermedio). Ambos certificados deben agregarse para solucionar el error.

Si no está seguro de dónde procede el certificado, puede intentar estos pasos para encontrarlo:

1. Instale Open SSL.

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (cualquiera de las versiones ligeras debería ser suficiente).
    * Mac y Linux: debe estar incluido con el sistema operativo.
2. Ejecute Open SSL.

    * Windows: abra el directorio de instalación, haga clic en **/bin/** y, luego, haga doble clic en **openssl.exe**.
    * Mac y Linux: ejecute **openssl** desde un terminal.
3. Ejecute `s_client -showcerts -connect microsoft.com:443`
4. Busque certificados autofirmados. Si no está seguro de cuáles son autofirmados, busque cualquier lugar en el que el asunto `("s:")` y el emisor `("i:")` sean el mismo.
5. Cuando haya encontrado cualquier certificado autofirmado, para cada uno, copie y pegue todo el contenido desde **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** (inclusive) a un archivo .cer nuevo.
6. Abra el Explorador de Storage, haga clic en **Editar** > **Certificados SSL** > **Importar certificados** y, luego, utilice el selector de archivos para encontrar, seleccionar y abrir los archivos .cer que ha creado.

Si no se encuentra ningún certificado autofirmado siguiendo los pasos anteriores, póngase en contacto con nosotros mediante la herramienta de comentarios para más ayuda. Como alternativa, puede elegir iniciar el Explorador de Storage desde la línea de comandos con la marca `--ignore-certificate-errors`. Cuando se inicia con esta marca, el Explorador de Storage pasará por alto los errores de certificado.

## <a name="sign-in-issues"></a>Problemas de inicio de sesión

Si no puede iniciar sesión en, pruebe los siguientes métodos de solución de problemas:

* Reinicio de Explorador de Storage
* Si la ventana de autenticación está en blanco, espere al menos un minuto antes de cerrar el cuadro de diálogo de autenticación.
* Asegúrese de que el servidor proxy y el certificado está configurado correctamente para la máquina como para el Explorador de Storage.
* Si está en Windows y tiene acceso a Visual Studio 2017 en la misma máquina e inicia sesión, intente iniciar sesión en Visual Studio 2017.

Si ninguno de estos métodos funciona, [abra una incidencia en GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="unable-to-retrieve-subscriptions"></a>No se pueden recuperar las suscripciones

Si no puede recuperar las suscripciones después de iniciar sesión correctamente, siga los métodos siguientes de solución de problemas:

* Compruebe que la cuenta tiene acceso a las que espera. Para comprobar que tiene acceso, inicie sesión en el portal para el entorno de Azure que está intentando usar.
* Asegúrese de que ha iniciado sesión con el entorno correcto de Azure (Azure, Azure China, Azure Alemania, Azure US Government, o entorno personalizado o Azure Stack).
* Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage.
* Pruebe a quitar la cuenta y volver a agregarla.
* Observe la consola de herramientas para desarrolladores (Ayuda > Alternar herramientas de desarrollo) mientras el Explorador de Storage carga las suscripciones. Busque mensajes de error (texto rojo) o cualquier mensaje que contenga un texto que indique que no se pueden cargar suscripciones para el inquilino. Si ve algún mensaje preocupante, [abra una incidencia en GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>No se puede quitar la cuenta adjunta o el recurso de almacenamiento

Si no puede quitar una cuenta adjunta o un recurso de almacenamiento a través de la interfaz de usuario, puede eliminar manualmente todos los recursos adjuntos mediante la eliminación de las siguientes carpetas:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Cierre el Explorador de Storage antes de eliminar las carpetas anteriores.

> [!NOTE]
>  Si alguna vez ha importado un certificado SSL, haga una copia de seguridad del contenido del directorio `certs`. Después, puede utilizar la copia de seguridad para volver a importar los certificados SSL.

## <a name="proxy-issues"></a>Problemas de proxy

En primer lugar, asegúrese de que la siguiente información que especificó sea correcta:

*Dirección URL del proxy y número de puerto *Nombre de usuario y contraseña si lo requiere el proxy

### <a name="common-solutions"></a>Soluciones comunes

Si todavía tiene problemas, pruebe los siguientes métodos de solución de problemas:

* Si puede conectarse a Internet sin usar el proxy, compruebe que el Explorador de Storage funciona sin la configuración del proxy habilitada. Si este es así, puede haber un problema con la configuración de proxy. Trabaje con el administrador del servidor proxy para identificar los problemas.
* Compruebe que otras aplicaciones que utilicen el servidor proxy funcionen según lo esperado.
* Compruebe que puede conectarse al portal del entorno de Azure que está intentando usar
* Compruebe que puede recibir las respuestas de los puntos de conexión de servicio. Especifique una de las direcciones URL de punto de conexión en el explorador. Si puede conectarse, debería recibir un InvalidQueryParameterValue o una respuesta XML similar.
* Si otra persona también usa el Explorador de Storage con el servidor proxy, compruebe que pueda conectarse. Si se puede conectar, quizá deba ponerse en contacto con el administrador del servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Herramientas para el diagnóstico de problemas

Si dispone de herramientas de red, como Fiddler para Windows, es posible que pueda diagnosticar los problemas como se indica a continuación:

* Si tiene que trabajar a través del proxy, es posible que tenga que configurar la herramienta de red para conectarse a través del proxy.
* Compruebe el número de puerto utilizado por la herramienta de red.
* Escriba la dirección URL del host local y el número de puerto de la herramienta de red como configuración de proxy en el Explorador de Storage. Cuando lo haya hecho correctamente, la herramienta de red inicia el registro de las solicitudes de red realizadas por el Explorador de Storage para puntos de conexión de servicio y administración. Por ejemplo, escriba https://cawablobgrs.blob.core.windows.net/ para el punto de conexión de blob en un explorador y recibirá una respuesta similar a la siguiente, lo que sugiere que el recurso existe, aunque no se puede obtener acceso a él.

![ejemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Póngase en contacto con el administrador del servidor proxy

Si es correcta la configuración de proxy, tendrá que ponerse en contacto con el administrador del servidor proxy y, además, deberá hacer lo siguiente:

* Asegúrese de que el proxy no bloquee el tráfico a los puntos de conexión de administración de Azure o de los recursos.
* Compruebe el protocolo de autenticación utilizado por el servidor proxy. En estos momentos, el Explorador de Storage no admite los servidores proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensaje de error "No se pueden recuperar los elementos secundarios"

Si está conectado a Azure a través de un servidor proxy, compruebe que la configuración de proxy sea correcta. Si el propietario de una suscripción o una cuenta concedió acceso a un recurso, compruebe que disponga de permisos de lectura o lista para dicho recurso.

### <a name="issues-with-sas-url"></a>Problemas con la URL de SAS
Si se conecta a un servicio mediante una dirección URL de SAS y se produce este error:

* Compruebe que la dirección URL proporciona los permisos necesarios para leer o enumerar los recursos.
* Compruebe que la dirección URL no haya expirado.
* Si la dirección URL de SAS se basa en una directiva de acceso, compruebe que la directiva de acceso no haya sido revocada.

Si asoció accidentalmente una dirección URL de SAS no válida y no se puede desasociar, siga estos pasos:
1.  Cuando se ejecute el Explorador de Storage, presione F12 para abrir la ventana de herramientas de desarrollador.
2.  Haga clic en la pestaña Aplicación y, a continuación, haga clic en Almacenamiento Local > file:// en el árbol de la izquierda.
3.  Busque la clave asociada al tipo de servicio del URI de SAS problemática. Por ejemplo, si el identificador URI de SAS incorrecto es para un contenedor de blobs, busque la clave llamada `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  El valor de la clave debe ser una matriz JSON. Busque el objeto asociado con el URI incorrecto y quítelo.
5.  Presione Ctrl + R para volver a cargar el Explorador de Storage.

## <a name="linux-dependencies"></a>Dependencias de Linux

En distribuciones de Linux diferentes de Ubuntu 16.04, debe instalar manualmente algunas dependencias. En general, se requieren los siguientes paquetes:
* [.NET Core 2.x](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* GCC actualizado

Según la distribución, puede que deba instalar otros paquetes. Las [notas de la versión](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) del Explorador de Storage contienen pasos específicos para algunas distribuciones.

## <a name="next-steps"></a>Pasos siguientes

Si ninguna de las soluciones funciona, [abrir una incidencia en GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). También puede acceder rápidamente a GitHub con el botón "Notificar problema a GitHub" en la esquina inferior izquierda.

![Comentarios](./media/storage-explorer-troubleshooting/feedback-button.PNG)

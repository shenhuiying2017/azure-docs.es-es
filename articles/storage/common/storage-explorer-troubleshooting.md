---
title: "Guía de solución de problemas del Explorador de Azure Storage | Microsoft Docs"
description: "Información general sobre las dos funciones de depuración de Azure"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 3187939fa813f941c2fe12a359df474a6c487c71
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guía de solución de problemas del Explorador de Azure Storage

El Explorador de Microsoft Azure Storage (versión preliminar) es una aplicación independiente que permite trabajar fácilmente con los datos de Azure Storage en Windows, macOS y Linux. La aplicación puede conectarse a las cuentas de Storage hospedadas en Azure, nubes independientes y Azure Stack.

Esta guía resume las soluciones para problemas comunes en el Explorador de Storage.

## <a name="sign-in-issues"></a>Problemas de inicio de sesión

Solo se admiten las cuentas de Azure Active Directory (AAD). Si usa una cuenta de AD FS, se espera que el inicio de sesión en el Explorador de Storage no funcione. Antes de continuar, pruebe a reiniciar la aplicación para ver si se pueden corregir los problemas.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>Error: Certificado autofirmado de cadena de certificados

Hay varias razones por las puede encontrar este error, y las dos causas más comunes son los siguientes:

1. La aplicación se conecta a través de un "proxy transparente", lo que significa que un servidor (por ejemplo, el servidor de la compañía) intercepta el tráfico HTTPS, lo descifra y, luego, lo cifrar mediante un certificado autofirmado.

2. Ejecuta una aplicación, como software antivirus, que inserta un certificado SSL autofirmado en los mensajes HTTPS que recibe.

Cuando el Explorador de Storage se encuentra uno de estos problemas, no puede saber si se alteró el mensaje HTTPS recibido. Si tiene una copia del certificado autofirmado, puede dejar que el Explorador de Storage confíe en él. Si no está seguro de quién insertó el certificado, siga estos pasos para descubrirlo:

1. Instale Open SSL.

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (cualquiera de las versiones ligeras debería ser suficiente).

    - Mac y Linux: debe estar incluido con el sistema operativo.

2. Ejecute Open SSL.

    - Windows: abra el directorio de instalación, haga clic en **/bin/**y, luego, haga doble clic en **openssl.exe**.
    - Mac y Linux: ejecute **openssl** desde un terminal.

3. Ejecute s_client -showcerts -connect microsoft.com:443.

4. Busque certificados autofirmados. Si no está seguro de cuáles son autofirmados, busque cualquier lugar en el que el asunto ("s:") y el emisor ("i:") sean el mismo.

5. Cuando haya encontrado cualquier certificado autofirmado, para cada uno, copie y pegue todo el contenido desde **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** (inclusive) a un archivo .cer nuevo.

6. Abra el Explorador de Storage, haga clic en **Editar** > **Certificados SSL** > **Importar certificados** y, luego, utilice el selector de archivos para encontrar, seleccionar y abrir los archivos .cer que ha creado.

Si no se encuentra ningún certificado autofirmado siguiendo los pasos anteriores, póngase en contacto con nosotros mediante la herramienta de comentarios para obtener más ayuda.

### <a name="unable-to-retrieve-subscriptions"></a>No se pueden recuperar las suscripciones

Si no puede recuperar las suscripciones después de iniciar la sesión correctamente, siga estos pasos para solucionar este problema:

- Verifique que la cuenta tiene acceso a las suscripciones al iniciar sesión en Azure Portal.

- Asegúrese de que ha iniciado sesión usando el entorno correcto (Azure, Azure China, Azure Alemania, Azure US Government, o entorno personalizado o Azure Stack).

- Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage.

- Pruebe a quitar la cuenta y volver a agregarla.

- Intente eliminar los siguientes archivos del directorio raíz (es decir, C:\Users\ContosoUser) y, luego, intente volver a agregar la cuenta:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Revise la consola de las herramientas de desarrollo (presionando F12) cuando se inicie sesión por si aparecieran mensajes de error:

![herramientas de desarrollo](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>No se puede ver la página de autenticación

Si no puede ver la página de autenticación, siga estos pasos para solucionar este problema:

- Según la velocidad de la conexión, la página de inicio de sesión puede tardar unos instantes en cargar; espere al menos un minuto antes de cerrar el cuadro de diálogo de autenticación.

- Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage.

- Consulte la consola del desarrollador presionando la tecla F12. Revise las respuestas de la consola del desarrollador y vea si puede encontrar alguna pista para saber por qué no funciona la autenticación.

### <a name="cannot-remove-account"></a>No se puede quitar la cuenta

Si no puede quitar una cuenta, o si el vínculo para volver a autenticar no hace nada, siga estos pasos para solucionar este problema:

- Intente eliminar los siguientes archivos del directorio raíz y, luego, intente volver a agregar la cuenta:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Si desea quitar recursos de Storage conectados a SAS, elimine los siguientes archivos:

    - Carpeta %AppData%/StorageExplorer para Windows

    - /Users/<su_nombre>/Library/Applicaiton SUpport/StorageExplorer para Mac

    - ~/.config/StorageExplorer para Linux

> [!NOTE]
>  Tendrá que volver a escribir todas sus credenciales si elimina estos archivos.

## <a name="proxy-issues"></a>Problemas de proxy

En primer lugar, asegúrese de que la siguiente información que especificó sea correcta:

- La dirección URL del proxy y el número de puerto

- El nombre de usuario y la contraseña si los exige el proxy

### <a name="common-solutions"></a>Soluciones comunes

Si sigue experimentando problemas, siga estos pasos para solucionarlos:

- Si puede conectarse a Internet sin usar el proxy, compruebe que el Explorador de Storage funciona sin la configuración del proxy habilitada. Si este es así, puede haber un problema con la configuración de proxy. Trabaje con el administrador del servidor proxy para identificar los problemas.

- Compruebe que otras aplicaciones que utilicen el servidor proxy funcionen según lo esperado.

- Compruebe que pueda conectarse a Microsoft Azure Portal mediante el explorador web.

- Compruebe que puede recibir las respuestas de los puntos de conexión de servicio. Especifique una de las direcciones URL de punto de conexión en el explorador. Si puede conectarse, debería recibir un InvalidQueryParameterValue o una respuesta XML similar.

- Si otra persona también usa el Explorador de Storage con el servidor proxy, compruebe que pueda conectarse. Si se puede conectar, quizá deba ponerse en contacto con el administrador del servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Herramientas para el diagnóstico de problemas

Si dispone de herramientas de red, como Fiddler para Windows, es posible que pueda diagnosticar los problemas como se indica a continuación:

- Si tiene que trabajar a través del proxy, es posible que tenga que configurar la herramienta de red para conectarse a través del proxy.

- Compruebe el número de puerto utilizado por la herramienta de red.

- Escriba la dirección URL del host local y el número de puerto de la herramienta de red como configuración de proxy en el Explorador de Storage. Si se hace correctamente, la herramienta de red inicia el registro de las solicitudes de red realizadas por el Explorador de Storage para puntos de conexión de servicio y administración. Por ejemplo, escriba https://cawablobgrs.blob.core.windows.net/ para el punto de conexión de blob en un explorador y recibirá una respuesta similar a la siguiente, lo que sugiere que el recurso existe, aunque no se puede obtener acceso a él.

![ejemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Póngase en contacto con el administrador del servidor proxy

Si es correcta la configuración de proxy, tendrá que ponerse en contacto con el administrador del servidor proxy y, además, deberá hacer lo siguiente:

- Asegúrese de que el proxy no bloquee el tráfico a los puntos de conexión de administración de Azure o de los recursos.

- Compruebe el protocolo de autenticación utilizado por el servidor proxy. En estos momentos, el Explorador de Storage no admite los servidores proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensaje de error "No se pueden recuperar los elementos secundarios"

Si está conectado a Azure a través de un servidor proxy, compruebe que la configuración de proxy sea correcta. Si el propietario de una suscripción o una cuenta concedió acceso a un recurso, compruebe que disponga de permisos de lectura o lista para dicho recurso.

### <a name="issues-with-sas-url"></a>Problemas con la URL de SAS
Si se conecta a un servicio mediante una dirección URL de SAS y se produce este error:

- Compruebe que la dirección URL proporciona los permisos necesarios para leer o enumerar los recursos.

- Compruebe que la dirección URL no haya expirado.

- Si la dirección URL de SAS se basa en una directiva de acceso, compruebe que la directiva de acceso no haya sido revocada.

Si asoció accidentalmente una dirección URL de SAS no válida y no se puede desasociar, siga estos pasos:
1.  Cuando se ejecute el Explorador de Storage, presione F12 para abrir la ventana de herramientas de desarrollador.
2.  Haga clic en la pestaña Aplicación y, a continuación, haga clic en Almacenamiento Local > file:// en el árbol de la izquierda.
3.  Busque la clave asociada al tipo de servicio del URI de SAS problemática. Por ejemplo, si el URI incorrecto de SAS es para un contenedor de blobs, busque la clave denominada "StorageExplorer_AddStorageServiceSAS_v1_blob".
4.  El valor de la clave debe ser una matriz JSON. Busque el objeto asociado con el URI incorrecto y quítelo.
5.  Presione Ctrl + R para volver a cargar el Explorador de Storage.


## <a name="next-steps"></a>Pasos siguientes

Si ninguna de las soluciones funciona, envíe su problema mediante la herramienta de comentarios con su dirección de correo electrónico y todos los detalles sobre el problema como sea posible, para que podemos ponernos en contacto con usted para corregir el problema.

Para ello, haga clic en el menú **Ayuda** y, luego, haga clic en **Enviar comentarios**.

![Comentarios](./media/storage-explorer-troubleshooting/4022503_en_1.png)

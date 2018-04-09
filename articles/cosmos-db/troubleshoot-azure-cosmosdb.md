---
title: Administración de Azure Cosmos DB en el Explorador de Azure Storage
description: Aprenda a administrar Azure Cosmos DB en el Explorador de Azure Storage.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Guía de solución de problemas de Azure Cosmos DB en el Explorador de Storage

[Azure Cosmos DB en el Explorador de Azure Storage](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) es una aplicación independiente que le permite conectarse a las cuentas de Azure Cosmos DB hospedadas en Azure y nubes soberanas de Windows, macOS o Linux. Permite a los usuarios administrar entidades de Azure Cosmos DB, manipular datos, actualizar procedimientos y desencadenadores almacenados, además de otras entidades de Azure como los blobs y las colas de Storage.

Esta guía resume las soluciones para problemas comunes en Azure Cosmos DB en el Explorador de Storage.

- [Problemas de inicio de sesión](#Sign-in-issues)
  - [Certificado autofirmado de cadena de certificados](#Self-signed-certificate-in-certificate-chain)
  - [No se pueden recuperar las suscripciones](#Unable-to-retrieve-subscriptions)
  - [No se puede ver la página de autenticación](#Unable-to-see-the-authentication-page)
  - [No se puede quitar la cuenta](#Cannot-remove-account)
- [Problema de proxy de HTTP/HTTPS](#Http/Https-proxy-issue)
- [Problema de nodo "Development" (Desarrollo) en nodo "Local and Attached" (Locales y conectados)](#Development-node-under-Local-and-Attached-node-issue)
- [Error al adjuntar la cuenta de Azure Cosmos DB al nodo "Local and Attached" (Locales y conectados)](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Error al expandir el nodo de Azure Cosmos DB](#Expand-Azure-Cosmos-DB-node-error)
- [Pasos siguientes](#Next-steps)

<h2 id="Sign-in-issues">Problemas de inicio de sesión</h2>

Antes de continuar, pruebe a reiniciar la aplicación para ver si se pueden corregir los problemas.

<h2 id="Self-signed-certificate-in-certificate-chain">Certificado autofirmado de cadena de certificados</h2>

Existen varias razones por las que puede ver este error, las dos más comunes son:

1. Se encuentra detrás de un "proxy transparente", lo que significa que alguien (por ejemplo el departamento de TI) intercepta el tráfico HTTPS, lo descifra y después lo cifra mediante un certificado autofirmado.

2. Está ejecutando un software, como un software antivirus, que inserta un certificado SSL autofirmado en los mensajes HTTPS que recibe.

Cuando el Explorador de Storage se encuentra uno de estos "certificados autofirmados", ya no sabe si el mensaje HTTPS que recibe se ha alterado. Pero si tiene una copia del certificado autofirmado, puede hacer que el Explorador de Storage confíe en él. Si no está seguro de qué o quién está insertando el certificado, puede intentar encontrarlo realizando los pasos siguientes:

1. Instale Open SSL.
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (cualquiera de las versiones ligeras vale)
     - Mac y Linux: debe estar incluido con el sistema operativo
2. Ejecute Open SSL.
    - Windows: Vaya al directorio de instalación y después a **/bin/**, a continuación, haga doble clic en **openssl.exe**.
    - Mac y Linux: ejecute **openssl** desde un terminal
3. Ejecute `s_client -showcerts -connect microsoft.com:443`
4. Busque certificados autofirmados. Si no está seguro de cuáles son autofirmados, busque cualquier lugar en el que el asunto ("s:") y el emisor ("i:") sean el mismo.
5.  Cuando haya encontrado cualquier certificado autofirmado, copie y pegue todo el contenido desde **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** (inclusive) a un archivo .cer nuevo para cada uno de los certificados.
6.  Abra el Explorador de Storage y vaya a **Editar** > **Certificados SSL** > **Importar certificados**. Mediante el selector de archivos, busque, seleccione y abra los archivos .cer que creó.

Si no encuentra ningún certificado autofirmado siguiendo los pasos anteriores, envíenos un comentario para obtener más ayuda.

<h2 id="Unable-to-retrieve-subscriptions">No se pueden recuperar las suscripciones</h2>

Si no puede recuperar las suscripciones después de haber iniciado sesión correctamente en:

- Verifique que la cuenta tiene acceso a las suscripciones iniciando sesión en [Azure Portal](http://portal.azure.com/)
- Asegúrese de que ha iniciado sesión usando el entorno correcto ([Azure](http://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Alemania](https://portal.microsoftazure.de/), [Azure US Government](http://portal.azure.us/), o entorno personalizado o Azure Stack)
- Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage
- Pruebe a eliminar la cuenta y volver a agregarla
- Intente eliminar los siguientes archivos del directorio de inicio (como, C:\Users\ContosoUser) y, luego, vuelva a agregar la cuenta:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Revise la consola de las herramientas de desarrollo (f12) cuando inicie sesión por si aparecieran mensajes de error

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">No se puede ver la página de autenticación</h2>  

Si no puede ver la página de autenticación:

- Según la velocidad de la conexión, la página de inicio de sesión puede tardar unos instantes en cargar; espere al menos un minuto antes de cerrar el diálogo de autenticación
- Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage
- Abra la consola del desarrollador presionando la tecla F12. Revise las respuestas de la consola del desarrollador y vea si puede encontrar alguna pista para saber por qué no funciona la autenticación

<h2 id="Cannot-remove-account">No se puede quitar la cuenta</h2>

Si no puede quitar una cuenta, o si el vínculo para volver a autenticar no hace nada

- Intente eliminar los siguientes archivos del directorio de inicio y vuelva a agregar la cuenta:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Si desea quitar recursos de Storage conectados a SAS, elimine:
  - Carpeta %AppData%/StorageExplorer para Windows
  - /Users/<su_nombre>/Library/Applicaiton SUpport/StorageExplorer para Mac
  - ~/.config/StorageExplorer para Linux
  - **Tendrá que volver a escribir todas sus credenciales** si elimina estos archivos


<h2 id="Http/Https-proxy-issue">Problema de proxy de HTTP/HTTPS</h2>

No puede mostrar los nodos de Azure Cosmos DB en el árbol de la izquierda al configurar el proxy de http/https en ASE. Es un problema conocido y se corregirá en la siguiente versión. En este momento puede usar el Explorador de datos de Azure Cosmos DB en Azure Portal como solución alternativa. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">Problema de nodo "Development" (Desarrollo) en nodo "Local and Attached" (Locales y conectados)</h2>

No hay ninguna respuesta tras hacer clic en el nodo "Development" (Desarrollo) en el nodo "Local and Attached" (Locales y conectados) en el árbol de la izquierda.  Este comportamiento es normal. El emulador local de Azure Cosmos DB se admitirá en la próxima versión.

![Nodo "Development" (Desarrollo)](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">Error al adjuntar la cuenta de Azure Cosmos DB al nodo "Local and Attached" (Locales y conectados)</h2>

Si ve el error siguiente después de adjuntar la cuenta de Azure Cosmos DB en el nodo"Local and Attached" (Locales y conectados), compruebe si está usando la cadena de conexión correcta.

![Error al adjuntar Azure Cosmos DB en "Local and Attached" (Locales y conectados)](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Error al expandir el nodo de Azure Cosmos DB</h2>

Puede ver error siguiente al intentar expandir los nodos del árbol de la izquierda. 

![Error de expansión](./media/troubleshoot-cosmosdb/expand-error.png)

Pruebe con las sugerencias siguientes:

- Compruebe si la cuenta de Azure Cosmos DB está en curso de aprovisionamiento e inténtelo de nuevo cuando ya se haya creado la cuenta correctamente.
- Si la cuenta está en el nodo de "Quick Access" (Acceso rápido) o los nodos "Local and Attached" (Locales y conectados), compruebe si la cuenta se ha eliminado. Si es así, tiene que quitar manualmente el nodo.

<h2 id="Next-steps">Pasos siguientes</h2>

Si ninguna de las soluciones funciona, envíe un correo electrónico al equipo de herramientas de desarrollo de Azure Cosmos DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) con información detallada acerca del problema para poder corregirlo.






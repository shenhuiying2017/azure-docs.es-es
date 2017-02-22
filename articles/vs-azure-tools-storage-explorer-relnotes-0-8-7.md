---
title: "Explorador de Microsoft Azure Storage 0.8.7 (versión preliminar) | Microsoft Docs"
description: "Notas de la versión del Explorador de Microsoft Azure Storage 0.8.7 (versión preliminar)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: release-notes
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: bf47274a97342253dfd4ebfa9f1029a7c18b81eb
ms.openlocfilehash: fc3620ca19d4824bc8ffb3bbe89ef47c5127b9d1


---
# <a name="microsoft-azure-storage-explorer-087-preview"></a>Explorador de Microsoft Azure Storage 0.8.7 (versión preliminar)
## <a name="overview"></a>Información general
Este artículo contiene las notas de la versión de la versión preliminar del Explorador de Azure Storage 0.8.7.

[Explorador de Microsoft Azure Storage (versión preliminar)](./vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente que permite trabajar fácilmente con los datos de Azure Storage en Windows, macOS y Linux.

## <a name="azure-storage-explorer-087-preview"></a>Explorador de Azure Storage 0.8.7 (versión preliminar)
### <a name="download-azure-storage-explorer-087-preview"></a>Descarga del Explorador de Azure Storage 0.8.7 (versión preliminar)
- [Explorador de Azure Storage 0.8.7 - versión preliminar para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorador de Azure Storage 0.8.7 - versión preliminar para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorador de Azure Storage 0.8.7 - versión preliminar para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new-updates"></a>Nuevas actualizaciones
* Puede elegir cómo resolver conflictos al principio de una actualización, descarga o sesión de copia en la ventana **Actividades**.
* Mantenga el puntero sobre una pestaña para ver la ruta de acceso completa del recurso de almacenamiento.
* Al hacer clic en una pestaña, se sincroniza con su ubicación en el panel de navegación izquierdo.

### <a name="fixes"></a>Correcciones
* Problema corregido: El Explorador de Storage es ahora una aplicación de confianza en macOS.
* Problema corregido: Ubuntu 14.04 se admite de nuevo.
* Problema corregido: En ocasiones, la interfaz de usuario para agregar una cuenta parpadea al cargar las suscripciones.
* Problema corregido: En ocasiones, no todos los recursos de almacenamiento se muestran en el panel de navegación izquierdo.
* Problema corregido: El panel de acciones a veces muestra acciones vacías.
* Problema corregido: Ahora se conserva el tamaño de la ventana de la última sesión que se cerró.
* Problema corregido: Se pueden abrir varias pestañas para el mismo recurso mediante el menú contextual.

### <a name="known-issues"></a>Problemas conocidos
* Acceso rápido solo funciona con elementos basados en la suscripción. En esta versión no se admiten recursos locales o recursos adjuntados a través de la clave o token de SAS.
* Es posible que Acceso rápido tarde unos segundos en desplazarse hasta el recurso de destino, en función del número de recursos que tenga.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado o se pueden producir excepciones no controladas.
* Por primera vez con el Explorador de Storage en macOS, es posible que aparezcan varias solicitudes que piden permiso del usuario para acceder a las llaves. Se recomienda seleccionar **Permitir siempre** para que la solicitud no vuelva a aparecer más.

## <a name="previous-releases"></a>Versiones anteriores
### <a name="features"></a>Características
#### <a name="main-features"></a>Características principales
* Versiones de macOS, Linux y Windows
* Inicie sesión para ver las cuentas de almacenamiento agrupadas por suscripción:
    * Use la cuenta de organización, cuenta Microsoft, 2FA, etc.
    * Configure y administre la configuración de proxy.
    * Quite cuentas cerrando la sesión.
* Conéctese a las cuentas de almacenamiento mediante:
    * Nombre y clave de la cuenta
    * Puntos de conexión personalizados (incluido Azure China)
    * URI de SAS para cuentas de almacenamiento
* Compatibilidad de Azure Resource Manager y almacenamiento clásico
* Genere claves de SAS para blobs, contenedores de blobs, colas, tablas o recursos compartidos de archivos.
* Conéctese a contenedores de blobs, colas, tablas o recursos compartidos de archivos con la clave de Firmas de acceso compartido (SAS).
* Administre directivas de acceso almacenadas para contenedores de blobs, colas, tablas o recursos compartidos de archivos.
* Almacenamiento de desarrollo local con el emulador de almacenamiento (solo Windows)
* Cree y elimine contenedores de blobs, colas o tablas.
* Vea contenedores de blobs $logs y tablas $metrics.
* Busque blobs, colas, tablas o recursos compartidos de archivos específicos.
* Vínculos directos a cuentas de almacenamiento o contenedores, colas, tablas o archivo de recursos compartidos para compartir y acceder fácilmente a los recursos
* Cambie el nombre de contenedores de blobs, tablas y recursos compartidos de archivos.
* Capacidad para administrar y configurar reglas de CORS
* Copie fácilmente las claves principal y secundaria para las cuentas de almacenamiento.
* Comprobaciones de MD5 de la integridad de datos y la coherencia al cargar y descargar
* Busque contenedores de blobs, tablas, colas, recursos compartidos de archivos o cuentas de almacenamiento en el cuadro de búsqueda.
* Ya puede anclar los servicios más utilizados en Acceso rápido para facilitar la navegación.
* Ya puede abrir varios editores en diferentes pestañas. Haga un solo clic para abrir una pestaña temporal y doble clic para abrir una pestaña permanente. También puede hacer clic en la pestaña temporal para que se convierta en una pestaña permanente.
* Notables mejoras de rendimiento y estabilidad para cargas y descargas, especialmente para archivos de gran tamaño en máquinas rápidas
* Hemos vuelto a introducir la búsqueda restringida mejorada y agregado el concepto de restricción. Escriba la ruta de acceso a un nodo mediante el icono sobre el que se mantiene el puntero, haga clic con el botón derecho en-> "Search From Here" (Buscar desde aquí) o manualmente para restringir el ámbito de búsqueda de ese nodo. Una vez restringido el ámbito, puede agregar un término de búsqueda al final de la ruta de acceso para realizar una búsqueda en profundidad a partir de ese nodo.
* Hemos agregado varios temas: claro (valor predeterminado), oscuro, negro en alto contraste y blanco en alto contraste.
* Vaya a Editar-> Temas para cambiar las preferencias de temas.
* En Linux, ahora es necesario utilizar un sistema operativo de 64 bits.
* ¡Hemos actualizado nuestro logotipo!
#### <a name="blobs"></a>Blobs
* Vea blobs y navegue por los directorios.
* Cargue, descargue, elimine y copie blobs y carpetas.
* Abra y vea blobs de imagen y texto de contenido.
* Vea y edite propiedades y metadatos de blob.
* Busque blobs por prefijo.
* Cree e interrumpa concesiones de blobs y contenedores de blobs.
* Arrastre y coloque archivos para cargar.
* Cambie el nombre de blobs y carpetas.
* Las carpetas vacías "virtuales" ya se pueden crear en contenedores de blobs.
* Puede modificar las propiedades de blobs y archivos.
#### <a name="tables"></a>Tablas
* Vea y consulte entidades con ODATA o utilice el generador de consultas para crear consultas complejas.
* Agregue, edite y elimine entidades.
* Importe y exporte el contenido de tablas en formato CSV (incluidos los resultados de la consulta de exportación).
* Personalice el orden de las columnas.
* Capacidad de guardar consultas
#### <a name="queues"></a>Colas
* Ojee los 32 mensajes más recientes.
* Agregue, quite de la cola y vea mensajes.
* Borre mensajes de la cola.
* Hemos hecho realidad que pueda decidir si va a codificar o descodificar los mensajes de la cola.
#### <a name="file-shares"></a>Recursos compartidos de archivos
* Vea archivos y navegue por los directorios.
* Cargue, descargue, elimine y copie archivos y directorios.
* Vea las propiedades del archivo.
* Cambie el nombre archivos y directorios.

### <a name="bug-fixes"></a>Corrección de errores
* Problema corregido: Problemas de inmovilización de pantalla
* Problema corregido: Mayor seguridad

### <a name="known-issues"></a>Problemas conocidos
* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado, las instalaciones de macOS pueden requerir permisos elevados.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Azure Stack no admite archivos actualmente por lo que, al intentar expandir el nodo **Files**, se produce un error.
* Linux 14.04 instala la versión de gcc actualizada o ampliada. Los siguientes pasos muestran cómo realizar una actualización:

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

### <a name="previous-versions"></a>Versiones anteriores
#### <a name="october-2016-release-version-085"></a>Versión de octubre de 2016 (versión 0.8.5)
* [Descargar para Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Descargar para Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Descargar para Linux](https://go.microsoft.com/fwlink/?LinkId=809308)



<!--HONumber=Jan17_HO3-->



---
title: "Notas de la versión de Explorador de Microsoft Azure Storage (versión preliminar) | Microsoft Docs"
description: "Notas de la versión de Explorador de Microsoft Azure Storage (versión preliminar)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.openlocfilehash: cf077fef6df2fd21cf51f6b4fd4e26a4b5081247
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="microsoft-azure-storage-explorer-preview-release-notes"></a>Notas de la versión de Explorador de Microsoft Azure Storage (versión preliminar)

En este artículo se detallan las notas de la versión 0.9.2 del Explorador de Azure Storage (versión preliminar), así como las de versiones anteriores.

[Explorador de Microsoft Azure Storage (versión preliminar)](./vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente que permite trabajar fácilmente con los datos de Azure Storage en Windows, macOS y Linux.

## <a name="version-092"></a>Versión 0.9.2
01/11/2017

### <a name="download-azure-storage-explorer-092-preview"></a>Descarga del Explorador de Azure Storage 0.9.2 (versión preliminar)
- [Explorador de Azure Storage 0.9.2 (versión preliminar) para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorador de Azure Storage 0.9.2 (versión preliminar) para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorador de Azure Storage 0.9.2 (versión preliminar) para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="hotfixes"></a>Revisiones
* Se podían producir cambios de datos inesperados al editar los valores de Edm.DateTime para entidades de tabla según la zona horaria local. El editor ahora utiliza un cuadro de texto sin formato, lo que proporciona un control preciso y coherente de los valores de Edm.DateTime.
* La carga o descarga de un grupo de blobs cuando se asocia con un nombre y una clave no se iniciarían. Esto se ha solucionado.
* El Explorador de Storage solo le preguntaría si desea volver a autenticar una cuenta obsoleta si una o varias de las suscripciones de la cuenta se seleccionaron. Ahora el Explorador de Storage le pedirá incluso si la cuenta se filtra completamente.
* El dominio de los puntos de conexión para Azure Gobierno de EE.UU. era incorrecto. Esto se ha solucionado.
* A veces resultaba difícil hacer clic en el botón Aplicar del panel Administrar cuentas. Esto ya no debería ocurrir.

### <a name="new"></a>Nuevo
* Compatibilidad de versión preliminar para Azure Cosmos DB:
    * [Documentación en línea](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)
    * Crear bases de datos y colecciones
    * Manipular datos
    * Consultar, crear o eliminar documentos
    * Actualizar procedimientos almacenados, desencadenadores y funciones definidas por el usuario
    * Usar las cadenas de conexión para conectarse y administrar las bases de datos
* Se mejoró el rendimiento de la carga y descarga de muchos blobs pequeños.
* Se agregó una acción "Reintentar todo" si hay errores en un grupo de carga de blobs o en un grupo de descarga de blobs.
* El Explorador de Storage ahora pausará la iteración durante la carga o descarga de blobs si detecta que se perdió la conexión de red. A continuación, puede reanudar la iteración una vez que se haya restablecido la conexión de red.
* Se agregó la posibilidad de "Cerrar todo", "Cerrar otros" y "Cerrar" pestañas a través del menú contextual.
* El Explorador de Storage ahora usa los cuadros de diálogo nativos y los menús contextuales nativos.
* El Explorador de Storage ahora es más accesible. Estas mejoras incluyen:
    * Compatibilidad mejorada con el lector de pantalla, para NVDA en Windows y para VoiceOver en Mac
    * Temas mejorados de contraste alto
    * Correcciones del foco del teclado y del tabulador del teclado

### <a name="fixes"></a>Correcciones
* Si intenta abrir o descargar un blob con un nombre de archivo no válido de Windows, se podría producir un error en la operación. El Explorador de Storage detectará si el nombre de un blob no es válido y le solicitará si quiere codificarlo u omitir el blob. El Explorador de Storage también detectará si un nombre de archivo parece estar codificado y le solicitará si quiere descodificarlo antes de cargarlo.
* Durante la carga del blob, el editor del contenedor del blob de destino podría no actualizarse correctamente. Esto se ha solucionado.
* La compatibilidad con varios formatos de cadenas de conexión y URI de SAS se ha revertido. Se han resuelto todos los problemas conocidos, pero agradecemos que nos envíe sus comentarios si encuentra algún otro.
* La notificación de actualización se interrumpió para algunos usuarios en 0.9.0. Este problema se ha corregido y, si este error le afectó, se puede descargar manualmente la última versión del Explorador de Storage [aquí](https://azure.microsoft.com/en-us/features/storage-explorer/).

### <a name="known-issues"></a>Problemas conocidos
* El Explorador de Storage no admite cuentas de AD FS.
* Las teclas de método abreviado de "Ver explorador" y "Ver administración de cuentas" deberían ser respectivamente CTRL/CMD+MAIÚS+E y CTRL/CMD+MAYÚS+A.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe aquí.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las demás propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:
```
./StorageExplorer.exe --disable-gpu
```
* Los usuarios de Ubuntu 14.04 tendrán que asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos. Después, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```



## <a name="version-091--090-preview"></a>Versión 0.9.1 / 0.9.0 (versión preliminar)
20/10/2017
### <a name="download-azure-storage-explorer-091-preview"></a>Descarga del Explorador de Azure Storage 0.9.1 (versión preliminar)
* [Descargar el Explorador de Azure Storage 0.9.1 (versión preliminar) para Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Descargar el Explorador de Azure Storage 0.9.1 (versión preliminar) para Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Descargar el Explorador de Azure Storage 0.9.1 (versión preliminar) para Linux](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>Nuevo
* Compatibilidad de versión preliminar para Azure Cosmos DB:
    * [Documentación en línea](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)
    * Crear bases de datos y colecciones
    * Manipular datos
    * Consultar, crear o eliminar documentos
    * Actualizar procedimientos almacenados, desencadenadores y funciones definidas por el usuario
    * Usar las cadenas de conexión para conectarse y administrar las bases de datos
* Se mejoró el rendimiento de la carga y descarga de muchos blobs pequeños.
* Se agregó una acción "Reintentar todo" si hay errores en un grupo de carga de blobs o en un grupo de descarga de blobs.
* El Explorador de Storage ahora pausará la iteración durante la carga o descarga de blobs si detecta que se perdió la conexión de red. A continuación, puede reanudar la iteración una vez que se haya restablecido la conexión de red.
* Se agregó la posibilidad de "Cerrar todo", "Cerrar otros" y "Cerrar" pestañas a través del menú contextual.
* El Explorador de Storage ahora usa los cuadros de diálogo nativos y los menús contextuales nativos.
* El Explorador de Storage ahora es más accesible. Estas mejoras incluyen:
    * Compatibilidad mejorada con el lector de pantalla, para NVDA en Windows y para VoiceOver en Mac
    * Temas mejorados de contraste alto
    * Correcciones del foco del teclado y del tabulador del teclado

### <a name="fixes"></a>Correcciones
* Si intenta abrir o descargar un blob con un nombre de archivo no válido de Windows, se podría producir un error en la operación. El Explorador de Storage detectará si el nombre de un blob no es válido y le solicitará si quiere codificarlo u omitir el blob. El Explorador de Storage también detectará si un nombre de archivo parece estar codificado y le solicitará si quiere descodificarlo antes de cargarlo.
* Durante la carga del blob, el editor del contenedor del blob de destino podría no actualizarse correctamente. Esto se ha solucionado.
* La compatibilidad con varios formatos de cadenas de conexión y URI de SAS se ha revertido. Se han resuelto todos los problemas conocidos, pero agradecemos que nos envíe sus comentarios si encuentra algún otro.
* La notificación de actualización se interrumpió para algunos usuarios en 0.9.0. Este problema se ha corregido y, si este error le afectó, se puede descargar manualmente la versión más reciente del Explorador de Storage [aquí](https://azure.microsoft.com/en-us/features/storage-explorer/).

### <a name="known-issues"></a>Problemas conocidos
* El Explorador de Storage no admite cuentas de AD FS.
* Las teclas de método abreviado de "Ver explorador" y "Ver administración de cuentas" deberían ser respectivamente CTRL/CMD+MAIÚS+E y CTRL/CMD+MAYÚS+A.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe aquí.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las demás propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:
```
./StorageExplorer.exe --disable-gpu
```
* Los usuarios de Ubuntu 14.04 tendrán que asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos. Después, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```



## <a name="previous-releases"></a>Versiones anteriores

* [Versión 0.8.16](#version-0816)
* [Versión 0.8.14](#version-0814)
* [Versión 0.8.13](#version-0813)
* [Versión 0.8.12 / 0.8.11 / 0.8.10](#version-0812--0811--0810)
* [Versión 0.8.9 / 0.8.8](#version-089--088)
* [Versión 0.8.7](#version-087)
* [Versión 0.8.6](#version-086)
* [Versión 0.8.5](#version-085)
* [Versión 0.8.4](#version-084)
* [Versión 0.8.3](#version-083)
* [Versión 0.8.2](#version-082)
* [Versión 0.8.0](#version-080)
* [Versión 0.7.20160509.0](#version-07201605090)
* [Versión 0.7.20160325.0](#version-07201603250)
* [Versión 0.7.20160129.1](#version-07201601291)
* [Versión 0.7.20160105.0](#version-07201601050)
* [Versión 0.7.20151116.0](#version-07201511160)

## <a name="version-0816"></a>Versión 0.8.16
8/21/2017

### <a name="new"></a>Nuevo
* Al abrir un blob, el Explorador de Storage le pedirá que cargue el archivo descargado si se detectó un cambio
* Experiencia mejorada de inicio de sesión de Azure Stack
* Mejorado el rendimiento de la carga y descarga de muchos archivos pequeños al mismo tiempo


### <a name="fixes"></a>Correcciones
* En algunos tipos de blob, al elegir la opción "reemplazar" durante un conflicto de carga, a veces provocaría que la carga se volviese a reiniciar.
* En la versión 0.8.15, las cargas se detendrían a veces en un 99 %.
* Al cargar archivos en un recurso compartido de archivos, si decide cargar en un directorio que todavía no existe, la carga produciría un error.
* El Explorador de Storage estaba generando marcas de tiempo de forma incorrecta para las firmas de acceso compartido y las consultas de tabla.


### <a name="known-issues"></a>Problemas conocidos
* El uso de un nombre y una cadena de conexión de clave no funciona actualmente. Funcionará en la próxima versión. Hasta ese momento, puede usar la asociación con el nombre y la clave.
* Si intenta abrir un archivo con un nombre de archivo de Windows no válido, la descarga provocará un error de archivo no encontrado.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Se trata de una limitación de la biblioteca Azure Storage Node.
* Después de completar una carga de blobs, se actualiza la pestaña en la que se inició la carga. Se trata de un cambio con respecto al comportamiento anterior y también provocará que se le devuelva a la raíz del contenedor en el que se halle.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las demás propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* Los usuarios de Ubuntu 14.04 tendrán que asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos. Después, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versión 0.8.14
22/06/2017

### <a name="new"></a>Nuevo

* Versión de Electron actualizada a la 1.7.2 para aprovechar las múltiples actualizaciones de seguridad críticas
* Ahora se puede acceder rápidamente a la guía de solución de problemas en línea desde el menú de ayuda
* [Guía][2] de solución de problemas del Explorador de Storage
* [Instrucciones][3] sobre cómo conectarse a una suscripción de Azure Stack

### <a name="known-issues"></a>Problemas conocidos

* Los botones del cuadro de diálogo de confirmación de eliminación de carpeta no registran los clics del mouse en Linux. La solución alternativa consiste en usar la tecla Entrar.
* Si no elige el certificado de tarjeta inteligente o el PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide la decisión.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las demás propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* La instalación de Ubuntu 14.04 requiere que se actualice la versión de gcc. A continuación puede ver los pasos para actualizarla:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versión 0.8.13
05/12/2017

#### <a name="new"></a>Nuevo

* [Guía][2] de solución de problemas del Explorador de Storage
* [Instrucciones][3] sobre cómo conectarse a una suscripción de Azure Stack

#### <a name="fixes"></a>Correcciones

* Problema corregido: Había una alta probabilidad de que, al cargar un archivo, se produjese un error de memoria agotada.
* Problema corregido: Ahora puede iniciar sesión con una tarjeta inteligente o un PIN.
* Problema corregido: Abrir en Portal ahora funciona con Azure China, Azure Germany, Azure US Government y Azure Stack.
* Problema corregido: Al cargar una carpeta en un contenedor de blobs, a veces se produce un error de “operación ilegal”.
* Problema corregido: Seleccionar todo se deshabilita durante la administración de instantáneas.
* Problema corregido: Los metadatos del blob base puede que se sobrescriban después de ver las propiedades de las instantáneas.

#### <a name="known-issues"></a>Problemas conocidos

* Si no elige el certificado de tarjeta inteligente o el PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide la decisión.
* Cuando se amplía o reduce, el nivel de zoom puede restablecerse momentáneamente al nivel predeterminado.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las demás propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* La instalación de Ubuntu 14.04 requiere que se actualice la versión de gcc. A continuación puede ver los pasos para actualizarla:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812--0811--0810"></a>Versión 0.8.12 / 0.8.11 / 0.8.10
07/04/2017

#### <a name="new"></a>Nuevo

* Explorador de Storage ahora se cerrará automáticamente al instalar una actualización desde la notificación de actualización.
* El acceso rápido local proporciona una experiencia mejorada a la hora de trabajar con los recursos a los que accede con más frecuencia.
* En el editor de contenedores de blobs, ahora puede ver a qué máquina virtual pertenece un blob concedido.
* Ahora puede contraer el panel izquierdo.
* Ahora la detección se ejecuta al mismo tiempo que la descarga.
* Use Estadísticas del contenedor de blobs, el recurso compartido de archivos y los editores de tablas para ver el tamaño del recurso o de la selección.
* Ahora puede iniciar sesión en Azure Active Directory (AAD) basándose en cuentas de Azure Stack.
* Ahora puede cargar archivos de archivo de más de 32 MB en cuentas de almacenamiento Premium.
* Compatibilidad de accesibilidad mejorada.
* Ahora puede agregar certificados SSL X.509 cifrados en Base-64 de confianza en Editar -&gt; Certificados SSL -&gt; Importar certificados.

#### <a name="fixes"></a>Correcciones

* Problema corregido: Después de actualizar las credenciales de una cuenta, a veces no se actualizaba la vista de árbol.
* Problema corregido: Al generar un SAS para tablas y colas de emulador, se producía una URL no válida.
* Problema corregido: Las cuentas de almacenamiento premium ahora se pueden ampliar mientras haya un proxy habilitado.
* Problema corregido: El botón Aplicar de la página de administración de cuentas no funcionaba si había una o cero cuentas seleccionadas.
* Problema corregido: Se puede producir un error al cargar blobs que requieran resoluciones de conflictos. Esto se ha corregido en la versión 0.8.11.
* Problema corregido: No se podían enviar comentarios en la versión 0.8.11. Corregido en la versión 0.8.12.

#### <a name="known-issues"></a>Problemas conocidos

* Después de actualizar a la versión 0.8.10, tendrá que actualizar todas las credenciales.
* Cuando se amplía o reduce, el nivel de zoom puede restablecerse momentáneamente al nivel predeterminado.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las demás propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* La instalación de Ubuntu 14.04 requiere que se actualice la versión de gcc. A continuación puede ver los pasos para actualizarla:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089--088"></a>Versión 0.8.9 / 0.8.8
23/02/2017

<iframe width="560" height="315" src="https://www.youtube.com/embed/R6gonK3cYAc?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/SrRPCm94mfE?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>Nuevo

* Explorador de Storage 0.8.9 descargará automáticamente la última versión de las actualizaciones.
* Revisión: Al usar un URI de SAS generado en el portal para conectarse a una cuenta de almacenamiento, se producía un error.
* Ahora puede crear, administrar y promover instantáneas de blobs.
* Ahora puede iniciar sesión en cuentas de Azure China, Azure Germany y Azure US Government.
* Ahora puede cambiar el nivel de zoom. Use las opciones del menú Ver para acercar, alejar y restablecer el zoom.
* Ahora se admiten caracteres Unicode en los metadatos de usuarios para blobs y archivos.
* Mejoras de accesibilidad.
* Las notas de la versión de la siguiente versión se pueden ver desde la notificación de actualización. También puede ver las notas de la versión actual desde el menú Ayuda.

#### <a name="fixes"></a>Correcciones

* Problema corregido: El número de versión ahora se muestra correctamente en el Panel de control de Windows.
* Problema corregido: La búsqueda ya no está limitada a 50 000 nodos.
* Problema corregido: La carga a un recurso compartido de archivos se alargaba infinitamente si el directorio de destino no existía anteriormente.
* Problema corregido: Estabilidad mejorada para cargas y descargas largas.

#### <a name="known-issues"></a>Problemas conocidos

* Cuando se amplía o reduce, el nivel de zoom puede restablecerse momentáneamente al nivel predeterminado.
* Acceso rápido solo funciona con elementos basados en la suscripción. En esta versión no se admiten recursos locales o recursos adjuntados a través de la clave o token de SAS.
* Es posible que Acceso rápido tarde unos segundos en desplazarse hasta el recurso de destino, en función del número de recursos que tenga.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.

16/12/2016
### <a name="version-087"></a>Versión 0.8.7

<iframe width="560" height="315" src="https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nuevo

* Puede elegir cómo resolver conflictos al principio de una actualización, descarga o sesión de copia en la ventana Actividades.
* Mantenga el puntero sobre una pestaña para ver la ruta de acceso completa del recurso de almacenamiento.
* Al hacer clic en una pestaña, se sincroniza con su ubicación en el panel de navegación izquierdo.

#### <a name="fixes"></a>Correcciones

* Problema corregido: El Explorador de Storage es ahora una aplicación de confianza en Mac.
* Problema corregido: Ubuntu 14.04 se admite de nuevo.
* Problema corregido: En ocasiones, la interfaz de usuario para agregar una cuenta parpadea al cargar las suscripciones.
* Problema corregido: En ocasiones, no todos los recursos de almacenamiento se muestran en el panel de navegación izquierdo.
* Problema corregido: El panel de acciones a veces muestra acciones vacías.
* Problema corregido: Ahora se conserva el tamaño de la ventana de la última sesión que se cerró.
* Problema corregido: Se pueden abrir varias pestañas para el mismo recurso mediante el menú contextual.

#### <a name="known-issues"></a>Problemas conocidos

* Acceso rápido solo funciona con elementos basados en la suscripción. En esta versión no se admiten recursos locales o recursos adjuntados a través de la clave o token de SAS.
* Es posible que Acceso rápido tarde unos segundos en desplazarse hasta el recurso de destino, en función del número de recursos que tenga.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado o se pueden producir excepciones no controladas.
* Por primera vez con el Explorador de Storage en macOS, es posible que aparezcan varias solicitudes que piden permiso del usuario para acceder a las llaves. Se recomienda seleccionar Permitir siempre para que la solicitud no vuelva a aparecer más.

18/11/2016
### <a name="version-086"></a>Versión 0.8.6

#### <a name="new"></a>Nuevo

* Ya puede anclar los servicios más utilizados en Acceso rápido para facilitar la navegación.
* Ya puede abrir varios editores en diferentes pestañas. Haga un solo clic para abrir una pestaña temporal y doble clic para abrir una pestaña permanente. También puede hacer clic en la pestaña temporal para que se convierta en una pestaña permanente.
* Hemos realizado mejoras notables de rendimiento y estabilidad para cargas y descargas, especialmente para archivos de gran tamaño en máquinas rápidas
* Las carpetas vacías "virtuales" ya se pueden crear en contenedores de blobs.
* Hemos vuelto a incorporar la búsqueda en ámbito con la nueva y mejorada búsqueda de subcadenas, por lo que ahora tiene dos opciones para realizar búsquedas:
    * Búsqueda global: solo tiene que indicar un término de búsqueda en el cuadro de texto de búsqueda.
    * Búsqueda de ámbito: haga clic en el icono de lupa al lado de un nodo; después, agregue un término de búsqueda al final de la ruta o haga clic con el botón derecho y seleccione “Buscar desde aquí”.
* Hemos agregado varios temas: claro (valor predeterminado), oscuro, negro en alto contraste y blanco en alto contraste. Vaya a Editar -&gt; Temas para cambiar las preferencias de temas.
* Puede modificar las propiedades de blobs y archivos.
* Ahora se admiten mensajes de cola cifrados (Base64) y sin cifrar.
* En Linux, ahora es necesario usar un sistema operativo de 64 bits. En esta versión solo se admite Ubuntu 16.04.1 LTS de 64 bits.
* ¡Hemos actualizado nuestro logotipo!

#### <a name="fixes"></a>Correcciones

* Problema corregido: Problemas de inmovilización de pantalla
* Problema corregido: Mayor seguridad
* Problema corregido: A veces, podrían aparecer cuentas conectadas duplicadas.
* Problema corregido: Un blob con un tipo de contenido sin definir podría generar una excepción.
* Problema corregido: No se podía abrir el Panel de consulta en una tabla vacía.
* Problema corregido: Varios errores en la búsqueda.
* Problema corregido: Aumento del número de recursos que se cargan de 50 a 100 al hacer clic en “Cargar más”.
* Problema corregido: En la primera ejecución, si ha iniciado sesión con una cuenta, ahora se seleccionan todas las suscripciones para esa cuenta de manera predeterminada.

#### <a name="known-issues"></a>Problemas conocidos

* Esta versión del Explorador de Storage no se ejecuta en Ubuntu 14.04.
* Para abrir varias pestañas para el mismo recurso, no haga clic varias veces en el mismo recurso. Haga clic en otro recurso y, después, vuelva y haga clic en el recurso original para volver a abrirlo en otra pestaña.
* Acceso rápido solo funciona con elementos basados en la suscripción. En esta versión no se admiten recursos locales o recursos adjuntados a través de la clave o token de SAS.
* Es posible que Acceso rápido tarde unos segundos en desplazarse hasta el recurso de destino, en función del número de recursos que tenga.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado o se pueden producir excepciones no controladas.

03/10/2016
### <a name="version-085"></a>Versión 0.8.5

#### <a name="new"></a>Nuevo

* Ahora puede usar claves SAS generadas por el Portal para conectarse a cuentas de almacenamiento y recursos

#### <a name="fixes"></a>Correcciones

* Problema corregido: A veces, la condición de carrera durante las búsquedas provocaba que los nodos no se pudiesen expandir.
* Problema corregido: “Usar HTTP” no funciona al conectarse a cuentas de almacenamiento con un nombre de cuenta y una clave.
* Problema corregido: Las claves SAS (especialmente las generadas en el Portal) devuelven un error de “barra oblicua final”.
* Problema corregido: Problemas al importar tablas.
    * A veces se revertían la clave de fila y la clave de partición
    * No se puede leer las claves de partición “null”.

#### <a name="known-issues"></a>Problemas conocidos

* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado.
* Azure Stack no admite Files actualmente por lo que, al intentar expandir Files, se produce un error.

12/09/2016
### <a name="version-084"></a>Versión 0.8.4

<iframe width="560" height="315" src="https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nuevo

* Genere vínculos directos a cuentas de almacenamiento, contenedores, colas, tablas o recursos compartidos de archivos para compartir y acceder fácilmente a sus recursos. Es compatible con Windows y Mac OS.
* Busque contenedores de blobs, tablas, colas, recursos compartidos de archivos o cuentas de almacenamiento en el cuadro de búsqueda.
* Ahora puede agrupar las cláusulas en el generador de consultas de tablas.
* Cambie el nombre y copie y pegue contenedores de blobs, recursos compartidos de archivos, tablas, blobs, carpetas de blobs, archivos y directorios desde cuentas conectadas mediante SAS y contenedores.
* Al cambiar el nombre y copiar contenedores de blobs y recursos compartidos de archivos, ahora se conservan las propiedades y los metadatos.

#### <a name="fixes"></a>Correcciones

* Problema corregido: No se pueden editar las entidades de tablas si contienen propiedades binarias o booleanas.

#### <a name="known-issues"></a>Problemas conocidos

* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado.

03/08/2016
### <a name="version-083"></a>Versión 0.8.3

<iframe width="560" height="315" src="https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nuevo

* Cambie el nombre de contenedores, tablas y recursos compartidos de archivos.
* Experiencia mejorada con el generador de consultas.
* Posibilidad de guardar y cargar consultas.
* Vínculos directos a cuentas de almacenamiento o contenedores, colas, tablas o recursos compartidos de archivos para compartir y acceder fácilmente a los recursos (solo disponible para Windows, macOS se admitirá pronto).
* Capacidad para administrar y configurar reglas de CORS

#### <a name="fixes"></a>Correcciones

* Problema corregido: Las cuentas de Microsoft requieren que vuelva a autenticarse en períodos de 8 a 12 horas.

#### <a name="known-issues"></a>Problemas conocidos

* A veces, puede que parezca que la IU se bloquea. Este problema se resuelve maximizando la ventana.
* Puede que la instalación en macOS requiera permisos elevados.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los recursos compartidos, contenedores de blobs y tablas, no se conservan los metadatos ni otras propiedades del contenedor, como la cuota de uso compartido de archivos, el nivel de acceso público ni las directivas de acceso.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* No se puede copiar o cambiar de nombre recursos en cuentas conectadas mediante SAS.

07/07/2016
### <a name="version-082"></a>Versión 0.8.2

<iframe width="560" height="315" src="https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nuevo

* Las cuentas de almacenamiento se agrupan por suscripciones; el almacenamiento de desarrollo y los recursos conectados mediante clave o SAS se muestran en el nodo (Local y Adjuntos).
* Cierre la sesión de las cuentas en el panel “Configuración de la cuenta de Azure”.
* Configure el proxy para habilitar y administrar el inicio de sesión.
* Cree y rompa concesiones de blobs.
* Abra contenedores de blobs, colas, tablas y archivos con un solo clic.

#### <a name="fixes"></a>Correcciones

* Problema corregido: Los mensajes de cola insertados con bibliotecas de Java o .NET no se descodifican correctamente desde Base64.
* Problema corregido: Las tablas de $metrics no se muestran en las cuentas de Blob Storage.
* Problema corregido: El nodo de tablas no funciona para el almacenamiento local (desarrollo).

#### <a name="known-issues"></a>Problemas conocidos

* Puede que la instalación en macOS requiera permisos elevados.

15/06/2016
### <a name="version-080"></a>Versión 0.8.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/ycfQhKztSIY?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1" frameborder="0" allowfullscreen></iframe>

#### <a name="new"></a>Nuevo

* Compatibilidad con recursos compartidos de archivos: visualización, carga, descarga, copia de archivos y directorios, URI de SAS (creación y conexión).
* Mejoras de la experiencia del usuario para conectarse a Storage con URI de SAS o claves de cuentas.
* Exportación de resultados de consulta de tabla.
* Reordenación y personalización de columnas de tabla.
* Consulta de contenedores de blob $logs y tablas de $metrics para cuentas de almacenamiento con las métricas habilitadas.
* Comportamiento de importación y exportación mejorado. Ahora incluye el tipo de valor de propiedad.

#### <a name="fixes"></a>Correcciones

* Problema corregido: Al cargar o descargar blobs grandes, se puede producir una carga o descarga incompleta.
* Problema corregido: Al editar, agregar o importar una entidad con un valor de cadena numérico (“1”) se convertirá en doble.
* Problema corregido: No se puede expandir el nodo de tabla en el entorno de desarrollo local.

#### <a name="known-issues"></a>Problemas conocidos

* Las tablas de $metrics no son visibles para cuentas de Blob Storage.
* Los mensajes de cola que se agregan mediante programación pueden no mostrarse correctamente si los mensajes se codifican con Base 64.

17/05/2016
### <a name="version-07201605090"></a>Versión 0.7.20160509.0

#### <a name="new"></a>Nuevo

* Mejor control de errores para bloqueos de la aplicación.

#### <a name="fixes"></a>Correcciones

* Se ha corregido un error en el que los mensajes de la barra de información a veces no se mostraban cuando se requerían credenciales de inicio de sesión.

#### <a name="known-issues"></a>Problemas conocidos

* Tablas: Al agregar, editar o importar una entidad con una propiedad con un valor numérico ambiguo, como “1” o “1.0” y cuando el usuario intenta enviarla como `Edm.String`, el valor volverá a través de la API del cliente como un Edm.Double.

31/03/2016

### <a name="version-07201603250"></a>Versión 0.7.20160325.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/imbgBRHX65A?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>Nuevo

* Compatibilidad con tablas: visualización, realización de consultas, exportación, importación y operaciones CRUD para entidades.
* Compatibilidad con colas: visualización, adición y eliminación de mensajes de la cola.
* Generación de URI de SAS para cuentas de almacenamiento.
* Conexión a cuentas de almacenamiento con URI de SAS.
* Notificaciones de actualizaciones para actualizaciones futuras a Explorador de Storage.
* Apariencia actualizada.

#### <a name="fixes"></a>Correcciones

* Mejoras de rendimiento y confiabilidad.

### <a name="known-issues-amp-mitigations"></a>Problemas conocidos y mitigaciones

* La descarga de archivos de blob grandes no funciona correctamente. Le recomendamos que use AzCopy mientras no se soluciona este problema.
* Las credenciales de cuentas no se recuperan ni se almacenan en caché si la carpeta de inicio no se encuentra o no se puede escribir en ella.
* Al agregar, editar o importar una entidad con una propiedad con un valor numérico ambiguo, como “1” o “1.0” y cuando el usuario intenta enviarla como `Edm.String`, el valor volverá a través de la API del cliente como un Edm.Double.
* Al importar archivos CSV con registros de varias líneas, es posible que los datos se corten o se desordenen.

03/02/2016

### <a name="version-07201601291"></a>Versión 0.7.20160129.1

#### <a name="fixes"></a>Correcciones

* Rendimiento general mejorado al cargar, descargar y copiar blobs.

14/01/2016

### <a name="version-07201601050"></a>Versión 0.7.20160105.0

#### <a name="new"></a>Nuevo

* Compatibilidad con Linux (características de paridad para OSX)
* Agregue contenedores de blobs con clave de Firmas de acceso compartido (SAS).
* Agregue cuentas de almacenamiento para Azure China.
* Agregue cuentas de almacenamiento con puntos de conexión personalizados.
* Abra y vea blobs de imagen y texto de contenido.
* Vea y edite propiedades y metadatos de blob.

#### <a name="fixes"></a>Correcciones

* Problema corregido: La carga o descarga de un gran número de blobs (más de 500) puede provocar en ocasiones que la aplicación muestre una pantalla en blanco.
* Problema corregido: Al establecer el nivel de acceso público en un contenedor de blobs, el valor nuevo no se actualiza hasta que vuelva a establecer el foco en el contenedor. Además, el diálogo siempre tiene como valor predeterminado “Sin acceso público” y no el valor real actual.
* Mejor accesibilidad de teclado general y compatibilidad con IU.
* El historial de enlaces se ajusta cuando es demasiado largo con espacios en blanco.
* El cuadro de diálogo de SAS admite la validación de entradas.
* El almacenamiento local sigue estando disponible incluso aunque las credenciales de usuario hayan expirado.
* Cuando se elimina un contenedor de blobs abierto, el explorador de blobs de la derecha se cierra.

#### <a name="known-issues"></a>Problemas conocidos

* La instalación de Linux requiere que se actualice la versión de gcc. A continuación puede ver los pasos para actualizarla:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

18/11/2015
### <a name="version-07201511160"></a>Versión 0.7.20151116.0

#### <a name="new"></a>Nuevo

* Versiones de Windows y macOS.
* Inicie sesión para ver las cuentas de almacenamiento: use su cuenta de organización, cuenta Microsoft, 2FA, etc.
* Almacenamiento de desarrollo local (use un emulador de almacenamiento, solo para Windows).
* Compatibilidad de Azure Resource Manager y recursos clásicos.
* Cree y elimine blobs, colas o tablas.
* Busque blobs, colas o tablas específicos.
* Explore el contenido de los contenedores de blobs.
* Vea y navegue por directorios.
* Cargue, descargue y elimine blobs y carpetas.
* Vea y edite propiedades y metadatos de blob.
* Genere claves SAS.
* Administre y cree directivas de acceso a almacenamiento (SAP).
* Busque blobs por prefijo.
* Arrastre y coloque archivos para cargarlos o descargarlos.

#### <a name="known-issues"></a>Problemas conocidos

* Al establecer el nivel de acceso público en un contenedor de blobs, el valor nuevo no se actualiza hasta que vuelva a establecer el foco en el contenedor.
* Al abrir el cuadro de diálogo para establecer el nivel de acceso público, siempre se muestra “Sin acceso público” como valor predeterminado y no el valor real actual.
* No se puede cambiar el nombre de los blobs descargados.
* Las entradas del registro de actividades a veces se bloquean en un estado de progreso cuando se produce un error y el error no se muestra.
* A veces se bloquea o se pone en blanco al intentar cargar o descargar un gran número de blobs.
* A veces no se puede cancelar una operación de copia.
* Durante la creación de un contenedor (blob, cola o tabla), si indica un nombre no válido y continúa creando otro en otro tipo de contenedor, no podrá establecer el foco en el tipo nuevo.
* No se puede crear una carpeta nueva ni cambiarle el nombre.




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md

---
title: "Introducción al control de acceso en Data Lake Store | Microsoft Docs"
description: "Descripción de cómo funciona el control de acceso en Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: ec0d1fa9c422dbe4958c5d5f0b7a6e093aeb32da
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="access-control-in-azure-data-lake-store"></a>Control de acceso en Azure Data Lake Store

Azure Data Lake Store implementa un modelo de control de acceso que se deriva de HDFS que, a su vez, se deriva del modelo de control de acceso POSIX. Este artículo resume los datos básicos del modelo de control de acceso de Data Lake Store. Para más información sobre el modelo de control de acceso HDFS, consulte [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)(Guía de permisos de HDFS).

## <a name="access-control-lists-on-files-and-folders"></a>Listas de control de acceso en archivos y carpetas

Hay dos tipos de listas de control de acceso (ACL): **ACL de acceso** y **ACL predeterminadas**.

* **ACL de acceso**: controlan el acceso a un objeto. Tanto los archivos como las carpetas tienen ACL de acceso.

* **ACL predeterminadas**: una "plantilla" de las ACL asociadas a una carpeta que determinan las ACL de acceso de todos los elementos secundarios que se crean en dicha carpeta. Los archivos no tienen ACL predeterminadas.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Tanto las ACL de acceso como las ACL predeterminadas tienen la misma estructura.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> El cambio de la ACL predeterminada en un elemento primario no afecta a la ACL de acceso o a la ACL predeterminada de los elementos secundarios que ya existen.
>
>

## <a name="users-and-identities"></a>Usuarios e identidades

Todos los archivos y carpetas tienen permisos distintos para estas identidades:

* El usuario propietario del archivo
* El grupo propietario
* Usuarios designados
* Grupos designados
* Los restantes usuarios

Las identidades de usuarios y grupos son las identidades de Azure Active Directory (Azure AD). Por tanto, a menos que se indique lo contrario, un "usuario", en el contexto de Data Lake Store, puede referirse a un usuario de Azure AD o a un grupo de seguridad de Azure AD.

## <a name="permissions"></a>Permisos

Los permisos de un objeto del sistema de archivos son de **lectura**, **escritura** y **ejecución**, y se pueden usar en archivos y carpetas, como se muestra en la tabla siguiente:

|            |    Archivo     |   Carpeta |
|------------|-------------|----------|
| **Lectura (R)** | Puede leer el contenido de un archivo | Requiere permisos de **lectura** y **ejecución** para enumerar el contenido de la carpeta.|
| **Escritura (W)** | Puede escribir o anexar a un archivo | Requiere permisos de **lectura** y **ejecución** para crear elementos secundarios en una carpeta. |
| **Ejecución (X)** | No significa nada en el contexto de Data Lake Store | Se requiere para atravesar los elementos secundarios de una carpeta. |

### <a name="short-forms-for-permissions"></a>Formas abreviadas de los permisos

**RWX** se usa para indicar **Lectura + Escritura + Ejecución**. Existe un formato numérico más condensado en el que **Lectura = 4**, **Escritura = 2** y **Ejecución = 1**, y su suma representa los permisos. A continuación se muestran algunos ejemplos.

| Formato numérico | Formato abreviado |      Qué significa     |
|--------------|------------|------------------------|
| 7            | RWX        | Lectura + Escritura + Ejecución |
| 5            | R-X        | Lectura + ejecución         |
| 4            | R--        | Lectura                   |
| 0            | ---        | Sin permisos         |


### <a name="permissions-do-not-inherit"></a>Los permisos no se heredan

En el modelo de estilo de POSIX usado por Data Lake Store, los permisos de un elemento se almacenan en el propio elemento. En otras palabras, los permisos de un elemento no se pueden heredar de los elementos primarios.

## <a name="common-scenarios-related-to-permissions"></a>Escenarios comunes relacionados con los permisos

A continuación, hay algunos escenarios comunes para ayudarle a entender qué permisos se necesitan para realizar ciertas operaciones en una cuenta de Data Lake Store.

### <a name="permissions-needed-to-read-a-file"></a>Permisos necesitados para leer un archivo

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Para el archivo que se va a leer: el autor de la llamada necesita permisos de **lectura**.
* Para todas las carpetas de la estructura de carpetas que contienen el archivo: el autor de la llamada necesita permisos de **ejecución**.

### <a name="permissions-needed-to-append-to-a-file"></a>Permisos necesarios para anexar a un archivo

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Para el archivo al que se va a anexar: el autor de la llamada necesita permisos de **escritura**.
* Para todas las carpetas que contienen el archivo: el autor de la llamada necesita permisos de **ejecución**.

### <a name="permissions-needed-to-delete-a-file"></a>Permisos necesitados para eliminar un archivo

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Para la carpeta primaria: el autor de la llamada necesita permisos de **escritura y ejecución**.
* Para todas las demás carpetas en la ruta de acceso del archivo: el autor de la llamada necesita permisos de **ejecución**.



> [!NOTE]
> Para eliminar un archivo no es preciso tener permisos de escritura en él, siempre y cuando se cumplan las dos condiciones anteriores.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Permisos necesarios para enumerar una carpeta

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Para la carpeta que se enumera: el autor de la llamada necesita permisos de **lectura y ejecución**.
* Para todas las carpetas antecesoras: el autor de la llamada necesita permisos de **ejecución**.

## <a name="viewing-permissions-in-the-azure-portal"></a>Visualización de permisos en el portal de Azure

En la hoja **Data Explorer** (Explorador de datos) de la cuenta de Data Lake Store, haga clic en **Access** (Acceso) para ver las ACL de un archivo o una carpeta. Haga clic en **Access** (Acceso) para ver las ACL de la carpeta **catalog** de la cuenta **mydatastore**.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

En esta hoja, en la sección superior se muestra una descripción general de los permisos que tiene. (En la captura de pantalla, el usuario es Bob). Después, se muestran los permisos de acceso. Después, en la hoja **Acceso**, haga clic en **Vista simple**  para usar la vista más simple.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Haga clic en **Advanced View** (Vista avanzada) para ver la vista más avanzada, donde se muestran los conceptos de las ACL predeterminadas, la máscara y el superusuario.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>El superusuario

Un superusuario es el usuario con más derechos en Data Lake Store. Un superusuario:

* Tiene permisos de lectura, escritura y ejecución (RWX) en **todos** los archivos y carpetas.
* Puede cambiar los permisos de cualquier archivo o carpeta.
* Puede cambiar el usuario propietario o el grupo propietario de cualquier archivo o carpeta.

En Azure, una cuenta de Data Lake Store tiene varios roles de Azure, incluidos:

* Propietarios
* Colaboradores
* Lectores

Todos los miembros del rol **Propietarios** de una cuenta de Data Lake Store son automáticamente superusuarios de dicha cuenta. Para más información, consulte el artículo sobre el [control de acceso basado en roles](../active-directory/role-based-access-control-configure.md).
Si desea crear un rol de control de acceso basado en roles (RBAC) personalizado que tenga permisos de superusuario, debe tener los permisos siguientes:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>El usuario propietario

El usuario que creó el elemento es automáticamente el usuario propietario del elemento. Un usuario propietario puede:

* Cambiar los permisos de un archivo que le pertenece.
* Cambiar el grupo propietario de un archivo que le pertenece, siempre que el usuario propietario también sea miembro del grupo de destino.

> [!NOTE]
> El usuario propietario *no puede* cambiar el usuario propietario de otro archivo que le pertenece. Solo los superusuarios pueden cambiar el usuario propietario de un archivo o carpeta.
>
>

## <a name="the-owning-group"></a>El grupo propietario

En las ACL de POSIX, todos los usuarios están asociados a un "grupo principal". Por ejemplo, el usuario "alice" puede pertenecer al grupo "finance". Alice puede pertenecer a varios grupos, pero uno de ellos siempre se designa como su grupo principal. En POSIX, cuando Alice crea un archivo, el grupo propietario de dicho archivo se establece como su grupo principal, que en este caso es "finance".

Cuando se crea un nuevo elemento del sistema de archivos, Data Lake Store asigna un valor al grupo propietario.

* **Caso 1**: la carpeta raíz "/". Esta carpeta se crea cuando se crea una cuenta de Data Lake Store. En este caso, el grupo propietario se establece en el usuario que creó la cuenta.
* **Caso 2** (cada dos casos): cuando se crea un elemento, se copia el grupo propietario de la carpeta primaria.

El grupo propietario se puede cambiar por:
* Cualquier superusuario.
* El usuario propietario, si el usuario propietario también es miembro del grupo de destino.

## <a name="access-check-algorithm"></a>Algoritmo de comprobación de acceso

La siguiente ilustración representa el algoritmo de comprobación de acceso de las cuentas de Data Lake Store.

![Algoritmo de ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>La propiedad mask y los "permisos efectivos"

**mask** es un valor de RWX que se usa para limitar el acceso a los **usuarios designados**, al **grupo propietario** y a los **grupos designados** al aplicar el algoritmo de comprobación de acceso. Estos son los conceptos clave de la máscara.

* La propiedad mask crea "permisos efectivos". Es decir, modifica los permisos en el momento de la comprobación de acceso.
* El propietario del archivo y todos los superusuarios pueden editar directamente la propiedad mask.
* La propiedad mask puede quitar permisos para crear el permiso efectivo. La propiedad mask *no puede* agregar permisos al permiso efectivo.

Veamos algunos ejemplos. En el siguiente ejemplo, la propiedad mask se establece como **RWX**, lo que significa que mask no quita ningún permiso. Los permisos efectivos para el usuario designado, el grupo propietario y el grupo designado no se modifican durante la comprobación de acceso.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

En el ejemplo siguiente, mask se establece en **R-X**. Esto significa que **desactiva el permiso de escritura** para el **usuario designado**, el **grupo propietario** y el **grupo designado** en el momento de la comprobación de acceso.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Como referencia, aquí es donde la propiedad mask de un archivo o carpeta aparece en Azure Portal.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> En una nueva cuenta de Data Lake Store, el valor predeterminado de la propiedad mask de la ACL de acceso y de la ACL predeterminada de la carpeta raíz ("/") es RWX.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Permisos en los archivos y carpetas nuevos

Cuando se crea un nuevo archivo o carpeta en una carpeta existente, la ACL predeterminada de la carpeta primaria determina:

- La ACL predeterminada y la ACL de acceso de una carpeta secundaria.
- La ACL de acceso de un archivo secundario (los archivos no tienen ninguna ACL predeterminada).

### <a name="the-access-acl-of-a-child-file-or-folder"></a>La ACL de acceso de un archivo o carpeta secundarios.

Cuando se crea un archivo o una carpeta secundarios, la ACL predeterminada de la carpeta primaria se copia como ACL de acceso del archivo o la carpeta secundarios. Además, si **otro** usuario tiene permisos de RWX en la ACL predeterminada del elemento primario, se quita de la ACL de acceso del elemento secundario.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

En la mayoría de los escenarios, la información anterior es todo lo que necesita saber acerca de cómo se determina la ACL de acceso de un elemento secundario. Sin embargo, si conoce los sistemas POSIX y desea conocer en profundidad cómo se logra esta transformación, consulte la sección [Rol de Umask en la creación de la ACL de acceso para archivos y carpetas nuevos](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) de este mismo artículo.


### <a name="a-child-folders-default-acl"></a>ACL predeterminada de una carpeta secundaria.

Cuando se crea una carpeta secundaria en una carpeta primaria, la ACL predeterminada de la carpeta primaria se copia tal cual en la ACL predeterminada de la carpeta secundaria.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Temas avanzados para entender las ACL en Data Lake Store

A continuación encontrará algunos temas avanzados que le ayudarán a conocer cómo se determinan las ACL para los archivos o carpetas de Data Lake Store.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Rol de Umask en la creación de la ACL de acceso para archivos y carpetas nuevos

En un sistema compatible con POSIX, el concepto general es que umask es un valor de 9 bits en la carpeta primaria que se utiliza para transformar el permiso del **usuario propietario**, el **grupo propietario** y **otros** en la ACL de acceso de un archivo o carpeta secundarios nuevos. Los bits de umask identifican qué bits se desactivan en la ACL de acceso del elemento secundario. Por consiguiente, se usa para evitar selectivamente la propagación de permisos para el **usuario propietario**, el **grupo propietario** y **otros**.

En un sistema HDFS, umask suele ser una opción de configuración de todo el sitio que controlan los administradores. Data Lake Store usa una **umask para toda la cuenta** que no se puede cambiar. En la tabla siguiente se muestra la propiedad umask de Data Lake Store.

| Grupo de usuarios  | Configuración | Efecto en la ACL de acceso de un nuevo elemento secundario |
|------------ |---------|---------------------------------------|
| usuario propietario | ---     | Sin efecto                             |
| grupo propietario| ---     | Sin efecto                             |
| otro       | RWX     | Se elimina lectura + escritura + ejecución         |

En la siguiente ilustración se muestra esta umask, o máscara de usuario, en acción. El efecto neto es quitar **lectura + escritura + ejecución** para el usuario **otros**. Dado que la propiedad umask no especificó los bits del **usuario propietario** y del **grupo propietario**, dichos permisos no se transforman.

![ACL de Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>El bit persistente

El bit persistente es una característica más avanzada de un sistema de archivos POSIX. En el contexto de Data Lake Store, es improbable que se necesite el bit persistente.

En la tabla siguiente se muestra cómo funciona el bit persistente en Data Lake Store.

| Grupo de usuarios         | Archivo    | Carpeta |
|--------------------|---------|-------------------------|
| Sticky Bits **DESACTIVADO** | Sin efecto   | Sin efecto.           |
| Sticky Bits **ACTIVADO**  | Sin efecto   | Impide que alguien elimine o cambie el nombre de un elemento secundario, excepto los **superusuarios** y el **usuario propietario** del mismo.               |

El bit persistente no se muestra en Azure Portal.

## <a name="common-questions-about-acls-in-data-lake-store"></a>Preguntas frecuentes sobre las ACL en Data Lake Store

Estas son algunas preguntas que surgen a menudo con respecto a las ACL en Data Lake Store.

### <a name="do-i-have-to-enable-support-for-acls"></a>¿Es preciso habilitar la compatibilidad con las ACL?

Nº El control de acceso mediante las ACL siempre está activado en las cuentas de Data Lake Store.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>¿Qué permisos son necesarios para eliminar de forma recursiva una carpeta y su contenido?

* La carpeta primaria debe tener permisos de **escritura y ejecución**.
* Tanto la carpeta que se va a eliminar como todas las carpetas dentro de ella, requieren permisos de **lectura, escritura y ejecución**.

> [!NOTE]
> No necesita permisos de escritura para eliminar archivos en carpetas. Además, la carpeta raíz "/" **nunca** se puede eliminar.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>¿Quién es el propietario de un archivo o carpeta?

El creador de un archivo o carpeta se convierte en el propietario.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>¿Qué grupo se establece como grupo propietario de un archivo o carpeta al crearlo?

El grupo propietario se copia de la carpeta primaria en la que se crea el archivo o carpeta.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Soy el usuario propietario de un archivo, pero no tengo los permisos de RWX que necesito. ¿Qué puedo hacer?

El usuario propietario puede cambiar los permisos del archivo y concederse los permisos de RWX que necesite.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Cuando examino las ACL en Azure Portal veo los nombres de usuario, pero cuando lo hago mediante API veo GUID, ¿por qué?

Las entradas de las ACL se almacenan como GUID que corresponden a los usuarios en Azure AD. Las API devuelven los GUID tal cual. Azure Portal intenta que las ACL sean más fáciles de usar convirtiendo los GUID en nombres descriptivos siempre que sea posible.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>¿Por qué a veces veo GUID en las ACL al usar Azure Portal?

Cuando el usuario ya no existe en Azure AD, se mostrará un GUID. Normalmente esto sucede cuando el usuario ha dejado la empresa o si se ha eliminado su cuenta de Azure AD.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>¿Admite Data Lake Store la herencia de ACL?

Nº

### <a name="what-is-the-difference-between-mask-and-umask"></a>¿Cuál es la diferencia entre mask y umask?

| mask | umask|
|------|------|
| La propiedad **mask** está disponible en todos los archivos y carpetas. | **umask** es una propiedad de la cuenta de Data Lake Store. Por lo tanto, en Data Lake Store solo hay una propiedad umask.    |
| La propiedad mask de un archivo o carpeta puede modificarla el usuario propietario o grupo propietario de un archivo, o bien un superusuario. | Ningún usuario, ni siquiera un superusuario, puede modificar la propiedad umask. Es un valor constante y que no puede cambiarse.|
| La propiedad mask se usa durante el algoritmo de comprobación de acceso en tiempo de ejecución para determinar si un usuario tiene el derecho necesario para realizar una operación en un archivo o carpeta. El rol de la propiedad mask es crear "permisos efectivos" en el momento de la comprobación de acceso. | umask no se utiliza durante la comprobación de acceso. umask se utiliza para determinar la ACL de acceso de los nuevos elementos secundarios de una carpeta. |
| mask es un valor RWX de 3 bits que se aplica al usuario designado, grupo designado y usuario propietario en el momento de la comprobación de acceso.| umask es un valor de 9 bits que se aplica al usuario propietario, al grupo propietario y **otros** de un nuevo elemento secundario.|

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>¿Dónde puedo obtener más información sobre el modelo de control de acceso POSIX?

* [POSIX Access Control Lists on Linux](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html) (Listas de control de acceso de POSIX en Linux)

* [HDFS Permission Guide](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Guía de permisos de HDFS)

* [POSIX FAQ (PREGUNTAS MÁS FRECUENTES SOBRE POSIX)](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)

* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)

* [ACL de POSIX en Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)

* [ACL: Using Access Control Lists on Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/) (ACL: uso de listas de control de acceso en Linux)

## <a name="see-also"></a>Otras referencias

* [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)

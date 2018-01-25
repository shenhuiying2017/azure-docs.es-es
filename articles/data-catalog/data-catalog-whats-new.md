---
title: Novedades de Azure Data Catalog | Microsoft Docs
description: "En este artículo se proporciona una introducción a las nuevas funciones agregadas a Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 9fb7814a8412200f6d31cfb9dcaee4663d7cea97
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="whats-new-in-azure-data-catalog"></a>Novedades en Azure Data Catalog
De forma periódica se publican actualizaciones de **Azure Data Catalog**. No todas las versiones incluyen nuevas características de cara al usuario, ya que algunas se centran en las funcionalidades del servicio back-end. En esta página se resaltan las nuevas funcionalidades de cara al usuario que se han agregado al servicio Azure Data Catalog.

## <a name="whats-new-for-november-2017"></a>Novedades de noviembre de 2017 
A partir de noviembre de 2017 se han ido agregando las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad para vincular directamente los términos del glosario empresarial específico en el portal de Data Catalog. Los usuarios pueden copiar vínculos desde el glosario empresarial e insertarlos en documentos, correos electrónicos, informes u otras ubicaciones, para así vincular todos estos elementos directamente con la definición del término del glosario.
* Compatibilidad con las entidades de servicio de Azure Active Directory. Los administradores de Data Catalog pueden dar permiso a las aplicaciones cliente que usan las entidades de servicio para que puedan obtener acceso al catálogo; asimismo, pueden otorgar a esas aplicaciones permisos específicos del mismo modo que pueden otorgar permisos a usuarios y grupos de seguridad. Para obtener más información, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).
* Compatibilidad con la autenticación de Azure Active Directory al conectarse a los orígenes de datos de Azure SQL Database y a Azure SQL Data Warehouse mediante la herramienta de registro de orígenes de datos de Data Catalog. Para obtener más información, consulte [Uso de la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>Novedades de septiembre de 2017 
A partir de septiembre de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad con la extracción de los metadatos de las relaciones de combinación desde los orígenes de datos DB2 cuando se registran tablas relacionadas mediante la herramienta de registro de origen de datos.
* Compatibilidad con el registro de los orígenes de datos de MongoDB versión 3.4 utilizando la herramienta de registro de orígenes de datos.
* Compatibilidad con la eliminación de todos los metadatos de los objetos contenidos en una sola operación al quitar una base de datos u otro contenedor desde Data Catalog.
* Compatibilidad con la visualizacion de hasta 1000 etiquetas, términos del glosario empresarial u otras facetas de búsqueda, al refinar una búsqueda en el portal de Data Catalog.


## <a name="whats-new-for-august-2017"></a>Novedades de agosto de 2017 
A partir de agosto de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:

*   Hay disponible un ejemplo de desarrollador nuevo para crear y administrar metadatos de relación mediante la API de REST de Data Catalog. El ejemplo *Import relationship information into Data Catalog* (Importación de información de relación en Data Catalog) está disponible en la [página de ejemplos de código de Data Catalog](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Compatibilidad para extraer metadatos de relación de combinación desde orígenes de datos Teradata cuando se registran tablas relacionadas mediante la herramienta de registro de origen de datos.
* Compatibilidad con objetos de función con valores de tabla (TVF) de SQL Server cuando se registran orígenes de datos de SQL Server mediante la herramienta de registro de origen de datos.
* Varias actualizaciones y refinamientos para aumentar el rendimiento y la usabilidad del portal de Data Catalog.

## <a name="whats-new-for-july-2017"></a>Novedades de julio de 2017 
A partir de julio de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Compatibilidad para un control más pormenorizado sobre las operaciones permitidas de metadatos, entre las que se incluyen:
    - Los administradores de catálogo pueden restringir la capacidad de los usuarios para contribuir con etiquetas y metadatos relacionados al catálogo, habilitando el acceso de solo lectura al catálogo.
    - Los administradores de catálogo pueden restringir la capacidad de los usuarios de registrar orígenes de datos nuevos en el catálogo.
    - Los administradores de catálogo pueden restringir la capacidad de los usuarios de asumir la propiedad de los metadatos del recurso de datos en el catálogo.
    - Es posible conceder permisos a usuarios y grupos de seguridad de Azure Active Directory para facilitar la administración de los permisos.
* Compatibilidad con relaciones entre recursos de datos registrados y detección de recursos de datos relacionados en el portal de Data Catalog, incluidos:
    - Extracción de metadatos de relación desde orígenes de datos de SQL Server (incluido Azure SQL Database), Oracle y MySQL cuando se usa la herramienta de registro de origen de datos de Data Catalog.
    - Detección de recursos de datos relacionados cuando se consultan metadatos de recursos en el portal de Data Catalog.
    - Operaciones para definir, detectar y administrar relaciones entre recursos de datos mediante la API de REST de Data Catalog.

Para detalles adicionales sobre la administración de permisos en Data Catalog, consulte [Acceso seguro al catálogo de datos y a los recursos de datos](data-catalog-how-to-secure-catalog.md).
Para detalles adicionales sobre las relaciones en Data Catalog, consulte [Visualización de recursos de datos relacionados en Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Novedades de junio de 2017 
A partir de junio de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Compatibilidad con los orígenes de datos de Sybase, Apache Cassandra y MongoDB. Ahora los usuarios pueden registrar y detectar tablas y bases de datos de Cassandra y MongoDB, así como bases de datos, tablas y vistas de Sybase.

> [!NOTE]
> Al registrar tablas de MongoDB y Cassandra que contienen columnas con tipos de datos complejos, como registros y matrices, estas columnas no se incluirán en los metadatos que se agregan a Data Catalog.

## <a name="whats-new-for-may-2017"></a>Novedades de mayo de 2017 
A partir de mayo de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Hay disponible una nueva muestra para desarrolladores para la importación masiva de términos del glosario empresarial. La muestra de la importación masiva del glosario está disponible en la página [Muestras para desarrolladores del Catálogo de datos](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Compatibilidad con la edición de la información de conexión de ODBC en el portal de Azure Data Catalog. Los propietarios de recursos de datos y los administradores de Data Catalog ahora pueden editar la información de conexión de los orígenes de datos ODBC registrados sin tener que volver a registrar los orígenes de datos.
*   Compatibilidad con las direcciones URL interactivas en las definiciones y las descripciones del glosario empresarial. Cuando se incluyen direcciones URL en las propiedades de descripción y definición de términos del glosario empresarial, las direcciones URL se mostrarán como hipervínculos en el portal de Data Catalog.
*   Compatibilidad para mostrar nombres de expertos, además de las direcciones de correo electrónico de expertos. Al visualizar expertos en las propiedades de un recurso de datos en el portal de Data Catalog, se mostrará el nombre completo del experto de Azure Active Directory en la información sobre herramientas.

## <a name="whats-new-for-april-2017"></a>Novedades de abril de 2017 
A partir de abril de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Compatibilidad con los orígenes de datos ODBC. Los usuarios ahora pueden registrar y detectar bases de datos, tablas y vistas de ODBC con la herramienta de registro de orígenes de datos de Data Catalog.

## <a name="whats-new-for-march-2017"></a>Novedades de marzo de 2017 
A partir de marzo de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Compatibilidad con el uso de grupos de seguridad de AAD para los administradores de Data Catalog.
*   Azure Data Catalog ahora está disponible en una nueva región de Azure. Los clientes pueden aprovisionar Azure Data Catalog en la región central de EE. UU, además del este de EE. UU., el oeste de EE. UU., Europa Occidental, este de Australia, Europa del Norte y Sudeste Asiático. Para más información, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Novedades de febrero de 2017 
A partir de febrero de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Compatibilidad con la configuración avanzada de la herramienta de registro de orígenes de datos de Data Catalog. Los usuarios pueden especificar valores de tiempo de espera de comando al registrar orígenes de datos de gran tamaño.
*   Compatibilidad con los orígenes de datos de FTP y PostgreSQL. Los usuarios ahora pueden registrar y detectar archivos y directorios de FTP, así como bases de datos, tablas y vistas de PostgreSQL.

## <a name="whats-new-for-january-2017"></a>Novedades de enero de 2017 
A partir de enero de 2017 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Azure Data Catalog ahora es compatible con [CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification).
*   Integración con [Obtener y transformar en Excel 2016 y Power Query para Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Los usuarios de Excel pueden compartir y detectar consultas mediante Azure Data Catalog desde Excel. Esta funcionalidad está disponible para los usuarios que disponen de licencias de Power BI Pro.

## <a name="whats-new-for-december-2016"></a>Novedades de diciembre de 2016
A partir de diciembre de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Azure Data Catalog ahora es compatible con [HIPAA](https://www.microsoft.com/trustcenter/Compliance/HIPAA) y con las [cláusulas según el modelo de la UE](https://www.microsoft.com/TrustCenter/Compliance/EU-Model-Clauses).
*   Compatibilidad con la edición de la información de conexión de los orígenes de datos. Los propietarios de recursos de datos y los administradores de Data Catalog ahora pueden editar la información de conexión de los orígenes de datos registrados sin tener que volver a registrar los orígenes de datos.
*   Compatibilidad con los orígenes de datos de Salesforce.com. Los usuarios ahora pueden registrar y detectar objetos de Salesforce.


## <a name="whats-new-for-november-2016"></a>Novedades de noviembre de 2016
A partir de noviembre de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:
*   Azure Data Catalog ahora es compatible con [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) y con [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018).
*   Compatibilidad con el registro manual de los orígenes de datos ODBC mediante el portal de Data Catalog y la API de REST.

## <a name="whats-new-for-september-2016"></a>Novedades de septiembre de 2016
A partir de septiembre de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad con orígenes de datos de IBM DB2. Ahora los usuarios pueden registrar y detectar tablas y vistas de IBM DB2.
* Compatibilidad con orígenes de datos de Azure Cosmos DB. Los usuarios ahora pueden registrar y detectar colecciones y bases de datos de Cosmos DB.
* Soporte para personalizar el nombre del catálogo en el portal de Data Catalog. Ahora los administradores de Catalog pueden proporcionar el texto que se muestra en el título del portal como, por ejemplo, el nombre de la organización.

## <a name="whats-new-for-august-2016"></a>Novedades de agosto de 2016
A partir de agosto de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Mejoras para el registro de orígenes de datos de SQL Server Master Data Services (MDS). Los usuarios ahora pueden incluir perfiles de datos y vistas previas al registrar entidades MDS mediante la herramienta de registro del origen de datos de Data Catalog.
* Compatibilidad con las búsquedas guardadas de la organización definidas por el administrador. Al guardar una búsqueda en el portal de Azure Data Catalog, los administradores de este ya pueden elegir guardar las búsquedas para uso personal o para el de todos los usuarios del catálogo. Las búsquedas guardadas de la organización se comparten con todos los usuarios del catálogo y pueden proporcionar puntos de partida estandarizados para la detección de orígenes de datos.
* Actualizaciones de la vista de propiedades en el portal de Data Catalog. Ahora, todas las propiedades de los recursos de datos se muestran y administran en un panel único y redimensionable que proporciona una experiencia más coherente e intuitiva.

## <a name="whats-new-for-july-2016"></a>Novedades de julio de 2016
A partir de julio de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad con orígenes de datos de SQL Server Master Data Services (MDS). Los usuarios ahora pueden registrar y detectar entidades y modelos MDS.
* Compatibilidad con los procedimientos almacenados de SQL Server. Los usuarios ahora pueden registrar y descubrir objetos de procedimientos almacenados en orígenes de datos de SQL Server.
* Compatibilidad con idiomas adicionales en el portal de Azure Data Catalog y en la herramienta de registro de origen de datos, para un total de 18 idiomas. La experiencia de usuario de Azure Data Catalog se traduce en función de las preferencias de idioma establecidas en Windows o en el explorador web.
* Actualizaciones y perfeccionamiento de la página principal del portal de Data Catalog, incluidas mejoras de rendimiento y una experiencia de usuario mejorada.

## <a name="whats-new-for-june-2016"></a>Novedades de junio de 2016
A partir de junio de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad para ajustar el tamaño de las columnas en la vista de lista durante la detección de recursos de datos en el portal de Data Catalog. Los usuarios ahora pueden cambiar el tamaño de las columnas individuales para leer más fácilmente metadatos de recursos de gran longitud como etiquetas y descripciones.
* Se ha agregado Power Query para Excel al menú "Abrir en" del portal de Data Catalog. Los usuarios ahora pueden abrir orígenes de datos admitidos en Excel 2016 o en Excel 2010 y Excel 2013 con el complemento [Power Query para Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) instalado.
* Compatibilidad con orígenes de datos de Azure Table Storage. Los usuarios ahora pueden registrar y detectar objetos de tabla en los orígenes de datos de Azure Storage.

## <a name="whats-new-for-may-2016"></a>Novedades de mayo de 2016
A partir de mayo de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Un glosario empresarial que permite a los administradores del Catálogo definir términos y jerarquías empresariales para crear un vocabulario empresarial común. Los usuarios pueden etiquetar recursos de datos registrados con términos del glosario para que resulte más fácil de detectar y comprender el contenido del catálogo. Para más información, consulte [Configuración del glosario empresarial para el etiquetado regulado](data-catalog-how-to-business-glossary.md)  
* Mejoras en el glosario empresarial del catálogo de datos que permite a los usuarios actualizar varios términos de glosario en una sola operación. Los usuarios pueden seleccionar varios términos para editar los campos siguientes:
  * Término primario: el usuario puede seleccionar un nuevo término primario y todos los términos seleccionados se actualizan para ser secundarios del término primario seleccionado. Si los términos seleccionados tienen el mismo elemento primario, este último se muestra en el cuadro de texto. De lo contrario, el campo de término primario está en blanco.   
  * Etiquetas y partes interesadas: los usuarios pueden agregar y quitar etiquetas y partes interesadas para varios términos de glosario utilizando el mismo modo que para el etiquetado de varios recursos de datos.

> [!NOTE]
> El glosario empresarial solo está disponible en la Edición Estándar de Azure Data Catalog. La Edición Gratis no proporciona funcionalidades para el etiquetado regulado o para un glosario empresarial.

## <a name="whats-new-for-march-2016"></a>Novedades de marzo de 2016
A partir de marzo de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Un punto de conexión de API de REST consolidado para acceder mediante programación a las funcionalidades de búsqueda y a las funcionalidades de administración de activos del catálogo del servicio Azure Data Catalog. El punto de conexión de API de búsqueda y el punto de conexión de API de catálogo están en desuso y dejarán de estar operativos el 21 de marzo de 2016. No se han producido cambios en la semántica de la API. Solo cambia el identificador URI del punto de conexión. Para más información, consulte la [referencia de la API de REST de Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267595.aspx). Para obtener ejemplos de la API, consulte [Muestras para desarrolladores de Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Novedades de febrero de 2016
A partir de febrero de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Una experiencia de selección de origen de datos recientemente rediseñada en la herramienta de registro de origen de datos de Azure Data Catalog. La herramienta de registro de origen de datos se ha actualizado para facilitarle la búsqueda y selección en los orígenes de datos compatibles con Azure Data Catalog.
* Compatibilidad con diez idiomas adicionales en el portal de Azure Data Catalog y en la herramienta de registro de origen de datos. Además del inglés, la experiencia de Azure Data Catalog ahora está disponible en alemán, español, francés, italiano, japonés, coreano, portugués (Brasil), ruso, chino simplificado y chino tradicional. La experiencia de usuario de Azure Data Catalog se traduce en función de las preferencias de idioma establecidas en Windows o en el explorador web del usuario.
* Compatibilidad con la replicación geográfica de datos de Azure Data Catalog para continuidad empresarial y recuperación ante desastres. Todo el contenido de Azure Data Catalog como, por ejemplo, anotaciones de micromecenazgo y metadatos de origen de datos, ahora se replica entre dos regiones de Azure sin costo adicional para los clientes. Las regiones de Azure están emparejadas previamente, con una separación de al menos 804 km de distancia, y siguen la asignación que se describe en [Continuidad empresarial y recuperación ante desastres (BCDR): regiones emparejadas de Azure](../best-practices-availability-paired-regions.md).
* Compatibilidad para cambiar la suscripción de Azure de Azure Data Catalog. Los administradores de Azure Data Catalog pueden usar la página de Configuración en el portal de Azure Data Catalog para seleccionar una suscripción de Azure diferente para la facturación.

## <a name="whats-new-for-january-2016"></a>Novedades de enero de 2016
A partir de enero de 2016 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad con el registro manual de orígenes de datos adicionales. Ahora puede usar "Crear entrada manual" en el portal de Azure Data Catalog, o bien usar la API de REST de Azure Data Catalog para registrar los siguientes orígenes de datos:
  * OData: función, conjunto de entidades y contenedor de entidades
  * HTTP: archivo, punto de conexión, informe y sitio
  * Sistema de archivos: archivo
  * SharePoint: lista
  * FTP: archivo y directorio
  * Salesforce.com: objeto
  * DB2: tabla, vista y base de datos
  * PostgreSQL: tabla, vista y base de datos
* Compatibilidad con orígenes de datos de "Abrir en SQL Server Data Tools" para SQL Server (incluyendo Azure SQL DB y Azure SQL Data Warehouse).  
* Compatibilidad con el registro y la detección de vistas y paquetes de SAP HANA. Puede registrar orígenes de datos de SAP HANA con la herramienta de registro de orígenes de datos de Azure Data Catalog, así como anotar y detectar los orígenes de datos de SAP HANA registrados con el portal de Azure Data Catalog.
* La posibilidad de anclar y desanclar activos de datos en el portal de Azure Data Catalog. Puede elegir anclar recursos de datos para que sea más sencillo volver a detectarlos y usarlos.
* Una página principal recientemente rediseñada del portal de Azure Data Catalog. La nueva página principal incluye información sobre la actividad de usuarios actual, incluidos recursos publicados recientemente, recursos anclados y búsquedas guardadas, e información sobre la actividad en el catálogo en su totalidad.
* Compatibilidad con la configuración de usuarios persistente en el portal de Azure Data Catalog. La configuración de la experiencia de usuario (incluidos la vista de cuadrícula o de icono, el número de resultados por página y el resultado de referencias activado o desactivado) se conserva entre sesiones de usuario.
* Azure Data Catalog está ahora disponible en dos nuevas regiones de Azure. Los clientes pueden aprovisionar Azure Data Catalog en las regiones de Europa del Norte y Sudeste Asiático, además del este de EE. UU., el oeste de EE. UU., Europa Occidental y este de Australia. Para más información, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Abrir en SQL Server Data Tools" requiere Visual Studio 2013 con la Update 4 y las herramientas de SQL Server para instalarse. Para instalar la versión más reciente de SQL Server Data Tools, visite [Descargar SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Novedades de diciembre de 2015
A partir de diciembre de 2015 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad con perfiles de datos para orígenes de datos de Azure SQL Data Warehouse. Al registrar las vistas y tablas de Azure SQL Data Warehouse, los usuarios pueden optar por incluir métricas de perfil de datos con los metadatos extraídos del origen de datos.
* Compatibilidad para el registro y la detección de objetos de MySQL y bases de datos. Los usuarios pueden registrar orígenes de datos de MySQL mediante la herramienta de registro de orígenes de datos de Azure Data Catalog y pueden anotar y detectar los orígenes de datos de MySQL registrados mediante el portal de Azure Data Catalog.
* Compatibilidad con SPNEGO y la autenticación de Windows para orígenes de datos de Teradata. Al registrar tablas y vistas de Teradata, los usuarios pueden optar por conectarse a Teradata mediante SPNEGO y Windows, y con LDAP y autenticación TD2.
* Compatibilidad con orígenes de datos de Almacén de Azure Data Lake. Los usuarios ahora pueden registrar y detectar los orígenes de datos de Azure Data Lake Store mediante Azure Data Catalog.
* Compatibilidad para especificar manualmente la configuración de proxy de red en la herramienta de registro de orígenes de datos de Azure Data Catalog. Los usuarios pueden seleccionar "Modifique la configuración del proxy" en la página de bienvenida de la herramienta y especificar el puerto y la dirección de proxy y el puerto que va a usar la herramienta.

## <a name="whats-new-for-november-2015"></a>Novedades de noviembre de 2015
A partir de noviembre de 2015 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Capacidad para ver y copiar cadenas de conexión desde dentro del portal de Azure Data Catalog para orígenes de datos de SQL Server (incluida Azure SQL Database) y Oracle. Los usuarios pueden hacer clic en el vínculo "Ver cadenas de conexión" en la información de conexión para una tabla, vista o base de datos de Oracle o SQL Server, para ver las cadenas de conexión usadas para conectarse al origen de datos. Se ofrecen cadenas de conexión de ADO.NET, ODBC, OLEDB y JDBC para los orígenes de datos de SQL Server. Se ofrecen cadenas de conexión de OLEDB y ODBC para los orígenes de datos de Oracle.
* Compatibilidad para incluir perfiles de datos al registrar tablas y vistas de Teradata.
* Compatibilidad con "Abrir en Power BI Desktop" para orígenes de SQL Server (incluido Azure SQL Database y Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage y HDFS.  
* Compatibilidad con la autenticación LDAP para orígenes de datos de Teradata. Al registrar tablas y vistas de Teradata, los usuarios pueden elegir conectarse a Teradata mediante LDAP y autenticación TD2.
* Compatibilidad con "Abrir en Excel" para orígenes de datos de Teradata.
* Compatibilidad con términos de búsqueda recientes en el portal de Azure Data Catalog. Al realizar búsquedas en el portal, los usuarios pueden seleccionar términos de búsqueda usados recientemente para acelerar la experiencia de detección.
* Compatibilidad con la vista preliminar de orígenes de datos de Teradata. Al registrar las vistas y tablas de Teradata, los usuarios pueden elegir incluir registros de instantánea con los metadatos extraídos del origen de datos.
* Compatibilidad con "Abrir en Excel" para orígenes de datos de Azure SQL Data Warehouse.
* Compatibilidad con definir y modificar esquemas de nivel de columna para los activos de datos registrados manualmente. Después de crear manualmente un recurso de datos mediante el portal de Azure Data Catalog, los usuarios pueden agregar definiciones de columna en las propiedades del activo de datos.
* Compatibilidad con consultas "has" al buscar en Azure Data Catalog para habilitar la detección de activos de datos registrados que poseen metadatos específicos. La sintaxis de consulta de Azure Data Catalog ahora incluye:

| Sintaxis de consulta | Propósito |
| --- | --- |
| `has:previews` |Busca los activos de datos que incluyen una vista previa. |
| `has:documentation` |Busca los activos de datos para los que se ha proporcionado documentación. |
| `has:tableDataProfiles` |Busca activos de datos con información de perfil de datos de nivel de tabla. |
| `has:columnsDataProfiles` |Busca activos de datos con información de perfil de datos de nivel de columna. |


> [!NOTE]
> "Abrir en Power BI Desktop" requiere la instalación de una versión actual de Power BI Desktop. Si encuentra problemas o errores al usar esta característica, asegúrese de que tiene la versión más reciente de Power BI Desktop disponible en [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Novedades de octubre de 2015
A partir de octubre de 2015 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad con cifrado en reposo de vistas previas de datos y perfiles de datos para orígenes de datos registrados. Azure Data Catalog cifra de forma transparente los registros de vista previa y los orígenes de datos de los perfiles de datos registrados con el servicio sin necesidad de que los administradores del Catálogo administren las claves.
* Compatibilidad con orígenes de datos de Teradata. Ahora los usuarios pueden registrar y detectar tablas y vistas de Teradata.
* Compatibilidad con orígenes de datos de Hive locales. Los usuarios ahora pueden registrarse y detectar tablas de Hive de Apache Hive en orígenes de datos locales de Hadoop.
* Compatibilidad con búsquedas guardadas en el portal de Azure Data Catalog. Los usuarios pueden guardar términos de búsqueda y filtrar selecciones para repetir con facilidad búsquedas anteriores y definir vistas útiles del contenido del Catálogo. El usuario puede marcar una búsqueda guardada como su búsqueda predeterminada. Cuando un usuario hace clic en el icono de búsqueda de "lupa" en la página principal del portal de Azure Data Catalog o en la página de "introducción", el usuario va a parar directamente a la búsqueda guardada marcada como predeterminada.
* Compatibilidad para la documentación de texto enriquecido para los activos de datos registrados y contenedores en el portal de Azure Data Catalog. Los usuarios ahora pueden proporcionar documentación para los activos de datos como tablas, vistas e informes y para contenedores, como bases de datos y modelos, para escenarios donde las etiquetas y descripciones no sean suficientes.
* Compatibilidad con el registro manual de tipos de orígenes de datos conocidos. Los usuarios pueden escribir manualmente información del origen de datos mediante el portal de Azure Data Catalog para todos los tipos de orígenes de datos admitidos por Azure Data Catalog.
* Compatibilidad con la autorización de grupos de seguridad de Azure Active Directory. Los administradores del catálogo pueden permitir el acceso al catálogo a los grupos de seguridad y a las cuentas de usuario, lo que facilita la administración del acceso a Azure Data Catalog.
* Compatibilidad con la apertura de orígenes de datos de Hive en Excel desde el portal de Azure Data Catalog.

> [!NOTE]
> En la versión actual, solo se admite la autenticación de Teradata TD2. En futuras versiones se admitirán también otros mecanismos de autenticación.

> [!NOTE]
> Para usar la característica "Abrir en Excel" con orígenes de datos de Hive, los usuarios deben tener instalado el controlador ODBC para Hive.

## <a name="whats-new-for-september-2015"></a>Novedades de septiembre de 2015
A partir de septiembre de 2015 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad para incluir perfiles de datos al registrar orígenes de datos de Hive.
* Compatibilidad con la detección de la API del catálogo mediante programación, lo que facilita la integración de las aplicaciones con Azure Data Catalog.
* Una nueva experiencia de detección de orígenes de datos "introductoria" en el portal de Azure Data Catalog. Cuando los usuarios entran en la página "detectar" del portal de Azure Data Catalog sin escribir un término de búsqueda, se muestra información general del contenido del catálogo, incluidas las etiquetas que se usan con más frecuencia y los expertos, los tipos de orígenes de datos y los tipos de objeto.
* Compatibilidad para el registro y la detección de objetos y bases de datos de Azure SQL Data Warehouse. Para obtener información adicional sobre Azure SQL Data Warehouse, consulte [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Compatibilidad para el registro y la detección de modelos de SQL Server Analysis Services y servidores de SQL Server Reporting Services como contenedores. Al registrar objetos SSAS y SSRS, Azure Data Catalog crea una entrada para el modelo SSAS y el servidor SSRS, y para los informes y otros objetos. Los contenedores se pueden detectar y anotar mediante el portal de Azure Data Catalog. Los usuarios también pueden buscar y filtrar el contenido de un modelo o servidor además de buscar y filtrar el contenido del catálogo.
* Compatibilidad con el registro y la detección de objetos de SQL Server Analysis Services mediante HTTP o HTTPS. Ahora los usuarios pueden conectarse a servidores SSAS mediante una dirección URL (como https://servername/olap/msmdpump.dll) en lugar de un nombre de servidor y pueden usar la autenticación básica y las conexiones anónimas además de la autenticación de Windows. Para obtener más información sobre las conexiones HTTP/HTTPS a SSAS, consulte [Configurar el acceso HTTP a Analysis Services en Internet Information Services (IIS) 8.0](https://msdn.microsoft.com/library/gg492140.aspx).
* Compatibilidad con orígenes de datos de Hive en HDInsight. Los usuarios ahora pueden registrarse y detectar tablas de Hive de Apache Hive de Hadoop en orígenes de datos de HDInsight. Para obtener más información sobre Hive en HDInsight, consulte el [centro de documentación de HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Compatibilidad para el registro y la detección de bases de datos de SQL Server y clústeres de HDFS como contenedores. Al registrar tablas y vistas de Oracle o HDFS, Azure Data Catalog crea una entrada para la base de datos, las tablas y las vistas. La base de datos se puede detectar y anotar mediante el portal de Azure Data Catalog. Los usuarios también pueden buscar y filtrar el contenido de una base de datos o clúster además de buscar y filtrar el contenido del catálogo.
* Compatibilidad para el registro manual de tipos de orígenes de datos desconocidos. Los usuarios pueden escribir manualmente la información del origen de datos mediante el portal de Azure Data Catalog para que los orígenes de datos no admitidos explícitamente por la herramienta de registro del origen de datos se puedan anotar y detectar.
* Compatibilidad para el registro y la detección de bases de datos de SQL Server como contenedores. Al registrar tablas y vistas de SQL Server, Azure Data Catalog crea una entrada para la base de datos, las tablas y las vistas. La base de datos se puede detectar y anotar mediante el portal de Azure Data Catalog. Los usuarios también pueden buscar y filtrar el contenido de una base de datos además de buscar y filtrar el contenido del catálogo.

> [!NOTE]
> Los objetos SSAS y SSRS que se han registrado antes de la versión del 18 de septiembre se deben volver a registrar mediante la herramienta de registro de orígenes de datos antes de que la entrada del modelo o del servidor se agregue al catálogo. Registrar de nuevo un origen de datos no afecta a las anotaciones agregadas por los usuarios en el portal de Azure Data Catalog.

> [!NOTE]
> Las vistas y las tablas de Oracle y los archivos y directorios de HDFS que se han registrado antes de la versión del 11 de septiembre se deben volver a registrar mediante la herramienta de registro de orígenes de datos antes de que la entrada del clúster o de la base de datos se agregue al catálogo. Registrar de nuevo un origen de datos no afecta a las anotaciones agregadas por los usuarios en el portal de Azure Data Catalog.

> [!NOTE]
> Las vistas y las tablas de SQL Server que se han registrado antes de la versión del 4 de septiembre se deben volver a registrar mediante la herramienta de registro de orígenes de datos antes de que la entrada de la base de datos se agregue al catálogo. Registrar de nuevo un origen de datos no afecta a las anotaciones agregadas por los usuarios en el portal de Azure Data Catalog.

## <a name="whats-new-for-august-2015"></a>Novedades de agosto de 2015
A partir de agosto de 2015 se han agregado las siguientes funcionalidades a Azure Data Catalog:

* Compatibilidad con la generación de perfiles de datos de orígenes de datos de SQL Server y Oracle. Al registrar vistas y tablas de SQL Server y Oracle, los usuarios puede elegir incluir información de perfil de datos para los objetos que se están registrando. El perfil de datos incluye las estadísticas de nivel de objeto y de nivel de columna.
* Compatibilidad con orígenes de datos de Hadoop HDFS. Los usuarios pueden registrarse ahora y detectar directorios y archivos HDFS.
* Soporte técnico para proporcionar información de solicitud de acceso a orígenes de datos registrados. Para cualquier recurso de datos registrado, los usuarios pueden ahora proporcionar instrucciones para solicitar el acceso, incluidos los vínculos de correo electrónico o direcciones URL, para integrar fácilmente herramientas y procesos existentes.
* Información sobre herramientas para las etiquetas y expertos, para que resulte más fácil de descubrir qué usuarios han proporcionado los metadatos de los activos de datos registrados.
* Hemos agregado un nuevo botón de "Usuario" y un menú a la barra de navegación superior. Este menú permite al usuario ver la cuenta usada para iniciar sesión en Azure Data Catalog y cerrar la sesión si así lo desea. Este menú también muestra el nombre del catálogo, lo que resulta útil para los desarrolladores que usan la API de REST de Azure Data Catalog.
* Solo en la edición estándar: al agregar propietarios a los recursos de datos, Azure Data Catalog ahora admite tanto cuentas de usuario como grupos de seguridad como propietarios. Para agregar un grupo de seguridad como propietario de los activos de datos seleccionados, puede especificar el nombre para mostrar del grupo o la dirección de correo electrónico UPN del grupo, si tiene alguna.
* Compatibilidad con orígenes de datos de Azure Blob Storage. Los usuarios pueden registrarse ahora y detectar los blobs de Azure Storage y los directorios.


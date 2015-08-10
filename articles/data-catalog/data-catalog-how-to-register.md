<properties
   pageTitle="Registro de orígenes de datos"
   description="Artículo de procedimientos que destaca cómo registrar orígenes de datos con el Catálogo de datos de Azure, incluidos los campos de metadatos que se extraen y los orígenes de datos que se admiten durante la vista previa."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/27/2015"
   ms.author="maroche"/>


# Registro de orígenes de datos

## Introducción
El **Catálogo de datos de Microsoft Azure** es un servicio en la nube totalmente administrado que actúa como sistema de registro y de detección de orígenes de datos empresariales. En otras palabras, el **Catálogo de datos de Azure** consiste en ayudar a las personas a detectar, comprender y usar orígenes de datos, y en ayudar a las organizaciones a obtener más valor de sus datos. El primer paso para crear un origen de datos que se pueda detectar a través del **Catálogo de datos de Azure** es registrar ese origen de datos.
## Registro de orígenes de datos
El registro es el proceso de extraer metadatos del origen de datos y copiarlos en el servicio **Catálogo de datos de Azure**. Los datos permanecen donde se encuentren en ese momento y quedan bajo el control de los administradores y las directivas del sistema actual.

Para registrar un origen de datos, basta con iniciar la herramienta de registro de orígenes de datos del **Catálogo de datos de Azure** en el portal **Catálogo de datos de Azure**. Inicie sesión con su cuenta profesional o educativa (las mismas credenciales de Azure Active Directory que emplea para iniciar sesión en el portal) y seleccione el origen de datos que quiera registrar. Vea el tutorial [Introducción al Catálogo de datos de Azure](data-catalog-get-started.md) para obtener las instrucciones paso a paso.

Una vez registrado el origen de datos, el Catálogo realiza el seguimiento de su ubicación e indexa los metadatos para que los usuarios puedan buscar, examinar y detectar el origen de datos y luego usen su ubicación para conectarse al mismo con la aplicación o herramienta de su elección.
## Orígenes admitidos
En la vista previa actual, el **Catálogo de datos de Azure** admite el registro de estos orígenes de datos y tipos de objeto:

* Tablas y vistas del Motor de base de datos de SQL Server
* Tablas y vistas de Base de datos de Oracle
* Dimensiones, medidas e indicadores multidimensionales de SQL Server Analysis Services
* Tablas de modo tabular de SQL Server Analysis Services
* Informes de SQL Server Reporting Services

> [AZURE.NOTE]El soporte de SQL Server incluye también Base de datos SQL de Microsoft Azure.

<br/>

> [AZURE.NOTE]El soporte de SQL Server Reporting Services solo se refiere a servidores en modo nativo: aún no se admite el modo de SharePoint.

<br/>

## Metadatos estructurales
Al registrar un origen de datos, la herramienta de registro extrae información sobre la estructura de los objetos seleccionados, lo que se conoce como metadatos estructurales. Para todos los objetos, estos metadatos estructurales incluirán la ubicación del objeto, para que las herramientas del cliente detecten los datos. Entre otros metadatos estructurales se incluyen el nombre y tipo del objeto, así como el nombre de atributo/columna y el tipo de datos.
## Metadatos descriptivos
Además de los metadatos estructurales de núcleo extraídos del origen de datos, la herramienta de registro de orígenes de datos también extraerá metadatos descriptivos. En el caso de SQL Server Analysis Services y SQL Server Reporting Services, esto procede de las propiedades de descripción expuestas por estos servicios. En SQL Server, se extraerán los valores especificados mediante la propiedad extendida ms\_description. En el caso de Base de datos de Oracle, la herramienta de registro de orígenes de datos extraerá la columna COMMENTS de la vista ALL\_TAB\_COMMENTS.

Además de los metadatos descriptivos extraídos del origen de datos, los usuarios también pueden especificar metadatos descriptivos con la herramienta de registro de orígenes de datos. Los usuarios pueden agregar etiquetas e identificar expertos para los objetos que se registran. Todos estos metadatos descriptivos se copian en el servicio **Catálogo de datos de Azure** junto con los metadatos estructurales.
## Inclusión de vistas previas
De forma predeterminada, solo se extraen metadatos de orígenes de datos y se copian en el servicio **Catálogo de datos de Azure**, pero la comprensión de un origen de datos suele simplificarse viendo una muestra de los datos que contiene.

La herramienta de registro de orígenes de datos del **Catálogo de datos de Azure** permite a los usuarios incluir una vista previa de instantánea de los datos presentes en cada tabla y vista que se registre. Si el usuario opta por incluir vistas previas durante el registro, la herramienta de registro incluirá un máximo de 20 registros de cada tabla y vista. Esta instantánea se copia después en el Catálogo junto con los metadatos descriptivos y estructurales. Nota: las tablas anchas con gran número de columnas pueden tener menos de 20 registros incluidos en la vista previa.
## Actualización de registros
El registro de un origen de datos hará que sea detectable en el **Catálogo de datos de Azure** con los metadatos y la vista previa opcional que se extraen durante el registro. Si el origen de datos debe actualizarse en el Catálogo (por ejemplo, si cambia el esquema de un objeto, hay que incluir tablas que se excluyeron originalmente o un usuario quiere actualizar los datos incluidos en las vistas previas), puede volver a ejecutarse la herramienta de registro de orígenes de datos.

Al volver a registrar un origen de datos ya registrado se realiza una operación de combinación "upsert": los objetos existentes se actualizan al tiempo que los nuevos objetos se crean. Los metadatos especificados por los usuarios a través del portal **Catálogo de datos de Azure** se mantendrán.

## Resumen
Al registrar un origen de datos con el **Catálogo de datos de Azure** se facilita la detección y comprensión de ese origen de datos, copiando los metadatos descriptivos y estructurales del origen de datos en el servicio Catálogo. Una vez registrado un origen de datos, se puede anotar, administrar y detectar mediante el portal **Catálogo de datos de Azure**.

<!---HONumber=July15_HO5-->
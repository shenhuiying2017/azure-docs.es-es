<properties
   pageTitle="Novedades en el Catálogo de datos de Azure"
   description="Información general de vista previa de las nuevas funcionalidades del Catálogo de datos de Azure."
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
   ms.date="09/04/2015"
   ms.author="maroche"/>

# Novedades en el Catálogo de datos de Azure

De forma periódica se publican actualizaciones al **Catálogo de datos de Azure**. No todas las versiones incluirán nuevas características de cara al usuario, algunas se centran en la funcionalidad del servicio back-end. En esta página se resaltan las nuevas funcionalidades de cara al usuario que se han agregado al servicio **Catálogo de datos de Azure** .

## Novedades de la versión correspondiente a la semana del 4 de septiembre de 2015

A partir de la semana del 4 de septiembre de 2015, se han agregado las siguientes funcionalidades al **Catálogo de datos de Azure**:

- Compatibilidad para el registro manual de tipos de orígenes de datos desconocidos. Los usuarios pueden escribir la información del origen de datos manualmente mediante el portal del **Catálogo de datos de Azure** para que los orígenes de datos no admitidos explícitamente por la herramienta de registro del origen de datos se puedan anotar y detectar.
- Compatibilidad para el registro y la detección de bases de datos de SQL Server como contenedores. Al registrar tablas y vistas de SQL Server, el **Catálogo de datos de Azure** creará una entrada para la base de datos, así como para las tablas y las vistas. La base de datos se puede detectar y anotar mediante el portal del **Catálogo de datos de Azure**. Los usuarios también pueden buscar y filtrar el contenido de una base de datos además de buscar y filtrar el contenido del catálogo.


> [AZURE.NOTE]Las vistas y las tablas de SQL Server que se han registrado antes de la versión 2015-09-04 se deben volver a registrar mediante la herramienta de registro de origen de datos antes de que se agregue la entrada de la base de datos al catálogo. El nuevo registro de un origen de datos no afecta a las anotaciones que se han agregado por los usuarios en el portal del **Catálogo de datos de Azure**.

## Novedades de la versión correspondiente a la semana del 28 de agosto de 2015

A partir de la semana del 28 de agosto de 2015, se han agregado las siguientes funcionalidades al **Catálogo de datos de Azure**:

- Compatibilidad con la generación de perfiles de datos de orígenes de datos de SQL Server y Oracle. Al registrar vistas y tablas de SQL Server y Oracle, los usuarios puede elegir incluir información de perfil de datos para los objetos que se están registrando. El perfil de datos incluye las estadísticas de nivel de objeto y de nivel de columna.
- Compatibilidad con orígenes de datos de Hadoop HDFS. Los usuarios pueden registrarse ahora y detectar directorios y archivos HDFS.

## Novedades de la versión correspondiente a la semana del 21 de agosto de 2015

A partir de la semana del 21 de agosto de 2015, se han agregado las siguientes funcionalidades al **Catálogo de datos de Azure**:

- Soporte técnico para proporcionar información de solicitud de acceso a orígenes de datos registrados. Para cualquier recurso de datos registrado, los usuarios pueden ahora proporcionar instrucciones para solicitar el acceso, incluidos los vínculos de correo electrónico o direcciones URL, para integrar fácilmente herramientas y procesos existentes.
- Información sobre herramientas para las etiquetas y expertos, para que resulte más fácil de descubrir qué usuarios han proporcionado los metadatos de los activos de datos registrados.
- Hemos agregado un nuevo botón de "Usuario" y un menú a la barra de navegación superior. Este menú permite al usuario ver la cuenta usada para iniciar sesión en el **Catálogo de datos de Azure**, y cerrar la sesión si así lo desea. Este menú también muestra el nombre del catálogo, lo que resulta útil para los desarrolladores que usan la API de REST del **Catálogo de datos de Azure**.
- Solo en la edición estándar: al agregar propietarios a los activos de datos, el **Catálogo de datos de Azure** ahora admite tanto cuentas de usuario como grupos de seguridad como propietarios. Para agregar un grupo de seguridad como propietario de los activos de datos seleccionados, puede especificar el nombre para mostrar del grupo o la dirección de correo electrónico UPN del grupo, si tiene alguna.
- Compatibilidad con orígenes de datos de almacenamiento de blobs de Azure. Los usuarios pueden registrarse ahora y detectar el almacenamiento de blobs de Azure y los directorios.

<!---HONumber=Sept15_HO2-->
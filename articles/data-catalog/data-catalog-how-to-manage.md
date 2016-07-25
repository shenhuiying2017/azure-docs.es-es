<properties
   pageTitle="Cómo administrar recursos de datos | Microsoft Azure"
   description="En este artículo de procedimientos se resalta cómo controlar la visibilidad y la propiedad de los recursos de datos registrados en Catálogo de datos de Azure."
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
   ms.date="07/12/2016"
   ms.author="maroche"/>


# Cómo administrar recursos de datos

## Introducción

El **Catálogo de datos de Azure** ofrece funcionalidades de detección de orígenes de datos, permitiendo a los usuarios detectar y comprender fácilmente los orígenes de datos que necesitan para realizar análisis y tomar decisiones. Estas funcionalidades de detección consiguen el mayor impacto cuando todos los usuarios puedan buscar y comprender la gama más amplia de orígenes de datos disponibles. Con esto en mente, el comportamiento predeterminado del Catálogo de datos es que todos los orígenes de datos registrados sean visibles, y detectables, para todos los usuarios del catálogo.

Catálogo de datos no ofrece a los usuarios acceso a los mismos datos. El propietario del origen de datos es quien controla el acceso a los datos. Catálogo de datos permite a los usuarios detectar los orígenes de datos y ver los metadatos relacionados con los orígenes registrados en el catálogo.

Sin embargo, puede haber situaciones en las que los orígenes de datos solo deben ser visibles a usuarios específicos o a los miembros de grupos específicos. Para estos escenarios, Catálogo de datos permite a los usuarios tomar posesión de los recursos de datos registrados en el catálogo y, después, controlar la visibilidad de los recursos que poseen.

> [AZURE.NOTE] La funcionalidad descrita en este artículo solo está disponible en la edición Estándar del Catálogo de datos de Azure. La edición Gratis no ofrece funcionalidades para poseer y restringir la visibilidad de los recursos de datos.

## Administración de la propiedad de los recursos de datos
De forma predeterminada, los recursos de datos registrados en Catálogo de datos no tienen propietario; cualquier usuario con permiso para acceder al catálogo puede detectar y anotar estos recursos. Los usuarios pueden tomar posesión de los recursos de datos sin propietario y limitar la visibilidad de los recursos que poseen.

Cuando un recurso de datos de Catálogo de datos tiene propietario, solo los usuarios autorizados por los propietarios pueden detectar el recurso y ver sus metadatos, y solo los propietarios pueden eliminar el recurso del Catálogo.

> [AZURE.NOTE] La propiedad del Catálogo de datos solo afecta a los metadatos almacenados en él. No concede ningún permiso en el origen de datos subyacente.

### Tomar posesión
Los usuarios pueden tomar posesión de los recursos de datos mediante la selección de la opción "Tomar posesión" en el portal de Catálogo de datos. No se requiere ningún permiso especial para tomar posesión de un recurso de datos sin propietario; cualquier usuario puede tomar posesión de estos tipos de recursos de datos.

### Adición de propietarios y copropietarios
Si un recurso de datos ya tiene propietario, los usuarios no pueden tomar posesión de él: un propietario existente debe agregarlos como copropietarios. Cualquier propietario puede agregar más usuarios o grupos de seguridad como copropietarios.

> [AZURE.NOTE] Es aconsejable tener al menos dos personas como propietarios de cualquier recurso de datos con propietario.

### Supresión de propietarios
Al igual que un propietario de recursos puede agregar copropietarios, cualquier propietario de recursos puede quitar a un copropietario.

Si un propietario de recursos se quita a sí mismo como propietario, ya no podrá administrar el recurso. Si un propietario de recursos se quita a sí mismo como propietario y no hay ningún otro copropietario, el recurso se revertirá a un estado de sin propietario.

## Visibilidad
Los propietarios de recursos de datos pueden controlar la visibilidad de los recursos de datos que poseen. Para restringir la visibilidad del valor predeterminado, según el cual todos los usuarios del Catálogo de datos pueden detectar y ver el recurso de datos, el propietario del recurso puede cambiar la configuración de visibilidad de "Todos" a "Propietarios y estos usuarios" en las propiedades del recurso. Los propietarios pueden después agregar usuarios y grupos de seguridad específicos.

> [AZURE.NOTE] Siempre que sea posible, los permisos de propiedad y la visibilidad de los recursos deben asignarse a grupos de seguridad y no a usuarios individuales.

## Administradores del Catálogo
Los administradores del Catálogo de datos son implícitamente copropietarios de todos los recursos del Catálogo. Los propietarios de los recursos no pueden quitar la visibilidad a los administradores del Catálogo y los administradores pueden administrar la propiedad y la visibilidad de todos los recursos de datos en el Catálogo.

## Resumen
El modelo de micromecenazgo del Catálogo de datos para la detección de recursos de datos y metadatos permite que todos los usuarios de Catálogo colaboren en él y lo detecten. La edición Estándar del Catálogo de datos proporciona funcionalidades para la propiedad y administración con el fin de limitar la visibilidad y el uso de los recursos de datos específicos.

<!---HONumber=AcomDC_0713_2016-->
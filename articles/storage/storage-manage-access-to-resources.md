<properties 
	pageTitle="Administración del acceso anónimo a contenedores y blobs | Microsoft Azure" 
	description="Obtenga información acerca de cómo poner contenedores y blobs a disposición del acceso anónimo." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="micurd;tamram"/>

# Administración del acceso a los recursos de almacenamiento de Azure

## Información general

De forma predeterminada, solamente el propietario de la cuenta de almacenamiento puede acceder a los recursos de almacenamiento en esa cuenta. Si su servicio o aplicación debe hacer que estos recursos estén disponibles para otros clientes sin compartir la clave de acceso, tiene las opciones siguientes para permitir el acceso:

- Puede establecer los permisos de un contenedor para permitir el acceso de lectura anónimo al contenedor y a sus blobs. El acceso de lectura anónimo solo está disponible para los contenedores y blobs. 

- Puede exponer un recurso a través de una firma de acceso compartido, que permite delegar acceso restringido a un contenedor, blob, tabla, cola, recurso compartido de archivos o archivo, especificando el intervalo para el que los recursos estarán disponibles y los permisos que un cliente tendrá en él.

- Puede usar una directiva de acceso almacenada para administrar firmas de acceso compartido para un contenedor o sus blobs, para una cola, para una tabla, para un recurso compartido de archivo o sus archivos. La directiva de acceso ofrece una medida adicional de control sobre las firmas de acceso compartido y también proporciona medios directos para revocarlas.

## Concesión de permisos a usuarios anónimos a contenedores y blobs

De forma predeterminada, solamente el dueño de la cuenta de almacenamiento puede obtener acceso a un contenedor y a todos los blobs en su interior. Para dar a los usuarios anónimos permisos de lectura para un contenedor y sus blobs, puede establecer los permisos del contenedor de forma que se permita el acceso público. Los usuarios anónimos pueden leer los blobs que estén en un contenedor con acceso público sin necesidad de tener que autenticar la solicitud.

Los contenedores ofrecen las siguientes opciones para administrar el acceso al contenedor:

- **Acceso de lectura público completo**: los datos del contenedor y blobs se pueden leer mediante una solicitud anónima. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

- **Acceso de lectura público solo para blobs**: los datos de blob dentro de este contenedor pueden leerse a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.

- **Sin acceso público de lectura**: solamente el propietario de la cuenta puede leer los datos del contenedor y del blob.

>[AZURE.NOTE]Si el servicio requiere que lleve un control de los recursos de blob más minucioso o bien, si desea conceder permisos para operaciones a parte de las operaciones de lectura, puede utilizar la firma de acceso compartido para que otros usuarios puedan tener acceso a un recurso determinado.

## Características disponibles para los usuarios anónimos

En la siguiente tabla, se indican las operaciones a las que pueden llamar los usuarios anónimos cuando la ACL de un contenedor se establece para permitir un acceso público.

| Operación REST | Permiso con acceso de lectura público completo | Permiso con acceso de lectura público para solo para los blobs |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | Solo el propietario | Solo el propietario |
| Create Container | Solo el propietario | Solo el propietario |
| Get Container Properties | Todo | Solo el propietario |
| Get Container Metadata | Todo | Solo el propietario |
| Set Container Metadata | Solo el propietario | Solo el propietario |
| Get Container ACL | Solo el propietario | Solo el propietario |
| Set Container ACL | Solo el propietario | Solo el propietario |
| Delete Container | Solo el propietario | Solo el propietario |
| List Blobs | Todo | Solo el propietario |
| Put Blob | Solo el propietario | Solo el propietario |
| Get Blob | Todo | Todo |
| Get Blob Properties | Todo | Todo |
| Set Blob Properties | Solo el propietario | Solo el propietario |
| Get Blob Metadata | Todo | Todo |
| Set Blob Metadata | Solo el propietario | Solo el propietario |
| Put Block | Solo el propietario | Solo el propietario |
| Get Block List (solo bloques confirmados) | Todo | Todo |
| Get Block List (solo bloques sin confirmar o todos los bloques) | Solo el propietario | Solo el propietario |
| Put Block List | Solo el propietario | Solo el propietario |
| Delete Blob | Solo el propietario | Solo el propietario |
| Copia de blobs | Solo el propietario | Solo el propietario |
| Instantánea de blob | Solo el propietario | Solo el propietario |
| Lease Blob | Solo el propietario | Solo el propietario |
| Put Page | Solo el propietario | Solo el propietario |
| Get Page Ranges | Todo | Todo |


## Otras referencias

- [Autenticación para los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Firmas de acceso compartido: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md)
- [Delegación de acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=Oct15_HO1-->
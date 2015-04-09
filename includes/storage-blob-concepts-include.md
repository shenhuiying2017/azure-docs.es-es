## Qué es el almacenamiento de blobs

Almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de
datos no estructurados, como texto o datos binarios, a los que se puede obtener acceso desde cualquier lugar del mundo a través de
HTTP o HTTPS. El almacenamiento de blobs se puede usar para exponer datos públicamente al mundo o
para almacenar los datos de la aplicación en privado.

El almacenamiento de blobs suele usarse para realizar las siguientes tareas:

-   Servicio de imágenes o documentos directamente a un explorador
-   Almacenamiento de archivos para acceso distribuido
-   Streaming de audio y vídeo
-   Realización de copia de seguridad segura y recuperación ante desastres
-   Almacenamiento de datos para el análisis de un servicio local u hospedado de
    Azure

## Conceptos del servicio BLOB

El servicio BLOB contiene los componentes siguientes:

![Blob1][Blob1]

-   **Cuenta de almacenamiento:** se realiza todo el acceso al almacenamiento de Azure
    a través de una cuenta de almacenamiento. Para obtener información sobre la capacidad de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx).

-   **Contenedor:** un contenedor proporciona una agrupación de un conjunto de blobs.
    Todos los blobs deben residir en un contenedor. Una cuenta puede contener una
    cantidad ilimitada de contenedores. Un contenedor puede almacenar una
    cantidad ilimitada de blobs.

-   **Blob:** un archivo de cualquier tipo y tamaño. Hay dos tipos de blobs
    que pueden almacenarse en el almacenamiento de Azure: blobs en páginas y en bloques.
    La mayoría de los archivos son blobs en bloques. Un blob en bloques único puede tener
    un tamaño de hasta 200 GB. En este tutorial se usan blobs en bloques. Blobs en páginas, otro tipo de blob,
    puede tener un tamaño de hasta 1 TB y son más eficaces cuando se modifican con frecuencia intervalos
    de bytes de un archivo. Para obtener más información
    sobre blobs, consulte [Introducción a los blobs en bloques y a los blobs en páginas][].

-   **Formato de dirección URL:** los blobs son direccionables mediante la siguiente dirección URL
    :   
    http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`  
      
    En el diagrama anterior se podría usar la siguiente dirección URL de ejemplo para dirigir
    uno de los blobs.  
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg


<!--HONumber=49-->
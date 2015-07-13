## ¿Qué es el almacenamiento de blobs?

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados, como texto o datos binarios, a los que puede acceder desde cualquier lugar del mundo a través de HTTP o HTTPS. Puede usar el almacenamiento de blobs para exponer datos públicamente o para almacenar datos de la aplicación de manera privada.

El almacenamiento de blobs suele usarse para realizar las siguientes tareas:

-   Servicio de imágenes o documentos directamente a un explorador
-   Almacenamiento de archivos para acceso distribuido
-   Streaming de audio y vídeo
-   Realización de copia de seguridad segura y recuperación ante desastres
-   Almacenamiento de datos para el análisis en local o en un servicio hospedado de Azure

## Conceptos del servicio BLOB

El servicio BLOB contiene los componentes siguientes:

![Blob1][Blob1]

-   **Cuenta de almacenamiento:** todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](storage-scalability-targets.md) para obtener información sobre la capacidad de la cuenta de almacenamiento.

-   **Contenedor:** un contenedor proporciona una agrupación de un conjunto de blobs. Todos los blobs deben residir en un contenedor. Además, una cuenta puede disponer de un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs.

-   **Blob:** archivo de cualquier tipo y tamaño. Existen dos tipos de blobs que pueden almacenarse en Almacenamiento de Azure: blobs en páginas y en bloques. La mayoría de los archivos son blobs en bloques. Un blob en bloques único puede tener un tamaño de hasta 200 GB. En este tutorial se usan blobs en bloques. Los blobs en páginas, que son otro tipo de blobs, pueden tener un tamaño de hasta 1 TB y son más eficaces cuando los intervalos de bytes de un archivo se modifican con frecuencia. Para obtener más información sobre los blobs, consulte [Introducción a los blobs en bloques y a los blobs en páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx).

-   **Formato de dirección URL:** los blobs son direccionables con el siguiente formato de dirección URL: http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`
      
    En el diagrama anterior se usó la siguiente dirección URL de ejemplo para dirigir uno de los blobs: `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=62-->
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

-   **Contenedor:** un contenedor proporciona una agrupación de un conjunto de blobs. Todos los blobs deben residir en un contenedor. Además, una cuenta puede disponer de un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs. Tenga en cuenta que el nombre del contenedor debe estar en minúsculas.

-   **Blob:** archivo de cualquier tipo y tamaño. Almacenamiento de Azure ofrece tres tipos de blobs: blobs en bloques, blobs en páginas y blobs en anexos.
    
	Los *blobs en bloques* son ideales para almacenar archivos binarios o de texto, como documentos y archivos multimedia. Los *blobs en anexos* se parecen a los blobs en bloques porque se componen de bloques, pero están optimizados para las operaciones de anexión, por lo que son útiles para escenarios de registro. Un único blob en bloques o blob en anexos puede contener un máximo de 50.000 bloques de hasta 4 MB cada uno, hasta un tamaño total de algo más de 195 GB (4 MB × 50.000).
    
	Los *blobs en páginas* pueden tener un tamaño de hasta 1 TB y son más eficaces para operaciones frecuentes de lectura y escritura. Máquinas virtuales de Azure usa blobs en páginas como discos de sistema operativo y de datos.

	Para más información sobre la nomenclatura de contenedores y blobs, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](https://msdn.microsoft.com/library/azure/dd135715.aspx).


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=AcomDC_0413_2016-->
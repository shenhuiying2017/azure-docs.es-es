
## <a name="what-is"> </a>Qué es el almacenamiento de blobs

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un blob
único puede ocupar cientos de gigabytes y una cuenta de almacenamiento
única puede contener hasta 200 TB de blobs si se creó a partir del 8 de
junio de 2012; las cuentas de almacenamiento creadas antes de esa fecha pueden contener hasta 100 TB de blobs. Consulte [Azure Storage Scalability and Performance Targets][1] para obtener información sobre la capacidad de la cuenta de almacenamiento.

El almacenamiento de blobs suele usarse para realizar las siguientes tareas:

* Servicio de imágenes o documentos directamente a un explorador
* Almacenamiento de archivos para acceso distribuido
* Streaming de audio y vídeo
* Realización de copia de seguridad segura y recuperación ante desastres
* Almacenamiento de datos para el análisis en local o en un servicio
  hospedado de Azure

Puede usar el almacenamiento de blobs para exponer datos públicamente o de manera privada para el almacenamiento interno de aplicaciones.

## <a name="concepts"> </a>Conceptos

El servicio de blobs contiene los siguientes componentes:

![Blob1](./media/howto-blob-storage/blob1.jpg)

* **Cuenta de almacenamiento:** Todo el acceso a Almacenamiento de Azure
  se realiza a través de una cuenta de almacenamiento. Consulte [Azure
  Storage Scalability and Performance Targets][1] para obtener
  información sobre la capacidad de la cuenta de almacenamiento.

* **Contenedor:** Un contenedor ofrece una agrupación de un conjunto de
  blobs. Todos los blobs deben residir en un contenedor. Además, una
  cuenta puede disponer de un número ilimitado de contenedores y un
  contenedor puede almacenar un número ilimitado de blobs.

* **Blob:** Un archivo de cualquier tipo y tamaño. Existen dos tipos de
  blobs que pueden almacenarse en Almacenamiento de Azure: blobs en
  páginas y en bloques. La mayoría de los archivos son blobs en bloques.
  Un blob en bloques único puede tener un tamaño de hasta 200 GB. En
  este tutorial se usan blobs en bloques. Los blobs en páginas, que son
  otro tipo de blobs, pueden tener un tamaño de hasta 1 TB y son más
  eficaces cuando los intervalos de bytes de un archivo se modifican con
  frecuencia. Para obtener más información sobre los blobs, consulte
  [Understanding Block Blobs and Page Blobs][2].

* **Formato de dirección URL:** Es posible dirigir los blobs con el
  siguiente formato de dirección URL:   
   http://`<storage data-morhtml="true"
  account>`.blob.core.windows.net/`<container>`/`<blob>`
  
  En el diagrama anterior se utilizó la siguiente dirección URL de
  ejemplo para dirigir uno de los blobs:  
   `http://sally.blob.core.windows.net/movies/MOV1.AVI`



[1]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/ee691964.aspx
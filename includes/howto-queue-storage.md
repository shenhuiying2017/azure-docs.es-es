## <a name="what-is"> </a>¿Qué es el almacenamiento en cola?

El almacenamiento en cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento. Cada cuenta de almacenamiento puede contener hasta 200 TB de datos de blobs, colas y tablas. Consulte [Objetivos de escalabilidad y rendimiento del almacenamiento en Azure](http://msdn.microsoft.com/es-es/library/dn249410.aspx) para obtener detalles acerca de la capacidad de las cuentas de almacenamiento.

El almacenamiento en cola suele usarse para realizar las siguientes tareas:

-   <span>Creación de trabajo pendiente para el procesamiento asincrónico</span>
-   Transferencia de mensajes de un rol web de Azure a un
    rol de trabajo de Azure

## <a name="concepts"> </a>Conceptos

El servicio Cola contiene los siguientes componentes:

![Cola1](./media/howto-queue-storage/queue1.png)


- **Formato URL:** las colas son direccionables mediante el formato de dirección URL siguiente:   
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
Las siguientes direcciones URL desvían una de las colas del diagrama:  
	http://myaccount.queue.core.windows.net/imagesToDownload 

-**Cuenta de almacenamiento:** todo el acceso al almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Consulte [Objetivos de rendimiento y escalabilidad de almacenamiento de Azure](http://msdn.microsoft.com/es-es/library/dn249410.aspx) para obtener detalles acerca de la capacidad de las cuentas de almacenamiento.

- **Cola:** una cola contiene un conjunto de mensajes. Todos los mensajes deben estar en una cola.

- **Mensaje:** un mensaje, en cualquier formato, de hasta 64KB.


<!--HONumber=41-->

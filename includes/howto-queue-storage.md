
## <a name="what-is"> </a>&iquest;Qué es el almacenamiento en cola?

El almacenamiento en cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento. En el caso de las cuentas de almacenamiento creadas después del día 8 de junio de 2012, la capacidad total es de 200 TB; en aquellas cuentas de almacenamiento creadas antes de esa fecha, la capacidad total es de 100 TB. Consulte [Azure Storage Scalability and Performance Targets][1] para obtener información sobre la capacidad de la cuenta de almacenamiento.

El almacenamiento en cola suele usarse para realizar las siguientes tareas:

* <span>Creación de trabajo pendiente para el
  procesamiento asincrónico</span>
* Cambio de mensajes de un rol web de Azure a un rol de trabajo de Azure

## <a name="concepts"> </a>Conceptos

El servicio en cola contiene los siguientes componentes:

![Cola1](./media/howto-queue-storage/queue1.png)

* **Formato de dirección URL:** Es posible dirigir las colas con el
  siguiente formato de dirección URL:   
   http://`<storage 
  account>`.queue.core.windows.net/`<queue>`

Las siguientes direcciones URL desvían una de las colas del diagrama:  
 http://myaccount.queue.core.windows.net/imagesToDownload

-**Cuenta de almacenamiento:** Todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Consulte [Azure Storage Scalability and Performance Targets][1] para obtener información sobre la capacidad de la cuenta de almacenamiento.

* **Cola:** Una cola contiene un conjunto de mensajes. Todos los mensajes deben encontrarse en una cola.

* **Mensaje:** Un mensaje, en cualquier formato, de hasta 64 KB.



[1]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
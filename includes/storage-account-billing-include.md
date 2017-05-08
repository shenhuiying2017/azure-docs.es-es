El uso de Almacenamiento de Azure se le factura según su cuenta de almacenamiento. Los costos de almacenamiento se basan en los siguientes factores: región/ubicación, tipo de cuenta, capacidad de almacenamiento, esquema de replicación, transacciones de almacenamiento y salida de datos.

* La región hace referencia a la región geográfica en la que radica la cuenta.
* El tipo de cuenta hace referencia a si se utiliza una cuenta de almacenamiento general o una cuenta de Almacenamiento de blobs. Con una cuenta de Almacenamiento de blobs, el nivel de acceso también determina el modelo de facturación de la cuenta.
* La capacidad de almacenamiento se refiere a cuánto de la asignación correspondiente a cuentas de almacenamiento utiliza para almacenar datos.
* La replicación determina cuántas copias de los datos se conservan al mismo tiempo, y en qué ubicaciones.
* Las transacciones se refieren a todas las operaciones de lectura y escritura en Almacenamiento de Azure.
* La salida de los datos se refiere a los datos transferidos fuera de una región de Azure. Cuando una aplicación que no está en ejecución en la misma región tiene acceso a los datos en su cuenta de almacenamiento, se le cobra por la salida de los datos. (En el caso de los servicios de Azure, puede llevar a cabo pasos para agrupar sus datos y servicios en los mismos centros de datos para reducir o eliminar los cargos por concepto de salida de los datos).

La página [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) proporciona información detallada sobre los precios en función del tipo de cuenta, de la capacidad de almacenamiento, de la replicación y de las transacciones. La página [Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/) proporciona información detallada sobre los precios para la salida de datos. Puede usar la [Calculadora de precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para ayudarle a calcular los costes.


## ¿Qué es el servicio Tabla?

El servicio de almacenamiento de tablas de Azure permite almacenar una gran cantidad de
datos estructurados. El servicio es un almacén de datos NoSQL que acepta
llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure
son ideales para el almacenamiento de datos estructurados no relacionales. Entre los usos frecuentes
del servicio Tabla se encuentran los siguientes:

-   Almacenamiento de TB de datos estructurados capaces de ofrecer servicio a
    aplicaciones de escala de web
-   Almacenamiento de conjuntos de datos que no requieren combinaciones complejas, claves externas o
    procedimientos almacenados y que pueden desnormalizarse para obtener un acceso rápido
-   Consulta rápida de datos mediante un índice agrupado
-   Acceso a datos mediante el protocolo OData y consultas LINQ con bibliotecas .NET
    del servicio de datos de WCF

Puede usar el servicio Tabla para almacenar y consultar grandes conjuntos de
datos estructurados no relacionales y las tablas se escalan según aumenta
la demanda.

## Conceptos del servicio Tabla

El servicio Tabla contiene los siguientes componentes:

![Table1][Table1]

-   **Formato de dirección URL:** el código desvía las tablas de una cuenta con este
    formato de dirección:   
    http://`<storage account>`.table.core.windows.net/`<table>`  
      
    Puede desviar las tablas de Azure directamente mediante esta dirección con el
    protocolo OData. Para obtener más información, consulte [OData.org][]

-   **Cuenta de almacenamiento:** todo el acceso al almacenamiento de Azure se realiza
    a través de una cuenta de almacenamiento. Consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx) para obtener información sobre la capacidad de la cuenta de almacenamiento.

-   **Tabla**: Una tabla es una colección de entidades. Las tablas no aplican
    un esquema en entidades, lo que significa que una sola tabla puede contener
    entidades que tienen distintos conjuntos de propiedades. El número de tablas que una 
	cuenta de almacenamiento puede contener se encuentra restringido solo por el 
    límite de capacidad de la cuenta de almacenamiento.

-   **Entidad**: Una entidad es un conjunto de propiedades, similar a una fila
    de base de datos. Una entidad puede tener un tamaño de hasta 1 MB.

-   **Propiedades**: Una propiedad es un par nombre-valor. Cada entidad puede
    incluir hasta 252 propiedades para almacenar datos. Cada entidad dispone también de tres
    propiedades del sistema que especifican una clave de partición, una clave de fila y una
    marca de tiempo. Las entidades con la misma clave de partición pueden consultarse más
    rápidamente e insertarse o actualizarse en operaciones atómicas. Una clave de fila de la entidad
    es su identificador exclusivo dentro de una partición.


  
  [Table1]: ./media/storage-table-concepts-include/table1.png
  [OData.org]: http://www.odata.org/

<!--HONumber=49-->
---
title: "Migración de los datos a SQL Data Warehouse | Microsoft Docs"
description: Sugerencias para migrar los datos a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: d78f954a-f54c-4aa4-9040-919bc6414887
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/29/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 0d156bc2eecf8220bd5ff4eb811d91482f216837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-data"></a>Migración de los datos
Se pueden mover datos de distintos orígenes a Almacenamiento de datos SQL con diversas herramientas.  Puede usarse Copia de ADF, SSIS y bcp para lograr este objetivo. Sin embargo, a medida que la cantidad de datos aumente debería pensar en descomponer en pasos el proceso de migración de datos. Esto le ofrece la posibilidad de optimizar cada paso en cuanto a rendimiento y resistencia para garantizar una migración de datos sin problemas.

En este artículo se describen en primer lugar los escenarios sencillos de migración de Copia de ADF, SSIS y bcp. Luego ahonda algo más en cómo se puede optimizar la migración.

## <a name="azure-data-factory-adf-copy"></a>Copia de Factoría de datos de Azure (ADF)
[Copia de ADF][ADF Copy] forma parte de [Azure Data Factory][Azure Data Factory]. Puede usar Copia de ADF para exportar los datos a archivos planos que se encuentran en el almacenamiento local, a archivos planos remotos que se mantienen en el almacenamiento de blobs de Azure o directamente a Almacenamiento de datos SQL.

Si los datos comienzan en archivos planos, tendrá que transferirlos primero a Azure Storage Blob antes de iniciar su carga en Almacenamiento de datos SQL. Una vez que se transfieren los datos a Azure Blob Storage puede volver a usar [Copia de ADF][ADF Copy] para insertar los datos en Almacenamiento de datos SQL.

PolyBase ofrece también una opción de alto rendimiento para cargar los datos. Sin embargo, eso significa tener que usar dos herramientas en lugar de una. Si necesita el mejor rendimiento, use PolyBase. Si lo que quiere es una experiencia de una sola herramienta (y los datos no son grandes), ADF es la respuesta.


> 
> 

Vaya al siguiente artículo para ver algunos excelentes [ejemplos de ADF][ADF samples].

## <a name="integration-services"></a>Servicios de integración
Integration Services (SSIS) es una herramienta eficaz y flexible de extracción, transformación y carga de datos (ETL) que admite varias opciones de carga de datos, flujos de trabajo complejos y transformación de datos. Use SSIS para transferir simplemente datos a Azure o como parte de una migración más amplia.

> [!NOTE]
> SSIS puede exportar a UTF-8 sin la marca BOM en el archivo. Para configurar este comportamiento, debe usar primero el componente de columna derivada para convertir los datos de caracteres del flujo de datos para usar la página de códigos 65001 UTF-8. Una vez convertidas las columnas, escriba los datos en el adaptador de destino de archivo plano asegurándose de que 65001 también se ha seleccionado como la página de códigos para el archivo.
> 
> 

SSIS se conecta a Almacenamiento de datos SQL del mismo modo que se conectaría a una implementación de SQL Server. Sin embargo, las conexiones tendrán que usar un administrador de conexiones de ADO.NET. También debe ocuparse de configurar el ajuste "Usar la inserción masiva cuando esté disponible" para maximizar el rendimiento. Consulte el artículo [Adaptador de destino de ADO.NET][ADO.NET destination adapter] para obtener más información sobre esta propiedad.

> [!NOTE]
> No se admite la conexión a Almacenamiento de datos SQL de Azure mediante OLEDB.
> 
> 

Además, siempre existe la posibilidad de que se produzcan errores en un paquete por problemas de red o de limitación. Diseñe los paquetes de manera que se puedan reanudar en el punto de error, sin tener que rehacer el trabajo que se completó antes del error.

Para obtener más información, consulte la [documentación de SSIS][SSIS documentation].

## <a name="bcp"></a>bcp
bcp es una utilidad de línea de comandos que se ha diseñado para la importación y exportación de datos de archivos planos. Puede producirse alguna transformación durante la exportación de datos. Si desea realizar transformaciones simples, use una consulta para seleccionar y transformar los datos. Una vez exportados, los archivos planos pueden cargarse directamente en el destino de la base de datos de Almacenamiento de datos SQL.

> [!NOTE]
> A menudo resulta una buena idea encapsular las transformaciones usadas durante la exportación de datos en una vista en el sistema de origen. Con ello se garantiza que la lógica se conserva y el proceso se puede repetir.
> 
> 

Las ventajas de bcp son:

* Simplicidad. Los comandos de bcp son fáciles de generar y ejecutar
* Proceso de carga reiniciable. Una vez exportada, la carga se puede ejecutar un número ilimitado de veces

Las limitaciones de bcp son:

* bcp solo funciona con archivos planos tabulados. No funciona con archivos, como xml o JSON.
* Las capacidades de transformación de datos se limitan solo a la fase de exportación y son en esencia sencillas.
* bcp no está adaptado para que sea sólido al cargar datos a través de Internet. Cualquier inestabilidad en la red puede provocar un error de carga.
* bcp se basa en el esquema existente en la base de datos de destino antes de la carga.

Para obtener más información, vea [Uso de bcp para cargar datos en Almacenamiento de datos SQL][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optimización de migración de datos
Un proceso de migración de datos SQLDW puede dividirse eficazmente en tres pasos independientes:

1. Exportación de datos de origen
2. Transferencia de datos en Azure
3. Carga en la base de datos de destino SQLDW

Cada paso puede optimizarse individualmente para crear un proceso de migración eficaz, reiniciable y resistente que maximiza el rendimiento en cada paso.

## <a name="optimizing-data-load"></a>Optimización de carga de datos
Si miramos esto en orden inverso por un momento, la forma más rápida de cargar datos resulta ser a través de PolyBase. La optimización de un proceso de carga de PolyBase impone requisitos previos en los pasos anteriores, por lo que conviene comprender esto por adelantado. Son las siguientes:

1. Codificación de archivos de datos
2. Formato de archivos de datos
3. Ubicación de archivos de datos

### <a name="encoding"></a>Codificación
PolyBase requiere que los archivos de datos estén codificados en UTF-8 o UTF-16FE. 



### <a name="format-of-data-files"></a>Formato de archivos de datos
PolyBase exige un terminador de fila fijo de \n o una línea nueva. Los archivos de datos deben seguir este estándar. No hay ninguna restricción sobre terminadores de columna o de cadena.

Debe definir todas las columnas del archivo como parte de la tabla externa de PolyBase. Asegúrese de que todas las columnas exportadas son necesarias y los tipos se ajustan a los estándares necesarios.

Vuelva a consultar el artículo [migración del esquema] para obtener información detallada sobre los tipos de datos admitidos.

### <a name="location-of-data-files"></a>Ubicación de archivos de datos
Almacenamiento de datos SQL usa PolyBase para cargar exclusivamente datos del Almacenamiento de blobs de Azure. Por consiguiente, los datos deben transferirse primero al almacenamiento de blobs.

## <a name="optimizing-data-transfer"></a>Optimización de transferencia de datos
Una de las partes más lentas de la migración de datos es la transferencia de los datos en Azure. No solo el ancho de banda de red puede ser un problema, sino que la confiabilidad de la red también puede dificultar seriamente el progreso. De forma predeterminada, la migración de datos a Azure se realiza a través de Internet, por lo que es bastante probable que se produzcan errores de transferencia. Sin embargo, puede que estos errores requieran que los datos se vuelvan a enviar en su totalidad o en parte.

Afortunadamente se cuenta con varias opciones para mejorar la velocidad y la resistencia de este proceso:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Es aconsejable considerar el uso de [ExpressRoute][ExpressRoute] para acelerar la transferencia. [ExpressRoute][ExpressRoute] le proporcionará una conexión privada establecida en Azure para que la conexión no vaya por la red pública de Internet. No se trata en ningún caso de un paso obligatorio. Sin embargo, mejorará el rendimiento al insertar datos en Azure desde una instalación local o de ubicación compartida.

Las ventajas de usar [ExpressRoute][ExpressRoute] son:

1. Mayor confiabilidad
2. Mayor velocidad de red
3. Menor latencia de red
4. Mayor seguridad de red

[ExpressRoute][ExpressRoute] resulta útil en una serie de escenarios; no solo la migración.

¿Le interesa? Para obtener más información y precios, visite la [documentación de ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Servicio Importación/Exportación de Azure
El Servicio Importación/Exportación de Azure es un proceso de transferencia de datos diseñado para transferencias de datos de gran tamaño (GB++) a masivas (TB++) en Azure. Implica escribir los datos en los discos y enviarlos a un centro de datos de Azure. El contenido del disco se cargará después automáticamente en Azure Storage Blob.

A continuación, se ofrece una vista general del proceso de importación y exportación:

1. Configurar un contenedor de Almacenamiento de blobs de Azure para recibir los datos
2. Exportar los datos al almacenamiento local
3. Copiar los datos en unidades de disco duro de 3,5 pulgadas SATA II/III con la [herramienta Importación/Exportación de Azure]
4. Crear un trabajo de importación mediante el Servicio Importación/Exportación de Azure proporcionando los archivos del diario generados por la [herramienta Importación/Exportación de Azure]
5. Enviar los discos al centro de datos de Azure asignado
6. Los datos se transfieren a su contenedor de almacenamiento de blobs de Azure
7. Cargar los datos en SQLDW mediante PolyBase

### <a name="azcopyazcopy-utility"></a>Utilidad [AZCopy][AZCopy]
La utilidad [AZCopy][AZCopy] es una excelente herramienta para organizar los datos transferidos a blobs de Azure Storage. Está diseñado para transferencias de datos de pequeño tamaño (MB++) a muy grandes (GB++). [AZCopy] también se ha diseñado para proporcionar buen rendimiento resistente al transferir datos a Azure, por lo que es una excelente opción para el paso de transferencia de datos. Una vez transferidos, puede cargar los datos con PolyBase en Almacenamiento de datos SQL. También puede incorporar AZCopy a los paquetes SSIS con una tarea "Ejecutar proceso".

Para usar AZCopy, debe primero descargarlo e instalarlo. Hay una [versión de producción][production version] y una [versión preliminar][preview version] disponibles.

Para cargar un archivo desde su sistema de archivos, necesitará un comando como el siguiente:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Un resumen del proceso general podría ser:

1. Configurar un contenedor de blobs de almacenamiento de Azure para recibir los datos
2. Exportar los datos al almacenamiento local
3. AZCopy copia los datos en el contenedor de almacenamiento de blobs de Azure
4. Cargar los datos con PolyBase en Almacenamiento de datos SQL

Documentación completa disponible: [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Optimización de exportación de datos
Además de asegurarse de que la exportación se ajusta a los requisitos de PolyBase, también puede tratar de optimizar la exportación de datos para mejorar aún más el proceso.



### <a name="data-compression"></a>Compresión de datos
PolyBase puede leer datos comprimidos con gzip. Si puede comprimir los datos en archivos gzip, minimizará la cantidad de datos que se transmite por la red.

### <a name="multiple-files"></a>Varios archivos
La división de tablas grandes en varios archivos no solo ayuda a mejorar la velocidad de exportación, sino que también contribuye a la posibilidad de reiniciar la transferencia y a la facilidad de uso general de los datos una vez se encuentren en el almacenamiento de blobs de Azure. Una de las muchas características útiles de PolyBase es que leerá todos los archivos contenidos en una carpeta y los tratará como una sola tabla. Por lo tanto, conviene aislar los archivos de cada tabla en su propia carpeta.

PolyBase también admite una característica conocida como "cruce de carpetas recursivo". Puede usar esta característica para mejorar aún más la organización de los datos exportados a fin de mejorar la administración de datos.

Para obtener más información sobre la carga de datos con PolyBase, vea [Uso de PolyBase para cargar datos en Almacenamiento de datos SQL][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la migración, vea [Migración de la solución a Almacenamiento de datos SQL][Migrate your solution to SQL Data Warehouse].
Para más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/v1/data-factory-data-movement-activities.md 
[ADF samples]: ../data-factory/v1/data-factory-samples.md
[ADF Copy examples]: ../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[Use PolyBase to load data into SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx

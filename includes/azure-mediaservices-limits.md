>[!NOTE]
>En el caso de recursos que no son fijos, puede solicitar que se generen las cuotas abriendo un vale de soporte. **No** cree cuentas adicionales de Azure Media Services para obtener límites mayores.

| Recurso | Límite predeterminado | 
| --- | --- | 
| Cuentas de Azure Media Services (AMS) en una única suscripción | 25 (fijo) |
| Unidades reservadas de multimedia (RU) por cuenta de AMS |25 (S1, S2)<br/>10 (S3) <sup>(1)</sup> | 
| Trabajos por cuenta de AMS | 50,000<sup>(2)</sup> |
| Tareas encadenadas por trabajo | 30 (fijo) |
| Recursos por cuenta de AMS | 1 000 000|
| Recursos por tarea. | 50 |
| Recursos por trabajo | 100 |
| Localizadores únicos asociados a un recurso al mismo tiempo | 5<sup>(4)</sup> |
| Canales activos por cuenta de AMS |5|
| Programas en estado detenido por canal  |50|
| Programas en estado de ejecución por canal  |3|
| Extremos de streaming en estado de ejecución por cuenta de ASM|2|
| Unidades de streaming por extremo de streaming |10 |
| Cuentas de almacenamiento | 1000 <sup>(5)</sup> (fijo) |
| Directivas | 1 000 000<sup>(6)</sup> |
| Tamaño de archivo| En algunos casos, existe un límite máximo de tamaño de archivo admitido para el procesamiento en Media Services. <sup>7</sup> |
  
<sup>1</sup> Las RU S3 no están disponibles en India occidental. Si cambia el tipo (por ejemplo, desde S2 a S1), se restablecen los límites de RU máximos.

<sup>2</sup> Este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye los trabajos eliminados. Puede eliminar los trabajos antiguos con **IJob.Delete** o con la solicitud HTTP **DELETE**.

A partir del 1 de abril de 2017, se eliminarán automáticamente los registros de trabajo de más de 90 días de su cuenta, junto con los registros de tarea asociados, aunque el número total de registros no llegue a la cuota máxima. Si desea archivar la información del trabajo o la tarea, puede usar el código que se describe [aquí](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> Al realizar una solicitud para obtener una lista de las entidades Job, se devuelve un máximo de 1000 por solicitud. Si necesita realizar un seguimiento de todos los trabajos enviados, puede usar top y skip, tal como se describe en [Opciones de consulta del sistema de OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Los localizadores no están diseñados para administrar el control de acceso por usuario. Para conceder derechos de acceso diferentes a usuarios individuales, use las soluciones de administración de derechos digitales (DRM). Para obtener más información, consulte [esta](../articles/media-services/media-services-content-protection-overview.md) sección.

<sup>5</sup> Las cuentas de almacenamiento deben proceder de la misma suscripción de Azure.

<sup>6</sup> hay un límite de 1 000 000 directivas para diferentes directivas de AMS (por ejemplo, para la directiva de localizador o ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Debe utilizar el mismo identificador de directiva si siempre usa los mismos días, permisos de acceso, etc. Para obtener información y un ejemplo, consulte [esta](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) sección.

<sup>7</sup>Si va a cargar contenido en un recurso de Azure Media Services con la intención de procesarlo con uno de los procesadores de multimedia del servicio (es decir, codificadores como Media Encoder Standard y Media Encoder Premium Workflow, o motores de análisis como Face Detector), debe tener en cuenta el límite de tamaño máximo de archivo permitido. 

El tamaño máximo admitido para un único blob es actualmente de 5 TB en Azure Blob Storage. Sin embargo, en Azure Media Services se aplican límites adicionales en función de los tamaños de máquina virtual utilizados por el servicio. En la tabla siguiente se muestran los límites en cada una de las unidades reservadas de multimedia (S1, S2, S3.) Si el archivo de origen es mayor que los límites definidos en la tabla, se producirá un error en el trabajo de codificación. Si va a codificar orígenes de resolución de 4K de larga duración, debe usar unidades reservadas de multimedia S3 para lograr el rendimiento necesario. Si tiene contenido con formato 4K mayor que el límite de 260 GB en las unidades reservadas de multimedia S3, póngase en contacto con nosotros en amshelp@microsoft.com para posibles mitigaciones para admitir su escenario.

| Tipo de unidad reservada de medios | Tamaño máximo de entrada (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|

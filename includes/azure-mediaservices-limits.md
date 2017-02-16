>[!NOTE]
>En el caso de recursos que no son fijos, puede solicitar que se generen las cuotas abriendo un vale de soporte. **No** cree cuentas adicionales de Azure Media Services para obtener límites mayores.

| Recurso | Límite predeterminado | 
| --- | --- | 
| Cuentas de Servicios multimedia de Azure (AMS) en una única suscripción | 25 (fijo) |
| Recursos por cuenta de AMS | 1 000 000|
| Tareas encadenadas por trabajo | 30 (fijo) |
| Recursos por tarea. | 50 |
| Recursos por trabajo | 100 |
| Trabajos por cuenta de AMS | 50 000<sup>2</sup> |
| Localizadores únicos asociados a un recurso al mismo tiempo | 5<sup>4</sup> |
| Canales activos por cuenta de AMS |5|
| Programas en estado detenido por canal  |50|
| Programas en estado de ejecución por canal  |3|
| Extremos de streaming en estado de ejecución por cuenta de ASM|2|
| Unidades de streaming por extremo de streaming |10 |
| Unidades reservadas de multimedia (RU) por cuenta de AMS |25 (S1, S2)<br/>10 (S3) <sup>1</sup> | 
| Cuentas de almacenamiento | 1000<sup>5</sup> (fijo) |
| Directivas | |1,000,000<sup>6</sup> |
 
<sup>1</sup> Las RU S3 no están disponibles en India occidental.

<sup>2</sup> Este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye los trabajos eliminados. Puede eliminar los trabajos antiguos con **IJob.Delete** o con la solicitud HTTP **DELETE**.

<sup>3</sup> Al realizar una solicitud para obtener una lista de las entidades Job, se devolverá un máximo de 1000 por solicitud. Si necesita realizar un seguimiento de todos los trabajos enviados, puede usar top y skip, tal como se describe en [Opciones de consulta del sistema de OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Los localizadores no están diseñados para administrar el control de acceso por usuario. Para conceder derechos de acceso diferentes a usuarios individuales, use las soluciones de administración de derechos digitales (DRM). Para obtener más información, consulte [esta](../articles/media-services/media-services-content-protection-overview.md) sección.

<sup>5</sup> Las cuentas de almacenamiento deben proceder de la misma suscripción de Azure.

<sup>6</sup> hay un límite de 1 000 000 directivas para diferentes directivas de AMS (por ejemplo, para la directiva de localizador o ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Debe utilizar el mismo identificador de directiva si siempre usa los mismos días, permisos de acceso, etc.



<!--HONumber=Jan17_HO2-->



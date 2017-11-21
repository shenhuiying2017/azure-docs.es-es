Periódicamente se publicarán actualizaciones del agente de Azure File Sync con el fin de agregar nuevas funcionalidades y solucionar los problemas que se encuentren. Se recomienda configurar Microsoft Update para obtener todas las actualizaciones del agente de Azure File Sync que se publican. Se entiende que a algunas organizaciones les guste realizar un control y una prueba estrictos de las actualizaciones. 

En el caso de implementaciones en las que se usen versiones anteriores del agente de Azure File Sync:

- El servicio de sincronización de almacenamiento respetará la versión anterior principal durante tres meses después de la publicación inicial de una nueva versión principal. Por ejemplo, el servicio de sincronización de almacenamiento admitiría la versión 1.\* hasta tres meses después de publicarse la versión 2.\*.
- Transcurridos los tres meses, el servicio de sincronización de almacenamiento comenzará a bloquear los servidores registrados mediante la versión expirada de la sincronización con sus grupos de sincronización.
- Antes de los tres meses de una versión principal anterior, todas las correcciones de errores solo irán a la versión principal actual.

> [!Note]  
> Se le notificará a través de la notificación del sistema en Azure Portal si usa una versión Azure File Sync que expirará en los siguientes tres meses.
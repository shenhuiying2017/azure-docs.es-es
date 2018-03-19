El agente de Azure File Sync se actualiza periódicamente con el fin de agregar nueva funcionalidad y solucionar los problemas. Se recomienda configurar Microsoft Update para obtener todas las actualizaciones del agente de Azure File Sync a medida que están disponibles. Es comprensible que a algunas organizaciones les guste realizar un control y una prueba estrictos de las actualizaciones.

En el caso de implementaciones en las que se usen versiones anteriores del agente de Azure File Sync:

- El servicio de sincronización de almacenamiento respetará la versión anterior principal durante tres meses después de la publicación inicial de una nueva versión principal. Por ejemplo, el servicio de sincronización de almacenamiento admite la versión 1.\* hasta tres meses después de publicarse la versión 2.\*.
- Transcurridos los tres meses, el servicio de sincronización de almacenamiento bloquea los servidores registrados mediante la versión expirada de la sincronización con sus grupos de sincronización.
- La versión principal anterior se respetará durante tres meses, pero todas las correcciones de errores solo se producirán en la versión principal actual (nueva).

> [!Note]  
> Si la versión de Azure File Sync expira en un plazo de 3 meses, se le informará mediante una notificación del sistema en Azure Portal.

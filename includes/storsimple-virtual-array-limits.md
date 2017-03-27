

| **Identificador de límites** | **Límite** | **Comentarios** |
| --- | --- | --- |
| Capacidad total (incluida la nube) |Hasta 64 TB por dispositivo virtual |Puede conmutar por error una matriz virtual de StorSimple completa en otra matriz vacía. Si intenta restaurar al mismo dispositivo, asegúrese de que tiene suficiente espacio en el dispositivo para completar esta operación. Después de haber excedido 32 TB, no puede realizar la restauración en el mismo dispositivo. |
| Número máximo de credenciales de la cuenta de almacenamiento por dispositivo |1 | |
| Número máximo de volúmenes o recursos compartidos |16 | |
| Tamaño mínimo de un recurso compartido en capas |500 GB | |
| Tamaño mínimo de un volumen en capas |500 GB | |
| Tamaño máximo de un recurso compartido en capas |20 TB | |
| Tamaño máximo de un volumen en capas |5 TB | |
| Tamaño mínimo de un recurso compartido anclado localmente |50 GB | |
| Tamaño mínimo de un volumen anclado localmente |50 GB | |
| Tamaño máximo del recurso compartido anclado localmente |2 TB | |
| Tamaño máximo de un volumen anclado localmente |200 GB | |
| Número máximo de conexiones de iSCSI de iniciadores |512 | |
| Número máximo de registros de control de acceso por dispositivo |64 | |
| Número máximo de copias de seguridad retenidas por el dispositivo virtual en la carpeta *.backups* del servidor de archivos |5 |Esto incluye las últimas copias de seguridad manuales y programadas (generadas por la directiva de copias de seguridad predeterminada). |
| Número máximo de copias de seguridad programadas retenidas por el dispositivo |55 |30 copias de seguridad diarias<br>12 copias de seguridad mensuales<br>13 copias de seguridad anuales |
| Número máximo de copias de seguridad manuales retenidas por el dispositivo |45 | |
| Número máximo de archivos por recurso compartido |1 millón |Al realizar la restauración de un dispositivo, los tiempos de restauración son proporcionales al número de archivos que haya en todos los recursos compartidos del dispositivo. |
| Número máximo de archivos por matriz virtual |4 millones | |
| Tiempo de recuperación de la restauración |Restauración rápida |La restauración se basa en el mapa térmico y depende del tamaño del volumen.<br>Pueden producirse operaciones de copia de seguridad con una operación de restauración en curso. |


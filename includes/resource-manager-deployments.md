La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación.

## Implementaciones de incrementales y completadas

De forma predeterminada, el Administrador de recursos controla las implementaciones como las actualizaciones incrementales al grupo de recursos. Con la implementación incremental, el Administrador de recursos:

- **deja sin modificar** recursos que existen en el grupo de recursos, pero que no se especifican en la plantilla
- **agrega** recursos que se especifican en la plantilla, pero que no existen en el grupo de recursos 
- **no vuelve a aprovisionar** recursos que existen en el grupo de recursos en la misma condición definida en la plantilla

Con la implementación completa, el Administrador de recursos:

- **elimina** recursos que existen en el grupo de recursos, pero que no se especifican en la plantilla
- **agrega** recursos que se especifican en la plantilla, pero que no existen en el grupo de recursos 
- **no vuelve a aprovisionar** recursos que existen en el grupo de recursos en la misma condición definida en la plantilla
 
El tipo de implementación se especifica a través de la propiedad **Mode**.

<!---HONumber=AcomDC_0615_2016-->
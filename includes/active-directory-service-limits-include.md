Estas son las restricciones de uso y otros límites de servicio para el servicio Azure Active Directory.

### Directorios

Un solo usuario solo puede asociarse a un máximo de 20 directorios de Azure Active Directory. Este límite se puede alcanzar en cualquiera de los ejemplos siguientes:

- Un solo usuario crea 20 directorios.
- Un solo usuario se agrega a 20 directorios como miembro.
- Un solo usuario crea 10 directorios y posteriormente un tercero lo agrega a otros 10 directorios.

### Objetos

- No hay ningún límite para los suscriptores de Azure Active Directory Premium o Azure Active Directory Basic, Enterprise Mobility Suite, Office 365, Microsoft Intune o cualquier otro servicio en línea de Microsoft que se base en Azure Active Directory para servicios de directorio.
- Se puede usar un máximo de 500.000 objetos en un solo directorio con la edición gratuita de Azure Active Directory.
- Un usuario que no es administrador puede crear hasta 250 objetos.

###Extensiones de esquema

Actualmente, las entidades "User", "Group", "TenantDetail", "Device", "Application" y "ServicePrincipal" se pueden extender con atributos de valor único de tipo "String" o tipo "Binary". Estas incluyen las limitaciones siguientes:

- Las extensiones de tipo String pueden tener 256 caracteres como máximo.
- Las extensiones de tipo Binary están limitadas a 256 bytes.
- Los valores de extensión 100 (en todos los tipos y todas las aplicaciones) se pueden escribir en cualquier objeto único.
- Las extensiones de esquema solo están disponibles en la versión de vista previa 1.21 de API Graph. La aplicación debe tener acceso de escritura para registrar una extensión.

### Aplicaciones

Un máximo de 10 usuarios pueden ser propietarios de una sola aplicación.

### Grupos

- Un máximo de 10 usuarios pueden ser propietarios de un solo grupo.
- Cualquier número de objetos puede ser miembro de un solo grupo en Azure Active Directory.


> [AZURE.NOTE]
>
> Hay un límite para el número de objetos que se puede sincronizar desde su Active Directory local a Azure Active Directory.
>
> - Si usa DirSync, el límite es 15.000 usuarios.
> - Si usa Azure AD Connect, el límite es 50.000 usuarios.

<br/>
### Panel de acceso

- No existe ningún límite en el número de aplicaciones que se ven en el Panel de acceso por cada usuario final en el caso de los suscriptores de Azure AD Premium o Enterprise Mobility Suite.
- Se puede mostrar un máximo de 10 iconos de aplicación (ejemplos: Box, Salesforce o Dropbox) en el Panel de acceso para cada usuario final con las ediciones gratis o básica de Azure AD para Azure Active Directory. Este límite no se aplica a las cuentas de administrador.

### Informes

Se puede ver o descargar en cualquier informe un máximo de 1.000 filas. Los datos adicionales se truncan.

<!---HONumber=July15_HO3-->
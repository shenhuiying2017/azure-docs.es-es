
### <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Adición de claves de firma y de cifrado a un inquilino B2C para que las usen directivas personalizadas

1. Navegue a la hoja Identity Experience Framework (Marco de experiencia de identidad) en la configuración de su inquilino de Azure AD B2C.
1. Seleccione `Policy Keys` para ver las claves disponibles en el inquilino. Si `B2C_1A_TokenSigningKeyContainer` existe, omita esta clave.
1. Cree `TokenSigningKeyContainer`  
 * Haga clic en `+Add`
 * Opciones > `Generate`
 * Nombre >`TokenSigningKeyContainer` El prefijo B2C_1A_ puede agregarse automáticamente.
 * Tipo de clave > `RSA`
 * Fechas: usar los valores predeterminados
 * Uso de la clave > `Signature`
1. Haga clic en `Create`
1. Si existe una clave llamada `B2C_1A_TokenEncryptionKeyContainer`, omita esta clave.
1. Cree `TokenEncryptionKeyContainer`.
 * Opciones > `Generate`
 * Nombre >`TokenSigningKeyContainer` El prefijo B2C_1A_ puede agregarse automáticamente.
 * Tipo de clave > `RSA`
 * Fechas: usar los valores predeterminados
 * Uso de la clave > `Encryption`
1. Haga clic en `Create`


[!TIP]
Los siguientes pasos son OPCIONALES si desea ofrecer proveedores de identidades sociales o proveedores de identidades federadas a los usuarios finales.  Facebook proporciona un buen punto de partida para obtener información acerca de los proveedores de identidades externos cuando Azure AD B2C usa directivas personalizadas.

1. Cree `FacebookSecret`.  Aunque es opcional, este paso se recomienda para probar fácilmente la capacidad de federar externamente.  Esto crea un punto de partida sólido para seguir desarrollando sus directivas con otros proveedores de id.
 * Haga clic en `+Add`
 * Opciones > `Manual`
 * Nombre > `FacebookSecret` El prefijo B2C_1A_ puede agregarse automáticamente.
 * Secreto > Escriba la propiedad FacebookSecret desde developers.facebook.com.  *No es su id de aplicación de Facebook*
 * Uso de la clave > Firma
1. Haga clic en `Create` y confirme la creación, anote el nombre

[!NOTE]
Si usa las directivas integradas de Azure AD B2C, lo habitual es que utilice el mismo secreto para directivas integradas y las personalizadas. 

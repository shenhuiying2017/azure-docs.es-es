UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <Dirección IP que se usará para transferir datos>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parámetros:

* /ServerMode: Obligatorio. Especifica si se deben instalar los servidores de configuración y de procesos, o solo el servidor de procesos. Valores de entrada: CS, PS.
* InstallLocation: Obligatorio. La carpeta donde se instalan los componentes.
* /MySQLCredsFilePath. Obligatorio. La ruta de acceso al archivo en que se almacenan las credenciales del servidor MySQL. El archivo debe tener este formato:
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath. Obligatorio. Ubicación del archivo de credenciales del almacén.
* /EnvType. Obligatorio. Tipo de instalación. Valores: VMware, NonVMware
* /PSIP y /CSIP. Obligatorio. Dirección IP del servidor de procesos y del servidor de configuración.
* /PassphraseFilePath. Obligatorio. Ubicación del archivo de frase de contraseña.
* /ByPassProxy. Opcional. Especifica si el servidor de configuración se conectar a Azure sin proxy.
* /ProxySettingsFilePath. Opcional. Configuración del proxy (el proxy predeterminado requiere autenticación, o un proxy personalizado). El archivo debe tener este formato:
* [ProxySettings]
* ProxyAuthentication = "Sí/No"
* Proxy IP = "dirección IP >"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort. Opcional. Número de puerto para los datos de replicación.
* SkipSpaceCheck. Opcional. Omitir comprobación de espacio en memoria caché.
* AcceptThirdpartyEULA. Obligatorio. Acepta los términos de licencia de terceros.
* ShowThirdpartyEULA. Obligatorio. Muestra los términos de licencia de terceros. Si se proporciona como entrada, se omiten todos los demás parámetros.


<!--HONumber=Feb17_HO2-->



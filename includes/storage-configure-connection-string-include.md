## Configuración de una cadena de conexión de almacenamiento
La biblioteca del cliente de almacenamiento de Azure para .NET admite el uso de una cadena de conexión de almacenamiento para configurar extremos y credenciales a fin de obtener acceso a los servicios de almacenamiento. La mejor manera de conservar la cadena de conexión de almacenamiento es mediante un archivo de configuración.

Para más información sobre las cadenas de conexión, consulte [Configuración de una cadena de conexión a Almacenamiento de Azure](../articles/storage/storage-configure-connection-string.md).

> [!NOTE]
> La clave de la cuenta de almacenamiento es similar a la contraseña raíz de la cuenta de almacenamiento. Siempre debe proteger la clave de la cuenta de almacenamiento. Evite distribuirla a otros usuarios, codificarla de forma rígida o guardarla en un archivo de texto que sea accesible a otros usuarios. Vuelva a generar la clave mediante el Portal de Azure si cree que puede verse comprometida.
> 
> 

### Determine su entorno de destino
Tiene dos opciones de entorno para ejecutar los ejemplos de esta guía:

* Puede ejecutar el código en el emulador de almacenamiento de Azure. El emulador de almacenamiento es un entorno local que emula una cuenta de Almacenamiento de Azure en la nube. El emulador es una opción gratis para probar y depurar el código mientras la aplicación está en desarrollo. El emulador usa una cuenta y una clave conocidas. Para más información, consulte [Uso del emulador de almacenamiento de Azure para desarrollo y pruebas](../articles/storage/storage-use-emulator.md)
* Puede ejecutar el código en una cuenta de Almacenamiento de Azure en la nube. 

Si el destino va a ser una cuenta de almacenamiento en la nube, escriba la clave de acceso principal de su cuenta de almacenamiento desde el Portal de Azure. Para más información, consulte [Visualización y copia de las claves de acceso de almacenamiento](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> Puede dirigirse al emulador de almacenamiento para evitar incurrir en cualquier coste asociado con Almacenamiento de Azure. Sin embargo, si selecciona dirigirse a una cuenta de almacenamiento de Azure en la nube, los costes derivados de la realización de este tutorial serán insignificantes.
> 
> 

### Configuración de la cadena de conexión mediante la configuración .NET
Si su aplicación se ejecuta en un dispositivo móvil o de escritorio, en una máquina virtual de Azure, o en una aplicación web de Azure, guarde la cadena de conexión mediante la configuración .NET (*por ejemplo*, en la aplicación `web.config` o `app.config` en el archivo). Almacene la cadena de conexión mediante el elemento `<appSettings>` como se indica a continuación. Reemplace `account-name` por el nombre de su cuenta de almacenamiento, y `account-key` por la clave de acceso de su cuenta:

    <configuration>
          <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
          </appSettings>
    </configuration>

Por ejemplo, el valor de configuración debe ser parecido a:

    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=account-key" />

Para elegir como destino el emulador de almacenamiento, puede utilizar un acceso directo que se asigna al nombre y la clave conocidas de la cuenta. En ese caso, la configuración de la cadena de conexión será:

    <add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

### Configuración de la cadena de conexión para un servicio en la nube de Azure
Si su aplicación se ejecuta en un servicio en la nube de Azure, guarde su cadena de conexión mediante los archivos de configuración del servicio de Azure (archivos `*.csdef` y `*.cscfg`). Consulte [Creación e implementación de un servicio en la nube](../articles/cloud-services/cloud-services-how-to-create-deploy.md) para más información acerca de la configuración del servicio en la nube de Azure.

<!---HONumber=AcomDC_0406_2016-->
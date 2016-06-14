## Configuración de su entorno de desarrollo

A continuación, configure el entorno de desarrollo en Visual Studio para que esté listo para probar los ejemplos de código proporcionados en esta guía.

### Creación de un proyecto de aplicación de consola de Windows

En Visual Studio, cree una aplicación de consola de Windows tal y como se indica a continuación:

![Creación de una aplicación de consola de Windows](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Todos los ejemplos de código en este tutorial se pueden agregar al método **Main()** en `program.cs` en la aplicación de consola.

Tenga en cuenta que puede usar la biblioteca del cliente de Almacenamiento de Azure desde cualquier tipo de aplicación. NET, incluido un servicio en la nube de Azure, una aplicación web de Azure, una aplicación de escritorio o una aplicación móvil. En esta guía, usamos una aplicación de consola para hacerlo más sencillo.

### Uso de NuGet para instalar los paquetes necesarios

Hay dos paquetes que deberá instalar en el proyecto para completar este tutorial:

- [Biblioteca del cliente de Almacenamiento de Microsoft Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): este paquete proporciona acceso mediante programación a los recursos de datos de la cuenta de almacenamiento.
- [Biblioteca del Administrador de configuración de Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): este paquete proporciona una clase para analizar una cadena de conexión desde un archivo de configuración, independientemente de dónde se ejecute la aplicación.

Puede usar NuGet para obtener ambos paquetes. Siga estos pasos:

1. Haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y elija **Manage NuGet Packages**.
2. Busque "WindowsAzure.Storage" en línea y haga clic en **Instalar** para instalar el paquete Biblioteca del cliente de Almacenamiento y sus dependencias.
3. Busque "ConfigurationManager" en línea y haga clic en **Instalar** para instalar el Administrador de configuración de Azure.

>[AZURE.NOTE] El paquete Biblioteca del cliente de Almacenamiento también se incluye en el [SDK de Azure para .NET](https://azure.microsoft.com/downloads/). Sin embargo, se recomienda que instale también la biblioteca del cliente de almacenamiento desde NuGet para garantizar que siempre dispone de la versión más reciente.
>
>Las dependencias ODataLib de la biblioteca de clientes de almacenamiento para .NET se resuelven a través de los paquetes ODataLib (versión 5.0.2 y superior) disponibles en NuGet, y no en los Servicios de datos de WCF. A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto. Los paquetes ODataLib específicos utilizados por la biblioteca de cliente de almacenamiento son [OData](http://nuget.org/packages/Microsoft.Data.OData/5.0.2), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/5.0.2) y [Spatial](http://nuget.org/packages/System.Spatial/5.0.2) (Espacial). Aunque las clases de almacenamiento de tabla de Azure usan estas bibliotecas, son dependencias necesarias para programar con la biblioteca de cliente de almacenamiento.


### Determine su entorno de destino

Tiene dos opciones de entorno para ejecutar los ejemplos de esta guía:

- Puede ejecutar el código en una cuenta de Almacenamiento de Azure en la nube. 
- Puede ejecutar el código en el emulador de almacenamiento de Azure. El emulador de almacenamiento es un entorno local que emula una cuenta de Almacenamiento de Azure en la nube. El emulador es una opción gratis para probar y depurar el código mientras la aplicación está en desarrollo. El emulador usa una cuenta y una clave conocidas. Para obtener más información, consulte [Uso del emulador de Almacenamiento de Azure para desarrollo y pruebas](../articles/storage/storage-use-emulator.md).

Si el destino va a ser una cuenta de almacenamiento en la nube, escriba la clave de acceso principal de su cuenta de almacenamiento desde el Portal de Azure. Para obtener más información, consulte [Visualización y copia de las claves de acceso de almacenamiento](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [AZURE.NOTE] Puede dirigirse al emulador de almacenamiento para evitar incurrir en cualquier coste asociado con Almacenamiento de Azure. Sin embargo, si selecciona dirigirse a una cuenta de almacenamiento de Azure en la nube, los costes derivados de la realización de este tutorial serán insignificantes.

### Configuración de la cadena de conexión de almacenamiento.

La biblioteca del cliente de almacenamiento de Azure para .NET admite el uso de una cadena de conexión de almacenamiento para configurar extremos y credenciales a fin de obtener acceso a los servicios de almacenamiento. La mejor manera de conservar la cadena de conexión de almacenamiento es mediante un archivo de configuración.

Para obtener más información sobre las cadenas de conexión, consulte [Configuración de una cadena de conexión a Almacenamiento de Azure](../articles/storage/storage-configure-connection-string.md).

> [AZURE.NOTE] La clave de la cuenta de almacenamiento es similar a la contraseña raíz de la cuenta de almacenamiento. Siempre debe proteger la clave de la cuenta de almacenamiento. Evite distribuirla a otros usuarios, codificarla de forma rígida o guardarla en un archivo de texto que sea accesible a otros usuarios. Vuelva a generar la clave mediante el Portal de Azure si cree que puede verse comprometida.

Para configurar la cadena de conexión, abra el archivo `app.config` en el Explorador de soluciones de Visual Studio. Agregue el contenido del elemento `<appSettings>`, que se muestra a continuación. Reemplace `account-name` por el nombre de su cuenta de almacenamiento, y `account-key` por la clave de acceso de su cuenta:

	<configuration>
	    <startup> 
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
	    </startup>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Por ejemplo, el valor de configuración debe ser parecido a:

	<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln6fT7mvY+rxu2iWAEyzPKITGkhM88J8HUoyofvK7C6fHcZc2kRZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />

Para elegir como destino el emulador de almacenamiento, puede utilizar un acceso directo que se asigna al nombre y la clave conocidas de la cuenta. En ese caso, la configuración de la cadena de conexión será:

	<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

<!---HONumber=AcomDC_0601_2016-->
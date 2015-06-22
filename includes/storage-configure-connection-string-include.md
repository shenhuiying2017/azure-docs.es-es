## Configuración de una cadena de conexión de almacenamiento

La biblioteca del cliente de almacenamiento de Azure para .NET admite el uso de una cadena de conexión de almacenamiento para configurar extremos y credenciales a fin de obtener acceso a los servicios de almacenamiento. Es recomendable mantener la cadena de conexión de almacenamiento en un archivo de configuración en vez de codificarla de forma rígida en su aplicación. Tiene dos opciones para guardar su cadena de conexión:

- Si su aplicación se ejecuta en un servicio en la nube de Azure, guarde su cadena de conexión utilizando el sistema de configuración del servicio Azure (archivos (`*.csdef` y `*.cscfg`).
- Si su aplicación se ejecuta en máquinas virtuales de Azure, o si está compilando aplicaciones .NET que se ejecutarán fuera de Azure, guarde su cadena de conexión mediante el sistema de configuración de NET (por ejemplo, archivo  `web.config` o  `app.config`).

Le mostraremos más adelante en esta guía cómo recuperar la cadena de conexión a partir de su código.

### Configuración de la cadena de conexión desde un servicio en la nube de Azure

Servicios en la nube de Azure tiene un mecanismo de configuración de servicios exclusivo que le permite cambiar dinámicamente la configuración desde el Portal de administración de Azure sin volver a implementar la aplicación.

Siga estos pasos para configurar la cadena de conexión mediante la configuración de servicios de Azure:

1.  En el Explorador de soluciones de Visual Studio, en la carpeta de **roles**
    del proyecto de implementación de Azure, haga clic con el botón secundario en el
    rol web o en el rol de trabajo, y haga clic en **Propiedades**.  
    ![Select the properties on a Cloud Service role in Visual Studio][connection-string1]

2.  Haga clic en la pestaña **Configuración** y, a continuación, en el botón **Agregar configuración**.  
    ![Add a Cloud Service setting in visual Studio][connection-string2]

    Aparecerá una nueva entrada **Setting1** en la cuadrícula de configuración.

3.  En la lista desplegable **Tipo** de la nueva entrada **Setting1**, elija
    **Cadena de conexión**.  
    ![Set connection string type][connection-string3]

4.  Haga clic en el botón **...** situado en el extremo derecho de la entrada **Setting1**.
    Se abrirá el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

5.  Elija si desea que el objetivo sea el emulador de almacenamiento (almacenamiento
    de Microsoft Azure simulado en el equipo local) o una cuenta de
    almacenamiento en la nube. El código de esta guía funciona con cualquiera
    de las opciones. 

	> [AZURE.NOTE] Puede dirigirse al emulador de almacenamiento para evitar incurrir en cualquier coste asociado con Almacenamiento de Microsoft Azure. Sin embargo, si selecciona dirigirse a una cuenta de almacenamiento de Azure en la nube, los costes derivados de la realización de este tutorial serán insignificantes.

	Si el destino va a ser una cuenta de almacenamiento en la nube, escriba la clave de acceso primaria de dicha cuenta. Para aprender a copiar una clave de acceso primara a través del Portal de administración de Azure, consulte 	
	[Visualización, copia y regeneración de claves de acceso de almacenamiento](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).
	
    ![Select target environment][connection-string4]

6.  Cambie la entrada **Nombre** de **Setting1** por un nombre más descriptivo
    como **StorageConnectionString**. Más adelante, en el código
    de esta guía, hará referencia a esta cadena de conexión.  
    ![Change connection string name][connection-string5]
	
### Configuración de la cadena de conexión mediante la configuración .NET

Si está desarrollando una aplicación ajena a los servicios en la nube de Azure (consulte la sección anterior), es recomendable que utilice el sistema de configuración .NET (por ejemplo, `web.config` o  `app.config`). Esto afecta a las aplicaciones que se ejecutan en Sitios web Azure y en Máquinas virtuales de Azure, así como a aquellas diseñadas para ejecutarse al margen de Azure. La cadena de conexión se almacena utilizando el elemento `<appSettings>` como se indica a continuación. Reemplace  `account-name` por el nombre de su cuenta de almacenamiento, y  `account-key` por la clave de acceso de su cuenta:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Por ejemplo, la configuración del archivo config debería ser similar a lo siguiente:

	<configuration>
    	<appSettings>
      		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
    	</appSettings>
	</configuration>

Ahora está preparado para realizar los procedimientos de esta guía.

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Configuración de cadenas de conexión]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!--HONumber=49-->
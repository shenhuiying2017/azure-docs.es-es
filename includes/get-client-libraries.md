###Instalación mediante el compositor

1. [Instalar Git][install-git]. 

 
	> [AZURE.NOTE] En Windows, también tendrá que agregar el archivo ejecutable Git a la variable de entorno PATH.

2. Cree un archivo con el nombre **composer.json** en la raíz del proyecto y agréguele el código siguiente:

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }


3. Descargue **[composer.phar][composer-phar]** en la raíz del proyecto.

4. Abra un símbolo del sistema y ejecute esto en la raíz del proyecto

		php composer.phar install

###Instalación manual

Para descargar e instalar las bibliotecas de clientes PHP para Azure manualmente, siga estos pasos:

1. Descargue un archivo .zip que contenga las bibliotecas de [GitHub][php-sdk-github]. También puede bifurcar el repositorio y clonarlo en su máquina local. La última opción requiere una cuenta GitHub y tener Git instalado localmente.

	
	> [AZURE.NOTE] Las bibliotecas de clientes PHP para Azure tienen una dependencia en los paquetes PEAR [HTTP_Request2](, http://pear.php.net/package/HTTP_Request2)[Mail_mime](http://pear.php.net/package/Mail_mime) y [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). La forma recomendada de resolver estas dependencias es instalar esos paquetes con el [administrador de paquetes PEAR (en inglés)](http://pear.php.net/manual/en/installation.php).


2. Copie el directorio  `WindowsAzure` para el archivo descargado en la estructura de directorios de la aplicación.

Para obtener más información sobre la instalación de bibliotecas de cliente PHP para Azure (incluida la información sobre la instalación como paquete PEAR), consulte [Descarga del SDK de Azure para PHP][download-SDK-PHP].


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

<!--HONumber=49-->
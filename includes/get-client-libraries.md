### Instalación mediante el compositor

1.  Obtenga información sobre la instalación de Git en [Install Git][Install Git].

    <div class="dev-callout">

    **Nota:**
    En Windows, también tendrá que agregar el archivo ejecutable Git a la variable de entorno PATH.

    </div>

2.  Cree un archivo con el nombre de **composer.json** en la raíz del proyecto y agréguele el código siguiente:

        {
            "require": {
                "microsoft/windowsazure": "*"
            },          
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "minimum-stability": "dev"
        }

3.  Descargue **[composer.phar][composer.phar]** en la raíz del proyecto.

4.  Abra un símbolo del sistema y ejecute esto en la raíz del proyecto

        php composer.phar install

### Instalación manual

Para descargar e instalar las bibliotecas de clientes PHP para Azure manualmente, siga estos pasos:

1.  Descargue un archivo .zip que contenga las bibliotecas desde [GitHub][GitHub]. También puede bifurcar el repositorio y clonarlo en su máquina local. La última opción requiere una cuenta GitHub y tener Git instalado localmente.

    <div class="dev-callout">

    **Nota:**
    Las bibliotecas de clientes PHP para Azure tienen una dependencia en los paquetes PEAR [HTTP\_Request2][HTTP\_Request2], [Mail\_mime][Mail\_mime] y [Mail\_mimeDecode][Mail\_mimeDecode]. La forma recomendada de resolver estas dependencias es instalar esos paquetes con el [administrador de paquetes PEAR (en inglés)][administrador de paquetes PEAR (en inglés)].

    </div>

2.  Copie el directorio `WindowsAzure` para el archivo descargado en la estructura de directorios de la aplicación.

Para obtener más información sobre la instalación de bibliotecas de clientes PHP para Azure (incluida la información sobre la instalación como paquete PEAR), consulte [Descarga del SDK de Azure para PHP][Descarga del SDK de Azure para PHP].

  [Install Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [administrador de paquetes PEAR (en inglés)]: http://pear.php.net/manual/en/installation.php
  [Descarga del SDK de Azure para PHP]: ../php-download-sdk/

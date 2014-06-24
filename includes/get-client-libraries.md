### Instalación mediante el compositor

1.  Obtenga información sobre la instalación de Git en [Install Git][1].
    
    **Nota:**

    En Windows, también tendrá que agregar el archivo ejecutable Git a
    la variable de entorno PATH.
    
    
    </div>

2.  Cree un archivo con el nombre de **composer.json** en la raíz del
    proyecto y agréguele el código siguiente:
    
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

3.  Descargue **[composer.phar][2]** en la raíz del proyecto.

4.  Abra un símbolo del sistema y ejecute esto en la raíz del proyecto
    
         php composer.phar install
### Instalación manual

Para descargar e instalar las bibliotecas de clientes PHP para Azure
manualmente, siga estos pasos:

1.  Descargue un archivo .zip que contenga las bibliotecas desde
    [GitHub][3]. También puede bifurcar el repositorio y clonarlo en su
    máquina local. La última opción requiere una cuenta GitHub y tener
    Git instalado localmente.
    
    **Nota:**

    Las bibliotecas de clientes PHP para Azure tienen una dependencia en
    los paquetes PEAR [HTTP\_Request2][4],
    [Mail\_mime][5] y [Mail\_mimeDecode][6]. La forma recomendada de resolver estas
    dependencias es instalar esos paquetes con el [administrador de
    paquetes PEAR (en inglés)][7].
    
    
    </div>

2.  Copie el directorio `WindowsAzure` para el archivo descargado en la
    estructura de directorios de la aplicación.

Para obtener más información sobre la instalación de bibliotecas de
clientes PHP para Azure (incluida la información sobre la instalación
como paquete PEAR), consulte [Descarga del SDK de Azure para
PHP](../php-download-sdk/).



[1]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[2]: http://getcomposer.org/composer.phar
[3]: http://go.microsoft.com/fwlink/?LinkId=252719
[4]: http://pear.php.net/package/HTTP_Request2
[5]: http://pear.php.net/package/Mail_mime
[6]: http://pear.php.net/package/Mail_mimeDecode
[7]: http://pear.php.net/manual/en/installation.php

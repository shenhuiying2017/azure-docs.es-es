### <a name="install-via-composer"></a>Instalación mediante el compositor
1. [Instalar Git][install-git]. Tenga en cuenta que en Windows, también debe agregar el archivo ejecutable Git a la variable de entorno PATH. 
2. Cree un archivo con el nombre **composer.json** en la raíz del proyecto y agréguele el código siguiente:
   
    ```
    {
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
    ```
3. Descargue **[composer.phar][composer-phar]** en la raíz del proyecto.
4. Abra un símbolo del sistema y ejecute el siguiente comando en la raíz del proyecto
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

### <a name="install-via-composer"></a>Instalación mediante el compositor
1. Cree un archivo con el nombre **composer.json** en la raíz del proyecto y agréguele el código siguiente:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Descargue **[composer.phar][composer-phar]** en la raíz del proyecto.
3. Abra un símbolo del sistema y ejecute el siguiente comando en la raíz del proyecto
   
    ```
    php composer.phar install
    ```

También puede ir a la [biblioteca de cliente de PHP de Azure Storage][php-sdk-github] en GitHub para clonar el código fuente.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

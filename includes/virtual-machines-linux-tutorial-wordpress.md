## <a name="install-wordpress"></a>Instalación de WordPress

Si desea probar la pila, instale una aplicación de ejemplo. Por ejemplo, los pasos siguientes instalan la plataforma de código abierto [WordPress](https://wordpress.org/), que permite crear sitios web y blogs. Otras cargas de trabajo para probar podrían ser [Drupal](http://www.drupal.org) y [Moodle](https://moodle.org/). 

Esta instalación de WordPress es para la prueba de concepto. Para más información y opciones de configuración para la instalación de producción, consulte la [documentación de WordPress](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Instalación del paquete de WordPress

Ejecute el siguiente comando:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Configuración de WordPress

Configure WordPress para usar PHP y MySQL. Ejecute el siguiente comando para abrir un editor de texto y crear el archivo `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Copie las líneas siguientes en el archivo, sustituyendo la contraseña de la base de datos por *su_contraseña* (deje otros valores sin modificar). A continuación, guarde el archivo.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```

En un directorio de trabajo, cree un archivo de texto denominado `wordpress.sql` para configurar la base de datos de WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Agregue los siguientes comandos, sustituyendo la contraseña de la base de datos por *su_contraseña* (deje otros valores sin modificar). A continuación, guarde el archivo.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```


Ejecute el siguiente comando para crear la base de datos:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Una vez completado el comando, elimine el archivo `wordpress.sql`.

Mueva la instalación de WordPress a la raíz de documentos del servidor web:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Ahora puede completar la instalación de WordPress y publicar en la plataforma. Abra un explorador y vaya a `http://yourPublicIPAddress/wordpress`. Sustituya la dirección por la dirección IP pública de la máquina virtual. Debe tener un aspecto similar a esta imagen.

![Página de instalación de WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)
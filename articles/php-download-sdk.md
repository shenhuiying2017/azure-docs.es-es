<properties title="Download the Azure SDK for PHP" pageTitle="Download the Azure SDK for PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="robmcm" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Descarga del SDK de Azure para PHP

El SDK de Azure para PHP incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones PHP para Azure. Específicamente, el SDK de Azure para PHP incluye lo siguiente:

-   **Las bibliotecas de clientes PHP para Azure**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube.
-   **Las herramientas de línea de comandos de Azure para Mac y Linux**. Este es un conjunto de herramientas de línea de comandos que sirve para implementar y administrar servicios de Azure, como Sitios web Azure y Máquinas virtuales de Azure. Estas herramientas funcionan en cualquier plataforma, incluidas las plataformas Mac, Linux y Windows.
-   **Azure PowerShell (solo Windows)**. Este es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure, como Servicios en la nube y Máquinas virtuales.
-   **Los emuladores de Azure (solo Windows)**. Los emuladores de proceso y almacenamiento son emuladores locales de los servicios en la nube y los servicios de administración de datos que le permiten probar localmente una aplicación. Los emuladores de Azure solo se ejecutan en Windows.

Las secciones que vienen a continuación describen cómo descargar e instalar los componentes descritos.

En las instrucciones de este tema se asume que tiene instalado [PHP][].

> [WACOM.NOTE]
> Debe tener instalado PHP 5.3 o superior para utilizar las bibliotecas de clientes PHP para Azure.

## Bibliotecas de clientes PHP para Azure

Las bibliotecas de clientes PHP para Azure proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube, desde cualquier sistema operativo. Estas bibliotecas se pueden instalar manualmente o a través de administradores de paquetes PEAR o el compositor.

Si desea obtener información acerca del uso de las bibliotecas de clientes PHP para Azure, consulte [How to Use the Blob Service][], [How to Use the Table Service][] y [How to Use the Queue Service][].

### Instalación mediante el compositor

1.  Obtenga información sobre la instalación de Git en [Install Git][].

    > [WACOM.NOTE]
    > En Windows, también tendrá que agregar el archivo ejecutable Git a la variable de entorno PATH.

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

3.  Descargue **[composer.phar][]** en la raíz del proyecto.

4.  Abra un símbolo del sistema y ejecute esto en la raíz del proyecto

        php composer.phar install

### Instalación como paquete PEAR

Para instalar las bibliotecas de clientes PHP para Azure como paquete PEAR, siga estos pasos:

1.  [Instale PEAR][] (información en inglés).
2.  Configure el canal PEAR para Azure:

        pear channel-discover pear.windowsazure.com

3.  Instale el paquete PEAR:

        pear install pear.windowsazure.com/WindowsAzure-0.3.1

Una vez que se haya completado la instalación, puede hacer referencia a las bibliotecas de clases desde su aplicación.

### Instalación manual

Para descargar e instalar las bibliotecas de clientes PHP para Azure manualmente, siga estos pasos:

1.  Descargue un archivo .zip que contenga las bibliotecas desde [GitHub][]. También puede bifurcar el repositorio y clonarlo en su máquina local. La última opción requiere una cuenta GitHub y tener Git instalado localmente.

    > [WACOM.NOTE]
    > Las bibliotecas de clientes PHP para Azure tienen una dependencia en los paquetes PEAR [HTTP\_Request2][], [Mail\_mime][] y [Mail\_mimeDecode][]. La forma recomendada de resolver estas dependencias es instalar esos paquetes con el [administrador de paquetes PEAR][] (en inglés).

2.  Copie el directorio `WindowsAzure` para el archivo descargado en la estructura de directorios de la aplicación y haga referencia a clases desde su aplicación.

## Azure PowerShell y emuladores de Azure

Azure PowerShell es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure (como Servicios en la nube y Máquinas virtuales). Los emuladores de Azure son emuladores de servicios en la nube y servicios de administración de datos que le permiten probar localmente una aplicación. Estos componentes solo son compatibles con Windows.

La manera recomendada de instalar Azure PowerShell y los emuladores de Azure es utilizar el [instalador de plataforma web de Microsoft][]. Observe que también puede elegir instalar otros componentes de desarrollo, como PHP, SQL Server, los controladores de Microsoft para SQL Server para PHP y WebMatrix.

Para obtener más información acerca del uso Azure PowerShell, consulte [Uso de Azure PowerShell][].

## Herramientas de línea de comandos de Azure para Mac y Linux

Las herramientas de línea de comandos de Azure para Mac y Linux son un conjunto de herramientas de línea de comandos que sirven para implementar y administrar los servicios de Azure, como Sitios web Azure y Máquinas virtuales de Azure. La siguiente lista describe cómo instalar las herramientas, según el sistema operativo:

-   **Mac**: Descargue el Instalador de SDK de Azure aquí: [][]<http://go.microsoft.com/fwlink/?LinkId=252249></a>. Abra el archivo .pkg descargado y complete los pasos de instalación a medida que se le solicita.

-   **Linux**: Instale la última versión de [Node.js][] (consulte [Install Node.js via Package Manager][]) y, a continuación, ejecute el siguiente comando:

        npm install azure-cli -g

    > [WACOM.NOTE]
    > Es posible que necesite ejecutar este comando con privilegios elevados: `sudo npm install azure-cli -g`

Para obtener información sobre el uso de las herramientas de línea de comandos de Azure para Mac y Linux, consulte [Uso de las herramientas de línea de comandos para Mac y Linux][].

  [PHP]: http://www.php.net/manual/en/install.php
  [How to Use the Blob Service]: http://go.microsoft.com/fwlink/?LinkId=252714
  [How to Use the Table Service]: http://go.microsoft.com/fwlink/?LinkId=252715
  [How to Use the Queue Service]: http://go.microsoft.com/fwlink/?LinkId=252716
  [Install Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [Instale PEAR]: http://pear.php.net/manual/en/installation.getting.php
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [administrador de paquetes PEAR]: http://pear.php.net/manual/en/installation.php
  [instalador de plataforma web de Microsoft]: http://go.microsoft.com/fwlink/?LinkId=253447
  [Uso de Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=252718
  []: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Install Node.js via Package Manager]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Uso de las herramientas de línea de comandos para Mac y Linux]: http://go.microsoft.com/fwlink/?LinkId=252717

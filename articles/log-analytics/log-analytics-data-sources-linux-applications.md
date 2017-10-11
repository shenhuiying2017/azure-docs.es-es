---
title: Recopilar el rendimiento de las aplicaciones de Linux en Log Analytics de OMS | Microsoft Docs
description: "En este artículo se proporciona información para configurar el agente de OMS para Linux a fin de recopilar los contadores de rendimiento para MySQL y el servidor HTTP de Apache."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 04ea6f728e59ec8b47e54fe45e1adc6cbbfb85ff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>Recopilar los contadores de rendimiento para aplicaciones de Linux en Log Analytics 
En este artículo se proporciona información para configurar el [agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) a fin de recopilar los contadores de rendimiento para aplicaciones específicas.  Las aplicaciones incluidas en este artículo son las siguientes:  

- [MySQL](#MySQL)
- [Servidor HTTP de Apache](#apache-http-server)

## <a name="mysql"></a>MySQL
Si se detecta en el equipo un servidor de MySQL o de MariaDB cuando se instala el agente de OMS, se instalará automáticamente un proveedor de supervisión de rendimiento para el servidor MySQL. Este proveedor se conecta al servidor MySQL o MariaDB local para exponer las estadísticas de rendimiento. Debe configurar las credenciales de usuario de MySQL para que el proveedor pueda tener acceso al servidor de MySQL.

### <a name="configure-mysql-credentials"></a>Configurar las credenciales de MySQL
El proveedor de MySQL para OMI necesita un usuario de MySQL preconfigurado y bibliotecas de cliente de MySQL instaladas para consultar la información de rendimiento y mantenimiento de la instancia de MySQL.  Estas credenciales se almacenan en un archivo de autenticación que se almacena en el agente de Linux.  El archivo de autenticación especifica la dirección de enlace y el puerto que usa la instancia de MySQL para escuchar y las credenciales que tiene que usar para recopilar métricas.  

Durante la instalación del agente de OMS para Linux, el proveedor de MySQL para OMI examinará los archivos de configuración my.cnf de MySQL (ubicaciones predeterminadas) para encontrar la dirección de enlace y el puerto, y establecer parcialmente el archivo de autenticación de MySQL para OMI.

El archivo de autenticación de MySQL se almacena en `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formato del archivo de autenticación
A continuación se muestra el formato del archivo de autenticación de MySQL para OMI.

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Las entradas del archivo de autenticación se describen en la tabla siguiente.

| Propiedad | Descripción |
|:--|:--|
| Port | Representa el puerto actual en el que está escuchando la instancia de MySQL. El puerto 0 especifica que las propiedades que siguen se usan para la instancia predeterminada. |
| Dirección de enlace| Dirección de enlace actual de MySQL. |
| nombre de usuario| Usuario de MySQL usado para supervisar la instancia del servidor MySQL. |
| Contraseña codificada en Base64| Contraseña del usuario de supervisión de MySQL codificada en Base64. |
| Actualización automática| Especifica si se volverán a examinar los cambios en el archivo my.cnf y si se sobrescribirá el archivo de autenticación de MySQL para OMI cuando el proveedor de MySQL para OMI se actualice. |

### <a name="default-instance"></a>Instancia predeterminada
El archivo de autenticación de MySQL para OMI puede definir una instancia predeterminada y un número de puerto para facilitar la administración de varias instancias de MySQL en un host de Linux.  La instancia predeterminada se indica mediante una instancia con el puerto 0. Todas las instancias adicionales heredarán las propiedades establecidas en la instancia predeterminada, a menos que se especifiquen valores diferentes. Por ejemplo, si se agrega la instancia de MySQL que escucha en el puerto "3308", se usará la dirección de enlace, el nombre de usuario y la contraseña codificada en Base64 de la instancia predeterminada para probar y supervisar la instancia que escucha en 3308. Si la instancia en 3308 está enlazada a otra dirección y usa el mismo par de nombre de usuario y contraseña que MySQL, solo se necesitará la dirección de enlace, y las demás propiedades se heredarán.

En la tabla siguiente se indica una configuración de ejemplo de la instancia. 

| Descripción | Archivo |
|:--|:--|
| Instancia predeterminada e instancia con el puerto 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Instancia predeterminada e instancia con el puerto 3308 y nombre de usuario y contraseña diferentes. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programa del archivo de autenticación de MySQL para OMI
Con la instalación del proveedor de MySQL para OMI se incluye un programa del archivo de autenticación de MySQL para OMI que se puede usar para editar el archivo de autenticación de MySQL para OMI. El programa del archivo de autenticación se encuentra en la ubicación siguiente.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> El archivo de credenciales tiene que ser legible para la cuenta omsagent. Se recomienda ejecutar el comando mycimprovauth como omsgent.

En la tabla siguiente se proporciona información sobre la sintaxis para usar mycimprovauth.

| Operación | Ejemplo | Descripción
|:--|:--|:--|
| actualización automática *false\|true* | mycimprovauth autoupdate false | Establece si el archivo de autenticación se actualizará automáticamente o no al reiniciar o actualizar. |
| *nombre de usuario y contraseña de la dirección de enlace* predeterminada | mycimprovauth default 127.0.0.1 root pwd | Establece la instancia predeterminada del archivo de autenticación de MySQL para OMI.<br>El campo de contraseña debe escribirse en texto sin formato, ya que la contraseña del archivo de autenticación de MySQL para OMI se codificará en Base 64. |
| eliminar *valor predeterminado\|número de puerto* | mycimprovauth 3308 | Elimina la instancia especificada, ya sea por el valor predeterminado o por número de puerto. |
| help | mycimprov help | Imprime una lista de comandos que se pueden usar. |
| imprimir | mycimprov print | Imprime un archivo de autenticación de MySQL para OMI fácil de leer. |
| actualizar *nombre de usuario y contraseña de la dirección de enlace* del número de puerto | mycimprov update 3307 127.0.0.1 root pwd | Actualiza la instancia especificada o, si no existe, agrega la instancia. |

Los comandos del ejemplo siguiente definen una cuenta de usuario predeterminada para el servidor MySQL en localhost.  El campo de contraseña debe escribirse en texto sin formato, ya que la contraseña del archivo de autenticación de MySQL para OMI se codificará en Base 64.

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Permisos necesarios de la base de datos para los contadores de rendimiento de MySQL
El usuario de MySQL necesita acceso a las consultas siguientes para recopilar datos de rendimiento del servidor MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


El usuario de MySQL también necesita el acceso SELECT a las siguientes tablas predeterminadas.

- information_schema
- mysql. 

Estos privilegios se pueden conceder ejecutando los siguientes comandos de concesión.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Para conceder permisos a un usuario de supervisión de MySQL, el usuario que concede el permiso tiene que tener el privilegio "GRANT option", además del privilegio que se va a conceder.

### <a name="define-performance-counters"></a>Definir contadores de rendimiento

Una vez configurado el agente de OMS para Linux de modo que envíe datos a Log Analytics, debe configurar los contadores de rendimiento para que recopilen.  Use el procedimiento descrito en [Orígenes de datos de rendimiento de Windows y Linux en Log Analytics](log-analytics-data-sources-windows-events.md) con los contadores de la tabla siguiente.

| Nombre de objeto | Nombre del contador |
|:--|:--|
| Base de datos MySQL | Espacio en disco en bytes |
| Base de datos MySQL | Tablas |
| MySQL Server | Porcentaje de conexiones anuladas |
| MySQL Server | Porcentaje de uso de conexión |
| MySQL Server | Uso de espacio en disco en bytes |
| MySQL Server | Porcentaje de análisis de tabla completa |
| MySQL Server | Porcentaje de aciertos del grupo de búferes InnoDB |
| MySQL Server | Porcentaje de uso del grupo de búferes InnoDB |
| MySQL Server | Porcentaje de uso del grupo de búferes InnoDB |
| MySQL Server | Porcentaje de aciertos de la caché de claves |
| MySQL Server | Porcentaje de uso de la caché de claves |
| MySQL Server | Porcentaje de escritura de la caché de claves |
| MySQL Server | Porcentaje de aciertos de la caché de consultas |
| MySQL Server | Porcentaje de eliminaciones de la caché de consultas |
| MySQL Server | Porcentaje de uso de la caché de consultas |
| MySQL Server | Porcentaje de aciertos de la caché de tablas |
| MySQL Server | Porcentaje de uso de la caché de tablas |
| MySQL Server | Porcentaje de contención de bloqueo de tablas |

## <a name="apache-http-server"></a>Servidor HTTP de Apache 
Si se detecta en el equipo un servidor HTTP de Apache cuando se instala la agrupación de omsagent, se instalará automáticamente un proveedor de supervisión de rendimiento para el servidor HTTP de Apache. Este proveedor se basa en un módulo de Apache que se tiene que cargar en el servidor HTTP de Apache para tener acceso a los datos de rendimiento. Puede cargar el módulo con el comando siguiente:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para cargar el módulo de supervisión de Apache, ejecute el siguiente comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definir contadores de rendimiento

Una vez configurado el agente de OMS para Linux de modo que envíe datos a Log Analytics, debe configurar los contadores de rendimiento para que recopilen.  Use el procedimiento descrito en [Orígenes de datos de rendimiento de Windows y Linux en Log Analytics](log-analytics-data-sources-windows-events.md) con los contadores de la tabla siguiente.

| Nombre de objeto | Nombre del contador |
|:--|:--|
| Servidor HTTP de Apache | Trabajadores ocupados |
| Servidor HTTP de Apache | Trabajadores inactivos |
| Servidor HTTP de Apache | Porcentaje de trabajadores ocupados |
| Servidor HTTP de Apache | Porcentaje total de CPU |
| Host virtual de Apache | Errores por minuto, cliente |
| Host virtual de Apache | Errores por minuto, servidor |
| Host virtual de Apache | KB por solicitud |
| Host virtual de Apache | KB de solicitudes por segundo |
| Host virtual de Apache | Solicitudes por segundo |



## <a name="next-steps"></a>Pasos siguientes
* [Recopilar contadores de rendimiento](log-analytics-data-sources-performance-counters.md) en agentes de Linux.
* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de las soluciones y los orígenes de datos. 
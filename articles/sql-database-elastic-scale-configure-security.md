<properties title="Elastic Scale Security Configurations" pageTitle="Configuraciones de seguridad de Escalado elástico" description="Seguridad de los servicios de combinación dividida mediante la escala elástica para la base de datos de SQL de Azure" metaKeywords="Elastic Scale Security Configurations, Azure SQL Database sharding, elastic scale " services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Configuraciones de seguridad de Escalado elástico  

Escalado elástico de Base de datos SQL de Microsoft Azure incluye un servicio autohospedado. La distribución incluye un archivo de configuración de servicio que contiene ajustes relacionados con la seguridad que se deben configurar.

1. [Configuración de certificados][] 
2. [Direcciones IP permitidas][]
3. [Prevención de la denegación de servicio][]
4. [Otras consideraciones de seguridad][]

## <a name="configuring-certificates"></a>Configuring Certificates

Los certificados se configuran de dos maneras. 

1. [Configuración del certificado SSL][]
2. [Configuración de los certificados de cliente][] 

## <a name="obtain-certificates"></a>Obtención de certificados

Los certificados se pueden obtener de Entidades de certificación (CA) públicas o desde el [Servicio de certificados de Windows](http://msdn.microsoft.com/es-es/library/windows/desktop/aa376539.aspx). Estos son los métodos preferidos para obtener certificados.

Si esas opciones no están disponibles, puede generar **certificados autofirmados**.
 
## <a name="tools"></a>Herramientas para generar certificados

* [makecert.exe](http://msdn.microsoft.com/es-es/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/es-es/library/windows/hardware/ff550672.aspx)

###Ejecución de las herramientas

* Desde un Símbolo del sistema para desarrolladores para Visual Studio, consulte [Símbolo del sistema de Visual Studio](http://msdn.microsoft.com/es-es/library/ms229859.aspx) 

    Si está instalado, vaya a:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obtenga el WDK en [Windows 8.1: descarga de kits y herramientas](http://msdn.microsoft.com/en-US/windows/hardware/gg454513#drivers)

##    <a name="to-configure-ssl-cert"></a>Configuración del certificado SSL
Se requiere un certificado SSL para cifrar la comunicación y autenticar el servidor. Elija el escenario más aplicable entre los tres que aparecen a continuación y ejecute todos sus pasos:

###Creación de un nuevo certificado autofirmado

1.    [Creación de un certificado autofirmado][]
2.    [Creación de un archivo PFX para certificado SSL autofirmado][]
3.    [Carga de certificado SSL al servicio en la nube][]
4.    [Actualización del certificado SSL en un archivo de configuración del servicio][]
5.    [Importación de la Entidad de certificación SSL][]

#### Utilización de un certificado existente desde el almacén de certificados
1. [Exportación del certificado SSL desde el almacén de certificados][]
2. [Carga de certificado SSL al servicio en la nube][]
3. [Actualización del certificado SSL en un archivo de configuración del servicio][]

#### Utilización de un certificado existente en un archivo PFX

1. [Carga de certificado SSL al servicio en la nube][]
2. [Actualización del certificado SSL en un archivo de configuración del servicio][]

## <a name="configuring-client-certs"></a>Configuración de certificados de cliente
Se requieren certificados de cliente para autenticar solicitudes al servicio. Elija el escenario más aplicable entre los tres que aparecen a continuación y ejecute todos sus pasos:

###Desactivación de los certificados de cliente
1.    [Desactivación de la autenticación basada en certificado de cliente][]

###Emisión de nuevos certificados de cliente autofirmados
1.    [Creación de una Entidad de certificación autofirmada][]
2.    [Carga de un certificado de Entidad de certificación al servicio en la nube][]
3.    [Actualización del certificado de Entidad de certificación en un archivo de configuración del servicio][]
4.    [Emisión de certificados de cliente][]
5.    [Creación de archivos PFX para certificados de cliente][]
6.    [Importación de certificado de cliente][]
7.    [Copia de las huellas digitales de certificados de cliente][]
8.    [Configuración de clientes autorizados en el archivo de configuración de servicio][]

###Uso de certificados de cliente existentes
1.    [Búsqueda de clave pública de Entidad de certificación][]
2.    [Carga de un certificado de Entidad de certificación al servicio en la nube][]
3.    [Actualización del certificado de Entidad de certificación en un archivo de configuración del servicio][]
4.    [Copia de las huellas digitales de certificados de cliente][]
5.    [Configuración de clientes autorizados en el archivo de configuración de servicio][]
6.    [Configuración de la comprobación de revocación de certificado de cliente][]

## <a name="allowed-ip-addresses"></a>Allowed IP Addresses

Access to the service endpoints can be restricted to specific ranges of IP addresses.
 
## The Default Configuration

The default configuration denies all access to the HTTP endpoint. This is the recommended setting, since the requests to these endpoints may carry sensitive information like database credentials.
The default configuration allows all access to the HTTPS endpoint. This setting may be restricted further.

### Changing the Configuration

The group of access control rules that apply to and endpoint are configured in the **<EndpointAcls>** section in the **service configuration file**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

The rules in an access control group are configured in a <AccessControl name=""> section of the service configuration file. 

The format is explained in Network Access Control Lists documentation.
For example, to allow only IPs in the range 100.100.0.0 to 100.100.255.255 to access the HTTPS endpoint, the rules would look like this:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name = "denial-of-service-prevention"></a>Prevención de la denegación de servicio

Existen dos mecanismos distintos compatibles para detectar y evitar los ataques de denegación de servicio:

*    Restringir la cantidad de solicitudes simultáneas por host remoto (desactivado de manera predeterminada)
*    Restringir la velocidad de acceso por host remoto (activado de manera predeterminada)

Estos mecanismos se basan en las características más documentadas en Seguridad de IP dinámica en IIS. Cuando cambie esta configuración, tenga cuidado con los siguientes factores:

* El comportamiento de los servidores proxy y los dispositivos de traducción de direcciones de red sobre la información de host remoto
* Se considera cada solicitud a cualquier recurso en el rol web (por ejemplo, carga de scripts, imágenes, etc.)

## Restricción de la cantidad de acceso simultáneos

Los ajustes que configuran este comportamiento son:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Change DynamicIpRestrictionDenyByConcurrentRequests to true to enable this protection.

## Restricción de la velocidad del acceso

Los ajustes que configuran este comportamiento son:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## Configuración de la respuesta a una solicitud denegada

Los siguientes ajustes configuran la respuesta a una solicitud denegada:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Consulte la documentación para Seguridad de IP dinámica en IIS para otros valores compatibles.

## Operaciones para configurar certificados de servicio
Este tema es solo para referencia. Siga los pasos de configuración descritos en:

* Configuración del certificado SSL
* Configuración de certificados de cliente

## <a name = "create-self-signed-cert"></a>Creación de un certificado autofirmado
Ejecute:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Personalizar:

*    -n con la dirección URL de servicio. Caracteres comodín ("CN=*.cloudapp.net") y nombres alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") son compatibles.
*    -e con la fecha de caducidad del certificado
Cree una contraseña segura y especifíquela cuando se le solicite.

## <a name="create-pfx-for-self-signed-cert"></a>Creación de un archivo PFX para certificado SSL autofirmado

Ejecute:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:
* Sí, exportar la clave privada
* Exportar todas las propiedades extendidas

## <a name="export-ssl-from-store"></a>Exportación del certificado SSL desde el almacén de certificados

* Busque el certificado
* Haga clic en Acciones -> Todas las tareas -> Exportar...
* Exporte el certificado a un archivo .PFX con estas opciones:
    * Sí, exportar la clave privada
    * Incluir todos los certificados en la ruta de certificación si es posible
    *Exportar todas las propiedades extendidas

## <a name="upload-ssl"></a>Carga de certificado SSL al servicio en la nube

Cargue el certificado con el archivo .PFX existente o generado con el par de claves SSL:

* Escriba la contraseña para proteger la información de clave privada

## <a name="update-ssl-in-csfg"></a>Actualización del certificado SSL en un archivo de configuración del servicio

Actualice el valor de huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado al servicio en la nube:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-ca"></a>Importación de la Entidad de certificación SSL

Siga estos pasos en todas las cuentas/máquinas que se comunicarán con el servicio:

* Haga doble clic en el archivo .CER en el Explorador de Windows
* En el cuadro de diálogo Certificado, haga clic en Instalar certificado...
* Importe el certificado al almacén de Entidades de certificación de raíz de confianza

## <a name="turn-off-client-cert"></a>Desactivación de la autenticación basada en certificado de cliente

Solo se admite la autenticación basada en certificado de cliente y deshabilitarla permitirá el acceso público a los extremos del servicio, a menos que existan otros mecanismos (por ejemplo, Red virtual de Microsoft Azure).

Cambie esta configuración a falso en el archivo de configuración del servicio para deshabilitar la característica:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

A continuación, copie la misma huella digital del certificado SSL en la configuración del certificado de CA:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-self-signed-ca"></a>Creación de una Entidad de certificación autofirmada
Ejecute los siguientes pasos para crear un certificado autofirmado que actúe como Entidad de certificación:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Para personalizarlo

*    -e con la fecha de caducidad de la certificación


## <a name="find-ca-public-key"></a>Búsqueda de clave pública de Entidad de certificación

Una Entidad de certificación de la confianza del servicio debe haber emitido todos los certificados de cliente. Encuentre la clave pública para la Entidad de certificación que emitió los certificados de cliente que se van a usar para autenticación a fin de cargarla al servicio en la nube.

Si el archivo con la clave pública no está disponible, expórtelo desde el almacén de certificados:

* Busque el certificado
    * Busque un certificado de cliente emitido por la misma Entidad de certificación
* Haga doble clic en el certificado
* Seleccione la pestaña Ruta de certificación en el cuadro de diálogo Certificado
* Haga doble clic en la entrada de CA de la ruta 
* Tome notas de las propiedades del certificado
* Cierre el cuadro de diálogo Certificado
* Busque el certificado
    * Busque la CA anotada anteriormente
* Haga clic en Acciones -> Todas las tareas -> Exportar...
* Exporte el certificado a un archivo .CER con estas opciones:
    * No, no exportar la clave privada
    * Incluir todos los certificados en la ruta de certificación si es posible
    * Exportar todas las propiedades extendidas

## <a name="upload-ca-cert"></a>Carga de un certificado de Entidad de certificación al servicio en la nube

Cargue el certificado con el archivo .CER existente o generado con la clave pública de CA.

## <a name="update-ca-in-csft"></a>Actualización del certificado de Entidad de certificación en un archivo de configuración del servicio

Actualice el valor de huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado al servicio en la nube:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Actualice el valor de la siguiente configuración con la misma huella digital:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certs"></a>Emisión de certificados de cliente

Cada persona autorizada a tener acceso al servicio debe tener un certificado de cliente emitido para su uso exclusivo y debe elegir su propia contraseña segura para proteger su clave privada. 

Los siguientes pasos se deben ejecutar en la misma máquina donde se generó y almacenó el certificado de CA autofirmado:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalización:

* -n con un identificador para el cliente que se autenticará con este certificado
* -e con la fecha de caducidad del certificado
* MyID.pvk y MyID.cer con nombres de archivo únicos para este certificado de cliente

Este comando solicitará que se cree una contraseña y se use una vez. Utilice una contraseña segura.

## <a name="create-pfx-files"></a>Creación de archivos PFX para certificados de cliente

Para cada certificado de cliente generado, ejecute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalización:

    •    MyID.pvk y MyID.cer con el nombre de archivo para el certificado de cliente

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:

* Sí, exportar la clave privada
* Exportar todas las propiedades extendidas
* La persona para quien se emite este certificado debe elegir la contraseña de exportación

## <a name="import-client-cert"></a>Importación de certificado de cliente

Cada persona para quien se ha emitido un certificado de cliente debe importar el par de claves en las máquinas que usará para comunicarse con el servicio:

* Haga doble clic en el archivo .PFX en el Explorador de Windows
* Importe el certificado al almacén personal con al menos esta opción:
    * Incluir todas las propiedades extendidas comprobadas

## <a name=copy-client-cert"> </a> Copia de las huellas digitales de certificados de cliente
Cada usuario para el que se ha emitido un certificado de cliente debe seguir estos pasos para obtener la huella digital de su certificado, el que se agregará al archivo de configuración de servicio:
* Ejecute certmgr.exe
* Seleccione la pestaña Personal
* Haga doble clic en el certificado de cliente que se usará para la autenticación
* En el cuadro de diálogo Certificado que se abre, seleccione la pestaña Detalles
* Asegúrese de que Mostrar esté mostrando Todo
* Seleccione el campo denominado Huella digital en la lista
* Copie el valor de la huella digital
** Elimine caracteres Unicode no visibles antes del primer dígito
** Elimine todos los espacios

## <a name="configure-allowed-client"></a>Configuración de clientes autorizados en el archivo de configuración de servicio

Actualice el valor de los siguientes ajustes en el archivo de configuración de servicio con una lista delimitada por comas de las huellas digitales de los certificados de cliente que tienen permitido el acceso al servicio:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-revocation"></a>Configuración de la comprobación de revocación de certificado de cliente

La configuración predeterminada no se comprueba con la Entidad de certificación para el estado de revocación de certificado de cliente. Para activar las comprobaciones, si la Entidad de certificación que emitió los certificados de cliente admite dichas comprobaciones, cambie la siguiente configuración por uno de los valores definidos en la enumeración X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## Operaciones comunes de certificado

•    Configuración del certificado SSL
•    Configuración de certificados de cliente

## Busque el certificado

Siga estos pasos:

1. Ejecute mmc.exe.
2. Archivo -> Agregar o quitar complemento...
3. Seleccione Certificados
4. Haga clic en Agregar
5. Elija la ubicación del almacén de certificados
6. Haga clic en Finalizar
7. Click OK
8. Expanda Certificados
9. Expanda el almacén de certificados
10. Expanda el nodo secundario Certificado
11. Seleccione un certificado de la lista que aparece a la derecha

## Exportación de certificado
En el asistente para exportar certificados:

1. Haga clic en Siguiente
2. Seleccione Sí, exportar la clave privada
3. Haga clic en Siguiente
4. Seleccione el formato deseado del archivo de salida
5. Marque las opciones deseadas
6. Marque Contraseña
7. Escriba una contraseña segura y confírmela
8. Haga clic en Siguiente
9. Escriba o busque un nombre de archivo donde almacenar el certificado (use una extensión .PFX)
10. Haga clic en Siguiente
11. Haga clic en Finalizar
12. Click OK 

## Importación de certificado

En el asistente para importar certificados:

1. Seleccione la ubicación del almacén.
    1.     Usuario actual si solo los procesos en ejecución bajo el usuario actual tendrán acceso al servicio
    2.     Máquina local si otros procesos de este equipo tendrán acceso al servicio
2. Haga clic en Siguiente
3. Si se importa desde un archivo, confirme la ruta del archivo
4. Si se importa un archivo .PFX:
    1.     Escriba la contraseña que protege la clave privada
    2.     Seleccione las opciones de importación
5.     Seleccione Colocar certificados en el siguiente almacén
6.     Haga clic en Examinar
7.     Seleccione el almacén deseado
8.     Haga clic en Finalizar
    1.     Si se eligió el almacén de Entidad de certificación de raíz de confianza, haga clic en Sí
9.     Haga clic en Aceptar en todas las ventanas de diálogo

## <a name="upload-certificate"></a>Carga del certificado

En el [Portal de administración de Azure](http://manage.windowsazure.com/)

1. Seleccione Servicios en la nube
2. Seleccione el servicio en la nube
3. Haga clic en Certificados en el menú superior
4. Haga clic en Cargar en la barra inferior
5. Seleccione el archivo de certificado
6. Si es un archivo .PFX, escriba la contraseña de la clave privada
7. Una vez realizado, copie la huella digital del certificado desde la entrada nueva en la lista

# <a name="other-security"></a> Otras consideraciones de seguridad
 
La configuración SSL descrita en este documento cifra la comunicación entre el servicio y sus clientes cuando se usa el extremo HTTPS. Esto es importante, porque las credenciales para el acceso a la base de datos y potencialmente a otra información confidencial están contenidas en la comunicación. Sin embargo, tenga en cuenta que el servicio persiste en el estado interno, incluidas credenciales, en sus tablas internas en la base de datos SQL de Microsoft Azure que ha proporcionado para el almacenamiento de metadatos en la suscripción de Microsoft Azure. Esa base de datos se definió como parte de los siguientes ajustes en el archivo de configuración de servicio (archivo .CSCFG): 

    <Setting name="ElasticScaleMetadata" value="Server=..." />

Los datos almacenados en esta base de datos no se cifran. Para evitar divulgar las credenciales u otra información confidencial de las solicitudes de servicio, proteja esta base de datos y mantenga seguro el acceso a ella en cada momento. Además, asegúrese de que tanto el rol web como el rol de trabajo de sus implementaciones de servicio están actualizadas y protegidas mientras ambas tengan acceso a la base de datos de metadatos. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

[Configuración de certificados]:#configuring-certificates
[Direcciones IP permitidas]:#allowed-ip-addresses
[Configuración de los certificados de cliente]:#configuring-client-certs
[Creación de un certificado autofirmado]:#create-self-signed-cert
[Creación de un archivo PFX para certificado SSL autofirmado]:#create-pfx-for-self-signed-cert
[Carga de certificado SSL al servicio en la nube]: #upload-ssl
[Actualización del certificado SSL en un archivo de configuración del servicio]: #update-ssl-in-csfg
[Importación de la Entidad de certificación SSL]: "import-ssl-ca"
[Exportación del certificado SSL desde el almacén de certificados]: #export-ssl-from-store
[Desactivación de la autenticación basada en certificado de cliente]: #turn-off-client-cert
[Creación de una Entidad de certificación autofirmada]:#create-self-signed-ca
[Copia de las huellas digitales de certificados de cliente]:#copy-client-cert
[Carga de un certificado de Entidad de certificación al servicio en la nube]:#upload-ca-cert
[Actualización del certificado de Entidad de certificación en un archivo de configuración del servicio]:#update-ca-in-csft
[Emisión de certificados de cliente]:#issue-client-certs
[Creación de archivos PFX para certificados de cliente]:#create-pfx-files
[Importación de certificado de cliente]:#import-client-cert
[Configuración de clientes autorizados en el archivo de configuración de servicio]:#configure-allowed-client
[Búsqueda de clave pública de Entidad de certificación]:#find-ca-public-key
[Configuración de la comprobación de revocación de certificado de cliente]:#configure-client-revocation
[Prevención de la denegación de servicio]:#denial-of-service-prevention
[Obtención de certificados]:#obtain-certificates
[Herramientas para generar certificados]:#tools
[Configuración del certificado SSL]:#to-configure-ssl-cert
[Otras consideraciones de seguridad]:#other-security 
[Carga del certificado]:#upload-certificate

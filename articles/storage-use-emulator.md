<properties 
	pageTitle="Uso del emulador de almacenamiento de Azure para desarrollo y pruebas" 
	description="Aprenda a usar el emulador de almacenamiento de Azure para desarrollo y pruebas." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Uso del emulador de almacenamiento de Azure para desarrollo y pruebas

## Información general
El emulador de almacenamiento de Microsoft Azure proporciona un entorno local que emula los servicios de Azure de blob, cola y tabla para fines de desarrollo. Mediante el emulador de almacenamiento, puede probar la aplicación en los servicios de almacenamiento local sin ningún gasto.

> [AZURE.NOTE] El emulador de almacenamiento se encuentra disponible con el SDK de Microsoft Azure. También puede instalar el emulador de almacenamiento como un paquete independiente.
Para configurar el emulador de almacenamiento, debe tener privilegios de administrador en el equipo. 
> Tenga en cuenta que no se garantiza que los datos que se crean en una versión del emulador de almacenamiento estén disponibles cuando se utilice una versión diferente. Si necesita conservar los datos a largo plazo, es recomendable que almacene esos datos en una cuenta de almacenamiento de Azure y no en el mismo emulador de almacenamiento.
 
Existen algunas diferencias entre el emulador de almacenamiento y los servicios de almacenamiento de Azure. Para obtener más información sobre estas diferencias, consulte [Diferencias entre el Emulador de almacenamiento y los Servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/gg433135.aspx).

El emulador de almacenamiento usa una instancia de Microsoft(r) SQL Server(tm), así como el sistema de archivos local para emular los servicios de almacenamiento de Azure. El emulador de almacenamiento está configurado de forma predeterminada para una base de datos en Microsoft(r) SQL Server(tm) 2012 Express LocalDB. Puede instalar SQL Server Management Studio Express para administrar la instalación de LocalDB. El emulador de almacenamiento se conecta a SQL Server o LocalDB mediante la autenticación de Windows. Puede configurar el emulador de almacenamiento para tener acceso a una instancia local de SQL Server en lugar de una de LocalDB, mediante la Referencia de la herramienta de línea de comandos del emulador de almacenamiento.

## Solicitudes autenticadas

El emulador de almacenamiento es compatible con una sola cuenta fija y una clave de autenticación ya conocida. Esta cuenta y clave son las únicas credenciales que se admiten para su uso con el emulador. Son las siguientes:

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtrKBHBeksoGMGw==
    
> [AZURE.NOTE] La clave de autenticación admitida por el emulador de almacenamiento está pensada para comprobar únicamente la funcionalidad de su código de autenticación de cliente. No responde a ningún propósito de seguridad. A parte, no puede utilizar la cuenta de almacenamiento y la clave de producción con el emulador. Asimismo, se debe tener en cuenta que no se puede utilizar la cuenta de desarrollo con datos de producción.
 

## Iniciar e inicializar el emulador de almacenamiento
Para iniciar el emulador de almacenamiento de Azure, seleccione el botón Inicio o pulse la tecla Windows. Comience a escribir Emulador de almacenamiento de Azure y seleccione Emulador de almacenamiento de Azure en la lista de aplicaciones. 

Otra opción es, si el emulador de proceso de Azure ya está en ejecución, puede iniciar el emulador de almacenamiento haciendo clic con el botón secundario en el icono de la bandeja del sistema y seleccionar Iniciar el emulador de almacenamiento Si desea más información sobre cómo ejecutar el emulador de proceso, consulte [Ejecutar una aplicación de Azure en el emulador de proceso](https://msdn.microsoft.com/library/azure/hh403990.aspx).

Cuando se inicie el emulador de almacenamiento, aparecerá una línea de comandos. Puede utilizar esta línea de comandos para iniciar y detener el emulador de almacenamiento, así como borrar datos, obtener el estado actual e inicializar el emulador. Para obtener más información consulte la [Referencia de la herramienta de línea de comandos del emulador de almacenamiento](https://msdn.microsoft.com/library/azure/gg433005.aspx).

Cuando se cierra la ventana de la línea de comandos, el emulador de almacenamiento continuará ejecutándose. Para abrir de nuevo la línea de comandos, siga los pasos anteriores como si fuera a iniciar el emulador de almacenamiento.


Al ejecutar por primera vez el emulador de almacenamiento, el entorno de almacenamiento local se inicia automáticamente. Puede usar la herramienta de la línea de comandos del emulador de almacenamiento para señalar a una instancia de base de datos diferente o para volver a inicializar la base de datos existente. El proceso de inicialización crea una base de datos en LocalDB y se reserva puertos HTTP para cada servicio de almacenamiento local. Para este paso es necesario disponer de privilegios de administrador. Para obtener más información, consulte la [Referencia de la herramienta de línea de comandos del emulador de almacenamiento](https://msdn.microsoft.com/library/azure/gg433005.aspx).

## Acerca de los URI de servicio de almacenamiento

La forma de dirigir un recurso en los servicios de almacenamiento de Azure varía en función de si el recurso reside en Azure o en los servicios del emulador de almacenamiento. Esto es, se utiliza un esquema de URI para dirigir recursos de almacenamiento en Azure y otro esquema de URI para dirigir recursos de almacenamiento en el emulador de almacenamiento. Esta diferencia es debida al hecho de que el equipo local no lleva a cabo la resolución de nombres de dominio. Ambos esquemas de URI incluyen siempre el nombre de la cuenta y la dirección del recurso que se solicita.

## Dirigir los recursos de almacenamiento de Azure en la nube

En el esquema de URI para direccionar recursos de almacenamiento en Azure, el nombre de cuenta forma parte del nombre de host del URI y el recurso que se redirige forma parte de la ruta de acceso del URI. El esquema de redireccionamiento básico se utiliza para obtener acceso a los recursos de almacenamiento:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>


`<account-name>` es el nombre de su cuenta de almacenamiento. `<service-name>` es el nombre del servicio al que se obtiene acceso y `<resource-path>` es la ruta de acceso al recurso solicitado. La lista siguiente muestra el esquema de URI para cada uno de los servicios de almacenamiento:

	Blob Service: <http|https>://<account-name>.blob.core.windows.net/<resource-path>
	Queue Service: <http|https>://<account-name>.queue.core.windows.net/<resource-path>
	Table Service: <http|https>://<account-name>.table.core.windows.net/<resource-path>

Por ejemplo, la siguiente dirección se puede usar para obtener acceso a un blob en la nube:
    
    http://myaccount.blob.core.windows.net/mycontainer/myblob.txt

> [AZURE.NOTE] También puede asociar un nombre de dominio personalizado con un extremo de almacenamiento de Blob en la nube y utilizar ese nombre de dominio personalizado para dirigirse a blobs y contenedores. Consulte 
 
## Direccionar los recursos locales de almacenamiento de Azure en el emulador de almacenamiento

En el emulador de almacenamiento, dado que el equipo local no lleva a cabo la resolución de nombres de dominio, el nombre de la cuenta forma parte de la ruta de acceso del URI. El esquema URI de un recurso que se ejecuta en el emulador de almacenamiento tiene este formato:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

El siguiente formato se usa para direccionar los recursos que se ejecuten en el emulador de almacenamiento:

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

Por ejemplo, la siguiente dirección se puede usar para obtener acceso a un blob en el emulador de almacenamiento:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

> [AZURE.NOTE] HTTPS no es un protocolo permitido para redireccionar a los recursos de almacenamiento local.
 
## Direccionar la cuenta secundaria con RA-GRS
A partir de la versión 3.1, la cuenta del emulador de almacenamiento admite la replicación con redundancia geográfica con acceso de lectura (RA-GRS). Para los recursos de almacenamiento en la nube y en el emulador local, puedes obtener acceso a la ubicación de la cuenta secundaria si anexa -secondary al nombre de la cuenta. Por ejemplo, la siguiente dirección se puede usar para obtener acceso a un blob usando la cuenta secundaria de solo lectura en el emulador de almacenamiento:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Para el acceso mediante programación a la cuenta secundaria con el emulador de almacenamiento, usa la biblioteca de cliente de almacenamiento para la versión 3.2 de .NET o una versión posterior. Consulta Storage Client Library Reference para obtener más información.
 
## Inicializar el emulador de almacenamiento con la herramienta de línea de comandos
La herramienta de la línea de comandos del emulador de almacenamiento se puede usar para inicializar el emulador de almacenamiento para que señale a una instancia de base de datos distinta a la predeterminada. Si desea utilizar la instancia de base de datos LocalDB predeterminada, no es necesario ejecutar un paso de inicialización aparte.

Esta herramienta se instala de manera predeterminada en el directorio C:\Archivos de programa(x86)\Microsoft SDKs\Azure\Storage Emulator\. La inicialización se ejecuta automáticamente la primera vez que se inicia el emulador.

> [AZURE.NOTE] Necesita tener privilegios de administrador para ejecutar el comando `init` que se describe a continuación.

1. Haga clic en el botón **Inicio** o pulse la tecla **Windows**. Empiece a escribir `Emulador de almacenamiento de Azure` y seleccione este texto cuando aparezca. Se abrirá una ventana de símbolo del sistema.


2. En la ventana del símbolo del sistema, escriba los comandos siguientes donde `<SQLServerInstance>` es el nombre de la instancia de SQL Server. Para utilizar LocalDb, especifique `(localdb)\v11.0` como instancia de SQL Server.

    	WAStorageEmulator init /sqlInstance <SQLServerInstance> 
    
También puede usar el siguiente comando, el cual indicará al emulador que utilice la instancia predeterminada de SQL Server:

    WAStorageEmulator init /server .\\ 

O bien puede utilizar el comando siguiente para reiniciar la base de datos:

    WAStorageEmulator init /forceCreate 

## Referencia de la herramienta de línea de comandos del emulador de almacenamiento

A partir de la versión 3.0, al iniciar el emulador de almacenamiento, se abrirá una ventana con el símbolo del sistema. Utilice el símbolo del sistema para iniciar y detener el emulador, así como para consultar el estado y realizar otras operaciones.

> [AZURE.NOTE] Si tiene instalado el emulador de proceso, aparecerá un icono de la bandeja del sistema al iniciar el emulador de almacenamiento. Haga clic con el botón secundario en el icono para abrir un menú que ofrece en forma de gráficos las opciones de iniciar y detener el emulador de almacenamiento.

### Sintaxis de la línea de comandos

	WAStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### Opciones
Para ver la lista de opciones, escriba `/help` en el símbolo del sistema.

## Pasos siguientes
- [Referencia de la herramienta de línea de comandos del emulador de almacenamiento](https://msdn.microsoft.com/library/azure/gg433005.aspx)
- [Diferencias entre el emulador de almacenamiento y los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/gg433135.aspx)
- [Notas de la versión del emulador de almacenamiento](https://msdn.microsoft.com/library/azure/dn683879.aspx) 

<!--HONumber=47-->

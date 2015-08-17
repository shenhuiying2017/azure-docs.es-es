<properties
   pageTitle="Notas de la versión del Catálogo de datos de Azure"
   description="Notas de la versión del 13 de julio de 2015 del Catálogo de datos de Azure"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Notas del 13 de julio de 2015 del Catálogo de datos de Azure

## Registro y conexión a Oracle Database

Para conectarse a orígenes de datos de Oracle Database, los usuarios deben tener instalados los controladores de Oracle correctos que coinciden con el valor de bits (32 bits o 64 bits) del software que se va a usar.

-	Al registrar orígenes de datos de Oracle en un equipo con Windows de 32 bits, se usarán controladores de Oracle de 32 bits
-	Al registrar orígenes de datos de Oracle en un equipo con Windows de 64 bits, se usarán los controladores de Oracle de 64 bits
-	Para conectarse a orígenes de datos de Oracle mediante Excel en un equipo con la versión de 32 bits de Microsoft Office, incluida en Windows de 64 bits, se usarán los controladores de Oracle de 32 bits
-	Para conectarse a orígenes de datos de Oracle mediante Excel en un equipo con la versión de 64 bits de Microsoft Office, se usarán los controladores de Oracle de 64 bits

## Registro y conexión a SQL Server Reporting Services

La compatibilidad con orígenes de datos de SQL Server Reporting Services (SSRS) en la versión inicial de vista previa del Catálogo de datos de Azure está limitada a tan solo los servidores de modo nativo. La compatibilidad con SSRS en modo SharePoint se agregará en una versión posterior.

## Apertura de recursos de datos en Excel

Al abrir recursos de datos en Microsoft Excel desde el portal del Catálogo de datos de Azure, es posible que a los usuarios se les muestre un cuadro de diálogo de **Aviso de seguridad de Microsoft Excel**. Este es el comportamiento estándar esperado y los usuarios pueden seleccionar **Habilitar** para continuar.

Para obtener más información, consulte [Habilitar o deshabilitar las alertas de seguridad acerca de vínculos y archivos de sitios web sospechosos](https://support.office.com/es-es/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

## Faltan las columnas BLOB y UDT en las vistas previas

Al registrar tablas y vistas que contienen columnas de objeto binario grande (BLOB) y de tipo de datos definidos por el usuario (UDT) y al seleccionar la inclusión de una vista previa de los recursos de datos, estas columnas no se incluirán en la vista previa.

## Configuración de proxy y directiva, y registro de orígenes de datos

Es posible que los usuarios se encuentren en una situación en la que puedan iniciar sesión en el portal del Catálogo de datos de Azure, pero cuando intenten iniciar sesión en la herramienta de registro de orígenes de datos se encuentren un mensaje de error que les impida iniciar sesión.

Hay dos causas posibles para este comportamiento del problema:

**Causa 1: configuración de servicios de federación de Active Directory** La herramienta de registro de origen de datos usa la autenticación de formularios para validar los inicios de sesión de usuario en Active Directory. Para iniciar sesión correctamente, la autenticación de formularios debe ser habilitada en la directiva de autenticación global por un administrador de Active Directory.

En algunas situaciones, es posible que este comportamiento incorrecto ocurra solamente cuando el usuario está en la red de la compañía o solo cuando el usuario se conecta desde fuera de la red de empresa. La directiva de autenticación global permite habilitar los métodos de autenticación de forma independiente para las conexiones de extranet y de intranet. Es posible que se produzcan errores de inicio de sesión si no está habilitada la autenticación de formularios para la red desde la que se conecta el usuario.

Para obtener más información, consulte [Configuración de la autenticación basada en formularios de intranet para dispositivos que no son compatibles con WIA](https://technet.microsoft.com/library/dn727110.aspx).

**Causa 2: configuración del proxy de red** Si la red corporativa usa un servidor proxy, es posible que la herramienta de registro no pueda conectarse a Azure Active Directory a través del proxy. Los usuarios pueden asegurarse de que la herramienta de registro se conecta mediante la edición del archivo de configuración de la herramienta, agregando esta sección al archivo:


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


Para localizar el archivo RegistrationTool.exe.config, inicie la herramienta de registro y, a continuación, abra la utilidad Administrador de tareas de Windows. En la pestaña Detalles del Administrador de tareas, haga clic con el botón derecho en RegistrationTool.exe y elija Abrir ubicación de archivo en el menú emergente.

<!---HONumber=August15_HO6-->
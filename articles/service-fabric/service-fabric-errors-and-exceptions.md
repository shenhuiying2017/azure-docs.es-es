<properties
   pageTitle="Excepciones de FabricClient frecuentes | Microsoft Azure"
   description="Describe las excepciones y errores frecuentes que pueden producir las API FabricClient al realizar operaciones de administración de aplicaciones y clústeres."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/11/2016"
   ms.author="ryanwi"/>

# Excepciones y errores frecuentes cuando se trabaja con las API FabricClient
Las API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) permiten a los administradores de clústeres y aplicaciones realizar tareas administrativas en un clúster, servicio o aplicación de Service Fabric. Por ejemplo, la implementación, actualización o eliminación de aplicaciones, la comprobación del estado de un clúster o la prueba de un servicio. Los desarrolladores de aplicaciones y los administradores de clústeres pueden usar las API FabricClient para desarrollar herramientas para la administración de aplicaciones y clústeres de Service Fabric.

Hay muchos tipos diferentes de operaciones que pueden realizarse mediante FabricClient. Cada método puede producir excepciones para errores debido a una entrada incorrecta, errores en tiempo de ejecución o problemas de infraestructura transitorios. Consulte la documentación de referencia de la API para buscar las excepciones que produce un método específico. Sin embargo, existen algunas excepciones que se pueden producir por muchas API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) diferentes. En la tabla siguiente se muestran las excepciones que son frecuentes en las API FabricClient.

|Excepción| Se produce cuando|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|El objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) está en un estado cerrado. Desecha el objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) que está usando y crea instancias de un nuevo objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|La operación ha agotado el tiempo de espera. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) se devuelve cuando la operación tarda más que MaxOperationTimeout en completarse.|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/es-ES/library/system.unauthorizedaccessexception.aspx)|Se genera un error de comprobación de acceso de la operación. Se devuelve E\_ACCESSDENIED.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Se generó un error en tiempo de ejecución al realizar la operación. Potencialmente, cualquier método FabricClient puede producir [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx); la propiedad [ErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indica la causa exacta de la excepción. Los códigos de error se definen en la enumeración [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx).|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|Se genera un error en la operación debido a una condición de error transitorio de algún tipo. Por ejemplo, se puede producir un error en la operación porque no se puede obtener acceso temporalmente a un cuórum de réplicas. Las excepciones transitorias corresponden a las operaciones erróneas que se pueden reintentar.|

Algunos errores [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) frecuentes que pueden devolverse en [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Error| Condición|
|---------|:-----------|
|CommunicationError|Un error de comunicación ha provocado que se produzca un error en la operación; vuelva a intentar la operación.|
|InvalidCredentialType|El tipo de credencial no es válido.|
|InvalidX509FindType|X509FindType no es válido.|
|InvalidX509StoreLocation|La ubicación del almacén X509 no es válida.|
|InvalidX509StoreName|El nombre del almacén X509 no es válido.|
|InvalidX509Thumbprint|La cadena de huella digital de certificado X509 no es válida.|
|InvalidProtectionLevel|El nivel de protección no es válido.|
|InvalidX509Store|El almacén de certificados X509 no se puede abrir.|
|InvalidSubjectName|El nombre de sujeto no es válido.|
|InvalidAllowedCommonNameList|El formato de la cadena de lista de nombres frecuente no es válido. Debe ser una lista separada por comas.|

<!---HONumber=AcomDC_0713_2016-->
<properties 
	pageTitle="Cifrado del lado de cliente para el Almacenamiento de Microsoft Azure | Microsoft Azure" 
	description="La biblioteca de cliente de Almacenamiento de Azure para la vista previa de .NET ofrece compatibilidad para el cifrado de cliente e integración con el Almacén de claves de Azure. Cifrado de cliente ofrece una seguridad máxima para las aplicaciones de Almacenamiento de Azure, ya que las claves de acceso nunca están disponibles para el servicio. El cifrado del cliente está disponible para blobs, colas y tablas." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="tamram"/>


# Cifrado del lado cliente para el Almacenamiento de Microsoft Azure (vista previa)

## Información general

Bienvenido a la [vista previa de la nueva biblioteca de cliente de Almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview). Esta biblioteca de vista previa contiene nuevas funcionalidades para ayudar a los desarrolladores a cifrar los datos de las aplicaciones de cliente antes de cargarlas en el Almacenamiento de Azure y para descifrar los datos mientras se descargan. La biblioteca de vista previa también admite la integración con el [Almacén de claves](http://azure.microsoft.com/services/key-vault/) de Azure para la administración de claves de cuenta de almacenamiento.

## ¿Por qué es recomendable usar el cifrado de cliente?

El cifrado de cliente ofrece una importante ventaja sobre el cifrado de servidor: controla totalmente las claves de acceso de la cuenta. El cifrado de cliente ofrece máxima seguridad para las aplicaciones, ya que el Almacenamiento de Azure nunca ve sus claves y nunca puede descifrar los datos. La biblioteca de vista previa está abiertamente disponible en [GitHub](https://github.com/Azure/azure-storage-net/tree/preview), para que pueda ver cómo cifra la biblioteca sus datos para asegurarse de que cumplen los estándares.

## ¿Por qué ofrecemos una biblioteca con compatibilidad con el cifrado de cliente?

Mientras que cualquier desarrollador puede cifrar sus datos en el cliente antes de cargarlos, para ello se requiere tener conocimientos de cifrado. También requiere efectuar un diseño para obtener rendimiento y seguridad. Distintos desarrolladores tendrían que diseñar su propia solución de cifrado, y puesto que cada solución sería diferente, ninguna de ellas funcionaría conjuntamente.

La biblioteca de vista previa está diseñada para:

- Implementar recomendaciones de seguridad para usted.
- Maximizar el rendimiento.
- Ofrecer facilidad de uso para escenarios comunes.
- Admitir la interoperabilidad entre idiomas. Los datos cifrados mediante la biblioteca de cliente .NET podrán ser descifrados en el futuro por las bibliotecas de cliente para los otros idiomas admitidos, incluidos Java, Node.js y C++ (y viceversa).

## ¿Qué está disponible ahora?

La biblioteca de vista previa actualmente admite el cifrado para blobs, tablas y colas mediante la técnica de sobres. El cifrado y descifrado con claves asimétricas es costoso desde el punto de vista computacional. Por lo tanto, en la técnica de sobres, los datos en sí no se cifran directamente con estas claves, pero en su lugar, se cifran mediante una clave de cifrado de contenido simétrica aleatoria. A continuación, esta clave de cifrado de contenido se cifra con una clave pública. La compatibilidad con el Almacén de claves de Azure le ayuda a administrar las claves de forma eficaz.

Usar el cifrado de cliente es sencillo. Puede especificar opciones de solicitud con la directiva de cifrado adecuad (Blob, cola o tabla) y pasarla a API de carga y descarga de datos. La biblioteca cliente cifra automáticamente los datos en el cliente cuando se cargan en el Almacenamiento de Azure y descifra los datos cuando se recuperan. Puede encontrar más detalles y códigos de ejemplo en la entrada de blog [Introducción con cifrado de cliente para el Almacenamiento de Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx).

Algunos detalles adicionales acerca del cifrado de cliente:

- **Seguridad**: los datos cifrados no son legibles, incluso si las claves de cuenta de almacenamiento de los clientes se ven comprometidas.
- **Cifrado de sobrecarga fijo**: los datos cifrados tendrán un tamaño predecible en función del tamaño original.
- **Cifrado independiente**: cada blob, entidad de tabla o mensaje de la cola almacena toda la información de cifrado en el propio objeto o en sus metadatos. El único valor externo necesario es la clave de cifrado.
- **Giro de clave**: los usuarios pueden girar las claves ellos mismos, y se admiten varias claves durante el proceso de rotación de claves.
- **Limpiar ruta de acceso de actualización**: se admitirán versiones de algoritmos de cifrado y protocolos adicionales en el futuro sin necesidad de cambios significativos en el código.
- **Compatibilidad con el cifrado de blob**:
	- **Carga completa del blob**: puede cifrar y cargar archivos, como documentos, fotos y vídeos en su totalidad.
	- **Descarga completa o basada en intervalo de blob**: puede descargar y descifrar un blob en su totalidad o en intervalos.


>[AZURE.IMPORTANT]Tenga en cuenta estos puntos importantes al utilizar la biblioteca de vista previa:
>
>- No utilice la biblioteca de vista previa para los datos de producción. En el futuro, los cambios realizados en la biblioteca afectarán a los esquemas utilizados. El descifrado de datos que se ha cifrado con la biblioteca de vista previa no está garantizado en futuras versiones.  
>- Al leer de o escribir en un blob cifrado, utilice comandos de carga completa del blob y comandos de descarga de blobs de intervalo/completos. Evite escribir en un blob cifrado mediante operaciones de protocolo, como Colocar bloque, Colocar lista de bloque, Escribir páginas o Borrar páginas; de lo contrario, puede dañar el objeto blob cifrado y que no sea legible.
>- Para las tablas, existe una restricción similar. Tenga cuidado de no actualizar propiedades cifradas sin actualizar los metadatos de cifrado.
>- Si establece los metadatos en el objeto blob cifrado, puede sobrescribir los metadatos relacionados con el cifrado necesarios para el descifrado, ya que el establecimiento de metadatos no es aditivo. Esto también es cierto para las instantáneas: evite la especificación de metadatos durante la creación de una instantánea de un blob cifrado.

## Consulte también

- [Introducción a cifrado de cliente para el Almacenamiento de Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx)  
- [Biblioteca de cliente de almacenamiento de Azure para el paquete NuGet de .NET (versión preliminar)](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview)  
- [Biblioteca de cliente de Almacenamiento de Azure para el código fuente de .NET (versión preliminar)](https://github.com/Azure/azure-storage-net/tree/preview)
 

<!---HONumber=62-->
<properties title="How to Create a DocumentDB Account" pageTitle="How to create a DocumentDB account | Azure" description="Find out how to create a DocumentDB account and choose account settings in the Azure Preview portal."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Creación de una cuenta de Base de datos de documentos

Para usar Base de datos de documentos, debe crear una cuenta en una región geográfica disponible. En este tema se describe la forma de crear una cuenta de Base de datos de documentos en el Portal de vista previa de Azure.

## Tabla de contenido

-   [Creación de una cuenta de Base de datos de documentos][]
-   [Pasos siguientes][]

## <span id="HowTo"></span></a>Creación de una cuenta de Base de datos de documentos

1.  Inicie sesión en el [Portal de vista previa de administración de Azure][].
2.  Haga clic en Nuevo -\> Cuenta de Base de datos de documentos.
    ![][]

    También puede ir a la Galería de Azure, seleccionar la categoría "Datos, almacenamiento, + copia de seguridad", elegir **Base de datos de documentos** y luego hacer clic en **Crear**.

    ![][1]

    En el cuadro **Nueva Base de datos de documentos (vista previa)**, especifique la configuración para la cuenta.

    ![][2]

    En Nombre, escriba el nombre que vaya usar en el URI para ella. Este valor se convierte en el nombre de host en el URI que se usa para direccionar la cuenta de Base de datos de documentos. La entrada de nombre solo puede contener letras minúsculas, números y el carácter '-' y debe tener entre 3 y 50 caracteres.

    *Tenga en cuenta que documents.azure.com se anexará al nombre del extremo que elija y, como resultado, se convertirá en el extremo de la cuenta de Base de datos de documentos.*

    El modo **Nivel de precios** está bloqueado, puesto que la vista previa de Base de datos de documentos admite un único nivel de precios estándar.

    *Para obtener más información, consulte los [Detalles de precios de Base de datos de documentos][]*

    El modo **Configuración opcional** se usa para especificar la capacidad inicial asignada a la cuenta. Base de datos de documentos aprovecha las unidades de capacidad para permitirle escalar su cuenta de Base de datos de documentos, donde cada unidad de capacidad incluye almacenamiento y rendimiento de base de datos reservados. De manera predeterminada, se aprovisiona una unidad de capacidad. Pero puede ajustar el número de unidades de capacidad disponibles para su cuenta en cualquier momento a través del [Portal de vista previa de administración de Azure][].

    *Para obtener detalles acerca de la capacidad y el rendimiento de la cuenta de Base de datos de documentos, consulte el artículo [Administración de capacidad y rendimiento de Base de datos de documentos][].*

    En **Grupo de recursos**, seleccione o cree un grupo de recursos para su cuenta de Base de datos de documentos. De forma predeterminada, se creará un nuevo grupo de recursos. Puede, sin embargo, optar por seleccionar un grupo de recursos existente al que agregar su cuenta de Base de datos de documentos.

    *Para obtener más información, consulte [Uso del grupo de recursos para administrar los recursos de Azure][].*

    En **Suscripción**, seleccione la suscripción a Azure que quiere usar para la cuenta de Base de datos de documentos.

    *Si su cuenta solo tiene una suscripción, se seleccionará automáticamente.*

    Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará su cuenta de Base de datos de documentos.

3.  Una vez que las nuevas opciones de cuenta de Base de datos de documentos estén configuradas, haga clic en **Crear**. La creación de la cuenta puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio.

    ![][3]

    O bien, desde el Centro de notificaciones.

    ![][4]

    ![][5]

4.  Después de crear la cuenta de Base de datos de documentos, ya se puede usar con la configuración predeterminada.

    *Tenga en cuenta que la coherencia predeterminada de la cuenta se establecerá en Sesión. Pero puede cambiar la configuración predeterminada a través del [Portal de vista previa de administración de Azure][].*

    ![][6]

5.  También puede acceder a las cuentas de Base de datos de documentos existentes desde el cuadro **Examinar**.

    ![][7]

## <span id="NextSteps"></span></a>Pasos siguientes

-   Para obtener más información sobre Base de datos de documentos, consulte la documentación correspondiente en [azure.com][]

<!--Image references-->

  [Creación de una cuenta de Base de datos de documentos]: #Howto
  [Pasos siguientes]: #NextSteps
  [Portal de vista previa de administración de Azure]: https://portal.azure.com/
  []: ./media/documentdb-create-account/ca1.png
  [1]: ./media/documentdb-create-account/ca2.png
  [2]: ./media/documentdb-create-account/ca3.png
  [Detalles de precios de Base de datos de documentos]: http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409
  [Administración de capacidad y rendimiento de Base de datos de documentos]: ../documentdb-manage/
  [Uso del grupo de recursos para administrar los recursos de Azure]: http://azure.microsoft.com/es-es/documentation/articles/azure-preview-portal-using-resource-groups/
  [3]: ./media/documentdb-create-account/ca4.png
  [4]: ./media/documentdb-create-account/ca5.png
  [5]: ./media/documentdb-create-account/ca6.png
  [6]: ./media/documentdb-create-account/ca7.png
  [7]: ./media/documentdb-create-account/ca8.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319

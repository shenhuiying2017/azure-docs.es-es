---
title: Cifrado en Azure Data Lake Store | Microsoft Docs
description: "Descripción del funcionamiento del cifrado y la rotación de claves en Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.openlocfilehash: 20444d368c568ee716ff242e33323b91ffd198eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Cifrado de datos en Azure Data Lake Store

El cifrado en Azure Data Lake Store le ayuda a proteger sus datos, implementar directivas de seguridad de empresa y satisfacer los requisitos de cumplimiento normativo. En este artículo se proporciona información general sobre el diseño y se discuten algunos de los aspectos técnicos de la implementación.

Data Lake Store admite el cifrado de datos tanto en reposo como en tránsito. En el caso de datos en reposo, Data Lake Store admite el cifrado transparente "activado de forma predeterminada". Con más detalle, esto significa:

* **Activado de forma predeterminada**: cuando se crea una cuenta de Data Lake Store, la configuración predeterminada habilita el cifrado. Por lo tanto, los datos que se almacenan en Data Lake Store siempre se cifran antes de almacenarlos en un medio persistente. Este es el comportamiento para todos los datos y no se puede cambiar después de crear una cuenta.
* **Transparente**: Data Lake Store cifra automáticamente los datos antes de guardarlos y los descifra antes de recuperarlos. Un administrador configura y administra el cifrado de cada instancia de Data Lake Store. No se realizan cambios en las API de acceso a datos. Por lo tanto, no se requiere ningún cambio en las aplicaciones y los servicios que interactúan con Data Lake Store a causa del cifrado.

Los datos en tránsito (también conocidos como datos en movimiento) también se cifran siempre en Data Lake Store. Además de que los datos se cifran antes de almacenarse en un medio persistente, también se protegen cuando están en tránsito mediante HTTPS. HTTPS es el único protocolo admitido para las interfaces de REST de Data Lake Store. En el diagrama siguiente se muestra cómo se cifran los datos en Data Lake Store:

![Diagrama de cifrado de datos en Data Lake Store](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Configuración del cifrado con Data Lake Store

El cifrado para Data Lake Store se configura durante la creación de la cuenta y siempre está habilitado de forma predeterminada. Puede administrar las claves por su cuenta o dejar que Data Lake Store las administre en su nombre (esta es la opción predeterminada).

Para más información, consulte [Introducción](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-store"></a>Funcionamiento del cifrado en Data Lake Store

A continuación se explica cómo administrar las claves de cifrado maestras y se describen los tres tipos diferentes de claves que puede usar en el cifrado de datos de Data Lake Store.

### <a name="master-encryption-keys"></a>Claves de cifrado maestras

Data Lake Store proporciona dos modos de administración de claves de cifrado maestras (MEK). Por ahora, suponga que la clave de cifrado maestra es la clave de nivel superior. Se necesita acceso a la clave de cifrado maestra para descifrar cualquier dato almacenado en Data Lake Store.

Los dos modos para administrar la clave de cifrado maestra son los siguientes:

*   Claves administradas por el servicio
*   Claves administradas por el cliente

En ambos modos, la clave de cifrado maestra está protegida al estar almacenada en Azure Key Vault. Key Vault es un servicio de Azure totalmente administrado y enormemente seguro que se puede utilizar para proteger las claves criptográficas. Para más información, consulte [Key Vault](https://azure.microsoft.com/services/key-vault).

Esta es una breve comparación de las funcionalidades que proporcionan ambos modos de administración de las MEK.

|  | Claves administradas por el servicio | Claves administradas por el cliente |
| --- | --- | --- |
|¿Cómo se almacenan los datos?|Siempre se cifran antes de almacenarse.|Siempre se cifran antes de almacenarse.|
|¿Dónde se almacena la clave de cifrado maestra?|Key Vault|Key Vault|
|¿Hay claves de cifrado almacenadas sin cifrar fuera de Key Vault? |No|No|
|¿Se puede recuperar la clave de cifrado maestra mediante Key Vault?|No. Después de que la clave de cifrado maestra se almacena en Key Vault, solo se puede usar para el cifrado y el descifrado.|No. Después de que la clave de cifrado maestra se almacena en Key Vault, solo se puede usar para el cifrado y el descifrado.|
|¿Quién posee la instancia de Key Vault y la clave de cifrado maestra?|El servicio Data Lake Store.|Usted es el propietario de la instancia de Key Vault, que pertenece a su propia suscripción de Azure. La clave de cifrado maestra de Key Vault se puede administrar mediante software o hardware.|
|¿Puede revocar el acceso a la clave de cifrado maestra para el servicio Data Lake Store?|No|Sí. Puede administrar listas de control de acceso en Key Vault y eliminar entradas de control de acceso a la identidad de servicio para el servicio Data Lake Store.|
|¿Puede eliminar permanentemente la clave de cifrado maestra?|No|Sí. Si elimina la clave de cifrado maestra de Key Vault, nadie podrá cifrar los datos de la cuenta de Data Lake Store, incluido el servicio Data Lake Store. <br><br> Si ha realizado copia de seguridad explícita de la clave de cifrado maestra antes de eliminarla de Key Vault, se puede restaurar y entonces se pueden recuperar los datos. Sin embargo, si no lo ha hecho, los datos de la cuenta de Data Lake Store nunca se podrán cifrar después.|


Aparte de esta diferencia sobre quién administra la clave de cifrado maestra y la instancia de Key Vault en la que reside, el resto del diseño es igual en ambos modos.

Al elegir el modo de las claves de cifrado maestras, es importante recordar lo siguiente:

*   Puede elegir si se usarán claves administradas por el cliente o claves administradas por el servicio al aprovisionar una cuenta de Data Lake Store.
*   Después de aprovisionar una cuenta de Data Lake Store, no se puede cambiar el modo.

### <a name="encryption-and-decryption-of-data"></a>Cifrado y descifrado de datos

Son tres los tipos de claves que se usan en el diseño del cifrado de datos. En la tabla siguiente se proporciona un resumen:

| Clave                   | Abreviatura | Asociada a | Ubicación de almacenamiento                             | Escriba       | Notas                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Clave de cifrado maestra | MEK          | Cuenta de Data Lake Store | Key Vault                              | Asimétrica | Puede administrarla Data Lake Store o usted.                                                              |
| Clave de cifrado de datos   | DEK          | Cuenta de Data Lake Store | Almacenamiento persistente, administrada por el servicio Data Lake Store | Simétrica  | La DEK se cifra mediante la clave de cifrado maestra. La DEK cifrada es lo que se almacena en el medio persistente. |
| Clave de cifrado de bloque  | BEK          | Un bloque de datos | None                                         | Simétrica  | La clave BEK se obtiene de la clave de cifrado de datos y del bloque de datos.                                                      |

En el siguiente diagrama, se ilustra este concepto:

![Claves de cifrado de datos](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritmo para descifrar un archivo:
1.  Compruebe si la clave DEK de la cuenta de Data Lake Store está almacenada en caché y lista para su uso.
    - Si no es así, lea la clave DEK cifrada del almacenamiento persistente y envíela a Key Vault para descifrarla. Almacene en caché la clave DEK descifrada en memoria. Ahora está lista para su uso.
2.  Para cada bloque de datos del archivo:
    - Lea el bloque de datos cifrado del almacenamiento persistente.
    - Genere la clave BEK con la clave DEK y el bloque de datos cifrado.
    - Use la clave BEK para descifrar los datos.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritmo para cifrar un archivo:
1.  Compruebe si la clave DEK de la cuenta de Data Lake Store está almacenada en caché y lista para su uso.
    - Si no es así, lea la clave DEK cifrada del almacenamiento persistente y envíela a Key Vault para descifrarla. Almacene en caché la clave DEK descifrada en memoria. Ahora está lista para su uso.
2.  Generar una clave BEK única para el bloque de datos con la clave DEK.
3.  Cifre el bloque de datos con la clave BEK mediante el cifrado AES-256.
4.  Almacene el bloque de datos cifrado en el almacenamiento persistente.

> [!NOTE] 
> Por motivos de rendimiento, la clave DEK sin cifrar se almacena en caché en memoria durante un corto período de tiempo, transcurrido el cual se borra inmediatamente. En medios persistentes, siempre se almacena cifrada con la clave MEK.

## <a name="key-rotation"></a>Rotación de claves

Cuando se usan claves administradas por el cliente, puede rotar la clave MEK. Para aprender a configurar una cuenta de Data Lake Store con claves administradas por el cliente, consulte [Introducción](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Requisitos previos

Cuando configuró la cuenta de Data Lake Store, eligió usar sus propias claves. Esta opción no se puede cambiar una vez creada la cuenta. En los siguientes pasos se supone que usa claves administradas por el cliente (es decir, ha elegido sus propias claves de Key Vault).

Tenga en cuenta que si usa las opciones predeterminadas para el cifrado, los datos siempre se cifran mediante claves administradas con Data Lake Store. En esta opción, no tiene la posibilidad de rotar claves, ya que se administran en Data Lake Store.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Rotación de la clave MEK en Data Lake Store

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Vaya a la instancia de Key Vault que almacena las claves asociadas con la cuenta de Data Lake Store. Seleccione **Claves**.

    ![Captura de pantalla de Key Vault](./media/data-lake-store-encryption/keyvault.png)

3.  Seleccione la clave asociada con su cuenta de Data Lake Store y cree una nueva versión de esta clave. Tenga en cuenta que Data Lake Store solo admite actualmente la rotación de claves a una nueva versión de clave. No admite la rotación a una clave diferente.

   ![Captura de pantalla de la ventana Claves, donde se resalta Nueva versión](./media/data-lake-store-encryption/keynewversion.png)

4.  Vaya a la cuenta de almacenamiento de Data Lake Store y seleccione **Cifrado**.

    ![Captura de pantalla de la ventana de la cuenta de almacenamiento de Data Lake Store, con Cifrado resaltado](./media/data-lake-store-encryption/select-encryption.png)

5.  Un mensaje le notifica que hay una nueva versión de la clave. Haga clic en **Rotar clave** para actualizar la clave a la nueva versión.

    ![Captura de pantalla de la ventana Data Lake Store con el mensaje y Rotar clave resaltado](./media/data-lake-store-encryption/rotatekey.png)

Esta operación tardará menos de dos minutos y no hay ningún tiempo de inactividad previsto debido a la rotación de claves. Una vez completada la operación, la nueva versión de la clave está en uso.

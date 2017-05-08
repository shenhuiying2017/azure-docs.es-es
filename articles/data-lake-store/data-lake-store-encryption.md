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
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: es-es
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Cifrado de datos en Azure Data Lake Store

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Información general del cifrado en Azure Data Lake Store

El cifrado en Azure Data Lake Store (ADLS) proporciona la capacidad de proteger sus datos, implementar directivas de seguridad empresarial y cumplir requisitos de cumplimiento de normas. Este artículo proporciona información general sobre el diseño y trata aspectos técnicos de la implementación del cifrado de datos de Azure Data Lake Store.

ADLS admite el cifrado de datos en reposo, activado de forma predeterminada y transparente. Con más detalle, esto significa:

* Activado de forma predeterminada: al crear una nueva cuenta de Azure Data Lake Store, la configuración predeterminada habilita el cifrado. Por tanto, los datos almacenados en Azure Data Lake Store se cifran antes de almacenarlos en medios persistentes. Este es el comportamiento para todos los datos y no se puede cambiar después de crear una cuenta.
* Transparente: ADLS automáticamente cifra los datos antes de guardarlos y descifra los datos antes de su recuperación. Un administrador configura y administra el cifrado de cada instancia de Azure Data Lake Store. No se realizan cambios en las API de acceso a datos, ni, por tanto, se requieren cambios en las aplicaciones y servicios que interactúan con Azure Data Lake Store por motivo del cifrado.

Los datos en tránsito (también conocidos como datos en movimiento) siempre se cifran en Azure Data Lake Store. Además de cifrarse los datos antes de ser guardados, los datos también están protegidos en tránsito o en movimiento mediante HTTPS (HTTP sobre SSL). HTTPS es el único protocolo admitido para las interfaces REST de Azure Data Lake Store.

![En la Ilustración 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Configuración del cifrado con Azure Data Lake Store

El cifrado en Azure Data Lake Store se configura durante la creación de la cuenta, siempre está habilitado de forma predeterminada. Los clientes tienen la opción de administrar las claves o permitir que Azure Data Lake Store (valor predeterminado) lo haga en su lugar.

Para más información sobre la configuración del cifrado en Azure Data Lake Store, consulte: [Introducción a Azure Data Lake Store mediante Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>Funcionamiento del cifrado en Azure Data Lake Store

### <a name="master-encryption-keys"></a>Claves de cifrado maestras

Azure Data Lake Store proporciona dos modos de administración de claves de cifrado maestras (MEK). El uso de claves de cifrado maestras se explica con más detalle a continuación. Por ahora, suponga que la clave de cifrado maestra es la clave de nivel superior. Se necesita acceso a la clave de cifrado maestra para descifrar cualquier dato almacenado en Azure Data Lake Store.

Los dos modos para administrar la clave de cifrado maestra son los siguientes:

1.    Claves administradas por el servicio
2.    Claves administradas por el cliente

En ambos modos, la clave de cifrado maestra está protegida al estar almacenada en Azure Key Vault. Azure Key Vault es un servicio de Azure totalmente administrado y altamente seguro que se puede utilizar para proteger las claves criptográficas. Puede leer más sobre Azure Key Vault [aquí](https://azure.microsoft.com/services/key-vault)

Esta es una breve comparación de las funcionalidades que proporcionan ambos modos de administración de las MEK.

|  | Claves administradas por el servicio | Claves administradas por el cliente |
| --- | --- | --- |
|¿Cómo se almacenan los datos?|Siempre se cifran antes de almacenarse|Siempre se cifran antes de almacenarse|
|¿Dónde se almacena la clave de cifrado maestra?|Almacén de claves de Azure|Almacén de claves de Azure|
|¿Hay claves de cifrado almacenadas sin cifrar fuera de Azure Key Vault?|No|No|
|¿Se puede recuperar la clave MEK de Azure Key Vault?|No. Una vez almacenada en Azure Key Vault solo se puede utilizar para cifrado y descifrado.|No. Una vez almacenada en Azure Key Vault solo se puede utilizar para cifrado y descifrado.|
|¿Quién posee Azure Key Vault y la clave MEK?|El servicio de Azure Data Lake Store.|El cliente posee Azure Key Vault, que pertenece a la propia suscripción de Azure. La clave MEK en el almacén de claves puede ser administrada por software o hardware (HSM).|
|¿El cliente puede revocar el acceso a la clave MEK para el servicio de Azure Data Lake Store?|No|Sí. Pueden administrar listas de control de acceso de Azure Key Vault y eliminar entradas de control de acceso a la identidad de servicio para el servicio de Azure Data Lake Store.|
|¿Puede el cliente eliminar permanentemente la clave MEK?|No|Sí. Si el cliente elimina la clave MEK de Azure Key Vault, nadie podrá descifrar los datos de la cuenta de ADLS, incluido el servicio de Azure Data Lake Store. <br><br> Si el cliente hizo una copia de seguridad de la clave MEK explícitamente antes de eliminarla de Azure Key Vault, se podrá restaurar y se podrán recuperar los datos. Sin embargo, si el cliente no hizo una copia de seguridad de la clave MEK antes de eliminarla de Azure Key Vault, los datos de la cuenta de ADLS no se podrán descifrar.|


Además de la diferencia de nivel superior, de quién administra la clave MEK y el almacén de claves en el que reside, el resto del diseño es el mismo para ambos modos.

Hay algunos aspectos importantes que hay que recordar relacionados con la elección del modo para las claves de cifrado maestras.

1.    Puede elegir si desea usar claves administradas por el cliente o claves administradas por ADLS al aprovisionar una cuenta de ADLS
2.    Una vez aprovisionada la cuenta de ADLS, no se puede cambiar el modo

### <a name="encryption-and-decryption-of-data"></a>Cifrado y descifrado de datos

Se usan tres tipos de claves en el diseño del cifrado de datos en Azure Data Lake. La tabla siguiente resume su utilización:

| Clave                   | Abreviatura | Asociada con | Ubicación de almacenamiento                             | Tipo       | Notas                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Clave de cifrado maestra | MEK          | Una cuenta de ADLS | Almacén de claves de Azure                              | Asimétrica | Puede ser administrada por ADLS o administrada por el cliente                                                              |
| Clave de cifrado de datos   | DEK          | Una cuenta de ADLS | Almacenamiento persistente: administrado por el servicio ADLS | Simétrica  | La clave DEK se cifra con la clave MEK y la clave DEK cifrada es lo que el servicio almacena en medios persistentes |
| Clave de cifrado de bloque  | BEK          | Un bloque de datos | None                                         | Simétrica  | La clave BEK se deriva de la clave DEK y el bloque de datos                                                      |

En el siguiente diagrama, se ilustra este concepto:

![Figura 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritmo para descifrar un archivo:
1.    Comprobar si la clave DEK para la cuenta de ADLS está almacenada en caché y está lista para su uso.
    * Si no es así, leer la clave DEK cifrada desde el almacenamiento persistente y enviarla a Azure Key Vault para descifrarla. Almacenar en caché la clave DEK descifrada en memoria y ya está lista para su uso.
2.    Para cada bloque de datos del archivo
    * Leer el bloque de datos cifrado desde el almacenamiento persistente
    * Generar la clave BEK con la clave DEK y el bloque de datos cifrados
    * Usar la clave BEK para descifrar los datos
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritmo para cifrar un archivo:
1.    Comprobar si la clave DEK para la cuenta de ADLS está almacenada en caché y está lista para su uso.
    * Si no es así, leer la clave DEK cifrada desde el almacenamiento persistente y enviarla a Azure Key Vault para descifrarla. Almacenar en caché la clave DEK descifrada en memoria y ya está lista para su uso.
2.    Generar una clave BEK única para el bloque de datos con la clave DEK.
3.    Cifrar el bloque de datos con la clave BEK mediante el cifrado AES-256.
4.    Almacenar el bloque de datos cifrado en el almacenamiento persistente

> [!NOTE] 
> Por motivos de rendimiento, la clave de cifrado de datos (DEK) sin cifrar se almacena en la memoria caché efímeramente por un periodo de tiempo corto y se borra inmediatamente después de que ha transcurrido esta duración. En medios persistentes, siempre se almacena cifrada con la clave de cifrado maestra (MEK).

## <a name="key-rotation"></a>Rotación de claves

Azure Data Lake Store permite la rotación de la clave de cifrado maestra (MEK) al utilizar claves administradas por el cliente. Para obtener información sobre la configuración de una cuenta de ADLS con claves administradas por el cliente, consulte [Introducción a Azure Data Lake Store mediante Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) page.

### <a name="pre-requisites"></a>Requisitos previos

Al configurar la cuenta de Azure Data Lake, los clientes han elegido usar sus propias claves. Esta opción no se puede cambiar una vez creada la cuenta. Si utiliza las opciones predeterminadas para cifrado, los datos siempre se cifrarán utilizando claves administradas por Azure Data Lake, en esta opción, el cliente no puede rotar las claves ya que son administradas por Azure Data Lake. En los pasos siguientes se supone que usa claves administradas por el cliente (ha elegido sus propias claves de su almacén de claves).

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Cómo rotar la clave (MEK) en Azure Data Lake Store

1. Inicie sesión en el nuevo [Azure Portal](https://portal.azure.com/).
2. Vaya al almacén de claves que contiene las claves asociadas con su cuenta de Azure Data Lake Store y seleccione Claves.

    ![simétricas](./media/data-lake-store-encryption/keyvault.png)

3.    Seleccione la clave asociada con su cuenta de Azure Data Lake Store y cree una nueva versión de la clave.
  
   En este momento, Azure Data Lake solo admite la rotación de clave a una nueva versión de la clave, no se admite la rotación a una clave diferente

   ![newversion](./media/data-lake-store-encryption/keynewversion.png)

4.    Vaya a la cuenta de Azure Data Lake Storage y seleccione Cifrado

    ![newversion](./media/data-lake-store-encryption/select-encryption.png)

5.    Verá una nota que le informa de que está disponible una nueva versión de la clave y un botón para rotar la clave a esta nueva versión. Haga clic en Rotar clave para actualizar la clave a la nueva versión.

    ![done](./media/data-lake-store-encryption/rotatekey.png)

6. Esta operación tardará menos de 2 minutos y no hay ningún tiempo de inactividad previsto debido a la rotación de claves. Una vez completada la operación, la nueva versión de la clave está en uso.


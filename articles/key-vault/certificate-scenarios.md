---
title: Introducción a los certificados de Key Vault
description: En los escenarios siguientes se describen algunos de los usos principales de servicio de administración de certificados de Key Vault, como los pasos adicionales necesarios para crear el primer certificado en el almacén de claves.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 5f3b8a7b9c7bf582ebc2fac2be8ff55134fbc6f2
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="get-started-with-key-vault-certificates"></a>Introducción a los certificados de Key Vault
En los escenarios siguientes se describen algunos de los usos principales de servicio de administración de certificados de Key Vault, como los pasos adicionales necesarios para crear el primer certificado en el almacén de claves.

Apartados siguientes:
- Creación del primer certificado de Key Vault
- Creación de un certificado con una entidad de certificación asociada a Key Vault
- Creación de un certificado con una entidad de certificación que no está asociada a Key Vault
- Importación de un certificado

## <a name="certificates-are-complex-objects"></a>Los certificados son objetos complejos
Los certificados se componen de tres recursos interrelacionados vinculados entre ellos como un certificado de Key Vault; los metadatos del certificado, y una clave y un secreto.


![Los certificados son complejos](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Creación del primer certificado de Key Vault  
 Para crear un certificado en Key Vault (KV), deben cumplirse los requisitos previos (pasos 1 y 2) y debe existir un almacén de claves para el usuario o la organización.  

**Paso 1**: emisores de la entidad de certificación  
-   La incorporación como administrador de TI, administrador de PKI o cualquiera que administre las cuentas con entidades de certificación para una empresa determinada (p. ej. Contoso) es un requisito previo para usar los certificados de Key Vault.  
    Las siguientes entidades de certificación son los emisores asociados actualmente con Key Vault:  
    -   DigiCert: Key Vault ofrece certificados SSL OV con DigiCert.  
    -   GlobalSign: Key Vault ofrece certificados SSL OV con GlobalSign.  
    -   WoSign: Key Vault ofrece certificados SSL OV o EV con WoSign en función de la configuración del usuario en su cuenta del portal de WoSign.  

**Paso 2**: un administrador de la cuenta del proveedor de la entidad de certificación crea las credenciales que utilizará Key Vault para la inscripción, la renovación y el uso de certificados SSL mediante Key Vault.

**Paso 3**: un administrador, junto con un empleado de Contoso (usuario de Key Vault) propietario de certificados que, dependiendo de la entidad de certificación, puede obtener un certificado del administrador o directamente desde la cuenta con la entidad de certificación.  

-   Comience una operación de incorporación de credenciales a un almacén de claves mediante la creación de un recurso de [emisor de certificados](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers). 
    -   Ejemplo: MyDigiCertIssuer  
        -   Proveedor  
        -   Credenciales: credenciales de la cuenta de la entidad de certificación. Cada entidad de certificación tiene sus datos específicos propios.  

     Para más información acerca de la creación de cuentas con proveedores de entidades de certificación, consulte la entrada correspondiente en el [blog de Key Vault](http://aka.ms/kvcertsblog).  

**Paso 3.1**: configure [contactos de certificados](https://docs.microsoft.com/rest/api/keyvault/certificate-contacts) para las notificaciones. Este es el contacto del usuario de Key Vault. Key Vault no aplica este paso.  

Nota: Este proceso, con el paso 3.1, es una operación que no tendrá que repetir.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Creación de un certificado con una entidad de certificación asociada a Key Vault

![Creación de un certificado con una entidad de certificación asociada a Key Vault](media/certificate-authority-2.png)

**Paso 4 **: las siguientes descripciones se corresponden con los pasos indicados con letras verdes que se enumeran en el diagrama anterior.  
  (1) - En el diagrama anterior, la aplicación crea un certificado que comienza con la creación interna de una clave en el almacén de claves.  
  (2) - Key Vault envía una solicitud de certificado SSL a la entidad de certificación.  
  (3) - La aplicación sondea, en proceso de bucle y espera, que Key Vault termine el certificado. La creación del certificado se completa cuando Key Vault recibe la respuesta de la entidad de certificación con el certificado X.509.  
  (4) - La entidad de certificación responde a la solicitud de certificado SSL de Key Vault con un certificado SSL X.509.  
  (5) - La creación del certificado se completa con la combinación del certificado X.509 para la entidad de certificación.  

  Usuario de Key Vault: crea un certificado mediante la especificación de una directiva

  -   Repítase según sea necesario  
  -   Restricciones de directiva  
      -   Propiedades de X.509  
      -   Propiedades de la clave  
      -   Referencia del proveedor - > p. ej. MyDigiCertIssure  
      -   Información de renovación - > p. ej. 90 días hasta la expiración  

  - La creación de certificados suele ser un proceso asincrónico e implica el sondeo de estado de la operación de creación del certificado en el almacén de claves.  
[Operación de obtención de certificado](https://docs.microsoft.com/en-us/rest/api/keyvault/getcertificateoperation): estado completado o erróneo con la información del error, o cancelado  
            - Si la creación se retrasa, se puede iniciar una operación de cancelación. Puede que la cancelación se lleve a cabo o no.  

## <a name="import-a-certificate"></a>Importación de un certificado  
 Como alternativa, se puede importar un certificado a Key Vault (PFX o PEM).  

 Para más información acerca del formato PEM, consulte la sección de certificados de [Información acerca de claves, secretos y certificados](about-keys-secrets-and-certificates.md).  

 Importación de certificados: requiere un PEM o PFX en el disco y tiene una clave privada. 
-   Debe especificar: nombre del certificado y del almacén (la directiva es opcional)

-   Los archivos PEM / PFX contienen atributos que KV analiza y usa para rellenar la directiva de certificados. Si ya se ha especificado una directiva de certificados, KV intentará hacerla coincidir con los datos del archivo PFX / PEM.  

-   Una vez terminada la importación, las operaciones posteriores utilizan la nueva directiva (nuevas versiones).  

-   Si no hay más operaciones, lo primero que hace Key Vault es enviar una notificación de expiración. 

-   Además, el usuario puede editar la directiva, que es funcional en el momento de la importación, pero contiene los valores predeterminados sin información especificada en la importación. Ejemplo: sin información del emisor  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Creación de un certificado con una entidad de certificación que no está asociada a Key Vault  
 Este método permite trabajar con entidades de certificación distintas a los proveedores asociados de Key Vault, lo que significa que la organización puede trabajar con la entidad de certificación que prefiera.  

![Creación de un certificado con la entidad emisora de certificados propia](media/certificate-authority-1.png)  

 Las descripciones del paso siguiente se corresponden con los pasos indicados con letras verdes del diagrama anterior.  

  (1) - En el diagrama anterior, la aplicación crea un certificado, que comienza con la creación interna de una clave en el almacén de claves.  

  (2) - Key Vault devuelve una solicitud de firma de certificado (CSR) a la aplicación.  

  (3) - La aplicación pasa la CSR a la entidad de certificación seleccionada.  

  (4) - Esta entidad de certificación responde con un certificado X.509.  

  (5) - La aplicación termina de crear el certificado con una combinación del certificado X.509 de la entidad de certificación.

## <a name="see-also"></a>Otras referencias
- [Certificate operations](/rest/api/keyvault/certificate-operations.md) (Operaciones con certificados)
- [Información acerca de claves, secretos y certificados](about-keys-secrets-and-certificates.md)

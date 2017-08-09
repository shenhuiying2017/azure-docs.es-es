---
title: "Cambio de la facturación de Azure RemoteApp | Microsoft Docs"
description: "Obtenga información sobre cómo detener la facturación de Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 8f94da9a-7848-4ddc-b7b7-d9c280ccf4d7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 32fc673eeef01e80c73375bf264206beea8cfbe5
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-from-azure-remoteapp-to-mycloudit"></a>Migrar de Azure RemoteApp a MyCloudIT 

**¿Actualmente usa Microsoft Azure RemoteApp?** : MyCloudIT ha creado una herramienta automatizada para migrar las colecciones de Azure RemoteApp (ARA) a la plataforma de administración de MyCloudIT mientras se sigue ejecutando en Microsoft Azure.

**Aproveche el portal de Azure Resource Manager**: una vez que se ha completado la migración a la plataforma de MyCloudIT, se puede tener acceso de inmediato al nuevo portal de Azure Resource Manager. Este portal contiene todas las nuevas características e innovaciones que ofrece Microsoft Azure, incluido el acceso a los tamaños de máquina virtual, para garantizar que el diseño de la implementación satisfaga las necesidades del negocio.

**Realice pruebas en paralelo para asegurarse de que encuentra la solución adecuada para sus necesidades**: la herramienta de migración de MyCloudIT está diseñada para iniciar el proceso de migración y realizar pruebas en paralelo mientras los usuarios de ARA actuales siguen usando ARA.  Los usuarios permanecerán en ARA hasta que la migración y las pruebas se completen.  La herramienta de migración está diseñada para administrar una colección típica de ARA.  Si cree que tiene un escenario único que no es estándar, escríbanos a [sales@conexlink.com](mailto:sales@conexlink.com) para que le proporcionemos un plan personalizado que le ayude con la migración.

**Funciones de Escritorio como servicio**: tenga en cuenta que MyCloudIT no solo ofrece las funciones de RemoteApp a las que está acostumbrado, sino funciones completas de Escritorio como servicio por el mismo costo al mes y sin requisitos mínimos de usuario.

## <a name="what-we-will-do-for-you"></a>Lo que haremos por usted

MyCloudIT automatiza la migración de la plantilla de Azure RemoteApp desde el Portal de Azure clásico de su suscripción al portal de Azure Resource Manager de su suscripción con nuestra herramienta de migración automatizada.  

> [!NOTE]
> La plantilla de Azure RemoteApp debe permanecer en la misma región de Azure que la implementación original de Azure RemoteApp.  Si necesita cambiar las regiones o suscripciones de Azure durante la migración, escríbanos a [sales@conexlink.com](mailto:sales@conexlink.com) para que le proporcionemos orientación adicional.

Siga leyendo para obtener información detallada sobre el proceso de migración automatizado con la herramienta de migración de MyCloudIT:

1. La herramienta de migración examina sus suscripciones actuales de todas las implementaciones de ARA existentes.  
2. Seleccione una colección de ARA para migrar de cada vez.  Si tiene varias colecciones, ejecute la herramienta varias veces.
3. Tiene la opción de copiar los discos de perfil de usuario (UPD) a la nueva implementación, por lo que puede recuperar los datos heredados o asignar manualmente sus UPD a la nueva implementación. Si quiere copiar los UPD, los guardaremos e incluiremos un archivo de texto que asigne el nombre de UPD al nombre de cada usuario.  Los UPD se copiarán en un recurso compartido en el servidor RDSMGMT `F:\Shares\LegacyUPD` y se expondrán a través del recurso compartido `\\RDSmgmt\LegacyUPD`. 
4. La migración no producirá tiempo de inactividad para la implementación de ARA actual.  A pesar de ello, si se realizan cambios en los UPD (desde ARA) después de la copia, dichos cambios no se reflejarán en los UPD almacenados en el portal de Azure Resource Manager. 
5. Si tiene máquinas virtuales adicionales, como controladores de dominio y servidores de archivos en una red virtual clásica de Azure, estableceremos un emparejamiento de redes virtuales entre la red virtual clásica de Azure existente y la nueva red virtual que creemos, en el nuevo portal de Azure Resource Manager.
6. Nuestra solución automatizada solo establecerá el emparejamiento de redes virtuales entre la red virtual clásica de Azure existente y la nueva red virtual si la implementación de ARA existente es híbrida, es decir, si se está autenticando con un controlador de dominio de Windows Server Active Directory en la red virtual clásica existente. Si no establecemos el emparejamiento de redes virtuales para su colección pero lo necesita, escríbanos a [sales@conexlink.com](mailto:sales@conexlink.com) y le ayudaremos encantados a configurarlo sin ningún costo adicional.
7. Nuestra solución automatizada se asegurará de que la configuración de DNS de Azure se actualice con la configuración de la nueva red virtual para garantizar que la nueva implementación pueda conectarse al controlador de dominio existente en la red virtual clásica.
8. Nuestra solución automatizada se asegurará de que no haya conflictos de direcciones IP cuando se cree esta red virtual y establecerá el emparejamiento de redes virtuales para las implementaciones que tengan un servidor de Windows Server Active Directory.
9. Si solo usa Azure AD para la autenticación, MyCloudIT creará un dominio de Windows Server Active Directory y usará Azure AD Connect para sincronizar a los usuarios entre la instancia de Azure AD existente y el dominio de Windows Server Active Directory creado por MyCloudIT.
10. Si usa un dominio de Windows Server Active Directory para autenticar a los usuarios de ARA, nuestra solución automatizada conectará la nueva implementación de MyCloudIT con el controlador de dominio de Windows Server Active Directory existente mediante el emparejamiento de redes virtuales.
11. Si usa Azure Active Directory Domain Services para la autenticación también podemos realizar la migración, pero debe ponerse en contacto con nosotros para que creemos un plan de migración personalizado.  Envíe un correo electrónico a [sales@conexlink.com](mailto:sales@conexlink.com). 
12. Una vez que haya confirmado la colección que quiere migrar, póngase cómodo y relájese mientras nuestra solución automatizada migra la colección de ARA y los discos de perfil de usuario (opcional) a la nueva solución RemoteApp controlada por MyCloudIT.
13. Una vez completada la implementación, volveremos a publicar las mismas aplicaciones que estaban publicadas en ARA y, tras la implementación, podrá publicar más aplicaciones.

## <a name="post-migration-benefits"></a>Ventajas tras la migración

1. Proporcionamos una consola de administración que permite administrar el ciclo de vida completo de la implementación de RemoteApp.
2. Podrá administrar las máquinas virtuales desde nuestro portal.  Inicie, detenga y cambie el tamaño de las máquinas virtuales si es necesario.
3. La consola de administración permite crear y administrar usuarios y grupos desde el portal de administración.
4. La consola de administración permite sincronizar a los usuarios con Office 365 para crear la misma experiencia de inicio de sesión.
5. La consola de administración permite crear colecciones adicionales de RemoteApp y de escritorio sin duplicar los costos y sin requisitos mínimos de usuario. 
6. La consola de administración permite publicar nuevas aplicaciones RemoteApp.
7. La consola de administración permite programar el inicio y el apagado de la implementación de RemoteApp si solo necesita la solución durante unas horas concretas.
8. La consola de administración permite automatizar la instalación y la configuración del agente de Azure Backup, que proporciona un historial de retención de documentos para los datos de los clientes.
9. La consola de administración proporciona acceso a las métricas de rendimiento de la implementación.  Esto permite identificar los posibles cuellos de botella de rendimiento sin necesidad de instalar herramientas adicionales de administración del rendimiento.
10. Si tiene varios hosts de sesión, podrá habilitar el ajuste de escala automático de modo que solo se ejecuten los hosts de sesión que se deben ejecutar.
11. MyCloudIT proporciona acceso al servidor de puerta de enlace RDWeb mediante un nombre de dominio de MyCloudIT.  También ofrecemos la posibilidad de reasignar la dirección URL a una dirección URL personalizada para la personalización de marca del usuario final.

## <a name="prerequisites-for-migration"></a>Requisitos previos para la migración

1. Debe tener acceso a la suscripción de Azure que hospeda la solución actual de Azure RemoteApp.
2. Debe conceder permisos a nuestro portal dentro de su suscripción para migrar su plantilla y para crear o modificar la nueva implementación de MyCloudIT.
3. Tenga en cuenta que, debido a una limitación en Azure RemoteApp, solo se puede migrar cada colección tres veces.  Si necesita migrar una colección más de tres veces, puede crear una incidencia para que Azure aumente el número de exportaciones, o bien ponerse en contacto con nosotros para que le ayudemos a realizar la solicitud de ARA para aumentar el número de exportaciones.

## <a name="mycloudit-billing"></a>Facturación de MyCloudIT

Vea el archivo PDF [MyCloudIT Pricing for RemoteApp Solutions](https://mcitdocuments.blob.core.windows.net/terms/MyCloudIT_Pricing_Overview.pdf) (Precios de MyCloudIT para soluciones de RemoteApp) para obtener información sobre cómo predecir y administrar los costos generales de Azure.

Si todavía tiene alguna pregunta, escríbanos a [sales@conexlink.com](mailto:sales@conexlink.com) o vea el vídeo de demostración completo [Azure RemoteApp Migration Tool - MyCloudIT](https://www.youtube.com/watch?v=YQ_1F-JeeLM&t=482s) (Herramienta de migración de Azure RemoteApp: MyCloudIT). 



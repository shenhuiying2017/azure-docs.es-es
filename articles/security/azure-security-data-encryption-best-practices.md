---
title: Procedimientos recomendados de cifrado y seguridad de datos | Microsoft Docs
description: "Este artículo proporciona un conjunto de procedimientos recomendados para el cifrado y la seguridad de los datos con funcionalidades de Azure integradas."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: yurid
ms.openlocfilehash: 81136e53756adfdba2f07c103b042499fe2967db
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Procedimientos recomendados de cifrado y seguridad de datos en Azure
Uno de los elementos clave para la protección de datos en la nube consiste en tener en cuenta los posibles estados en que se pueden producir datos y qué controles hay disponibles para ese estado. Como parte de los procedimientos recomendados de cifrado y seguridad de datos en Azure, se ofrecen recomendaciones relacionadas con los estados de datos siguientes:

* En reposo: esto incluye información sobre todos los objetos de almacenamiento, los contenedores y los tipos que existen de forma estática en medios físicos, ya sean discos magnéticos u ópticos.
* En tránsito: se considera que los datos están en movimiento cuando se transfieren entre componentes, ubicaciones o programas. Por ejemplo, a través de la red o un bus de servicio (desde una ubicación local hacia la nube, y viceversa, incluidas las conexiones híbridas como ExpressRoute) o durante el proceso de entrada y salida.

En este artículo, veremos un conjunto de procedimientos recomendados de cifrado y seguridad de datos en Azure. Estos procedimientos recomendados se derivan de nuestra experiencia con el cifrado y la seguridad de datos en Azure, y las experiencias de clientes como usted.

Para cada procedimiento recomendado, explicaremos:

* Qué es el procedimiento recomendado
* Por qué le conviene habilitar este procedimiento recomendado
* Cuál podría ser el resultado si no habilita el procedimiento recomendado
* Alternativas posibles al procedimiento recomendado
* Cómo aprender a habilitar el procedimiento recomendado

Este artículo parte de una opinión consensuada y se basa en las funcionalidades y los conjuntos de características de la plataforma Azure existentes cuando se redactó. Las opiniones y las tecnologías cambian con el tiempo, por lo que se actualizará de forma periódica para reflejar esos cambios.

Entre los procedimientos recomendados de cifrado y seguridad de datos en Azure, se incluyen:

* Aplicación de Multi-Factor Authentication
* Uso del control de acceso basado en rol (RBAC)
* Cifrado de máquinas virtuales de Azure
* Uso de modelos de seguridad de hardware
* Administración con estaciones de trabajo seguras
* Habilitación del cifrado de datos SQL
* Protección de los datos en tránsito
* Aplicación del cifrado de datos a nivel de archivos

## <a name="enforce-multi-factor-authentication"></a>Aplicación de Multi-Factor Authentication
El primer paso relativo al acceso y al control de datos en Microsoft Azure consiste en autenticar al usuario. [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) es un método de comprobación de la identidad del usuario que no se limita al nombre de usuario y la contraseña. Este método de autenticación ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer a los usuarios, que demandan un proceso de inicio de sesión simple.

Si habilita Azure MFA para los usuarios, agrega una segunda capa de seguridad a sus transacciones e inicios de sesión. En este caso, es posible que durante una transacción se acceda a un documento que se encuentra en un servidor de archivos o en SharePoint Online. Azure MFA también ayuda al equipo de TI a reducir la probabilidad de que se acceda a los datos de la organización usando credenciales en peligro.

Por ejemplo: si aplica Azure MFA para los usuarios y lo configura para que use una llamada de teléfono o un mensaje de texto como comprobación, si las credenciales del usuario están en peligro, el atacante no podrá acceder a los recursos porque no tendrá acceso al teléfono del usuario. Las organizaciones que no agreguen esta capa adicional de protección de la identidad son más susceptibles a los ataques de robo de credenciales, que pueden poner en peligro los datos.

Una alternativa para las organizaciones que quieren mantener un control local de la autenticación es usar [Servidor Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), también denominado MFA local. Mediante este método, podrá seguir aplicando la autenticación multifactor y mantener el servidor MFA local.

Para más información sobre Azure MFA, lea el artículo [Introducción a Azure Multi-Factor Authentication en la nube](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Uso del control de acceso basado en rol (RBAC)
Restrinja el acceso siguiendo los principios de seguridad de limitar el acceso a lo que se [necesita saber](https://en.wikipedia.org/wiki/Need_to_know) y a los principios de seguridad con [privilegios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Esto es fundamental para las organizaciones que deseen aplicar directivas de seguridad para el acceso a los datos. El control de acceso basado en rol (RBAC) de Azure sirve para asignar permisos a usuarios, grupos y aplicaciones en un ámbito determinado. El ámbito de una asignación de roles puede ser una suscripción, un grupo de recursos o un único recurso.

Puede aprovechar los [roles RBAC integrados](../active-directory/role-based-access-built-in-roles.md) en Azure para asignar privilegios a los usuarios. Considere usar *Colaborador de la cuenta de almacenamiento* para los operadores en la nube que necesiten administrar cuentas de almacenamiento y el rol *Colaborador de la cuenta de almacenamiento clásica* para administrar cuentas de almacenamiento clásicas. En el caso de los operadores en la nube que necesiten administrar máquinas virtuales y cuentas de almacenamiento, podría agregarlos al rol *Colaborador de la máquina virtual*.

Es posible que las organizaciones que no apliquen el control de acceso a los datos mediante funcionalidades como RBAC estén concediendo más privilegios de los necesarios a sus usuarios. Esto puede poner en peligro los datos si algunos usuarios obtienen acceso a datos que no les conciernen.

Puede aprender más sobre Azure RBAC en el artículo [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Cifrado de máquinas virtuales de Azure
Para muchas organizaciones, el [cifrado de los datos en reposo](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) es un paso obligatorio en lo que respecta a la privacidad de los datos, el cumplimiento y la soberanía de los datos. Cifrado de discos de Azure permite que los administradores de TI cifren discos de máquina virtual IaaS con Windows y Linux. Cifrado de discos de Azure aprovecha la característica BitLocker de Windows, estándar en el sector, y la característica DM-Crypt de Linux para ofrecer cifrado de volumen para los discos de datos y del sistema operativo.

Puede aprovechar Cifrado de discos de Azure para proteger sus datos y así satisfacer los requisitos de cumplimiento y seguridad de la organización. Además, las organizaciones deberían pensar en usar cifrado como ayuda para mitigar los riesgos relacionados con el acceso no autorizado a los datos. También se recomienda cifrar las unidades antes de escribir datos confidenciales en ellas.

Asegúrese de cifrar los volúmenes de datos y el volumen de arranque de la máquina virtual para proteger los datos en reposo en su cuenta de almacenamiento de Azure. Proteja las claves de cifrado y los secretos mediante [Azure Key Vault](../key-vault/key-vault-whatis.md).

Para los servidores de Windows locales, podría aplicar los siguientes procedimientos recomendados:

* Usar [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) para el cifrado de datos
* Almacene la información de recuperación en AD DS.
* -Ante cualquier preocupación por que las claves de BitLocker se hayan puesto en peligro, se recomienda formatear la unidad para quitar todas las instancias de los metadatos de BitLocker o descifrar toda la unidad y cifrarla de nuevo.

Las organizaciones que no aplican el cifrado de datos están más expuestas a sufrir problemas de integridad de datos, como por ejemplo, usuarios no autorizados o malintencionados que roban datos y cuentas en peligro que acceden sin autorización a datos sin cifrar. Además de estos riesgos, las compañías que deben cumplir normativas del sector tienen que demostrar que son diligentes y que usan los controles adecuados para mejorar la seguridad de los datos.

Puede obtener más información sobre Azure Disk Encryption en el artículo [Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Uso de módulos de seguridad de hardware
Las soluciones de cifrado del sector usan claves secretas para cifrar los datos. Por lo tanto, es fundamental que estas claves se almacenen de forma segura. La administración de claves se convierte en una parte integral de la protección de datos, ya que se encargará de almacenar las claves secretas que sirven para cifrar datos.

Azure Disk Encryption usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para ayudarle a controlar y administrar las claves y los secretos de cifrado de discos en su suscripción del almacén de claves. Además, garantiza que todos los datos de los discos de las máquinas virtuales se cifren en reposo en Azure Storage. Debería usar el Almacén de claves de Azure para auditar el uso de la directiva y las claves.

Existen muchos riesgos inherentes a la carencia de controles de seguridad adecuados para proteger las claves secretas que se usaron para cifrar los datos. Si los atacantes obtienen acceso a las claves secretas, podrán descifrar los datos y acceder a información confidencial.

Puede obtener más recomendaciones generales para la administración de certificados en Azure en el artículo [Certificate Management in Azure: Do's and Don'ts (Administración de certificados en Azure: qué hacer y qué no hacer)](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Para más información sobre Azure Key Vault, lea [Introducción a Azure Key Vault](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Administración con estaciones de trabajo seguras
Puesto que la mayoría de los ataques van destinados al usuario final, el punto de conexión se convierte en uno de los principales puntos de ataque. Si un atacante pone en peligro el punto de conexión, puede aprovechar las credenciales del usuario para acceder a los datos de la organización. La mayoría de los ataques a los puntos de conexión aprovechan el hecho de que los usuarios finales son administradores en sus estaciones de trabajo locales.

Para reducir estos riesgos, use una estación de trabajo de administración segura. Se recomienda que use una [estación de trabajo con privilegios de acceso (PAW)](https://technet.microsoft.com/library/mt634654.aspx) para reducir la superficie expuesta a ataques en las estaciones de trabajo. Estas estaciones de trabajo de administración seguras pueden ayudar a mitigar algunos de estos ataques y a garantizar la mayor seguridad de sus datos. Asegúrese de usar PAW para proteger y bloquear la estación de trabajo. Se trata de un paso importante para proporcionar garantías de alta seguridad para la protección de cuentas, tareas y datos confidenciales.

La falta de protección en los puntos de conexión puede poner sus datos en riesgo. Asegúrese de aplicar directivas de seguridad en todos los dispositivos que se usen para consumir datos, independientemente de su ubicación (nube o local).

Puede obtener más información sobre las estaciones de trabajo con privilegios de acceso en el artículo [Protección del acceso con privilegios](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Habilitación del cifrado de datos SQL
El [Cifrado de datos transparente (TDE) de Azure SQL Database](https://msdn.microsoft.com/library/dn948096.aspx) facilita la protección contra la amenaza de actividades malintencionadas, ya que la base de datos se cifra y descifra en tiempo real, se realizan copias de seguridad asociadas y archivos de registro de transacciones en reposo sin necesitar cambios en la aplicación.  TDE cifra el almacenamiento de una base de datos completa mediante el uso de una clave simétrica denominada clave de cifrado de base de datos.

Incluso cuando se cifra el almacenamiento completo, es muy importante cifrar también la base de datos. Se trata de una implementación del enfoque defensivo en profundidad para la protección de los datos. Si usa [Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) y quiere proteger datos confidenciales como números de tarjetas de crédito o del seguro social, puede cifrar las bases de datos con el cifrado AES de 256 bits validado por FIPS 140-2, que cumple los requisitos de muchas normas del sector (por ejemplo, HIPAA, PCI).

Es importante comprender que los archivos relacionados con la [extensión del grupo de búferes](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) no se cifran cuando se cifra una base de datos mediante TDE. Necesita usar herramientas de cifrado de nivel de sistema de archivos como BitLocker o [Sistema de cifrado de archivos](https://technet.microsoft.com/library/cc700811.aspx) (EFS) para los archivos relacionados con la BPE.

Ya que un usuario autorizado, como un administrador de seguridad o de base de datos, puede acceder a los datos incluso si la base de datos está cifrada con TDE, también se deben seguir estas recomendaciones:

* Autenticación de SQL en el nivel de base de datos
* Autenticación de Azure AD mediante roles RBAC
* Los usuarios y las aplicaciones deberían usar cuentas independientes para la autenticación. De este modo, puede limitar los permisos que se conceden a usuarios y aplicaciones, y reducir los riesgos causados por la actividad malintencionada.
* Implemente la seguridad de nivel de base de datos con roles fijos de base de datos (como db_datareader o db_datawriter). También puede crear roles personalizados para su aplicación con el fin de conceder permisos explícitos a determinados objetos de la base de datos.

Las organizaciones que no usen el cifrado de nivel de base de datos pueden ser más susceptibles a ataques que podrían poner en peligro los datos ubicados en bases de datos SQL.

Para más información sobre el cifrado TDE de SQL, lea el artículo [Cifrado de datos transparente con Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Protección de los datos en tránsito
Proteger los datos en tránsito debe ser una parte esencial de su estrategia de protección de datos. Puesto que los datos se desplazan entre muchas ubicaciones, la recomendación general es utilizar siempre los protocolos SSL/TLS para intercambiar datos entre diferentes ubicaciones. En algunas circunstancias, podría aislar todo el canal de comunicación entre su infraestructura local y la nube mediante una red privada virtual (VPN).

Para los datos que se desplazan entre la infraestructura local y Azure, debe plantearse usar medidas de seguridad apropiadas, como HTTPS o VPN.

Para las organizaciones que necesitan proteger el acceso a Azure desde varias estaciones de trabajo locales, use una [VPN de sitio a sitio de Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Si la organización necesita proteger el acceso a Azure desde una estación de trabajo local, use una [VPN de punto a sitio](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Los conjuntos de datos más grandes se pueden mover por medio de un vínculo WAN dedicado de alta velocidad como [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si decide usar ExpressRoute, también puede cifrar los datos en el nivel de aplicación mediante [SSL/TLS](https://support.microsoft.com/kb/257591) u otros protocolos para una mayor protección.

Si interactúa con el Almacenamiento de Azure a través del Portal de Azure, todas las transacciones se realizan a través de HTTPS. También se puede usar la [API de REST de almacenamiento](https://msdn.microsoft.com/library/azure/dd179355.aspx) a través de HTTPS para interactuar con [Azure Storage](https://azure.microsoft.com/services/storage/) y [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Las organizaciones que no protegen los datos en tránsito son más susceptibles a los [ataques del tipo "Man in the middle"](https://technet.microsoft.com/library/gg195821.aspx), a la [interceptación](https://technet.microsoft.com/library/gg195641.aspx) y al secuestro de sesión. Estos ataques pueden ser el primer paso para obtener acceso a datos confidenciales.

Para más información sobre la opción de VPN de Azure, lea el artículo [Planeamiento y diseño de VPN Gateway](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Aplicación del cifrado de datos a nivel de archivos
Otra capa de protección que puede aumentar el nivel de seguridad de los datos es el cifrado del archivo, independientemente de su ubicación.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) usa directivas de autorización, identidad y cifrado para ayudar a proteger los archivos y el correo electrónico. Azure RMS funciona en varios dispositivos (teléfonos, tabletas y PC) y los protege tanto dentro como fuera de su organización. Esta funcionalidad es posible porque Azure RMS agrega una capa de protección que acompaña a los datos, incluso cuando salen de los límites de su organización.

Cuando usa Azure RMS para proteger los archivos, usa criptografía estándar del sector que es totalmente compatible con [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Cuando aprovecha Azure RMS para proteger los datos, tiene la garantía de que la protección permanece con el archivo, incluso si se copia en almacenamiento que no esté bajo el control de TI, como un servicio de almacenamiento en la nube. Lo mismo ocurre con los archivos compartidos por correo electrónico: se protege el archivo adjunto al mensaje de correo electrónico y se incluyen instrucciones para abrirlo.

Durante el planeamiento para adoptar Azure RMS, se recomienda lo siguiente:

* Instale la [aplicación RMS sharing](https://technet.microsoft.com/library/dn339006.aspx). Esta aplicación se integra con las aplicaciones de Office con la instalación de un complemento de Office con el que los usuarios pueden proteger los archivos directamente y de forma sencilla.
* Configure las aplicaciones y los servicios para que admitan Azure RMS.
* Cree [plantillas personalizadas](https://technet.microsoft.com/library/dn642472.aspx) que reflejen sus requisitos empresariales. Por ejemplo: una plantilla para los datos más delicados que se deba aplicar a todos los correos electrónicos de mayor confidencialidad.

Las organizaciones con puntos débiles en la [clasificación de datos](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) y la protección de archivos pueden ser más susceptibles a la fuga de datos. Sin una protección adecuada de los archivos, las organizaciones no pueden obtener perspectivas empresariales, supervisar el abuso ni evitar el acceso malicioso a archivos.

Puede obtener más información sobre Azure RMS en el artículo [Requisitos de Azure Information Protection](https://technet.microsoft.com/library/jj585016.aspx).

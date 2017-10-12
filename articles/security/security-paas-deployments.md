---
title: "Protección de implementaciones de PaaS | Microsoft Docs"
description: " Conozca las ventajas de seguridad de PaaS con respecto a los modelos de servicio en la nube y consulte los procedimientos recomendados para proteger la implementación de PaaS en Azure. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
ms.openlocfilehash: f218fe7e59e46683b544fd83bfea505b7cbe2d59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-deployments"></a>Protección de implementaciones de PaaS

En este artículo se proporciona información que ayuda a:

- Comprender las ventajas de seguridad del hospedaje de aplicaciones en la nube
- Evaluar las ventajas de seguridad de plataforma como servicio (PaaS) en comparación con otros modelos de servicio en la nube
- Cambiar el enfoque de seguridad de un perímetro basado en la red por un perímetro basado en identidades
- Implementar recomendaciones generales de procedimientos recomendados de PaaS

## <a name="cloud-security-advantages"></a>Ventajas de seguridad en la nube
La nube ofrece ventajas de seguridad. En un entorno local, las organizaciones probablemente tengan responsabilidades inadecuadas y recursos limitados disponibles para invertir en seguridad, de tal manera que se crea un entorno donde los atacantes pueden aprovechar vulnerabilidades a todos los niveles.

![Ventajas de seguridad de la era en la nube][1]

Las organizaciones pueden mejorar los tiempos de detección de amenazas y respuesta mediante la utilización de inteligencia de la nube y funciones de seguridad basada en la nube del proveedor.  Con la transferencia de responsabilidades al proveedor de nube, las organizaciones pueden obtener más cobertura de seguridad, lo que les permite reasignar recursos de seguridad y presupuestos a otras prioridades empresariales.

## <a name="division-of-responsibility"></a>División de responsabilidad
Es importante comprender la división de responsabilidad entre usted y Microsoft. De forma local, es propietario de la pila completa, pero a medida que se pasa a la nube, algunas responsabilidades se transfieren a Microsoft. La matriz de responsabilidad siguiente muestra las áreas de la pila en una implementación de IaaS, PaaS y SaaS de la que tanto usted como Microsoft son responsables.

![Zonas de responsabilidad][2]

Para todos los tipos de implementación de nube, es propietario de los datos y las identidades. Asimismo, es responsable de proteger la seguridad de los datos y las identidades, los recursos locales y los componentes en la nube que controla (que varía según el tipo de servicio).

Las responsabilidades que siempre tiene, con independencia del tipo de implementación, son:

- Datos
- Puntos de conexión
- Cuenta
- administración de acceso

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Ventajas de seguridad de un modelo de servicio en la nube de PaaS
Con la misma matriz de responsabilidad, se van a indicar las ventajas de seguridad de una implementación de PaaS en Azure frente a una implementación local.

![Ventajas de seguridad de PaaS][3]

A partir de la parte inferior de la pila, la infraestructura física, Microsoft mitiga las responsabilidades y los riesgos comunes. Dado que Microsoft supervisa continuamente Microsoft Cloud, es difícil atacarla. No tiene sentido que un atacante se fije Microsoft Cloud como un objetivo. A menos que el atacante tenga una gran cantidad de dinero y recursos, lo más probable es que el atacante se fije otro objetivo.  

En el medio de la pila, no hay ninguna diferencia entre una implementación de PaaS y una implementación local. A nivel de aplicación y de cuenta y administración del acceso, tiene riesgos similares. En la siguiente sección de pasos de este artículo, presentamos los procedimientos recomendados para eliminar o minimizar estos riesgos.

En la parte superior de la pila, gobierno de datos y administración de derechos, corre un riesgo que puede mitigarse con la administración de claves. (La administración de claves se cubre en los procedimientos recomendados). Si bien la administración de claves es una responsabilidad adicional, tiene áreas en una implementación de PaaS que ya no tiene que administrar, por lo que puede mover recursos a la administración de claves.

La plataforma Azure también proporciona protección segura contra ataques DDoS mediante el uso de varias tecnologías basadas en red. Sin embargo, todos los tipos de métodos de protección contra ataques DDoS basados en red tienen sus límites por vínculo y por centro de datos. Para ayudar a evitar el impacto de los ataques DDoS grandes, puede beneficiarse de la funcionalidad de la nube principal de Azure que permite escalar horizontalmente de manera rápida y automática para defenderse frente a ataques DDoS. En los artículos de procedimientos recomendados se proporcionan más detalles sobre cómo se puede hacer esto.

## <a name="modernizing-the-defenders-mindset"></a>Modernización de la mentalidad del defensor
Las implementaciones de PaaS conllevan un cambio del enfoque general en relación con la seguridad. Se pasa de la necesidad de controlar todo por uno mismo a compartir la responsabilidad con Microsoft.

Otra importante diferencia entre las implementaciones de PaaS y las implementaciones locales tradicionales es una nueva visión de lo que define el perímetro de seguridad principal. Históricamente, el perímetro de seguridad local principal era la red, y la mayoría de los diseños de seguridad locales utilizan la red como la dinámica de seguridad principal. Para las implementaciones de PaaS, se recomienda considerar la identidad como el perímetro de seguridad principal.

## <a name="identity-as-the-primary-security-perimeter"></a>Identidad como el perímetro de seguridad principal
Una de las cinco características fundamentales de la informática en la nube es el acceso amplio a la red, lo que hace que el concepto basado en la red resulte menos relevante. El objetivo de gran parte de la informática en la nube es permitir a los usuarios acceder a los recursos con independencia de su ubicación. Para la mayoría de los usuarios, su ubicación estará en alguna parte de Internet.

En la figura siguiente se muestra cómo el perímetro de seguridad ha evolucionado de un perímetro de red a un perímetro de identidad. La seguridad se centra menos en proteger la red y más en proteger los datos, así como en administrar la seguridad de las aplicaciones y los usuarios. La diferencia principal es que la intención es basar más la seguridad en lo que resulta importante para su empresa.

![La identidad como el nuevo perímetro de seguridad][4]

Inicialmente, los servicios PaaS en Azure (por ejemplo, los roles web y Azure SQL) ofrecían poca o ninguna defensa para el perímetro de red tradicional. Se entiende que el propósito del elemento era exponerse a Internet (rol web) y que la autenticación ofrece el nuevo perímetro (por ejemplo, BLOB o Azure SQL).

Las prácticas de seguridad modernas asumen que el adversario ha infringido el perímetro de red. Por lo tanto, las prácticas de defensa modernas se han pasado al perímetro de identidad. Las organizaciones deben establecer un perímetro de seguridad basado en identidades con autenticación sólida e higiene de autorización (procedimientos recomendados).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Recomendaciones para administrar el perímetro de identidad

Durante décadas, se han encontrado disponibles principios y patrones para el perímetro de red. En cambio, el sector tiene relativamente poca experiencia con el uso de la identidad como el perímetro de seguridad principal. Dicho esto, se ha adquirido suficiente experiencia para ofrecer algunas recomendaciones generales que se han probado sobre el terreno y que se han aplicado a casi todos los servicios PaaS.

A continuación se resume un enfoque de procedimientos recomendados generales para administrar el perímetro de identidad.

- **No pierda las claves o credenciales** Es fundamental proteger las claves y credenciales para preservar la seguridad de las implementaciones de PaaS. La pérdida de claves y credenciales es un problema común. Una buena solución es usar una solución centralizada donde se pueden almacenar claves y secretos en módulos de seguridad de hardware (HSM). Azure proporciona un HSM en la nube con [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **No coloque las credenciales y otros secretos en el código fuente o GitHub** Mucho peor que perder las claves y las credenciales es que un tercero no autorizado acceda a ellas. Los atacantes pueden aprovechar las tecnologías de bots para encontrar claves y secretos almacenados en repositorios de código, como GitHub. No coloque las claves ni los secretos en estos repositorios públicos de código fuente.
- **Proteja las interfaces de administración de máquinas virtuales en servicios híbridos IaaS y PaaS** Los servicios IaaS y PaaS se ejecutan en máquinas virtuales (VM). Según el tipo de servicio, existen varias interfaces de administración que permiten administrar estas máquinas virtuales directamente de forma remota. Se pueden usar protocolos de administración remota como el [Protocolo Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), el [Protocolo de escritorio remoto (RDP)](https://support.microsoft.com/kb/186607) y [PowerShell remoto](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting). En general, se recomienda no habilitar el acceso remoto directo a máquinas virtuales desde Internet. Si están disponibles, debe usar enfoques alternativos como el uso de una red privada virtual en una red virtual de Azure. Si no hay enfoques alternativos disponibles, asegúrese de utilizar frases de contraseña complejas y, si está disponible, la autenticación en dos fases (como [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)).
- **Use plataformas sólidas de autenticación y autorización**

  - Use identidades federadas en Azure AD en lugar de tiendas de usuarios personalizadas. Cuando se usan identidades federadas, se puede beneficiar de un enfoque basado en plataformas y delegar la administración de identidades autorizadas en sus asociados. Un enfoque federado resulta especialmente importante en escenarios en que los empleados terminan los contratos y dicha información necesita reflejarse a través de varios sistemas de identidad y autorización.
  - Use los mecanismos de autenticación y autorización proporcionados en la plataforma en lugar del código personalizado. La razón es que desarrollar código de autenticación personalizado puede resultar un método propenso a errores. La mayoría de los desarrolladores no son expertos en seguridad y es poco probable que conozcan los detalles y los últimos desarrollos en términos de autenticación y autorización. El código comercial (por ejemplo, de Microsoft) suele revisarse de manera amplia a efectos de seguridad.
  - Use Multi-factor Authentication. Multi-factor Authentication es el estándar actual para la autenticación y autorización porque evita las vulnerabilidades de seguridad inherentes a tipos de nombres de usuario y contraseñas de autenticación. El acceso a las interfaces de administración de Azure (portal/PowerShell remoto) y a los servicios usados por el cliente debe diseñarse y configurarse para usar [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md).
  - Use los protocolos de autenticación estándar, como OAuth2 y Kerberos. Estos protocolos se han sometido a revisiones exhaustivas del mismo nivel y posiblemente se implementen como parte de las bibliotecas de la plataforma a efectos de autenticación y autorización.

## <a name="next-steps"></a>Pasos siguientes
Este artículo se centra en las ventajas de seguridad que ofrece una implementación de PaaS de Azure. A continuación, conozca los procedimientos recomendados para proteger las soluciones móviles y web de PaaS. Se empieza por Azure App Service, Azure SQL Database y Azure SQL Data Warehouse. A medida que estén disponibles los artículos sobre procedimientos recomendados para otros servicios de Azure, se proporcionarán los vínculos correspondientes en la lista siguiente:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database y Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- Almacenamiento de Azure
- Azure REDIS Cache
- Bus de servicio
- Firewall de aplicaciones web

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png

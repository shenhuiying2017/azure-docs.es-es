---
title: Acerca de Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo Lab Services puede facilitar la creación, administración y protección de laboratorios con máquinas virtuales que pueden usar los desarrolladores, evaluadores, formadores, alumnos y otros usuarios.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: b42183369b9ad88c77a05a91fdba8fe0efca2a8c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="an-introduction-to-azure-lab-services-formerly-azure-devtest-labs"></a>Introducción a Azure Lab Services (anteriormente, Azure DevTest Labs)
Azure Lab Services le permite configurar rápidamente un entorno para el equipo (por ejemplo: entorno de desarrollo, entorno de pruebas, entorno de laboratorio educativo) en la nube. El propietario de un laboratorio crea uno, aprovisiona Windows o máquinas virtuales Linux, instala el software y las herramientas que necesite y las pone a disposición de los usuarios del laboratorio. Los usuarios del laboratorio se conectan a las máquinas virtuales (VM) del laboratorio y las usan para trabajos cotidianos, proyectos a corto plazo o ejercicios de clase. Cuando los usuarios empiecen a usar los recursos del laboratorio, el administrador podrá analizar el coste y uso entre varios laboratorios y establecer directivas exhaustivas para optimizar los costos de la organización o el equipo.

> [!IMPORTANT]
> Azure DevTest Labs se expande con nuevos tipos de laboratorio (Azure Lab Services). 
> 
> Azure Lab Services permite crear laboratorios administrados, como laboratorios educativos. El servicio controla toda la administración de la infraestructura para un laboratorio administrado, desde la sincronización de máquinas virtuales al control de errores y el escalado de infraestructuras. Los laboratorios administrados se encuentran actualmente en versión preliminar. Una vez finalice la versión preliminar, los nuevos tipos de laboratorio y los de DevTest Labs se unen bajo el nombre de Azure Lab Services en el que todos los tipos de laboratorio continúan evolucionando. 

## <a name="key-capabilities"></a>Principales capacidades
Azure Lab Services es compatible con las siguientes características o funcionalidades clave: 

- **Configuración rápida y flexible de un laboratorio**. Con Azure Lab Services, los propietarios de laboratorios pueden configurar un laboratorio rápidamente según sus necesidades. El servicio ofrece la opción de controlar todo el trabajo de infraestructura de Azure para laboratorios administrados o permite que los propietarios de laboratorio administren automáticamente y personalicen la infraestructura de la suscripción del propietario del laboratorio. El servicio proporciona escalado integrado y resiliencia de la infraestructura para laboratorios que administre el servicio. 
- **Experiencia simplificada para usuarios de laboratorios**. En un laboratorio administrado, como un laboratorio educativo, los usuarios pueden registrarse en un laboratorio con un código de registro y obtener acceso al laboratorio en cualquier momento para usar sus recursos. En un laboratorio personalizado creado en DevTest Labs, el propietario de un laboratorio puede conceder permisos a usuarios del laboratorio para crear máquinas virtuales, obtener acceso a ellas, administrar y volver a usar discos de datos y configurar secretos reutilizables.  
- **Optimización de costos y análisis**. Un propietario de laboratorio puede establecer programaciones de laboratorio para apagar e iniciar máquinas virtuales automáticamente. El propietario del laboratorio puede establecer una programación para especificar las franjas horarias de disponibilidad de las máquinas virtuales del laboratorio para los usuarios, establecer directivas de uso por usuario o por laboratorio para optimizar costos y analizar las tendencias de actividad y uso de un laboratorio. Actualmente, para laboratorios administrados, como laboratorios educativos, existe un subconjunto más pequeño de opciones de optimización de costos y análisis. 
- **Seguridad incrustada**. Un propietario de laboratorio puede configurar redes virtuales privadas y una subred para un laboratorio, además de habilitar una dirección IP pública compartida. Los usuarios de laboratorio pueden obtener acceso de forma segura a los recursos mediante su red virtual configurada con ExpressRoute o VPN de sitio a sitio. (solo disponible actualmente en DevTest Labs)
- **Integración en flujos de trabajo y herramientas**. Azure Lab Services le permite integrar laboratorios en los sistemas de administración y el sitio web de la organización. Puede aprovisionar automáticamente entornos a partir de las herramientas de integración continua (CI) o implementación continua (CD). (solo disponible actualmente en DevTest Labs)

## <a name="scenarios"></a>Escenarios
Estos son algunos de los escenarios compatibles con Azure Lab Services: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configuración de un laboratorio de equipos redimensionables en la nube para clases  

- Creación de un laboratorio educativo administrado. Solo indica al servicio exactamente qué necesita y creará y administrará la infraestructura del laboratorio para que pueda centrarse en la clase, no en los detalles técnicos del laboratorio. 
- Proporcione a los alumnos un laboratorio de máquinas virtuales que se configuran exactamente con todo lo necesario para una clase. Asigne a cada alumno un número limitado de horas para el uso de máquinas virtuales para trabajos de clase.  
- Desplace un laboratorio de equipos físicos del centro educativo a la nube. Escale automáticamente el número de máquinas virtuales solo al umbral de costo y uso máximo que establezca en el laboratorio. 
- Elimine el laboratorio con un solo clic cuando haya terminado. 

### <a name="use-devtest-labs-for-development-environments"></a>Uso de DevTest Labs para entornos de desarrollo 
Se puede utilizar Azure DevTest Labs para implementar numerosos escenarios clave pero uno de los principales escenarios implica el uso de DevTest Labs para máquinas de desarrollo host para desarrolladores. En este escenario, DevTest Labs ofrece estas ventajas: 

- Permite que los desarrolladores puedan aprovisionar rápidamente las máquinas de desarrollo a petición.
- Aprovisione entornos de Windows y Linux con artefactos y plantillas reutilizables.
- Los desarrolladores pueden personalizar fácilmente las máquinas de desarrollo, siempre que sea necesario.
- Los administradores pueden controlar los costos al garantizar que los desarrolladores no pueden recibir más máquinas virtuales de las que necesitan para el desarrollo y las máquinas virtuales se apagan cuando no se usan. 

Para obtener más información, consulte [Use DevTest Labs for development](devtest-lab-developer-lab.md) (Uso de DevTest Labs para desarrollo). 

### <a name="use-devtest-labs-for-test-environments"></a>Uso de DevTest Labs para entornos de prueba
Puede usar Azure DevTest Labs para implementar numerosos escenarios clave, aunque un escenario principal implica el uso de DevTest Labs para hospedar máquinas para evaluadores. En este escenario, DevTest Labs ofrece estas ventajas:

- Los evaluadores pueden probar la versión más reciente de la aplicación aprovisionando rápidamente entornos de Windows y Linux mediante plantillas y artefactos reutilizables.
- Los evaluadores pueden escalar verticalmente la prueba de carga aprovisionando varios agentes de prueba.
- Los administradores pueden controlar los costos al garantizar que los evaluadores no pueden recibir más máquinas virtuales de las que necesitan para las pruebas y las máquinas virtuales se apagan cuando no se usan.

Para obtener más información, consulte [Use DevTest Labs for testing](devtest-lab-test-env.md) (Uso de DevTest Labs para pruebas).

## <a name="user-profiles"></a>Perfiles de usuario
En este artículo se describen distintos perfiles de usuario de Azure Lab Services. 

### <a name="lab-account-owner"></a>Propietario de la cuenta del laboratorio
Por lo general, el administrador de TI de los recursos de nube de la organización, que es el propietario de la suscripción de Azure, actúa como propietario de la cuenta del laboratorio y realiza las siguientes tareas:   

- Configura una cuenta de laboratorio para la organización.
- Administra y configura las directivas en todos los laboratorios.
- Concede permisos a personas de la organización para crear un laboratorio en la cuenta del laboratorio.

### <a name="lab-creator"></a>Creador de laboratorio 
Normalmente, los usuarios, como un administrador o un responsable de desarrollo, un profesor, un host de Hackathon, un instructor o un instructor en línea, crean laboratorios en una cuenta de laboratorio. Un creador de laboratorio realiza las siguientes tareas: 

- Crea un laboratorio.
- Crea máquinas virtuales en el laboratorio. 
- Instala el software adecuado en máquinas virtuales.
- Especifica quién puede obtener acceso al laboratorio.
- Proporciona a los usuarios un vínculo al laboratorio.

### <a name="lab-user"></a>Usuario de laboratorio
Un usuario de laboratorio realiza las siguientes tareas:

- Usa el vínculo de registro al laboratorio que el usuario recibe de un creador de laboratorio para registrarse. 
- Se conecta a una máquina virtual del laboratorio y la usa para desarrollar, probar o realizar trabajos de clase. 

## <a name="next-steps"></a>Pasos siguientes
Introducción a la configuración de un laboratorio con Azure Lab Services:

- [Configuración de un laboratorio educativo](tutorial-setup-classroom-lab.md)
- [Configuración de un laboratorio personalizado](tutorial-create-custom-lab.md)

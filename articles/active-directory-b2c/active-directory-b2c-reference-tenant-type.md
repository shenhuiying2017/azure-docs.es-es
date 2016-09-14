<properties
	pageTitle="Azure Active Directory B2C: Escala de producción frente a versión preliminar de inquilinos de B2C | Microsoft Azure"
	description="Un tema sobre los tipos de inquilinos de Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: inquilinos de B2C de escala de producción frente a de versión preliminar

Si planea escribir una aplicación de producción en Azure Active Directory (Azure AD) B2C, es preciso que esté seguro de que tiene el "tipo" de inquilino correcto en la que activarla. Para ver lo que tiene, siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el Portal de Azure y mire en **Tenant type** (Tipo de inquilino).

## Resumen

Azure AD B2C admite aplicaciones de producción SOLO en inquilinos B2C de **escala de producción** de Norteamérica.

| Tipo de inquilino | Países o regiones | ¿Disponible generalmente? |
| ----------- | -------------- | --------------------- |
| **Inquilino de escala de producción** | Países o regiones de Norteamérica | Sí |
| **Inquilino de escala de producción** | Todos los países o regiones, excepto Norteamérica | No |
| **Inquilino de versión preliminar** | Todos los países o regiones | No |

> [AZURE.NOTE]
Los inquilinos de Azure AD B2C (para consumidores) actualmente no están disponibles en algunos países o regiones en los que están disponibles los inquilinos de Azure AD (para empleados). Para más información, consulte las secciones siguientes.

## Inquilino de B2C de escala de producción en Norteamérica

Si se [creado un inquilino de B2C](active-directory-b2c-get-started.md) en Norteamérica, es decir, en uno de los siguientes países o regiones: Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Puerto Rico y Trinidad y Tobago, Y el **tipo de inquilino** de la interfaz de usuario del administrador de B2C es **Escala de producción**, el inquilino puede utilizarse para aplicaciones de producción.

> [AZURE.NOTE]
Los inquilinos de la escala de producción son capaces de escalar a centenares de millones de identidades de consumidor por inquilino.

![Captura de pantalla de un inquilino de escala de producción](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## Obtención de una versión preliminar de un inquilino de B2C en cualquier país o región

Si había creado un inquilino de B2C durante el período de versión preliminar de Azure AD B2C, es probable que en **Tipo de inquilino** aparezca **Preview tenant** (Inquilino de versión preliminar). En ese caso, al inquilino se DEBE usar solo para fines de prueba y desarrollo, NO para aplicaciones de producción.

> [AZURE.IMPORTANT]
No hay ninguna ruta de migración de un inquilino de B2C de versión preliminar a un inquilino de B2C de escala de producción. Tenga en cuenta que existen problemas conocidos al eliminar un inquilino de B2C preliminar y volver a crear un inquilino B2C a escala de producción con el mismo nombre de dominio. Es necesario crear un inquilino de B2C a escala de producción con otro nombre de dominio.

![Captura de pantalla de un inquilino de versión preliminar](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## Inquilino de B2C de escala de producción fuera de Norteamérica

Azure AD B2C NO está disponible generalmente fuera de Norteamérica. Sin embargo, se pueden crear y usar inquilinos de escala de producción, con fines de desarrollo y pruebas, en cualquiera de los siguientes países o regiones: Alemania, Arabia Saudí, Argelia, Austria, Azerbaiyán, Bahréin, Bélgica, Bielorrusia, Bulgaria, Croacia, Chipre, Dinamarca, Egipto, Emiratos Árabes Unidos Eslovaquia, Eslovenia, España, Estonia, Finlandia, Francia, Grecia, Hungría, Irlanda, Islandia, Israel, Italia, Jordania, Kazajistán, Kenia, Kuwait, Letonia, Líbano, Liechtenstein, Lituania, Luxemburgo, Macedonia, Malta, Marruecos, Montenegro, Nigeria, Noruega , Omán, Países Bajos, Pakistán, Polonia, Portugal, Qatar, Reino Unido, República Checa, Rumanía, Rusia, Serbia, Sudáfrica, Suecia, Suiza, Túnez, Turquía y Ucrania.

Una vez que Azure AD B2C anuncie su disponibilidad general en anteriores países o regiones, puede seguir usando estos inquilinos a escala de producción y publicando las aplicaciones de producción sin ninguna pérdida de datos.

## Disponibilidad de los inquilinos de B2C

Actualmente, los inquilinos de B2C no están disponibles en los siguientes países o regiones: Afganistán, Argentina, Australia, Brasil, Chile, Colombia, Corea, Ecuador, Filipinas, India, Indonesia, Iraq, Japón, Malasia, Nueva Zelanda, Paraguay, Perú, RAE de Hong Kong, Singapur, Sri Lanka, Taiwán, Tailandia, Uruguay y Venezuela. No obstante, está previsto incluirlos en el futuro.

<!---HONumber=AcomDC_0831_2016-->
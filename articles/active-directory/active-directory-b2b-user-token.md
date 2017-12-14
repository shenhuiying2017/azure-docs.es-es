---
title: "Información sobre los tokens de usuario de la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Referencia de tokens de usuario de la colaboración B2B de Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/25/2017
ms.author: sasubram
ms.openlocfilehash: 7d19a6ff42ec905d68133e9940366f173140f916
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="understand-user-tokens-in-azure-ad-b2b-collaboration"></a>Información sobre los tokens de usuario de la colaboración B2B de Azure Active Directory

Si le interesa conocer el aspecto del token de un usuario de colaboración B2B, estos son los detalles del token de portador y el contenido del token para un invitado de Azure Active Directory (Azure AD) y un invitado de una cuenta de Microsoft en el inquilino de recursos (para el identificador de inquilino: 04dcc6ab-388a-4559-b527-fbec656300ea). Puede usar [https://jwt.io/](https://jwt.io/) o [http://calebb.net](http://calebb.net/)/ para ver el contenido del token de JSON Web Token (JWT).

## <a name="azure-ad-guest-token"></a>Token de invitado de Azure AD
```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ilk0dWVLMm9hSU5RaVFiNVlFQlNZVnlEY3BBVSIsImtpZCI6Ilk0dWVLMm9hSU5RaVFiNVlFQlNZVnlEY3BBVSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0LyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzA0ZGNjNmFiLTM4OGEtNDU1OS1iNTI3LWZiZWM2NTYzMDBlYS8iLCJpYXQiOjE0ODQ4MDM5MTgsIm5iZiI6MTQ4NDgwMzkxOCwiZXhwIjoxNDg0ODA3ODE4LCJhY3IiOiIxIiwiYWlvIjoiQVFBQkFBRUFBQURSTllSUTNkaFJTcm0tNEstYWRwQ0pJNWNncGtYQ0VOTHdnN1Z1emhFQURIajNOOWNIMzhRWGFBakhrYUtPRFhneWJpcnVRYVhpa3RZZ3I2M0xMQTVTVDlEeXV2dEtQSUdlXzJpVFRhdjNqSkxuTlRSZ2JWRFpwckhSaEtZbWl5RWdBQSIsImFsdHNlY2lkIjoiNTo6MTAwMzAwMDA4MDFCQUZDNyIsImFtciI6WyJwd2QiLCJyc2EiXSwiYXBwaWQiOiJjNDRiNDA4My0zYmIwLTQ5YzEtYjQ3ZC05NzRlNTNjYmRmM2MiLCJhcHBpZGFjciI6IjIiLCJlX2V4cCI6MTA4MDAsImVtYWlsIjoicmFqZXNiQG1pY3Jvc29mdC5jb20iLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaW5fY29ycCI6InRydWUiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTk1IiwibmFtZSI6InJhamVzaCIsIm9pZCI6IjA1ODAyY2M1LTgxMWUtNDZiZC1iMWI2LTU5NDZlNjY4ODIyZiIsInBsYXRmIjoiMyIsInB1aWQiOiIxMDAzM0ZGRjlEOEY5OTUzIiwic2NwIjoidXNlcl9pbXBlcnNvbmF0aW9uIiwic3ViIjoiS1d3QnVCNk5ROU5UYmpoSUI1OHEwM2FlQVl6cEk2TWxiMkpncGk1aV9ITSIsInRpZCI6IjA0ZGNjNmFiLTM4OGEtNDU1OS1iNTI3LWZiZWM2NTYzMDBlYSIsInVuaXF1ZV9uYW1lIjoicmFqZXNiQG1pY3Jvc29mdC5jb20iLCJ2ZXIiOiIxLjAifQ.Vllr1hGXpBlpXDBKRHHYbMr_1_DwKNY3eCObBOfEaxJirwqujqCZodPrAkIOJlFYyhkILyHZQUi_D1w7XoPsd6U4GQlgOoFfzbye-P_NdRFabHMlv32gCgHz1xo11aPP453EiwwG5OHnWaHYLBpuqi3sNeKx06xbTFj07HmADDaR4aM0jwy031d6GkD0LdU-Xkazi5-h8parVRLOkkLZA0oxMFoxl_-VHr1hOzxCkbWgRoug4t97161i5tGil99CcpJ6NK8uQld7TveC40sjJ735Sksn-Uq_NZcJuXCEVsH0xK5evaeFBFSEqACXjKTvYkJWtAx8Kr8yWZAcEg0YMQ
```

## <a name="microsoft-account-guest-token"></a>Token de cuenta Microsoft para invitados
```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ilk0dWVLMm9hSU5RaVFiNVlFQlNZVnlEY3BBVSIsImtpZCI6Ilk0dWVLMm9hSU5RaVFiNVlFQlNZVnlEY3BBVSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0LyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzA0ZGNjNmFiLTM4OGEtNDU1OS1iNTI3LWZiZWM2NTYzMDBlYS8iLCJpYXQiOjE0ODQ4MDMwNjEsIm5iZiI6MTQ4NDgwMzA2MSwiZXhwIjoxNDg0ODA2OTYxLCJhY3IiOiIxIiwiYWlvIjoiQVFBQkFBRUFBQURSTllSUTNkaFJTcm0tNEstYWRwQ0pEeEd4a3lUdmJ2d1RoSHJnTEdPaGZEbTA1aXJndC1lR1d3YTl5QUZQQTJQc19nZHF2bHQ1X1AtaDhrT2IwdUdza3dyYklBbUhvMEtRM005N2ZCVlRtdzRKY0NfaFVkWW1PZ25QYVlOY1BRQXBIYmFMcUlaZGhaRXhtQVZJeXFmaElBQSIsImFsdHNlY2lkIjoiMTpsaXZlLmNvbTowMDAzMDAwMEEwNzBCOTYyIiwiYW1yIjpbInB3ZCJdLCJhcHBpZCI6ImM0NGI0MDgzLTNiYjAtNDljMS1iNDdkLTk3NGU1M2NiZGYzYyIsImFwcGlkYWNyIjoiMiIsImVfZXhwIjoxMDgwMCwiZW1haWwiOiJiYXNhcmFqZXNoQGxpdmUuY29tIiwiZmFtaWx5X25hbWUiOiJiYXNhIiwiZ2l2ZW5fbmFtZSI6InJhamVzaCIsImlkcCI6ImxpdmUuY29tIiwiaXBhZGRyIjoiMTY3LjIyMC4xLjE5NSIsIm5hbWUiOiJiYXNhcmFqZXNoIiwib2lkIjoiMjU0NmU3NDEtNmZjNi00ZDI0LTg2NTQtZjkyNDc5MzI0ZjM3IiwicGxhdGYiOiIzIiwicHVpZCI6IjEwMDMzRkZGOURBQjk2NDYiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiI4Y2N5OEh4cmE5UTl2aGdYOXhBODFBeWJEV3dsVmxjXzRBZVJYZ2lzamM4IiwidGlkIjoiMDRkY2M2YWItMzg4YS00NTU5LWI1MjctZmJlYzY1NjMwMGVhIiwidW5pcXVlX25hbWUiOiJsaXZlLmNvbSNiYXNhcmFqZXNoQGxpdmUuY29tIiwidmVyIjoiMS4wIn0.LSIBlJpElXpsGXOGaFINW-jOBHsI0Dxe3oX-YIEsccegDCspl6UnRjpwzs0nBL09B4N0oqLd7ZwXZAQURpgaAFnWvROxkIGpNTE_ppSKU1suud8keG5VnTEu82em95G1_c_eW1nOemPvbADCC8h08p2wxNm8QyEhmYqauN6qYbeqOnioRERXO3zOPg8nSXFcGPhvumJ_BW8XKnW4zLdhK78c3PgynPnwtIm08SksMRDzGMgUc9RK1bpPQtgX8iFQByEljf5cuE_h_e1Nr5Y4StrhS3JCiQLTYZ727YY-lSm5DERiQrt7MkP5BHprEmSByofSvACj5TmVdqBFUjobuA
```


## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)

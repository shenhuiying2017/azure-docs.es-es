<properties
	pageTitle="Sincronización de Azure AD Connect: atributos sincronizados con Azure Active Directory"
	description="Enumera los atributos que se sincronizan con Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Sincronización de Azure AD Connect: atributos sincronizados con Azure Active Directory

En este tema se enumeran los atributos sincronizados mediante Sincronización de Azure AD Connect.<br> Los atributos se agrupan por aplicaciones relacionadas con Azure AD.
 




## Office 365 ProPlus

| Nombre del atributo| Usuario| Comentario |
| --- | :-: | --- |
| accountEnabled| X| Deriva de userAccountControl|
| cn| X| |
| DisplayName| X| |
| objectSID| X| |
| pwdLastSet| X| |
| sourceAnchor| X| El atributo usado para los usuarios se configura en la guía de instalación.|
| usageLocation| X| msExchUsageLocation en AD DS|
| userPrincipalName| X| |


## Exchange Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Deriva de userAccountControl|
| assistant| X| X| | |
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| DisplayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| |
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| |
| msExchExtensionCustomAttribute2| X| X| X| |
| msExchExtensionCustomAttribute3| X| X| X| |
| msExchExtensionCustomAttribute4| X| X| X| |
| msExchExtensionCustomAttribute5| X| X| X| |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg-IsOrganizational| | | X| |
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Deriva de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| El atributo usado para los usuarios se configura en la guía de instalación.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | msExchUsageLocation en AD DS|
| userCertificate| X| X| | |
| userPrincipalName| X| | | |
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |


## SharePoint Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Deriva de userAccountControl|
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| DisplayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Deriva de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| El atributo usado para los usuarios se configura en la guía de instalación.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | msExchUsageLocation en AD DS|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |

## Lync Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Deriva de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| DisplayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| |
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Deriva de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| El atributo usado para los usuarios se configura en la guía de instalación.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| usageLocation| X| | | msExchUsageLocation en AD DS|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |


## Azure RMS

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define si se habilita una cuenta.|
| cn| X| | X| Nombre común o alias. Frecuentemente el prefijo del valor de [mail].|
| DisplayName| X| X| X| Una cadena que representa el nombre que se muestra a menudo como el nombre descriptivo (nombre, apellido).|
| mail| X| X| X| Dirección de correo electrónico completa.|
| member| | | X| |
| objectSID| X| | X| Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| proxyAddresses| X| X| X| Propiedad mecánica. Usado por Azure AD. Contiene todas las direcciones de correo electrónico secundarias para el usuario.|
| pwdLastSet| X| | | Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos.|
| securityEnabled| | | X| Deriva de groupType.|
| sourceAnchor| X| X| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| usageLocation| X| | | Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| | | Este UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|


## Intune

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Deriva de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| DisplayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Deriva de groupType|
| sourceAnchor| X| X| X| El atributo usado para los usuarios se configura en la guía de instalación.|
| usageLocation| X| | | msExchUsageLocation en AD DS|
| userPrincipalName| X| | | |


## Dynamics CRM

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Deriva de userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| DisplayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Deriva de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| El atributo usado para los usuarios se configura en la guía de instalación.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| título| X| X| | |
| usageLocation| X| | | msExchUsageLocation en AD DS|
| userPrincipalName| X| | | |


## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO6-->
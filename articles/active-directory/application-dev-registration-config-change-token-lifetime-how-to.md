---
title: "Modificación de los valores predeterminados de vigencia de los tokens en una aplicación personalizada | Microsoft Docs"
description: "Aprenda a actualizar las directivas de vigencia de los tokens para la aplicación que está desarrollando en Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 54c30123bb582f515dfb0324cdfd897a6c4af5c0
ms.contentlocale: es-es
ms.lasthandoff: 04/14/2017

---


# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Modificación de los valores predeterminados de vigencia de los tokens en una aplicación personalizada

Azure AD Premium permite a los desarrolladores de aplicaciones y a los administradores de inquilinos configurar la vigencia de los tokens emitidos para clientes no confidenciales. Las directivas de vigencia de los tokens se establecen para todos los inquilinos o para los recursos a los que se va a acceder.

 * Para establecer una directiva de vigencia de tokens, debe descargar el [módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Ejecute el comando **Connect-AzureAD -Confirm**.

 * En esta directiva de ejemplo, se establece la actualización del token con arreglo a un único factor de antigüedad máxima. Cree la directiva: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Consulte el documento sobre la [configuración de la vigencia de los tokens](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes) para aprender a crear una directiva personalizada.

## <a name="next-steps"></a>Pasos siguientes
[Configuración de la vigencia de los tokens](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Referencia de tokens de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)



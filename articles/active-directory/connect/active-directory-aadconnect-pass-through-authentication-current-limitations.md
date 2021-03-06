---
title: "Autenticación de paso a través de Azure AD: limitaciones actuales | Microsoft Docs"
description: "En este artículo se describen las limitaciones actuales de la autenticación de paso a través de Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 3ed2ee8c996628caabefd60b5ecff7528402eb52
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticación de paso a través de Azure Active Directory: limitaciones actuales

>[!IMPORTANT]
>La autenticación de paso a través de Azure AD se encuentra actualmente en versión preliminar. Es una característica gratuita y no es necesario usar ninguna versión de pago de Azure AD para usarla. La autenticación de paso a través solo está disponible en la instancia mundial de Azure AD y no en [Microsoft Cloud Alemania](http://www.microsoft.de/cloud-deutschland) ni en [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Escenarios admitidos

Los siguientes escenarios son totalmente compatibles durante la versión preliminar:

- Inicios de sesión de usuario en todas las aplicaciones basadas en explorador web.
- Inicios de sesión de usuario en las aplicaciones cliente de Office 365 que admitan la [autenticación moderna](https://aka.ms/modernauthga).

## <a name="unsupported-scenarios"></a>Escenarios no admitidos

En la versión preliminar, los siguientes escenarios _no_ se admiten:

- Inicios de sesión de usuario en las aplicaciones cliente de Office heredadas y en Exchange ActiveSync (es decir, las aplicaciones de correo electrónico nativas en dispositivos móviles). Se recomienda a las organizaciones que cambien a la autenticación moderna, en caso de que sea posible. La autenticación moderna permite la compatibilidad de la autenticación de paso a través, pero también le ayuda a proteger sus identidades mediante características de [acceso condicional](../active-directory-conditional-access.md) como Multi-Factor Authentication (MFA).
- Inicios de sesión de usuario en las aplicaciones cliente de Skype Empresarial.
- Inicios de sesión de usuario en PowerShell v1.0. Se recomienda que use PowerShell v2.0 en su lugar.
- Azure AD Join para dispositivos con Windows 10.

>[!IMPORTANT]
>Como solución alternativa para escenarios no admitidos, habilite la sincronización de hash de contraseñas en la página [Características opcionales](active-directory-aadconnect-get-started-custom.md#optional-features) del asistente de Azure AD Connect. La sincronización de hash de contraseñas _solo_ actúa como reserva en los escenarios anteriores (y _no_ como reserva genérica en la autenticación de paso a través). Si no necesita estos escenarios, desactive la sincronización hash de contraseñas.

## <a name="next-steps"></a>Pasos siguientes
- [**Inicio rápido** ](active-directory-aadconnect-pass-through-authentication-quick-start.md): desarrollo y ejecución de la autenticación de paso a través de Azure AD.
- [**Profundización técnica** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md): descripción del funcionamiento de esta característica.
- [**Preguntas más frecuentes**](active-directory-aadconnect-pass-through-authentication-faq.md): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): aprenda a resolver problemas comunes de esta característica.
- [**SSO de conexión directa de Azure AD**](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.


---
title: "Descripción de acceso a los recursos de Azure | Microsoft Docs"
description: En este tema se explican conceptos acerca del uso de los administradores de suscripciones para controlar el acceso a los recursos en todo el portal de Azure.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: d9143c1ec2075f71659e8deaadc93d1972ea9a85
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="understanding-resource-access-in-azure"></a>Descripción de acceso a los recursos de Azure
> [!NOTE]
> En este tema se explican conceptos acerca del uso de los administradores de suscripciones para controlar el acceso a los recursos en todo el Portal de Azure. Como alternativa, el Portal de vista previa de Azure proporciona [control de acceso basado en rol](role-based-access-control-configure.md) para que los recursos de Azure se puedan administrar con mayor precisión.
> 
> 

En octubre de 2013, el Portal de Azure clásico y las API de administración de servicios se integraron con Azure Active Directory para sentar las bases para mejorar la experiencia del usuario al administrar el acceso a los recursos de Azure. Azure Active Directory ya proporciona excelentes funcionalidades, como la administración de usuarios, la sincronización de directorios locales, la autenticación multifactor y el control de acceso a aplicaciones. Naturalmente, también deben estar disponibles para administrar los recursos globales de Azure.

El control de acceso de Azure se inicia desde una perspectiva de facturación. El propietario de una cuenta de Azure, a la que se accede desde el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions), es el administrador de la cuenta (AA). Las suscripciones son un contenedor para la facturación, pero también actúan como límite de seguridad: cada suscripción tiene un administrador de servicios (SA) que puede agregar, quitar y modificar recursos de Azure en esa suscripción mediante el [Portal de Azure clásico](https://manage.windowsazure.com/). El administrador de servicios predeterminado de una suscripción nueva es el administrador de cuenta, pero este puede cambiar al administrador de servicios en el Centro de cuentas de Azure.

<br><br>![Cuentas de Azure][1]

Las suscripciones también tienen una asociación con un directorio. El directorio define un conjunto de usuarios. Pueden ser usuarios del trabajo o de la escuela que crearon el directorio, o bien pueden ser usuarios externos (es decir, Cuentas Microsoft). Las suscripciones son accesibles por un subconjunto de esos usuarios del directorio que se han asignado como administrador de servicios (SA) o coadministrador (CA); la única excepción es que, por razones heredadas, las Cuentas de Microsoft (antes Windows Live ID) pueden asignarse como SA o CA sin estar presentes en el directorio.

<br><br>![Control de acceso de Azure][2]

Una funcionalidad del Portal de Azure clásico permite a las asociaciones de seguridad que han iniciado sesión con una cuenta Microsoft cambiar el directorio al que está asociada una suscripción mediante el uso del comando **Editar directorio** de la página **Suscripciones** de **Configuración**. Tenga en cuenta que esta operación tiene implicaciones sobre el control de acceso de esa suscripción.

> [!NOTE]
> El comando **Editar directorio** del Portal de Azure clásico no está disponible para los usuarios que han iniciado sesión con una cuenta profesional o educativa, porque con dichas cuentas solo pueden iniciar sesión en el directorio al que pertenecen.
> 
> 

<br><br>![Flujo de inicio de sesión de usuario simple][3]

En un caso simple, una organización (como Contoso) aplicará la facturación y el control de acceso al mismo conjunto de suscripciones. Es decir, el directorio está asociado a suscripciones que pertenecen a una sola cuenta de Azure. Cuando inician sesión correctamente en el Portal de Azure clásico, los usuarios ven dos colecciones de recursos (representados en color naranja en la ilustración anterior):

* Directorios donde existe su cuenta de usuario (con origen o agregada como entidad de seguridad externa). Tenga en cuenta que el directorio usado para el inicio de sesión no es pertinente para este cálculo, por lo que los directorios se muestran independientemente de dónde se inicia sesión.
* Recursos que forman parte de las suscripciones asociadas al directorio usados para el inicio de sesión Y a los que puede tener acceso el usuario (donde sea un SA o un CA).

<br><br>![Usuario con varias suscripciones y directorios][4]

Los usuarios con suscripciones en varios directorios tienen la posibilidad de cambiar el contexto actual del Portal de Azure clásico mediante el filtro de suscripciones. De forma encubierta, esto da lugar a un inicio de sesión diferente en un directorio distinto, pero se realiza de forma transparente mediante el inicio de sesión único (SSO).

Operaciones tales como mover recursos entre suscripciones pueden ser más difíciles como resultado de esta vista única del directorio de suscripciones. Para realizar la transferencia de recursos, puede que sea necesario usar primero el comando **Editar directorio** de la página Suscripciones de **Configuración** para asociar las suscripciones al mismo directorio.

## <a name="next-steps"></a>Pasos siguientes
* Para más información acerca de cómo cambiar los administradores de una suscripción de Azure, consulte [Incorporación o cambio de roles de administrador de Azure](../billing/billing-add-change-azure-subscription-administrator.md)
* Para obtener más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* Para más información sobre cómo asignar roles en Azure AD, consulte [Asignación de roles de administrador en Azure Active Directory (Azure AD)](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png


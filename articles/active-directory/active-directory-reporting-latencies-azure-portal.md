---
title: Latencias de informes de Azure Active Directory | Microsoft Docs
description: "Obtenga información acerca de la cantidad de tiempo necesaria para que los eventos de informes aparezcan en Azure Portal"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: a1e385c235c3530744a83527574880c5f37caadd
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

---
# <a name="azure-active-directory-reporting-latencies"></a>Latencias de informes de Azure Active Directory

Con los [informes](active-directory-preview-explainer.md) de Azure Active Directory, obtendrá toda la información que necesita para determinar cómo funciona el entorno. La cantidad de tiempo necesaria para que los datos de informes aparezcan en Azure Portal se denomina latencia. 

En este tema se muestra la información de latencia de todas las categorías de informes en Azure Portal. 


## <a name="activity-reports"></a>Informes de actividad

Hay dos áreas de informes de actividad:

- **Actividades de inicio de sesión** : información sobre el uso de las aplicaciones administradas y las actividades de inicio de sesión de usuario
- **Registros de auditoría** : información de la actividad del sistema acerca de los usuarios y administración de grupos, sus aplicaciones administradas y actividades de directorio

La tabla siguiente enumera la información de latencia para los informes de actividad.

| Informe | Mínima | Media | Máxima |
| :-- | --- | --- | --- |
| Registros de auditoría             | 30 minutos  | 45 minutos | 1 hora     |
| Inicios de sesión               | 15 minutos  | 15 minutos | 2 horas*   |

>[!NOTE]
> En el caso de algunos datos de actividades de inicio de sesión procedentes de aplicaciones de oficina heredadas, los datos del informe pueden tardar hasta 8 horas en aparecer. 


## <a name="security-reports"></a>Informes de seguridad

Hay dos áreas de informes de seguridad:

- **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. 
- **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. 

La tabla siguiente enumera la información de latencia para los informes de seguridad.

| Informe | Mínima | Media | Máxima |
| :-- | --- | --- | --- |
| Usuarios en riesgo          | 5 minutos   | 15 minutos  | 2 horas  |
| Inicios de sesión no seguros         | 5 minutos   | 15 minutos  | 2 horas  |

## <a name="risk-events"></a>Eventos de riesgo

Azure Active Directory utiliza algoritmos y heurística de aprendizaje automático adaptable para detectar acciones sospechosas que están relacionadas con las cuentas de usuario. Cada acción sospechosa detectada se almacena en un registro llamado evento de riesgo.

La tabla siguiente enumera la información de latencia para eventos de riesgo.

| Informe | Mínima | Media | Máxima |
| :-- | --- | --- | --- |
| Inicios de sesión desde direcciones IP anónimas |5 minutos |15 minutos |2 horas |
| Inicios de sesión desde ubicaciones desconocidas |5 minutos |15 minutos |2 horas |
| Usuarios con credenciales perdidas |2 horas |4 horas |8 horas |
| Viaje imposible a ubicaciones inusuales |2 horas |4 horas |8 horas  |
| Inicios de sesión desde dispositivos infectados |2 horas |4 horas |8 horas  |
| Inicios de sesión desde direcciones IP con actividad sospechosa |2 horas |4 horas |8 horas  |



## <a name="next-steps"></a>Pasos siguientes

Si desea obtener más información acerca de los informes de actividad en Azure Portal, consulte:

- [Informes de actividad de inicio de sesión en el portal de Azure Active Directory](active-directory-reporting-activity-sign-ins.md)
- [Informes de actividad de auditoría en el portal de Azure Active Directory](active-directory-reporting-activity-audit-logs.md)

Si desea obtener más información acerca de los informes de seguridad en Azure Portal, consulte:

- [Informe de seguridad de usuarios en riesgo en el portal de Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Informe de inicios de sesión poco seguros en el portal de Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)

Si desea obtener más información acerca de los eventos de riesgo, consulte [Eventos de riesgo de Azure Active Directory](active-directory-reporting-risk-events.md).


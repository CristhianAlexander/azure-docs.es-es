---
title: Direcciones IP que emplea Application Insights | Microsoft Docs
description: Excepciones para el firewall del servidor requeridas por Application Insights
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: eec83ceb6edbc1aaa68d51a85d2a913063677530
ms.contentlocale: es-es
ms.lasthandoff: 06/29/2017


---
# <a name="ip-addresses-used-by-application-insights"></a>Direcciones IP que emplea Application Insights
El servicio [Azure Application Insights](app-insights-overview.md) usa diversas direcciones IP. Quizás deba conocer estas direcciones si la aplicación que está supervisando se hospeda bajo el amparo de un firewall.

> [!NOTE]
> Aunque estas direcciones son estáticas, es posible que tengamos que cambiarlas de vez en cuando.
> 
> 

## <a name="outgoing-ports"></a>Puertos de salida
Debe abrir algunos puertos de salida en el firewall del servidor para permitir que el SDK de Application Insights o el Monitor de estado envíe datos al portal:

| Propósito | URL | IP | Puertos |
| --- | --- | --- | --- |
| Telemetría |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244 |443 |
| Secuencia de métricas en directo |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |
| Telemetría interna |breeze.aimon.applicationinsights.io |52.161.11.71 |443 |

## <a name="status-monitor"></a>Monitor de estado
Configuración del Monitor de estado; solo lo necesitará en caso de que tenga que hacer cambios.

| Propósito | URL | IP | Puertos |
| --- | --- | --- | --- |
| Configuración |`management.core.windows.net` | |`443` |
| Configuración |`management.azure.com` | |`443` |
| Configuración |`login.windows.net` | |`443` |
| Configuración |`login.microsoftonline.com` | |`443` |
| Configuración |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Configuración |`auth.gfx.ms` | |`443` |
| Configuración |`login.live.com` | |`443` |
| Instalación |`packages.nuget.org` | |`443` |

## <a name="hockeyapp"></a>HockeyApp
| Propósito | URL | IP | Puertos |
| --- | --- | --- | --- |
| Datos de bloqueo |gate.hockeyapp.net |104.45.136.42 |80, 443 |

## <a name="availability-tests"></a>Pruebas de disponibilidad
Esta es la lista de direcciones a partir de las cuales se ejecutan [pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md) . Si quiere ejecutar pruebas web en su aplicación, pero su servidor web está restringido atender únicamente las solicitudes de clientes específicos, tendrá que permitir el tráfico entrante de nuestros servidores de pruebas de disponibilidad.

Abra los puertos 80 (http) y 443 (https) para asumir el tráfico entrante de estas direcciones (las IP se agrupan por ubicación):

```
AU : Sydney
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
BR : Sao Paulo
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
CH : Zurich
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48
FR : Paris
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
94.245.72.52
94.245.72.53
HK : Hong Kong
207.46.71.54
207.46.71.52
207.46.71.55
207.46.71.38
207.46.71.51
207.46.71.57
207.46.71.58
207.46.71.37
IE : Dublin
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.47
157.55.14.64
157.55.14.65
157.55.14.43
157.55.14.44
157.55.14.49
157.55.14.50
JP : Kawaguchi
202.89.228.67
202.89.228.68
202.89.228.69
202.89.228.57
NL : Amsterdam
213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.61
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.63
213.199.178.64
RU : Moscow
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38
SE : Stockholm
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
SG : Singapore
52.187.29.7 
52.187.179.17 
52.187.76.248 
52.187.43.24 
52.163.57.91 
52.187.30.120 
US : CA-San Jose
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.157
207.46.98.169
207.46.98.170
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.162
207.46.98.171
207.46.98.172
US : FL-Miami
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
65.54.78.57
65.54.78.58
65.54.78.59
65.54.78.60
US : IL-Chicago
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.55
207.46.14.63
207.46.14.64
207.46.14.51
207.46.14.52
207.46.14.56
207.46.14.65
207.46.14.67
207.46.14.68
US : TX-San Antonio
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.81
65.55.82.77
65.55.82.78
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
US : VA-Ashburn
13.106.106.20
13.106.106.21
13.106.106.22
13.106.106.23
13.106.106.24
13.106.106.25
13.106.106.26
13.106.106.27
13.106.106.28
13.106.106.29

```  

## <a name="data-access-api"></a>API de acceso a datos
| Propósito | URI | IP | Puertos |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |13.82.26.252<br/>40.76.213.73 |80 443 |
| Documentos de API |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |13.82.24.149<br/>40.114.82.10 |80 443 |
| API Interna |aigs.aisvc.visualstudio.com<br/>aigs1.aisvc.visualstudio.com<br/>aigs2.aisvc.visualstudio.com<br/>aigs3.aisvc.visualstudio.com<br/>aigs4.aisvc.visualstudio.com<br/>aigs5.aisvc.visualstudio.com<br/>aigs6.aisvc.visualstudio.com |dinámico|443 |

## <a name="application-insights-analytics"></a>Application Insights Analytics

| Propósito | URI | IP | Puertos |
| --- | --- | --- | --- |
| Portal de Analytics | analytics.applicationinsights.io | dinámico | 80 443 |
| CDN | applicationanalytics.azureedge.net | dinámico | 80 443 |
| Medios + CDN | applicationanalyticsmedia.azureedge.net | dinámico | 80 443 |

Nota: el dominio *.applicationinsights.io es propiedad del equipo de Application Insights.

## <a name="application-insights-azure-portal-extension"></a>Extensión de Application Insights de Azure Portal

| Propósito | URI | IP | Puertos |
| --- | --- | --- | --- |
| Extensión de Application Insights | stamp2.app.insightsportal.visualstudio.com | dinámico | 80 443 |
| CDN de la extensión Application Insights | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dinámico | 80 443 |

## <a name="application-insights-sdks"></a>SDK de Application Insights

| Propósito | URI | IP | Puertos |
| --- | --- | --- | --- |
| CDN del SDK de JS de Application Insights | az416426.vo.msecnd.net | dinámico | 80 443 |
| SDK de Java de Application Insights | aijavasdk.blob.core.windows.net | dinámico | 80 443 |

## <a name="profiler"></a>Generador de perfiles

| Propósito | URI | IP | Puertos |
| --- | --- | --- | --- |
| Agente | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | dinámico | 443
| Portal | gateway.azureserviceprofiler.net | dinámico | 443
| Almacenamiento | *.core.windows.net | dinámico | 443

## <a name="snapshot-debugger"></a>Depurador de instantáneas

| Propósito | URI | IP | Puertos |
| --- | --- | --- | --- |
| Agente | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | dinámico | 443
| Portal | ppe.gateway.azureserviceprofiler.net | dinámico | 443
| Almacenamiento | *.core.windows.net | dinámico | 443


---
title: Historial de versiones de conectores | Microsoft Docs
description: En este tema se incluyen todas las versiones de los conectores para Forefront Identity Manager (FIM) y Microsoft Identity Manager (MIM).
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: e9699abe0c1bdb6ea449c99e087ae56adb717b8d
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---
# <a name="connector-version-release-history"></a>Historial de versiones de conectores
Los conectores de Forefront Identity Manager (FIM) y Microsoft Identity Manager (MIM) se actualizan con frecuencia.

> [!NOTE]
> Este tema solo está disponible en FIM y MIM. Estos conectores no se admiten en Azure AD Connect.

En este tema se muestran todas las versiones de los conectores que se han publicado.

Vínculos relacionados:

* [Descargar los últimos conectores](http://go.microsoft.com/fwlink/?LinkId=717495)
* [conector LDAP genérico](active-directory-aadconnectsync-connector-genericldap.md) 
* [conector SQL genérico](active-directory-aadconnectsync-connector-genericsql.md) 
* [conector de servicios web](http://go.microsoft.com/fwlink/?LinkID=226245) 
* [conector PowerShell](active-directory-aadconnectsync-connector-powershell.md) 
* [conector Lotus Domino](active-directory-aadconnectsync-connector-domino.md) 

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Problemas corregidos:

* Servicios Web genéricos:
  * La herramienta Wsconfig no convirtió correctamente la matriz JSON de "solicitud de ejemplo" para el método del servicio REST. Por este motivo, se han producido problemas de serialización de esta matriz JSON para la solicitud de REST.
  * La herramienta de configuración de conectores del servicio web no admite el uso de símbolos de espacio en los nombres de atributo JSON. Se puede agregar manualmente un patrón de sustitución al archivo WSConfigTool.exe.config, por ejemplo, ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```

* Lotus Notes:
  * Cuando la opción **Allow custom certifiers for Organization/Organizational Units** (Permitir certificadores personalizados para la organización/unidades organizativas) está deshabilitada, el conector produce un error durante la exportación (Actualización) Después del flujo de exportación, se exportan todos los atributos a Domino, pero en el momento de la exportación se devuelve una excepción KeyNotFoundException a Sync. Esto sucede porque se produce un error en la operación de cambio de nombre al intentar cambiar DN (atributo UserName) cambiando uno de los atributos siguientes:  
    - Apellidos
    - Nombre
    - MiddleInitial
    - AltFullName
    - AltFullNameLanguage
    - ou
    - altcommonname

  * Cuando la opción **Allow custom certifiers for Organization/Organizational Units** (Permitir certificadores personalizados para la organización/unidades organizativas) está habilitada pero los certificadores necesarios siguen vacíos, se produce una excepción KeyNotFoundException.

### <a name="enhancements"></a>Mejoras:

* SQL genérico:
  * **Escenario: se volvió a implementar:** característica "*"
  * **Descripción de la solución:** se cambió el enfoque para el [control de los atributos de referencia multivalor](active-directory-aadconnectsync-connector-genericsql.md).


### <a name="fixed-issues"></a>Problemas corregidos:

* Servicios Web genéricos:
  * No se puede importar la configuración del servidor si está presente el conector de WebService
  * El conector de WebService no funciona con varios servicios web

* SQL genérico:
  * No hay tipos de objeto para atributos de referencia de un único valor
  * La importación diferencial en la estrategia de Change Tracking elimina el objeto cuando se quita el valor de la tabla multivalor
  * OverflowException en conector GSQL con DB2 en AS/400

Lotus:
  * Se ha agregado la opción para habilitar o deshabilitar la búsqueda de unidades organizativas antes de abrir la página de GlobalParameters

## <a name="114430"></a>1.1.443.0

Publicación: marzo de 2017

### <a name="enhancements"></a>Mejoras

* SQL genérico:</br>
  **Síntomas del escenario:** se trata de una limitación conocida con el Conector de SQL en la que solo se permite una referencia a un tipo de objeto y se requiere referencia cruzada con los miembros. </br>
  **Descripción de la solución:** en el paso de procesamiento de las referencias en que se elige la opción "*", TODAS las combinaciones de tipos de objeto se devolverán al motor de sincronización.

>[!Important]
- Con esto se crearán muchos marcadores de posición
- Es necesario asegurarse de que la nomenclatura sea única entre todos los tipos de objeto.


* LDAP genérico:</br>
 **Escenario:** cuando solo se seleccionan algunos contenedores en una partición específica, la búsqueda se seguirá haciendo en toda la partición. El servicio de sincronización filtrará la partición específica, pero MA no lo hará, lo que podría provocar una degradación del rendimiento. </br>

 **Descripción de la solución:** se modificó el código del conector GLDAP para permitir que pase por todos los contenedores y objetos de búsqueda de cada uno de ellos, en lugar de buscar en toda la partición.


* Lotus Domino:

  **Escenario:** compatibilidad de la eliminación de correo de Domino para la eliminación de una persona durante una exportación. </br>
  **Solución:** configuración de eliminación de correo configurable para la eliminación de una persona durante una exportación.

### <a name="fixed-issues"></a>Problemas corregidos:
* Servicios Web genéricos:
 * Cuando cambia la URL de servicio en proyectos wsconfig de SAP predeterminados mediante la herramienta de configuración WebService, aparece el error siguiente: No se pudo encontrar parte de la ruta de acceso

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* LDAP genérico:
 * Conector GLDAP no ve todos los atributos en AD LDS
 * El asistente se interrumpe cuando no se detectan atributos de UPN desde el esquema de directorio de LDAP
 * Error de importaciones diferenciales cuando no hay errores de detección durante la importación completa, cuando no se selecciona el atributo "objectclass"
 * La página de configuración "Configurar particiones y jerarquías" no muestra ningún objeto en que el tipo sea igual a la partición de los servidores Novel en MA del LDAP  
genérico. Solo se muestran objetos desde la partición RootDSE.


* SQL genérico:
 * Corrección para el error en que el atributo multivalor de importación diferencial de límite de SQL genérico no se puede importar
 * Cuando se exportan valores eliminados o agregados del atributo multivalor, no se eliminan ni agregan en el origen de datos.  


* Lotus Notes:
 * Un campo específico "Nombre completo" aparece correctamente en el metaverso; sin embargo, cuando se realiza la exportación a Notes, el valor del atributo es Null o Empty.
 * Corrección para error de certificador duplicado
 * Cuando se selecciona un objeto sin datos en el conector para Lotus Domino con otros objetos, se recibe el error de detección durante la importación completa.
 * Cuando la importación diferencial se ejecuta en el conector para Lotus Dominio, al final de esa ejecución, el servicio Microsoft.IdentityManagement.MA.LotusDomino.Service.exe a veces muestra un error de aplicación.
 * La pertenencia general a grupos funciona correctamente y se mantiene, excepto cuando se ejecuta la exportación para intentar quitar un usuario de la pertenencia; ahí se muestra como una acción correcta con una actualización, pero el usuario no se quita realmente de la pertenencia en Lotus Notes.
 * Se agregó una oportunidad para elegir el modo de exportación como "Append Item at bottom" (Anexar el elemento al final) en la GUI de configuración de Lotus MA para anexar elementos nuevos al final durante la exportación de atributos multivalor.
 * El conector agregará la lógica que se necesita para eliminar el archivo de la carpeta de correo y el almacén de id.
 * La eliminación de pertenencias no funciona entre los miembros de NAB.
 * Los valores se deben eliminar correctamente del atributo multivalor

## <a name="111170"></a>1.1.117.0
Publicación: marzo de 2016

**Nuevo conector**  
Versión inicial del [conector SQL genérico](active-directory-aadconnectsync-connector-genericsql.md).

**Nuevas características:**

* Conector LDAP genérico:
  * Se agregó compatibilidad para la importación diferencial con Isode.
* Conector de servicios web:
  * Se han actualizado las actividades csEntryChangeResult y setImportErrorCode para permitir que los errores de nivel de objeto se devuelvan al motor de sincronización.
  * Se han actualizado las plantillas SAP6 y SAP6User para utilizar la nueva funcionalidad de error de nivel de objeto.
* Conector Lotus Domino:
  * Al exportar, necesita un certificador por cada libreta de direcciones. Ahora puede usar la misma contraseña para todos los certificadores a fin de facilitar la administración.

**Problemas corregidos:**

* Conector LDAP genérico:
  * En el caso de IBM Tivoli DS, no se detectaban correctamente algunos atributos de referencia.
  * En el caso de Open LDAP durante una importación diferencial, se truncaban los espacios en blanco al principio y al final de las cadenas.
  * En el caso de Novell y NetIQ, se producía un error en las exportaciones que trasladaban un objeto entre UO/contenedores y, a la vez, cambiaban el nombre del objeto.
* Conector de servicios web:
  * Si el servicio web tenía varios puntos de conexión para el mismo enlace, el conector no los detectaba correctamente.
* Conector Lotus Domino:
  * No funcionaban las exportaciones del atributo fullName a una base de datos de recepción de correo electrónico.
  * Las exportaciones que agregaban y quitaban miembros de un grupo solo exportaban los miembros agregados.
  * Si un documento de notas no es válido (el atributo isValid se definía como false), se produce un error en el conector.

## <a name="older-releases"></a>Versiones anteriores
Antes de marzo de 2016, los conectores se publicaban como temas de soporte técnico.

**LDAP genérico**

* [KB3078617](https://support.microsoft.com/kb/3078617) : 1.0.0597, septiembre de 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) : 1.0.0549, marzo de 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) : 1.0.0534, enero de 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) : 1.0.0419, septiembre de 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) : 4.3.1082, marzo de 2014

**Servicios web**

* [KB3008178](https://support.microsoft.com/kb/3008178) : 1.0.0419, septiembre de 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) : 1.0.0419, septiembre de 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) : 1.0.0597, septiembre de 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) : 1.0.0549, marzo de 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) : 5.3.0712, agosto de 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) : 5.3.1003, febrero de 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) : 5.3.0721, octubre de 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) : 5.3.0534, agosto de 2013

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).


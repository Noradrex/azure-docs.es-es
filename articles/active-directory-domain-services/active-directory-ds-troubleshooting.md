---
title: Guía de solución de problemas de Azure Active Directory Domain Services | Microsoft Docs
description: Guía de solución de problemas Servicios de dominio de Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: maheshu

---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Guía de solución de problemas de Azure AD Domain Services
En este artículo se ofrecen sugerencias de solución de problemas para los problemas que puede encontrar al configurar o administrar Servicios de dominio de Azure Active Directory (AD).

### <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>No puede habilitar Azure AD Domain Services para su directorio de Azure AD
Si trata de habilitar Azure AD Domain Services para su directorio, pero se produce un error o se vuelve a cambiar a Deshabilitado, realice los siguientes pasos:

* Asegúrese de que no tenga un dominio existente con el mismo nombre de dominio disponible en esa red virtual. Por ejemplo, supongamos que tiene un dominio llamado 'contoso.com' ya disponible en la red virtual seleccionada. Posteriormente, intenta habilitar un dominio administrado de Azure AD Domain Services con el mismo nombre de dominio (es decir, contoso.com) en esa red virtual. Al intentar habilitar Azure AD Domain Services, aparece un error, que se debe a los conflictos de nombre en el nombre de dominio de la red virtual. En esta situación, debe utilizar un nombre diferente para configurar el dominio administrado de los Servicios de dominio de Azure AD. Como alternativa, puede aprovisionar el dominio existente y luego proceder a habilitar los Servicios de dominio de Azure AD.
* Vea si tiene una aplicación con el nombre 'Sincronización de Servicios de dominio de Azure AD' en el directorio de Azure AD. Si existe, deberá eliminarla y luego volver a habilitar Azure AD Domain Services. Realice los pasos siguientes para comprobar la presencia de la aplicación y eliminarla, en caso de que exista:
  
  1. Navegue hasta el **Portal de Azure clásico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. En el panel izquierdo, seleccione **Active Directory** .
  3. Seleccione al inquilino (directorio) de Azure AD para el que quiere habilitar los Servicios de dominio de Azure AD.
  4. Vaya a la pestaña **Aplicaciones** .
  5. Seleccione la opción **Aplicaciones que tiene mi compañía** en la lista desplegable.
  6. Busque una aplicación llamada **Sincronización de Azure AD Domain Services**. Si la aplicación existe, proceda a eliminarla.
  7. Cuando haya eliminado la aplicación, intente volver a habilitar los Servicios de dominio de Azure AD una vez más.

### <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Los usuarios no pueden iniciar sesión en el dominio administrado de los Servicios de dominio de Azure AD
Si uno o más usuarios de su inquilino de Azure AD no pueden iniciar sesión en el dominio administrado creado recientemente, lleve a cabo los siguientes pasos de solución de problemas:

* Trate de iniciar sesión con el formato UPN (por ejemplo, 'joeuser@contoso.com') en lugar del formato de SAMAccountName (CONTOSO\joeuse'). A veces, el atributo SAMAccountName puede generarse automáticamente para los usuarios cuyo prefijo UPN es demasiado largo o es igual que otro usuario del dominio administrado. El formato UPN garantiza que sea único dentro de un inquilino de Azure AD.

> [!NOTE]
> Recomendamos usar el formato UPN para iniciar sesión en el dominio administrado de Azure AD Domain Services.
> 
> 

* Asegúrese de que ha [habilitado la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos que se describen en la Guía de introducción.
* **Cuentas externas** Asegúrese de que la cuenta de usuario afectada no es una cuenta externa en el inquilino de Azure AD. Entre los ejemplos de las cuentas externas se incluyen las cuentas de Microsoft (por ejemplo, 'joe@live.com') o las cuentas de usuario de un directorio de Azure AD externo. Puesto que los Servicios de dominio de Azure AD no tienen las credenciales de dichas cuentas de usuario, estos usuarios no pueden iniciar sesión el dominio administrado.
* **Cuentas sincronizadas** : si las cuentas de usuario afectadas se sincronizan desde un directorio local, compruebe que ha hecho lo siguiente:
  
  * Ha implementado la [versión más reciente recomendada de Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect)o se ha actualización a dicha versión.
  * Ha configurado Azure AD Connect para [realizar una sincronización completa](active-directory-ds-getting-started-password-sync.md).
  * En función del tamaño de su directorio, se puede tardar algo en que las cuentas de usuario y los hash de credenciales estén disponibles en Servicios de dominio de Azure AD. Asegúrese de esperar el tiempo suficiente antes de volver a intentar la autenticación (depende del tamaño de su directorio, desde unas horas a un día o dos, para directorios grandes).
  * Si el problema persiste después de comprobar los pasos anteriores, pruebe a reiniciar el servicio de sincronización de Microsoft Azure AD. En el equipo de sincronización, inicie un símbolo del sistema y ejecute los comandos siguientes:
    
    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Cuentas de solo en la nube**: si la cuenta de usuario afectada es una cuenta de usuario de solo en la nube, asegúrese de que el usuario ha cambiado su contraseña después de habilitar Servicios de dominio de Azure AD. Este paso hace que se generen los hash de credenciales necesarios para los Servicios de dominio de Azure AD

### <a name="contact-us"></a>Ponerse en contacto con nosotros
Póngase en contacto con el equipo de productos de Azure Active Directory Domain Services para [compartir comentarios u obtener asistencia](active-directory-ds-contact-us.md).

<!--HONumber=Oct16_HO2-->



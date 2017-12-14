---
uid: web-api/overview/security/forms-authentication
title: "L’authentification par formulaire dans ASP.NET Web API | Documents Microsoft"
author: MikeWasson
description: "Décrit l’utilisation de l’authentification par formulaire dans ASP.NET Web API."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/12/2012
ms.topic: article
ms.assetid: 9f06c1f2-ffaa-4831-94a0-2e4a3befdf07
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/forms-authentication
msc.type: authoredcontent
ms.openlocfilehash: 9027d76bcf8854fc85f11906d3651511f350cd32
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="forms-authentication-in-aspnet-web-api"></a><span data-ttu-id="db747-103">Authentification par formulaire dans ASP.NET Web API</span><span class="sxs-lookup"><span data-stu-id="db747-103">Forms Authentication in ASP.NET Web API</span></span>
====================
<span data-ttu-id="db747-104">par [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="db747-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="db747-105">L’authentification par formulaire utilise un formulaire HTML pour envoyer des informations d’identification de l’utilisateur sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="db747-105">Forms authentication uses an HTML form to send the user's credentials to the server.</span></span> <span data-ttu-id="db747-106">Il n’est pas une norme Internet.</span><span class="sxs-lookup"><span data-stu-id="db747-106">It is not an Internet standard.</span></span> <span data-ttu-id="db747-107">L’authentification par formulaire n’est appropriée pour le web API est appelées à partir d’une application web, afin que l’utilisateur peut interagir avec le formulaire HTML.</span><span class="sxs-lookup"><span data-stu-id="db747-107">Forms authentication is only appropriate for web APIs that are called from a web application, so that the user can interact with the HTML form.</span></span>

| <span data-ttu-id="db747-108">Avantages</span><span class="sxs-lookup"><span data-stu-id="db747-108">Advantages</span></span> | <span data-ttu-id="db747-109">Inconvénients</span><span class="sxs-lookup"><span data-stu-id="db747-109">Disadvantages</span></span> |
| --- | --- |
| <span data-ttu-id="db747-110">-Facile à implémenter : intégré à ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="db747-110">- Easy to implement: Built into ASP.NET.</span></span> <span data-ttu-id="db747-111">-Utilise le fournisseur d’appartenances ASP.NET, ce qui le rend facile à gérer les comptes d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="db747-111">- Uses ASP.NET membership provider, which makes it easy to manage user accounts.</span></span> | <span data-ttu-id="db747-112">-N’est pas un mécanisme HTTP standard d’authentification ; utilise des cookies HTTP au lieu de l’en-tête d’autorisation standard.</span><span class="sxs-lookup"><span data-stu-id="db747-112">- Not a standard HTTP authentication mechanism; uses HTTP cookies instead of the standard Authorization header.</span></span> <span data-ttu-id="db747-113">-Nécessite un navigateur client.</span><span class="sxs-lookup"><span data-stu-id="db747-113">- Requires a browser client.</span></span> <span data-ttu-id="db747-114">-Informations d’identification sont envoyées en texte clair.</span><span class="sxs-lookup"><span data-stu-id="db747-114">- Credentials are sent as plaintext.</span></span> <span data-ttu-id="db747-115">-Vulnérable à la falsification de requête (CSRF) ; nécessite les mesures anti-CSRF.</span><span class="sxs-lookup"><span data-stu-id="db747-115">- Vulnerable to cross-site request forgery (CSRF); requires anti-CSRF measures.</span></span> <span data-ttu-id="db747-116">-Difficile à utiliser à partir de clients de nonbrowser.</span><span class="sxs-lookup"><span data-stu-id="db747-116">- Difficult to use from nonbrowser clients.</span></span> <span data-ttu-id="db747-117">Ouverture de session nécessite un navigateur.</span><span class="sxs-lookup"><span data-stu-id="db747-117">Login requires a browser.</span></span> <span data-ttu-id="db747-118">-Informations d’identification sont envoyées dans la demande.</span><span class="sxs-lookup"><span data-stu-id="db747-118">- User credentials are sent in the request.</span></span> <span data-ttu-id="db747-119">-Certains utilisateurs désactivent les cookies.</span><span class="sxs-lookup"><span data-stu-id="db747-119">- Some users disable cookies.</span></span> |

<span data-ttu-id="db747-120">En bref, l’authentification par formulaire dans ASP.NET fonctionne comme suit :</span><span class="sxs-lookup"><span data-stu-id="db747-120">Briefly, forms authentication in ASP.NET works like this:</span></span>

1. <span data-ttu-id="db747-121">Le client demande une ressource qui nécessite une authentification.</span><span class="sxs-lookup"><span data-stu-id="db747-121">The client requests a resource that requires authentication.</span></span>
2. <span data-ttu-id="db747-122">Si l’utilisateur n’est pas authentifié, le serveur renvoie HTTP 302 (trouvé) et le redirige vers une page de connexion.</span><span class="sxs-lookup"><span data-stu-id="db747-122">If the user is not authenticated, the server returns HTTP 302 (Found) and redirects to a login page.</span></span>
3. <span data-ttu-id="db747-123">L’utilisateur entre les informations d’identification et envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="db747-123">The user enters credentials and submits the form.</span></span>
4. <span data-ttu-id="db747-124">Le serveur renvoie un autre HTTP 302 qui redirige vers l’URI d’origine.</span><span class="sxs-lookup"><span data-stu-id="db747-124">The server returns another HTTP 302 that redirects back to the original URI.</span></span> <span data-ttu-id="db747-125">Cette réponse comprend un cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="db747-125">This response includes an authentication cookie.</span></span>
5. <span data-ttu-id="db747-126">Le client demande la ressource à nouveau.</span><span class="sxs-lookup"><span data-stu-id="db747-126">The client requests the resource again.</span></span> <span data-ttu-id="db747-127">La requête inclut le cookie d’authentification pour le serveur autorise la demande.</span><span class="sxs-lookup"><span data-stu-id="db747-127">The request includes the authentication cookie, so the server grants the request.</span></span>

![](forms-authentication/_static/image1.png)

<span data-ttu-id="db747-128">Pour plus d’informations, consultez [une vue d’ensemble de l’authentification par formulaire.](../../../web-forms/overview/older-versions-security/introduction/an-overview-of-forms-authentication-cs.md)</span><span class="sxs-lookup"><span data-stu-id="db747-128">For more information, see [An Overview of Forms Authentication.](../../../web-forms/overview/older-versions-security/introduction/an-overview-of-forms-authentication-cs.md)</span></span>

## <a name="using-forms-authentication-with-web-api"></a><span data-ttu-id="db747-129">À l’aide de l’authentification par formulaire avec l’API Web</span><span class="sxs-lookup"><span data-stu-id="db747-129">Using Forms Authentication with Web API</span></span>

<span data-ttu-id="db747-130">Pour créer une application qui utilise l’authentification par formulaire, sélectionnez le modèle de « Application Internet » dans l’Assistant de projet MVC 4.</span><span class="sxs-lookup"><span data-stu-id="db747-130">To create an application that uses forms authentication, select the "Internet Application" template in the MVC 4 project wizard.</span></span> <span data-ttu-id="db747-131">Ce modèle crée des contrôleurs MVC pour la gestion des comptes.</span><span class="sxs-lookup"><span data-stu-id="db747-131">This template creates MVC controllers for account management.</span></span> <span data-ttu-id="db747-132">Vous pouvez également utiliser le modèle « Application à Page unique », disponible dans la mise à jour ASP.NET automne 2012.</span><span class="sxs-lookup"><span data-stu-id="db747-132">You can also use the "Single Page Application" template, available in the ASP.NET Fall 2012 Update.</span></span>

<span data-ttu-id="db747-133">Dans les contrôleurs de votre API web, vous pouvez restreindre l’accès à l’aide de la `[Authorize]` d’attribut, comme décrit dans [à l’aide de l’attribut [Authorize]](authentication-and-authorization-in-aspnet-web-api.md#auth3).</span><span class="sxs-lookup"><span data-stu-id="db747-133">In your web API controllers, you can restrict access by using the `[Authorize]` attribute, as described in [Using the [Authorize] Attribute](authentication-and-authorization-in-aspnet-web-api.md#auth3).</span></span>

<span data-ttu-id="db747-134">Authentification par formulaire utilise un cookie de session pour authentifier les demandes.</span><span class="sxs-lookup"><span data-stu-id="db747-134">Forms-authentication uses a session cookie to authenticate requests.</span></span> <span data-ttu-id="db747-135">Les navigateurs envoient automatiquement tous les cookies pertinentes pour le site de destination.</span><span class="sxs-lookup"><span data-stu-id="db747-135">Browsers automatically send all relevant cookies to the destination web site.</span></span> <span data-ttu-id="db747-136">Cette fonctionnalité permet l’authentification par formulaire potentiellement vulnérables à voir des attaques par falsification (CSRF) de requête intersites [empêcher Cross-Site demande Forgery (CSRF) attaques](preventing-cross-site-request-forgery-csrf-attacks.md).</span><span class="sxs-lookup"><span data-stu-id="db747-136">This feature makes forms authentication potentially vulnerable to cross-site request forgery (CSRF) attacks See [Preventing Cross-Site Request Forgery (CSRF) Attacks](preventing-cross-site-request-forgery-csrf-attacks.md).</span></span>

<span data-ttu-id="db747-137">L’authentification par formulaire ne chiffre pas les informations d’identification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="db747-137">Forms authentication does not encrypt the user's credentials.</span></span> <span data-ttu-id="db747-138">Par conséquent, l’authentification par formulaire n’est pas sécurisée, sauf si utilisée avec SSL.</span><span class="sxs-lookup"><span data-stu-id="db747-138">Therefore, forms authentication is not secure unless used with SSL.</span></span> <span data-ttu-id="db747-139">Consultez [utilisation de SSL dans l’API Web](working-with-ssl-in-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="db747-139">See [Working with SSL in Web API](working-with-ssl-in-web-api.md).</span></span>
---
title: Erstellen eines SSL-Zertifikats
description: Problemumgehung zum manuellen Erstellen von Zertifikaten für den Entwicklerserver
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: c96489e6577f4887d2f22a9e81ea50f46cc9a5a3
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424275"
---
# <a name="create-an-ssl-certificate"></a><span data-ttu-id="666ae-103">Erstellen eines SSL-Zertifikats</span><span class="sxs-lookup"><span data-stu-id="666ae-103">Create an SSL certificate</span></span>

<span data-ttu-id="666ae-104">In diesem Artikel wird das Erstellen eines SSL-Zertifikats erläutert.</span><span class="sxs-lookup"><span data-stu-id="666ae-104">This article describes how to create an SSL certificate.</span></span>

<span data-ttu-id="666ae-105">Führen Sie den folgenden Befehl aus, um das Zertifikat mithilfe des PowerShell-Cmdlets `New-SelfSignedCertificate` unter Windows 8 oder höher zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="666ae-105">To generate the certificate by using the PowerShell `New-SelfSignedCertificate` cmdlet on Windows 8 or later, run the following command:</span></span>

```cmd
pbiviz --create-cert
```

<span data-ttu-id="666ae-106">Das Tool erfordert eine OpenSSL-Installation für Windows 7.</span><span class="sxs-lookup"><span data-stu-id="666ae-106">The tool requires an OpenSSL installation for Windows 7.</span></span> <span data-ttu-id="666ae-107">Das OpenSSL-Hilfsprogramm muss über die Befehlszeile verfügbar sein.</span><span class="sxs-lookup"><span data-stu-id="666ae-107">The OpenSSL utility must be available from the command line.</span></span>

<span data-ttu-id="666ae-108">Rufen Sie die Website für [OpenSSL](https://www.openssl.org) oder [OpenSSL-Binärdateien](https://wiki.openssl.org/index.php/Binaries) auf, um OpenSSL zu installieren.</span><span class="sxs-lookup"><span data-stu-id="666ae-108">To install OpenSSL, go to the [OpenSSL](https://www.openssl.org) or [OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries) site.</span></span>



## <a name="create-a-certificate-mac-os-x"></a><span data-ttu-id="666ae-109">Erstellen eines Zertifikats (Mac OS X)</span><span class="sxs-lookup"><span data-stu-id="666ae-109">Create a certificate (Mac OS X)</span></span>

<span data-ttu-id="666ae-110">Das OpenSSL-Hilfsprogramm ist in der Regel in den Betriebssystemen Linux und Mac OS X verfügbar.</span><span class="sxs-lookup"><span data-stu-id="666ae-110">Usually, the OpenSSL utility is available in the Linux or Mac OS X operating system.</span></span>

<span data-ttu-id="666ae-111">Sie können es auch installieren, indem Sie einen der folgenden Befehle ausführen:</span><span class="sxs-lookup"><span data-stu-id="666ae-111">You can also install the utility by running either of the following commands:</span></span>
* <span data-ttu-id="666ae-112">Über den *Brew*-Paket-Manager:</span><span class="sxs-lookup"><span data-stu-id="666ae-112">From the *Brew* package manager:</span></span>

    ```cmd
    brew install openssl
    brew link openssl --force
    ```

* <span data-ttu-id="666ae-113">Mithilfe von *MacPorts*:</span><span class="sxs-lookup"><span data-stu-id="666ae-113">By using *MacPorts*:</span></span>

    ```cmd
    sudo port install openssl
    ```

<span data-ttu-id="666ae-114">Nachdem Sie das OpenSSL-Hilfsprogramm zum Erstellen eines neuen Zertifikats installiert haben, führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="666ae-114">After you install the OpenSSL utility for generating a new certificate, run the following command:</span></span>

```cmd
pbiviz --create-cert
```

## <a name="create-a-certificate-linux"></a><span data-ttu-id="666ae-115">Erstellen eines Zertifikats (Linux)</span><span class="sxs-lookup"><span data-stu-id="666ae-115">Create a certificate (Linux)</span></span>

<span data-ttu-id="666ae-116">Wenn das OpenSSL-Hilfsprogramm für Ihr Linux-Betriebssystem nicht verfügbar ist, können Sie es mithilfe von einem der folgenden Befehle installieren:</span><span class="sxs-lookup"><span data-stu-id="666ae-116">If the OpenSSL utility isn't available in your Linux operating system, you can install it by using one of the following commands:</span></span>

* <span data-ttu-id="666ae-117">*APT*-Paket-Manager:</span><span class="sxs-lookup"><span data-stu-id="666ae-117">For *APT* package manager:</span></span>

    ```cmd
    sudo apt-get install openssl
    ```

* <span data-ttu-id="666ae-118">*Yellowdog Updater*:</span><span class="sxs-lookup"><span data-stu-id="666ae-118">For *Yellowdog Updater*:</span></span>

    ```cmd
    yum install openssl
    ```

* <span data-ttu-id="666ae-119">*Redhat*-Paket-Manager:</span><span class="sxs-lookup"><span data-stu-id="666ae-119">For *Redhat Package Manager*:</span></span>

    ```cmd
    rpm install openssl
    ```

<span data-ttu-id="666ae-120">Wenn das OpenSSL-Hilfsprogramm bereits auf Ihrem Betriebssystem verfügbar ist, erstellen Sie ein neues Zertifikat, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="666ae-120">If the OpenSSL utility is already available in your operating system, generate a new certificate by running the following command:</span></span>

```cmd
pbiviz --create-cert
```

<span data-ttu-id="666ae-121">Alternativ können Sie das OpenSSL-Hilfsprogramm über die Website für [OpenSSL](https://www.openssl.org) oder [OpenSSL-Binärdateien](https://wiki.openssl.org/index.php/Binaries) abrufen.</span><span class="sxs-lookup"><span data-stu-id="666ae-121">Or you can get the OpenSSL utility by going to the [OpenSSL](https://www.openssl.org) or [OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries) site.</span></span>

## <a name="generate-the-certificate-manually"></a><span data-ttu-id="666ae-122">Manuelles Generieren des Zertifikats</span><span class="sxs-lookup"><span data-stu-id="666ae-122">Generate the certificate manually</span></span>

<span data-ttu-id="666ae-123">Sie können festlegen, dass Ihre Zertifikate von beliebigen Tools generiert werden können.</span><span class="sxs-lookup"><span data-stu-id="666ae-123">You can specify that your certificates be generated by any tools.</span></span>

<span data-ttu-id="666ae-124">Wenn das OpenSSL-Hilfsprogramm bereits auf Ihrem System installiert ist, generieren Sie ein neues Zertifikat, indem Sie die folgenden Befehle ausführen:</span><span class="sxs-lookup"><span data-stu-id="666ae-124">If the OpenSSL utility is already installed in your system, generate a new certificate by running the following commands:</span></span>

```cmd
openssl req -x509 -newkey rsa:4096 -keyout PowerBICustomVisualTest_private.key -out PowerBICustomVisualTest_public.crt -days 365
```

<span data-ttu-id="666ae-125">In der Regel können Sie die PowerBI-visuals-tools-Webserverzertifikate finden, indem Sie einen der folgenden Befehle ausführen:</span><span class="sxs-lookup"><span data-stu-id="666ae-125">You can usually find the PowerBI-visuals-tools web server certificates by running one the following:</span></span>

* <span data-ttu-id="666ae-126">Für die globale Instanz der Tools:</span><span class="sxs-lookup"><span data-stu-id="666ae-126">For the global instance of the tools:</span></span>

    ```cmd
    %appdata%\npm\node_modules\PowerBI-visuals-tools\certs
    ```

* <span data-ttu-id="666ae-127">Für die lokale Instanz der Tools:</span><span class="sxs-lookup"><span data-stu-id="666ae-127">For the local instance of the tools:</span></span>

    ```cmd
    <custom visual project root>\node_modules\PowerBI-visuals-tools\certs
    ```

<span data-ttu-id="666ae-128">Wenn Sie das PEM-Format verwenden, speichern Sie die Zertifikatdatei als *PowerBICustomVisualTest_public.crt*, und speichern Sie privateKey als *PowerBICustomVisualTest_public.key*.</span><span class="sxs-lookup"><span data-stu-id="666ae-128">If you use the PEM format, save the certificate file as *PowerBICustomVisualTest_public.crt*, and save privateKey as *PowerBICustomVisualTest_public.key*.</span></span>

<span data-ttu-id="666ae-129">Wenn Sie das PFX-Format verwenden, speichern Sie die Zertifikatsdatei als *PowerBICustomVisualTest_public.pfx*.</span><span class="sxs-lookup"><span data-stu-id="666ae-129">If you use the PFX format, save the certificate file as *PowerBICustomVisualTest_public.pfx*.</span></span>

<span data-ttu-id="666ae-130">Wenn Ihre PFX-Zertifikatsdatei eine Passphrase benötigt, gehen Sie wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="666ae-130">If your PFX certificate file requires a passphrase, do the following:</span></span>
1. <span data-ttu-id="666ae-131">Legen Sie Folgendes in der Konfigurationsdatei fest:</span><span class="sxs-lookup"><span data-stu-id="666ae-131">In the config file, specify:</span></span>

    ```cmd
    \PowerBI-visuals-tools\config.json
    ```

1. <span data-ttu-id="666ae-132">Legen Sie im Abschnitt `server` die Passphrase fest, indem Sie den Platzhalter „*YOUR PASSPHRASE*“ (Ihre Passphrase) ersetzen:</span><span class="sxs-lookup"><span data-stu-id="666ae-132">In the `server` section, specify the passphrase by replacing the "*YOUR PASSPHRASE*" placeholder:</span></span>

    ```cmd
    "server":{
        "root":"webRoot",
        "assetsRoute":"/assets",
        "privateKey":"certs/PowerBICustomVisualTest_private.key",
        "certificate":"certs/PowerBICustomVisualTest_public.crt",
        "pfx":"certs/PowerBICustomVisualTest_public.pfx",
        "port":"8080",
        "passphrase":"YOUR PASSPHRASE"
    }
    ```

---

titleSuffix: Cognitive Services
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2021
ms.author: aahi
---

If you're having trouble running a Cognitive Services container, you can try using the Microsoft diagnostics container. Use this container to diagnose common errors in your deployment environment that might prevent Cognitive Services containers from functioning as expected.

To get the container, use the following `docker pull` command:

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/diagnostic
```

Then run the container. Replace `{ENDPOINT_URI}` with your endpoint, and replace `{API_KEY}` with your key to your resource:

```bash
docker run --rm mcr.microsoft.com/azure-cognitive-services/diagnostic \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

The container will test for network connectivity to the billing endpoint.
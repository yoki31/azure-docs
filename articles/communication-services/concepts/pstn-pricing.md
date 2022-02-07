---
title: Pricing for PSTN
titleSuffix: An Azure Communication Services concept document
description: Learn about Communication Services' Telephony Pricing Model.
author: sadas
ms.author: sadas
ms.date: 1/28/2022
ms.topic: conceptual
ms.service: azure-communication-services
---
# Telephony (PSTN) Pricing

> [!IMPORTANT]
> Number Retention and Portability: Phone numbers that are assigned to you during any preview program may need to be returned to Microsoft if you do not meet regulatory requirements before General Availability. During private preview and public preview, telephone numbers are not eligible for porting. [Details on offers in Public Preview / GA](../concepts/numbers/sub-eligibility-number-capability.md)

Numbers are billed on a per month basis, and pricing differs based on the type of a number and the source (country) of the number. Once a number is purchased, Customers can make / receive calls using that number and are billed on a per minute basis. PSTN call pricing is based on the type of number and location in which a call is terminated (destination), with few scenarios having rates based on origination location.

In most cases, customers with Azure subscriptions locations that match the country of the Number offer will be able to buy the Number. However, US and UK numbers may be purchased by customers with Azure subscription locations in other countries. Please see here for details on [in-country and cross-country purchases](../concepts/numbers/sub-eligibility-number-capability.md).

All prices shown below are in USD.

## United States Telephony Offers

### Phone Number Leasing Charges
|Number type   |Monthly fee   |
|--------------|-----------|
|Geographic     |USD 1.00/mo        |
|Toll-Free     |USD 2.00/mo        |

### Usage Charges
|Number type   |To make calls*   |To receive calls|
|--------------|-----------|------------|
|Geographic     |Starting at USD 0.0130/min       |USD 0.0085/min        |
|Toll-free |Starting at USD 0.0130/min   | USD 0.0220/min |

\* For destination-specific pricing for making outbound calls, please refer to details [here](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

## United Kingdom Telephony Offers

### Phone Number Leasing Charges
|Number type   |Monthly fee   |
|--------------|-----------|
|Geographic     |USD 1.00/mo        |
|Toll-Free     |USD 2.00/mo        |

### Usage Charges
|Number type   |To make calls*   |To receive calls|
|--------------|-----------|------------|
|Geographic     |Starting at USD 0.0150/min       |USD 0.0090/min        |
|Toll-free |Starting at USD 0.0150/min   |Starting at USD 0.0290/min |

\* For destination-specific pricing for making outbound calls, please refer to details [here](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

## Denmark Telephony Offers

### Phone Number Leasing Charges
|Number type   |Monthly fee   |
|--------------|-----------|
|Geographic     |USD 0.82/mo        |
|Toll-Free     |USD 25.00/mo        |

### Usage Charges
|Number type   |To make calls*   |To receive calls|
|--------------|-----------|------------|
|Geographic     |Starting at USD 0.0190/min       |USD 0.0100/min        |
|Toll-free |Starting at USD 0.0190/min   |Starting at USD 0.0343/min |

\* For destination-specific pricing for making outbound calls, please refer to details [here](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

***

Note: Pricing for all countries is subject to change as pricing is market-based and depends on third-party suppliers of telephony services. Additionally, pricing may include requisite taxes and fees.

***
## Next steps

In this quickstart, you learned how Telephony (PSTN) Offers are priced for Azure Communication Services.

The following documents may be interesting to you:
- [Learn more about Telephony](../concepts/telephony/telephony-concept.md)
- Get a Telephony capable [phone number](../quickstarts/telephony/get-phone-number.md)
- [Phone number types in Azure Communication Services](../concepts/telephony/plan-solution.md)

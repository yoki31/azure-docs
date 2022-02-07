---
title: 'Locations and connectivity providers: Azure ExpressRoute | Microsoft Docs'
description: This article provides a detailed overview of locations where services are offered and how to connect to Azure regions. Sorted by location.
services: expressroute
author: duongau

ms.service: expressroute
ms.topic: conceptual
ms.date: 01/24/2022
ms.author: duau
ms.custom: references_regions

---
# ExpressRoute partners and peering locations

> [!div class="op_single_selector"]
> * [Locations By Provider](expressroute-locations.md)
> * [Providers By Location](expressroute-locations-providers.md)


The tables in this article provide information on ExpressRoute geographical coverage and locations, ExpressRoute connectivity providers, and ExpressRoute System Integrators (SIs).

> [!Note]
> Azure regions and ExpressRoute locations are two distinct and different concepts, understanding the difference between the two is critical to exploring Azure hybrid networking connectivity. 
>
>

## Azure regions
Azure regions are global datacenters where Azure compute, networking, and storage resources are located. When creating an Azure resource, a customer needs to select a resource location. The resource location determines which Azure datacenter (or availability zone) the resource is created in.

## ExpressRoute locations
ExpressRoute locations (sometimes referred to as peering locations or meet-me-locations) are co-location facilities where Microsoft Enterprise edge (MSEE) devices are located. ExpressRoute locations are the entry point to Microsoft's network – and are globally distributed, providing customers the opportunity to connect to Microsoft's network around the world. These locations are where ExpressRoute partners and ExpressRoute Direct customers issue cross connections to Microsoft's network. In general, the ExpressRoute location does not need to match the Azure region. For example, a customer can create an ExpressRoute circuit with the resource location *East US*, in the *Seattle* Peering location.

You will have access to Azure services across all regions within a geopolitical region if you connected to at least one ExpressRoute location within the geopolitical region. 

[!INCLUDE [expressroute-azure-regions-geopolitical-region](../../includes/expressroute-azure-regions-geopolitical-region.md)]

## <a name="partners"></a>ExpressRoute connectivity providers

The following table shows connectivity locations and the service providers for each location. If you want to view service providers and the locations for which they can provide service, see [Locations by service provider](expressroute-locations.md).

* **Local Azure Regions** are the ones that [ExpressRoute Local](expressroute-faqs.md) at each peering location can access. **n/a** indicates that ExpressRoute Local is not available at that peering location.

* **Zone** refers to [pricing](https://azure.microsoft.com/pricing/details/expressroute/).

### Global commercial Azure
| **Location** | **Address** | **Zone** | **Local Azure regions** | **ER Direct** | **Service providers** |
| --- | --- | --- | --- | --- | --- |
| **Abu Dhabi** | Etisalat KDC | 3 | n/a | 10G | |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | West Europe | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | West Europe | 10G, 100G | BICS, British Telecom, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, euNetworks, GÉANT, Interxion, NL-IX, NOS, NTT Global DataCenters EMEA, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/a | 10G, 100G | Equinix, Megaport |
| **Auckland** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/a | 10G | Devoli, Kordia, Megaport, REANNZ, Spark NZ, Vocus Group NZ |
| **Bangkok** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n/a | 10G | AIS, National Telecom UIH |
| **Berlin** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Germany North | 10G | Colt, Equinix, NTT Global DataCenters EMEA|
| **Bogota** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | n/a | 10G | CenturyLink Cloud Connect, Equinix |
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Korea South | n/a | LG CNS |
| **Campinas** | [Ascenty](https://www.ascenty.com/en/data-centers-en/campinas/) | 3 | Brazil South | 10G, 100G | Ascenty |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Australia Central | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Australia Central 2| 10G, 100G | CDC, Equinix |
| **Cape Town** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | South Africa West | 10G | BCX, Internet Solutions - Cloud Connect, Liquid Telecom, MTN Global Connect, Teraco, Vodacom |
| **Chennai** | Tata Communications | 2 | South India | 10G | BSNL, DE-CIX, Global CloudXchange (GCX), SIFY, Tata Communications, VodafoneIdea |
| **Chennai2** | Airtel | 2 | South India | 10G | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | North Central US | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Chicago2** | [CoreSite CH1](https://www.coresite.com/data-center/ch1-chicago-il) | 1 | North Central US | 10G, 100G | CoreSite |
| **Copenhagen** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/a | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/a | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Cox Business Cloud Port, Equinix, Intercloud, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Orange, PacketFabric, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | West Central US | 10G, 100G | CoreSite, Megaport, PacketFabric, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | UAE North | n/a | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | UAE North | n/a | DE-CIX, du datamena, Equinix, GBI, Megaport, Orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | North Europe | 10G, 100G | CenturyLink Cloud Connect, Colt, eir, Equinix, GEANT, euNetworks, Interxion, Megaport, Zayo|
| **Dublin2** | [Interxion DUB2](https://www.interxion.com/locations/europe/dublin) | 1 | North Europe | 10G, 100G | Interxion |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Germany West Central | 10G, 100G | AT&T NetBond, British Telecom, CenturyLink Cloud Connect, China Unicom Global, Colt, DE-CIX, Equinix, euNetworks, GBI, GEANT, InterCloud, Interxion, Megaport, NTT Global DataCenters EMEA, Orange, Telia Carrier, T-Systems |
| **Frankfurt2** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | Germany West Central | 10G, 100G |  DE-CIX, Deutsche Telekom AG, Equinix |
| **Geneva** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Switzerland West | 10G, 100G | Colt, Equinix, InterCloud, Megaport, Swisscom |
| **Hong Kong** | [Equinix HK1](https://www.equinix.com/data-centers/asia-pacific-colocation/hong-kong-colocation/hong-kong-data-centers/hk1) | 2 | East Asia | 10G | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, China Unicom, Colt, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Hong Kong2** | [iAdvantage MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | East Asia | 10G | China Mobile International, China Telecom Global, iAdvantage, Megaport, PCCW Global Limited, SingTel |
| **Jakarta** | [Telin](https://www.telin.net/) | 4 | n/a | 10G | NTT Communications, Telin, XL Axiata |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | South Africa North | 10G | BCX, British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, MTN Global Connect, Orange, Teraco, Vodacom |
| **Kuala Lumpur** | [TIME dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | n/a | n/a | TIME dotCom |
| **Las Vegas** | [Switch LV](https://www.switch.com/las-vegas) | 1 | n/a | 10G, 100G | CenturyLink Cloud Connect, Megaport, PacketFabric |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | UK South | 10G, 100G | AT&T NetBond, British Telecom, CenturyLink, Colt, Equinix, euNetworks, Intelsat, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | UK South | 10G, 100G | BICS, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, GTT, Interxion, IX Reach, JISC, Megaport, NTT Global DataCenters EMEA, Orange, SES, Sohonet, Telehouse - KDDI, Zayo |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/a | 10G, 100G | CoreSite, Equinix*, Megaport, Neutrona Networks, NTT, Zayo</br></br> **New ExpressRoute circuits are no longer supported with Equinix in Los Angeles. Please create new circuits in Los Angeles2.* |
| **Los Angeles2** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | n/a | 10G, 100G | Equinix |
| **Madrid** | [Interxion MAD1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | West Europe | 10G, 100G |  DE-CIX, Interxion, Megaport, Telefonica |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | France South | n/a | Colt, DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australia Southeast | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Orange, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/a | 10G, 100G | Claro, C3ntro, Equinix, Megaport, Neutrona Networks |
| **Milan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/a | 10G | Colt, Equinix, Fastweb, IRIDEOS, Retelit |
| **Minneapolis** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | n/a | 10G, 100G | Cologix, Megaport |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/a | 10G, 100G | Bell Canada, CenturyLink Cloud Connect, Cologix, Fibrenoire, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | West India | 10G | BSNL, DE-CIX, Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | West India | 10G | Airtel, Sify, Orange, Vodafone Idea |
| **Munich** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | n/a | 10G | Colt, DE-CIX, Megaport |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 1 | n/a | 10G, 100G | CenturyLink Cloud Connect, Coresite, DE-CIX, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | UK West | 10G, 100G | British Telecom, Colt, Jisc, Level 3 Communications, Next Generation Data |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japan West | 10G, 100G | AT TOKYO, BBIX, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank, Tokai Communications |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norway East | 10G, 100G | GlobalConnect, Megaport, Telenor, Telia Carrier |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | France Central | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Jaguar Network, Megaport, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/a | 10G | Megaport, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | West US 3 | 10G, 100G | Cox Business Cloud Port, CenturyLink Cloud Connect, Megaport, Zayo |
| **Pune** | [STT GDC Pune DC1](https://www.sttelemediagdc.in/our-data-centres-in-india) | 2 | Central India| 10G | Tata Communications |
| **Quebec City** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Canada East | 10G, 100G | Bell Canada, Equinix, Megaport, Telus |
| **Queretaro (Mexico)** | [KIO Networks QR01](https://www.kionetworks.com/es-mx/) | 4 | n/a | 10G | Transtelco|
| **Quincy** | [Sabey Datacenter - Building A](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | West US 2 | 10G, 100G | |
| **Rio de Janeiro** | [Equinix-RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | Brazil Southeast | 10G | Equinix |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | South Central US | 10G, 100G | CenturyLink Cloud Connect, Megaport, Zayo |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brazil South | 10G, 100G | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, InterCloud, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Sao Paulo2** | [TIVIT TSM](https://www.tivit.com/en/tivit/) | 3 | Brazil South | 10G, 100G | Ascenty Data Centers |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | West US 2 | 10G, 100G | Aryaka Networks, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seoul** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | Korea Central | 10G, 100G | KINX, KT, LG CNS, LGUplus, Equinix, Sejong Telecom, SK Telecom |
| **Seoul2** | [KT IDC](https://www.kt-idc.com/eng/introduce/sub1_4_10.jsp#tab) | 2 | Korea Central | n/a | KT |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | West US | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Cox Business Cloud Port, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | West US | 10G, 100G | Colt, Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/data-centers/asia-pacific-colocation/singapore-colocation/singapore-data-center/sg1) | 2 | Southeast Asia | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Southeast Asia | 10G, 100G | CenturyLink Cloud Connect, China Unicom Global, Colt,  DE-CIX, Epsilon Global Communications, Equinix, Megaport, PCCW Global Limited, SingTel, Telehouse - KDDI |
| **Stavanger** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norway West | 10G, 100G |GlobalConnect, Megaport |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/a | 10G | Equinix, Megaport, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australia East | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australia East | 10G, 100G | Megaport, NextDC |
| **Taipei** | Chief Telecom | 2 | n/a | 10G | Chief Telecom, Chunghwa Telecom, FarEasTone |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japan East | N/A | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Telehouse - KDDI, Verizon </br></br> **We are currently unable to support new ExpressRoute circuits in Tokyo. Please create new circuits in Tokyo2 or Osaka.* |
| **Tokyo2** | [AT TOKYO](https://www.attokyo.com/) | 2 | Japan East | 10G, 100G | AT TOKYO, China Unicom Global, Colt, Fibrenoire, IX Reach, Megaport, PCCW Global Limited, Tokai Communications |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canada Central | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Toronto2** | [Allied REIT](https://www.alliedreit.com/property/905-king-st-w/) | 1 | Canada Central | 10G, 100G | |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n/a | 10G | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/), [Equinix DC6](https://www.equinix.com/data-centers/americas-colocation/united-states-colocation/washington-dc-data-centers/dc6) | 1 | East US, East US 2 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Cox Business Cloud Port, Equinix, Internet2, InterCloud, Iron Mountain, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite VA2](https://www.coresite.com/data-center/va2-reston-va) | 1 | East US, East US 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Megaport, Viasat, Zayo | 
| **Zurich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Switzerland North | 10G, 100G | Colt, Equinix, Intercloud, Interxion, Megaport, Swisscom, Zayo |

 **+** denotes coming soon

### National cloud environments

Azure national clouds are isolated from each other and from global commercial Azure. ExpressRoute for one Azure cloud cannot connect to the Azure regions in the others.

### US Government cloud
| **Location** | **Address** | **Local Azure regions**| **ER Direct** | **Service providers** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/a | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/a | 10G, 100G | AT&T NetBond, British Telecom, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | 10G, 100G | Equinix, Internet2, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/a | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov Arizona | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov Texas | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/a | 10G, 100G | AT&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/a | 10G, 100G | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD East, US Gov Virginia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### China
| **Location** | **Address** | **Local Azure regions** | **ER Direct** | **Service providers** |
| --- | --- | --- | --- | --- |
| **Beijing** | China Telecom | n/a | 10G | China Telecom |
| **Beijing2** | GDS | n/a | 10G | China Telecom, China Unicom, GDS |
| **Shanghai** | China Telecom | n/a | 10G | China Telecom |
| **Shanghai2** | GDS | n/a | 10G | China Telecom, China Unicom, GDS |

To learn more, see [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/).

** ExpressRoute Local is not available in this location.

### Germany
| **Location** | **Service providers** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Connectivity through Exchange providers
If your connectivity provider is not listed in previous sections, you can still create a connection.

* Check with your connectivity provider to see if they are connected to any of the exchanges in the table above. You can check the following links to gather more information about services offered by exchange providers. Several connectivity providers are already connected to Ethernet exchanges.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/resources/videos/cloud-exchange-overview)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Have your connectivity provider extend your network to the peering location of choice.
  * Ensure that your connectivity provider extends your connectivity in a highly available manner so that there are no single points of failure.
* Order an ExpressRoute circuit with the exchange as your connectivity provider to connect to Microsoft.
  * Follow steps in [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) to set up connectivity.

## Connectivity through satellite operators
If you are remote and do not have fiber connectivity or want to explore other connectivity options, you can check the following satellite operators. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Connectivity through additional service providers
| **Location** | **Exchange** | **Connectivity providers** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Level 3 Communications | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Crown Castle
| **Cape Town** | Teraco | MTN |
| **Chennai** | Tata Communications | Tata Teleservices |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hong Kong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **Mumbai** | Tata Communications | Tata Teleservices |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Oncore Cloud Services Inc., Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## ExpressRoute system integrators
Enabling private connectivity to fit your needs can be challenging, based on the scale of your network. You can work with any of the system integrators listed in the following table to assist you with onboarding to ExpressRoute.

| **Continent** | **System integrators** |
| --- | --- |
| **Asia** |Avanade Inc., OneAs1a |
| **Australia** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europe** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **North America** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **South America** |Avanade Inc., Venha Pra Nuvem |
## Next steps
* For more information about ExpressRoute, see the [ExpressRoute FAQ](expressroute-faqs.md).
* Ensure that all prerequisites are met. See [ExpressRoute prerequisites](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Location map"

---
title: Using Microsoft Defender for Endpoint in Microsoft Defender for Cloud to protect native, on-premises, and AWS machines.
description: Learn about deploying Microsoft Defender for Endpoint from Microsoft Defender for Cloud to protect Azure, hybrid, and multi-cloud machines.
ms.topic: how-to
ms.date: 01/10/2022
---

# Protect your endpoints with Defender for Cloud's integrated EDR solution: Microsoft Defender for Endpoint

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Endpoint is a holistic, cloud-delivered, endpoint security solution. Its main features are:

- Risk-based vulnerability management and assessment 
- Attack surface reduction
- Behavioral based and cloud-powered protection
- Endpoint detection and response (EDR)
- Automatic investigation and remediation
- Managed hunting services

> [!TIP]
> Originally launched as **Windows Defender ATP**, this Endpoint Detection and Response (EDR) product was renamed in 2019 as **Microsoft Defender ATP**.
>
> At Ignite 2020, we launched the [Microsoft Defender for Cloud XDR suite](https://www.microsoft.com/security/business/threat-protection) and this EDR component was renamed **Microsoft Defender for Endpoint**.


## Availability

| Aspect                                       | Details                                                                                                                                                                                                                                                                               |
|----------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Release state:                               | General availability (GA)                                                                                                                                    |
| Pricing:                                     | Requires [Microsoft Defender for servers](defender-for-servers-introduction.md)                                                                                                                                                                                                           |
| Supported environments:                      | :::image type="icon" source="./media/icons/yes-icon.png"::: Azure Arc-enabled machines running Windows/Linux<br>:::image type="icon" source="./media/icons/yes-icon.png":::Azure VMs running Linux ([supported versions](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux))<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure VMs running Windows Server 2022, 2019, 2016, 2012 R2, 2008 R2 SP1, [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md), [Windows 10 Enterprise multi-session](../virtual-desktop/windows-10-multisession-faq.yml) (formerly Enterprise for Virtual Desktops (EVD)<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure VMs running Windows 10 (other than EVD or WVD)           |
| Required roles and permissions:              | * To enable/disable the integration: **Security admin** or **Owner**<br>* To view Defender for Endpoint alerts in Defender for Cloud: **Security reader**, **Reader**, **Resource Group Contributor**, **Resource Group Owner**, **Security admin**, **Subscription owner**, or **Subscription Contributor** |
| Clouds:                                      | :::image type="icon" source="./media/icons/yes-icon.png"::: Commercial clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet <br>:::image type="icon" source="./media/icons/yes-icon.png"::: Connected AWS accounts                                                         |
|                                              |                                                                                                                                                                                                                                                                                       |

## Benefits of integrating Microsoft Defender for Endpoint with Defender for Cloud

Microsoft Defender for Endpoint protects your Windows and Linux machines whether they're hosted in Azure, hybrid clouds (on-premises), or AWS. Protections include:

- **Advanced post-breach detection sensors**. Defender for Endpoint's sensors collect a vast array of behavioral signals from your machines.

- **Vulnerability assessment from the Microsoft threat and vulnerability management solution**. With Microsoft Defender for Endpoint enabled, Defender for Cloud can show vulnerabilities discovered by the threat and vulnerability management module and also offer this module as a supported vulnerability assessment solution. Learn more in [Investigate weaknesses with Microsoft Defender for Endpoint's threat and vulnerability management](deploy-vulnerability-assessment-tvm.md).

    This module also brings the software inventory features described in [Access a software inventory](asset-inventory.md#access-a-software-inventory) and can be automatically enabled for supported machines with [the auto deploy settings](auto-deploy-vulnerability-assessment.md).

- **Analytics-based, cloud-powered, post-breach detection**. Defender for Endpoint quickly adapts to changing threats. It uses advanced analytics and big data. It's amplified by the power of the Intelligent Security Graph with signals across Windows, Azure, and Office to detect unknown threats. It provides actionable alerts and enables you to respond quickly.

- **Threat intelligence**. Defender for Endpoint generates alerts when it identifies attacker tools, techniques, and procedures. It uses data generated by Microsoft threat hunters and security teams, augmented by intelligence provided by partners.

By integrating Defender for Endpoint with Defender for Cloud, you'll benefit from the following extra capabilities:

- **Automated onboarding**. Defender for Cloud automatically enables the Defender for Endpoint sensor on all supported machines connected to Defender for Cloud.

- **Single pane of glass**. The Defender for Cloud portal pages display Defender for Endpoint alerts. To investigate further, use Microsoft Defender for Endpoint's own portal pages where you'll see additional information such as the alert process tree and the incident graph. You can also see a detailed machine timeline that shows every behavior for a historical period of up to six months.

    :::image type="content" source="./media/integration-defender-for-endpoint/microsoft-defender-security-center.png" alt-text="Microsoft Defender for Endpoint's own Security Center" lightbox="./media/integration-defender-for-endpoint/microsoft-defender-security-center.png":::

## What are the requirements for the Microsoft Defender for Endpoint tenant?

When you use Defender for Cloud to monitor your machines, a Defender for Endpoint tenant is automatically created.

- **Location:** Data collected by Defender for Endpoint is stored in the geo-location of the tenant as identified during provisioning. Customer data - in pseudonymized form - may also be stored in the central storage and processing systems in the United States. After you've configured the location, you can't change it. If you have your own license for Microsoft Defender for Endpoint and need to move your data to another location, [contact Microsoft support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) to reset the tenant.
- **Moving subscriptions:** If you've moved your Azure subscription between Azure tenants, some manual preparatory steps are required before Defender for Cloud will deploy Defender for Endpoint. For full details, [contact Microsoft support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).


## Enable the Microsoft Defender for Endpoint integration

### Prerequisites

Confirm that your machine meets the necessary requirements for Defender for Endpoint:

1. Ensure the machine is connected to Azure and the internet as required:

    - **Azure virtual machines (Windows or Linux)** - Configure the network settings described in configure device proxy and internet connectivity settings: [Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet) or [Linux](/microsoft-365/security/defender-endpoint/linux-static-proxy-configuration).

    - **On-premises machines** - Connect your target machines to Azure Arc as explained in [Connect hybrid machines with Azure Arc-enabled servers](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).

1. Enable **Microsoft Defender for servers**. See [Quickstart: Enable Defender for Cloud's enhanced security features](enable-enhanced-security.md).

    > [!IMPORTANT]
    > Defender for Cloud's integration with Microsoft Defender for Endpoint is enabled by default. So when you enable enhanced security features, you give consent for Microsoft Defender for servers to access the Microsoft Defender for Endpoint data related to vulnerabilities, installed software, and alerts for your endpoints.

1. If you've moved your subscription between Azure tenants, some manual preparatory steps are also required. For full details, [contact Microsoft support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).




### Enable the integration

### [**Windows**](#tab/windows)

1. From Defender for Cloud's menu, select **Environment settings** and select the subscription with the Windows machines that you want to receive Defender for Endpoint.

1. Select **Integrations**.

1. Select **Allow Microsoft Defender for Endpoint to access my data**, and select **Save**.

    :::image type="content" source="./media/integration-defender-for-endpoint/enable-integration-with-edr.png" alt-text="Enable the integration between Microsoft Defender for Cloud and Microsoft's EDR solution, Microsoft Defender for Endpoint":::

    Microsoft Defender for Cloud will automatically onboard your machines to Microsoft Defender for Endpoint. Onboarding might take up to 12 hours. For new machines created after the integration has been enabled, onboarding takes up to an hour.

### [**Linux**](#tab/linux)

You'll deploy Defender for Endpoint to your Linux machines in one of two ways - depending on whether you've already deployed it to your Windows machines:

- [Existing users with Defender for Cloud's enhanced security features enabled and Microsoft Defender for Endpoint for Windows](#existing-users-with-defender-for-clouds-enhanced-security-features-enabled-and-microsoft-defender-for-endpoint-for-windows)
- [New users who have never enabled the integration with Microsoft Defender for Endpoint for Windows](#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)


### Existing users with Defender for Cloud's enhanced security features enabled and Microsoft Defender for Endpoint for Windows

If you've already enabled the integration with **Defender for Endpoint for Windows**, you have complete control over when and whether to deploy Defender for Endpoint to your **Linux** machines.

1. From Defender for Cloud's menu, select **Environment settings** and select the subscription with the Linux machines that you want to receive Defender for Endpoint.

1. Select **Integrations**. You'll know that the integration is enabled, if the checkbox for **Allow Microsoft Defender for Endpoint to access my data** is selected as shown:

    :::image type="content" source="./media/integration-defender-for-endpoint/integration-enabled.png" alt-text="The integration between Microsoft Defender for Cloud and Microsoft's EDR solution, Microsoft Defender for Endpoint is enabled":::

    > [!NOTE]
    > If it isn't selected, use the instructions in [New users who've never enabled the integration with Microsoft Defender for Endpoint for Windows](#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows).

1. To add your Linux machines to your integration

    1. Select **Enable for Linux machines**.
    1. Select **Save**.
    1. In the confirmation prompt, verify the information and select **Enable** if you're happy to continue. 

    :::image type="content" source="./media/integration-defender-for-endpoint/enable-for-linux-result.png" alt-text="Confirming the integration between Defender for Cloud and Microsoft's EDR solution, Microsoft Defender for Endpoint for Linux":::

    Microsoft Defender for Cloud will:

    - Automatically onboard your Linux machines to Defender for Endpoint
    - Ignore any machines that are running other fanotify-based solutions (see details of the `fanotify` kernel option required in [Linux system requirements](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux#system-requirements))
    - Detect any previous installations of Defender for Endpoint and reconfigure them to integrate with Defender for Cloud

    Microsoft Defender for Cloud will automatically onboard your machines to Microsoft Defender for Endpoint. Onboarding might take up to 12 hours. For new machines created after the integration has been enabled, onboarding takes up to an hour.

    > [!NOTE]
    > The next time you return to this page of the Azure portal, the **Enable for Linux machines** button won't be shown. To disable the integration for Linux, you'll need to disable it for Windows too by clearing the checkbox for **Allow Microsoft Defender for Endpoint to access my data**, and selecting **Save**.


1. To verify installation of Defender for Endpoint on a Linux machine, run the following shell command on your machines:

    `mdatp health`

    If Microsoft Defender for Endpoint is installed, you'll see its health status:

    `healthy : true`

    `licensed: true`

    Also, in the Azure portal you'll see a new Azure extension on your machines called `MDE.Linux`.

### New users who've never enabled the integration with Microsoft Defender for Endpoint for Windows

If you've never enabled the integration for Windows, the **Allow Microsoft Defender for Endpoint to access my data** option will enable Defender for Cloud to deploy Defender for Endpoint to *both* your Windows and Linux machines.

1. From Defender for Cloud's menu, select **Environment settings** and select the subscription with the Linux machines that you want to receive Defender for Endpoint.

1. Select **Integrations**.

1. Select **Allow Microsoft Defender for Endpoint to access my data**, and select **Save**.

    Microsoft Defender for Cloud will:

    - Automatically onboard your Windows and Linux machines to Defender for Endpoint
    - Ignore any Linux machines that are running other fanotify-based solutions (see details of the `fanotify` kernel option required in [Linux system requirements](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux#system-requirements))
    - Detect any previous installations of Defender for Endpoint and reconfigure them to integrate with Defender for Cloud

    Onboarding might take up to 1 hour.

1. To verify installation of Defender for Endpoint on a Linux machine, run the following shell command on your machines:

    `mdatp health`

    If Microsoft Defender for Endpoint is installed, you'll see its health status:

    `healthy : true`

    `licensed: true`

    In addition, in the Azure portal you'll see a new Azure extension on your machines called `MDE.Linux`.
--- 

## Access the Microsoft Defender for Endpoint portal

1. Ensure the user account has the necessary permissions. Learn more in [Assign user access to Microsoft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Check whether you have a proxy or firewall that is blocking anonymous traffic. The Defender for Endpoint sensor connects from the system context, so anonymous traffic must be permitted. To ensure unhindered access to the Defender for Endpoint portal, follow the instructions in [Enable access to service URLs in the proxy server](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Open the [Defender for Endpoint Security Center portal](https://securitycenter.windows.com/). Learn more about the portal's features and icons, in [Defender for Endpoint Security Center portal overview](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 


## Send a test alert

To generate a benign test alert from Defender for Endpoint, select the tab for the relevant operating system of your endpoint:

### [**Windows**](#tab/windows)

For endpoints running Windows:

1. Create a folder 'C:\test-MDATP-test'.
1. Use Remote Desktop to access your machine.
1. Open a command-line window.
1. At the prompt, copy and run the following command. The command prompt window will close automatically.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/integration-defender-for-endpoint/generate-edr-alert.png" alt-text="A command prompt window with the command to generate a test alert.":::

    If the command is successful, you'll see a new alert on the workload protection dashboard and the Microsoft Defender for Endpoint portal. This alert might take a few minutes to appear.
1. To review the alert in Defender for Cloud, go to **Security alerts** > **Suspicious PowerShell CommandLine**.
1. From the investigation window, select the link to go to the Microsoft Defender for Endpoint portal.

    > [!TIP]
    > The alert is triggered with **Informational** severity.


### [**Linux**](#tab/linux)

For endpoints running Linux:

1. Download the test alert tool from https://aka.ms/LinuxDIY
1. Extract the contents of the zip file and execute this shell script:

    `./mde_linux_edr_diy`

    If the command is successful, you'll see a new alert on the workload protection dashboard and the Microsoft Defender for Endpoint portal. This alert might take a few minutes to appear.

1. To review the alert in Defender for Cloud, go to **Security alerts** > **Enumeration of files with sensitive data**.
1. From the investigation window, select the link to go to the Microsoft Defender for Endpoint portal.

    > [!TIP]
    > The alert is triggered with **Low** severity.

--- 

## Remove Defender for Endpoint from a machine

To remove the Defender for Endpoint solution from your machines:

1. Disable the integration:
    1. From Defender for Cloud's menu, select **Environment settings** and select the subscription with the relevant machines.
    1. Open **Integrations** and clear the checkbox for **Allow Microsoft Defender for Endpoint to access my data**.
    1. Select **Save**.

1. Remove the MDE.Windows/MDE.Linux extension from the machine.

1. Follow the steps in [Offboard devices from the Microsoft Defender for Endpoint service](/microsoft-365/security/defender-endpoint/offboard-machines?view=o365-worldwide) from the Defender for Endpoint documentation.

## FAQ - Microsoft Defender for Cloud integration with Microsoft Defender for Endpoint

- [What's this "MDE.Windows" / "MDE.Linux" extension running on my machine?](#whats-this-mdewindows--mdelinux-extension-running-on-my-machine)
- [What are the licensing requirements for Microsoft Defender for Endpoint?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [If I already have a license for Microsoft Defender for Endpoint, can I get a discount for Microsoft Defender for servers?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-microsoft-defender-for-servers)
- [How do I switch from a third-party EDR tool?](#how-do-i-switch-from-a-third-party-edr-tool)

### What's this "MDE.Windows" / "MDE.Linux" extension running on my machine?

In the past, Microsoft Defender for Endpoint was provisioned by the Log Analytics agent. When [we expanded support to include Windows Server 2019](release-notes-archive.md#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga) and Linux, we also added an extension to perform the automatic onboarding. 

Defender for Cloud automatically deploys the extension to machines running:

- Windows Server 2019 & 2022.
- Windows 10 Virtual Desktop (WVD).
- Other versions of Windows Server if Defender for Cloud doesn't recognize the OS version (for example, when a custom VM image is used). In this case, Microsoft Defender for Endpoint is still provisioned by the Log Analytics agent.
- Linux.

> [!IMPORTANT]
> If you delete the MDE.Windows/MDE.Linux extension, it will not remove Microsoft Defender for Endpoint. to 'offboard', see [Offboard Windows servers.](/microsoft-365/security/defender-endpoint/configure-server-endpoints).


### I've enabled the solution by the "MDE.Windows" / "MDE.Linux" extension isn't showing on my machine

If you've enabled the integration, but still don't see the extension running on your machines, check the following:

1. If 12 hours hasn't passed since you enabled the solution, you'll need to wait until the end of this period to be sure there's an issue to investigate. 
1. After 12 hours have passed, if you still don't see the extension running on your machines, check that you've met [Prerequisites](#prerequisites) for the integration.
1. Ensure you've enabled the [Microsoft Defender for servers](defender-for-servers-introduction.md) plan for the subscriptions related to the machines you're investigating.
1. If you've moved your Azure subscription between Azure tenants, some manual preparatory steps are required before Defender for Cloud will deploy Defender for Endpoint. For full details, [contact Microsoft support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).


### What are the licensing requirements for Microsoft Defender for Endpoint?
Defender for Endpoint is included at no extra cost with **Microsoft Defender for servers**. Alternatively, it can be purchased separately for 50 machines or more.

### If I already have a license for Microsoft Defender for Endpoint, can I get a discount for Microsoft Defender for servers?
If you've already got a license for **Microsoft Defender for Endpoint for Servers** , you won't have to pay for that part of your Microsoft Defender for servers license. Learn more about [this license](/microsoft-365/security/defender-endpoint/minimum-requirements#licensing-requirements).

To request your discount, [contact Defender for Cloud's support team](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). You'll need to provide the relevant workspace ID, region, and number of Microsoft Defender for Endpoint for servers licenses applied for machines in the given workspace.

The discount will be effective starting from the approval date, and won't take place retroactively.

### How do I switch from a third-party EDR tool?
Full instructions for switching from a non-Microsoft endpoint solution are available in the Microsoft Defender for Endpoint documentation: [Migration overview](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).


## Next steps

- [Platforms and features supported by Microsoft Defender for Cloud](security-center-os-coverage.md)
- [Learn how recommendations help you protect your Azure resources](review-security-recommendations.md)

---
title: Manage device certificates - Azure IoT Edge | Microsoft Docs
description: Create test certificates, install, and manage them on an Azure IoT Edge device to prepare for production deployment. 
author: kgremban

ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
---
# Manage certificates on an IoT Edge device

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

All IoT Edge devices use certificates to create secure connections between the runtime and any modules running on the device. IoT Edge devices functioning as gateways use these same certificates to connect to their downstream devices, too.

## Install production certificates

When you first install IoT Edge and provision your device, the device is set up with temporary certificates so that you can test the service.
These temporary certificates expire in 90 days, or can be reset by restarting your machine.
Once you move into a production scenario, or you want to create a gateway device, you need to provide your own certificates.
This article demonstrates the steps to install certificates on your IoT Edge devices.

To learn more about the different types of certificates and their roles, see [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md).

>[!NOTE]
>The term "root CA" used throughout this article refers to the topmost authority public certificate of the certificate chain for your IoT solution. You do not need to use the certificate root of a syndicated certificate authority, or the root of your organization's certificate authority. In many cases, it is actually an intermediate CA public certificate.

### Prerequisites

* An IoT Edge device.

  If you don't have an IoT Edge device set up, you can create one in an Azure virtual machine. Follow the steps in one of the quickstart articles to [Create a virtual Linux device](quickstart-linux.md) or [Create a virtual Windows device](quickstart.md).

* Have a root certificate authority (CA) certificate, either self-signed or purchased from a trusted commercial certificate authority like Baltimore, Verisign, DigiCert, or GlobalSign.

  If you don't have a root certificate authority yet, but want to try out IoT Edge features that require production certificates (like gateway scenarios) you can [Create demo certificates to test IoT Edge device features](how-to-create-test-certificates.md).

### Create production certificates

You should use your own certificate authority to create the following files:

* Root CA
* Device CA certificate
* Device CA private key

In this article, what we refer to as the *root CA* is not the topmost certificate authority for an organization. It's the topmost certificate authority for the IoT Edge scenario, which the IoT Edge hub module, user modules, and any downstream devices use to establish trust between each other.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Currently, a limitation in libiothsm prevents the use of certificates that expire on or after January 1, 2038.

:::moniker-end

To see an example of these certificates, review the scripts that create demo certificates in [Managing test CA certificates for samples and tutorials](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### Install certificates on the device

Install your certificate chain on the IoT Edge device and configure the IoT Edge runtime to reference the new certificates.

Copy the three certificate and key files onto your IoT Edge device. 

If you used the sample scripts to [create demo certificates](how-to-create-test-certificates.md), the three certificate and key files are located at the following paths:

* Device CA certificate: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Device CA private key: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Root CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

You can use a service like [Azure Key Vault](../key-vault/index.yml) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate files. If you generated the certificates on the IoT Edge device itself, you can skip this step and use the path to the working directory.

If you are using IoT Edge for Linux on Windows, you need to use the SSH key located in the Azure IoT Edge `id_rsa` file to authenticate file transfers between the host OS and the Linux virtual machine. Retrieve the Linux virtual machine's IP address using the `Get-EflowVmAddr` command. Then, you can do an authenticated SCP using the following command:

   ```powershell
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

### Configure IoT Edge with the new certificates

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

# [Linux containers](#tab/linux)

1. Open the IoT Edge security daemon config file: `/etc/iotedge/config.yaml`

1. Set the **certificate** properties in config.yaml to the file URI path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Make sure the **certificates:** line has no preceding whitespace and that nested items are indented by two spaces. For example:

   ```yaml
   certificates:
      device_ca_cert: "file:///<path>/<device CA cert>"
      device_ca_pk: "file:///<path>/<device CA key>"
      trusted_ca_certs: "file:///<path>/<root CA cert>"
   ```

1. Make sure that the user **iotedge** has read permissions for the directory holding the certificates.

1. If you've used any other certificates for IoT Edge on the device before, delete the files in the following two directories before starting or restarting IoT Edge:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

# [Windows containers](#tab/windows)

1. Open the IoT Edge security daemon config file: `C:\ProgramData\iotedge\config.yaml`

1. Set the **certificate** properties in config.yaml to the file URI path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Make sure the **certificates:** line has no preceding whitespace and that nested items are indented by two spaces. For example:

   ```yaml
   certificates:
      device_ca_cert: "file:///C:/<path>/<device CA cert>"
      device_ca_pk: "file:///C:/<path>/<device CA key>"
      trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
   ```

1. If you've used any other certificates for IoT Edge on the device before, delete the files in the following two directories before starting or restarting IoT Edge:

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. Restart IoT Edge.

   ```powershell
   Restart-Service iotedge
   ```
---

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Open the IoT Edge security daemon config file: `/etc/aziot/config.toml`

1. Find the `trust_bundle_cert` parameter at the beginning of the file. Uncomment this line, and provide the file URI to the root CA certificate on your device.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Find the `[edge_ca]` section in the config.toml file. Uncomment the lines in this section and provide the file URI paths for the certificate and key files on the IoT Edge device.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Make sure that the service has read permissions for the directories holding the certificates and keys.

   * The private key file should be owned by the **aziotks** group.
   * The certificate files should be owned by the **aziotcs** group.

   >[!TIP]
   >If your device CA certificate is read-only, meaning you created it and don't want the IoT Edge service to rotate it, set the private key file to mode 0440 and the certificate file to mode 0444. If you created the initial files and then configured the cert service to rotate the device CA certificate in the future, set the private key file to mode 0660 and the certificate file to mode 0664.

1. If you've used any other certificates for IoT Edge on the device before, delete the files in the following directory. IoT Edge will recreate them with the new CA certificate you provided.

   * `/var/lib/aziot/certd/certs`

1. Apply the configuration changes.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## Customize certificate lifetime

IoT Edge automatically generates certificates on the device in several cases, including:

* If you don't provide your own production certificates when you install and provision IoT Edge, the IoT Edge security manager automatically generates a **device CA certificate**. This self-signed certificate is only meant for development and testing scenarios, not production. This certificate expires after 90 days.
* The IoT Edge security manager also generates a **workload CA certificate** signed by the device CA certificate

For more information about the function of the different certificates on an IoT Edge device, see [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md).

For these two automatically generated certificates, you have the option of setting a flag in the config file to configure the number of days for the lifetime of the certificates.

>[!NOTE]
>There is a third auto-generated certificate that the IoT Edge security manager creates, the **IoT Edge hub server certificate**. This certificate always has a 30 day lifetime, but is automatically renewed before expiring. The auto-generated CA lifetime value set in the config file doesn't affect this certificate.

Upon expiry after the specified number of days, IoT Edge has to be restarted to regenerate the device CA certificate. The device CA certificate won't be renewed automatically.

<!-- 1.1. -->
:::moniker range="iotedge-2018-06"

# [Linux containers](#tab/linux)

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Currently, a limitation in libiothsm prevents the use of certificates that expire on or after January 1, 2038.

1. Delete the contents of the `hsm` folder to remove any previously generated certificates.

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Restart the IoT Edge service.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Confirm the lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.

# [Windows containers](#tab/windows)

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Currently, a limitation in libiothsm prevents the use of certificates that expire on or after January 1, 2038.

1. Delete the contents of the `hsm` folder to remove any previously generated certificates.

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. Restart the IoT Edge service.

   ```powershell
   Restart-Service iotedge
   ```

1. Confirm the lifetime setting.

   ```powershell
   iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.

---

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **Edge CA certificate (Quickstart)** section of the config file.

   ```toml
   [edge_ca]
   auto_generated_edge_ca_expiry_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Apply the configuration changes.

   ```bash
   sudo iotedge config apply
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 -->

## Next steps

Installing certificates on an IoT Edge device is a necessary step before deploying your solution in production. Learn more about how to [Prepare to deploy your IoT Edge solution in production](production-checklist.md).

---
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/28/2021
ms.author: eric-urban
ms.custom: ignite-fall-2021
---

## Download and install

#### [Windows installation](#tab/windowsinstall)

Follow these steps to install the Speech CLI on Windows:

1. Install the [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) for your platform. Installing it for the first time might require a restart.
1. Install [.NET Core 3.1 SDK](/dotnet/core/install/windows).
2. Install the Speech CLI via NuGet by entering this command:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI
   ```
Enter `spx` to see help for the Speech CLI.

> [!NOTE]
> As an alternative to NuGet, you can download and extract the Speech CLI for Windows as a [.zip file](https://aka.ms/speech/spx-windows).

### Font limitations

On Windows, the Speech CLI can show only fonts that are available to the command prompt on the local computer. [Windows Terminal](https://www.microsoft.com/p/windows-terminal/9n0dx20hk701) supports all fonts that the Speech CLI produces interactively.

If you output to a file, a text editor like Notepad or a web browser like Microsoft Edge can also show all fonts.

#### [Linux installation](#tab/linuxinstall)

The following Linux distributions are supported for x64 architectures that use the Speech CLI:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 18.04/20.04

> [!NOTE]
> The Speech SDK (not the Speech CLI) supports additional architectures. For more information, see [About the Speech SDK](../speech-sdk.md).

Follow these steps to install the Speech CLI on Linux on an x64 CPU:

1. Install the [.NET Core 3.1 SDK](/dotnet/core/install/linux).
2. Install the Speech CLI via NuGet by entering this command:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI`

3. On RHEL/CentOS Linux, [Configure OpenSSL for Linux](../how-to-configure-openssl-linux.md).
4. On Ubuntu 20.04 Linux, [install GStreamer](../how-to-use-codec-compressed-audio-input-streams.md).

Enter `spx` to see help for the Speech CLI.

> [!NOTE]
> As an alternative to NuGet, you can download the Linux binaries as a [.zip file](https://aka.ms/speech/spx-linux). Extract `spx-netcore-30-linux-x64.zip` to a new `~/spx` directory, enter `sudo chmod +r+x spx` on the binary, and add the `~/spx` path to your `PATH` system variable.


#### [Docker installation (Windows, Linux, macOS)](#tab/dockerinstall)

> [!IMPORTANT]
> You can't use your computer's microphone when you run the Speech CLI within a Docker container. However, you can read from and save audio files in your local mounted directory. 

> [!NOTE]
> The following example pulls a public container image from Docker Hub. We recommend that you authenticate with your Docker Hub account (`docker login`) first instead of making an anonymous pull request. To improve reliability when you're using public content, import and manage the image in a private Azure container registry. [Learn more about working with public images](../../../container-registry/buffer-gate-public-content.md).

Follow these steps to install the Speech CLI in a Docker container:

1. <a href="https://www.docker.com/get-started" target="_blank">Install Docker Desktop</a> for your platform if it isn't already installed.
2. In a new command prompt or terminal, enter this command:
   ```console   
   docker pull msftspeech/spx
   ```
Enter this command to display help information for the Speech CLI:

```console 
docker run -it --rm msftspeech/spx help
```

### Mount a directory in the container

The Speech CLI tool saves configuration settings as files. It loads these files when you're performing any command (except help commands).

When you're using the Speech CLI within a Docker container, you must mount a local directory from the container, so the tool can:

- Store or find the configuration settings.
- Read or write any files that the command requires, such as audio files of speech.

On Windows, enter this command to create a local directory that the Speech CLI can use from within the container:

`mkdir c:\spx-data`

On Linux or macOS, enter this command in a terminal to create a directory and see its absolute path:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

You'll use the absolute path when you call the Speech CLI.

### Run the Speech CLI in the container

This documentation shows the Speech CLI `spx` command used in non-Docker installations. When you're calling the `spx` command in a Docker container, you must mount a directory in the container to your file system where the Speech CLI can store and find configuration values and read and write files.

On Windows, your commands start like this:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

On Linux or macOS, your commands look like the following sample. Replace `ABSOLUTE_PATH` with the absolute path for your mounted directory. The `pwd` command returned this path in the previous section. If you run this command before setting your key and region, you'll get an error that tells you to set your key and region.

```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

To use the `spx` command installed in a container, always enter the full command as shown in the preceding sample, followed by the parameters of your request. For example, on Windows, this command sets your key:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

For more extended interaction with the command-line tool, you can start a container with an interactive Bash shell by adding an `entrypoint` parameter. On Windows, enter this command to start a container that exposes an interactive command-line interface where you can enter multiple `spx` commands:

```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command-line shortcut

If you're running the Speech CLI from a Docker container on Linux or macOS, you can create a shortcut:

1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Add the following function to `.bash_profile`. Be sure to update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just use `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***
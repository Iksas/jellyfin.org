---
uid: installation-advanced-source
title: Building from source
description: Compile and run Jellyfin yourself.
sidebar_position: 4
---

<!-- markdownlint-disable MD029 ol-prefix -->

As an alternative to using binary packages, you can build Jellyfin from source.

Jellyfin supports several methods of building for different platforms and instructions for all supported platforms are below.

All package builds begin with these two steps:

1. Clone the repository.

   ```sh
   git clone https://github.com/jellyfin/jellyfin-packaging.git
   cd jellyfin-packaging
   ```

2. Initialize the submodules.

   ```sh
   git submodule update --init
   ```

## Container image

3. Build the container image using Docker or Podman.

   ```sh
   docker build -t $USERNAME/jellyfin --file docker/Dockerfile .
   ```

   or

   ```sh
   podman build -t $USERNAME/jellyfin --file docker/Dockerfile .
   ```

   or use provided Python build script:

   ```sh
   ./build.py auto docker
   ```

   Replace "auto" with your own Jellyfin version tag if you want to.

4. Run Jellyfin in a new container using Docker or Podman from the built container image.

   ```sh
   docker run -d -p 8096:8096 $USERNAME/jellyfin
   ```

   or

   ```sh
   podman run -d -p 8096:8096 $USERNAME/jellyfin
   ```

## Linux or MacOS

3. Use the included `build` script to perform builds.

   ```sh
   ./build --help
   ./build --list-platforms
   ./build <platform> all
   ```

4. The resulting archives can be found at `../bin/<platform>`.

:::note

This will very likely be split out into a separate repository at some point in the future.

:::

## Windows

3. Install dotnet SDK 9.0 from [Microsoft's Website](https://dotnet.microsoft.com/en-us/download/dotnet/9.0) and [install Git for Windows](https://gitforwindows.org/).
   You must be on Powershell 3 or higher.

4. From Powershell set the execution policy to unrestricted.

   ```powershell
   set-executionpolicy unrestricted
   ```

5. If you are building a version of Jellyfin newer than 10.6.4, you will need to download the build script from a separate repository.

   ```powershell
   cd jellyfin-server
   git clone https://github.com/jellyfin/jellyfin-server-windows.git windows
   ```

6. Run the Jellyfin build script.

   ```powershell
   windows\build-jellyfin.ps1 -verbose
   ```

   - The `-WindowsVersion` and `-Architecture` flags can optimize the build for your current environment; the default is generic Windows x64.

   - The `-InstallLocation` flag lets you select where the compiled binaries go; the default is `$Env:AppData\Jellyfin-Server\`.

   - The `-InstallNSSM` flag will automatically pull the stable `nssm` binary appropriate to your architecture (x86/x64 only for now) from [NSSM's Website](https://nssm.cc/) and place it in your Jellyfin directory.

7. Install `jellyfin-ffmpeg`: Download the newest `.zip` from the [Jellyfin Repository](https://repo.jellyfin.org/?path=/ffmpeg/windows), and extract the files into `%APPDATA%\Jellyfin-Server\` (e.g. `%APPDATA%\Jellyfin-Server\ffmpeg.exe`).

8. (Optional) Install the web interface (`jellyfin-web`)

   Install [nodejs](https://nodejs.org/en/download), then run the following commands:

   ```powershell
   cd ../jellyfin-web/
   npm install -g npm@10
   npm install
   npm run build:production
   Copy-Item -Recurse ./dist/ -Destination "$env:APPDATA\Jellyfin-Server\jellyfin-web\"
   ```

9. Jellyfin is now available in the default directory, or whichever directory you chose.

   - Start it from PowerShell.

     ```powershell
     &"$env:APPDATA\Jellyfin-Server\jellyfin.exe"
     ```

   - Start it from CMD.

     ```cmd
     %APPDATA%\Jellyfin-Server\jellyfin.exe
     ```

   The web interface will be available on port `8096`. If you didn't install the web interface, add the `--nowebclient` flag to the start command.

10. (Optional) Use [NSSM](https://nssm.cc) to configure Jellyfin to run as a service.

:::note

This will very likely be split out into a separate repository at some point in the future.

:::

# escape=`
### ^MUST BE THE FIRST LINE^

# If the dotnet restore command can't connect to NuGet, follow the steps here:
# https://improveandrepeat.com/2019/09/how-to-fix-network-errors-with-docker-and-windows-containers/
# My VPN was the lowest, I just connected that and it worked

### Pull bare-bones ASP.NET 5.0 utilities in Windows Server Core 2019 to use as the base image
FROM mcr.microsoft.com/dotnet/aspnet:5.0.0-windowsservercore-ltsc2019 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

### Install the MATLAB Compiler Runtime (MCR) into the base ASP.NET image
FROM base AS matlab
# Quick, dirty, inelegant, and robust method of downloading a file for a Windows image
ADD https://ssd.mathworks.com/supportfiles/downloads/R2020b/Release/5/deployment_files/installer/complete/win64/MATLAB_Runtime_R2020b_Update_5_win64.zip /temp/mcr.zip
RUN powershell -Command `
        $ErrorActionPreference = 'Stop'; `
        $ProgressPreference = 'SilentlyContinue'; `
        `
        # Extract the zip into c:\temp\mcr\ and run setup.exe using the non-interactive settings, writing the install log to c:\mcr-install.log
        Expand-Archive c:\temp\mcr.zip -DestinationPath c:\temp\mcr; `
        `
        Start-Process c:\temp\mcr\setup.exe -ArgumentList '-mode silent', '-agreeToLicense yes', '-outputFile c:\mcr-install.log' -Wait; `
        `
        # Get rid of all the installation files
        Remove-Item -Force -Recurse c:\temp
# The below line is not needed for this base image but some dotnet images change the USER
# USER ContainerAdministrator
# Add the MCR folder to the PATH (requires admin rights)
RUN setx /m PATH "%PATH%;C:\Program Files\MATLAB\MATLAB Runtime\v99\runtime\win64"

# escape=`
FROM mcr.microsoft.com/windows/servercore:1809

WORKDIR C:/
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ENV NODE_VERSION 10.15.3

RUN $uri = 'https://nodejs.org/dist/v{0}/node-v{0}-x64.msi' -f $env:NODE_VERSION; `
Invoke-WebRequest -Uri $uri -OutFile 'node.msi' -UseBasicParsing; `
Start-Process msiexec.exe -ArgumentList '/i', 'node.msi', '/quiet', '/norestart' -NoNewWindow -Wait; `
Remove-Item 'node.msi' -Force;

ENV PWSH_VERSION 6.2.0

RUN $uri = 'https://github.com/PowerShell/PowerShell/releases/download/v{0}/PowerShell-{0}-win-x64.msi' -f $env:PWSH_VERSION; `
Invoke-WebRequest -Uri $uri -OutFile 'pwsh.msi' -UseBasicParsing; `
Start-Process msiexec.exe -ArgumentList '/i', 'pwsh.msi', '/quiet', '/norestart' -NoNewWindow -Wait; `
Remove-Item 'pwsh.msi' -Force;

ENV AGENT_VERSION 2.150.3

RUN $uri = 'https://vstsagentpackage.azureedge.net/agent/{0}/vsts-agent-win-x64-{0}.zip' -f $env:AGENT_VERSION; `
Invoke-WebRequest -Uri $uri -OutFile 'Agent.zip' -UseBasicParsing; `
Expand-Archive -Path 'Agent.zip' -DestinationPath 'Agent'; `
Remove-Item 'Agent.zip' -Force;

RUN $uri = 'https://dl.google.com/chrome/install/chrome_installer.exe'; `
Invoke-WebRequest -Uri $uri -OutFile 'ChromeSetup.exe' -UseBasicParsing; `
Start-Process -FilePath 'ChromeSetup.exe' -ArgumentList '/silent', '/install' -Wait; `
Remove-Item 'ChromeSetup.exe' -Force;

WORKDIR C:/Agent
ENV SKIP_CONFIG=1
CMD if ($env:SKIP_CONFIG -eq 0) { .\config.cmd; } .\run.cmd
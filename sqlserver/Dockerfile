FROM microsoft/mssql-server-windows-express

ENV MSDTC_TEST_DATABASE ODBCTest
ENV MSDTC_TESTER_HOSTNAME dtctester
ENV MSDTC_SQLSERVER_PASSWORD Passw0rd
ENV ACCEPT_EULA Y

COPY /files .

RUN powershell -Command \
  Stop-Service MSDTC ; \
  Uninstall-Dtc -Confirm:$false ; \
  Install-Dtc ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC\Security' -Name LuTransactions -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC\Security' -Name NetworkDtcAccess -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC\Security' -Name NetworkDtcAccessInbound -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC\Security' -Name NetworkDtcAccessOutbound -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC\Security' -Name NetworkDtcClients -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC\Security' -Name NetworkDtcAccessTransactions -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC\Security' -Name NetworkDtcAccessAdmin -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC\Security' -Name NetworkDtcAccessClients -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC' -Name AllowOnlySecureRpcCalls -Value 1 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC' -Name FallbackToUnsecureRPCIfNecessary -Value 0 ; \
  Set-ItemProperty -Path 'HKLM:\Software\Microsoft\MSDTC' -Name TurnOffRpcSecurity -Value 0 ; \
  Set-DtcNetworkSetting -AuthenticationLevel NoAuth -Confirm:$false ; \
  Set-Service MSDTC -StartupType Automatic ; \
  Restart-Service MSDTC -Force ;

RUN powershell -Command "& {Get-Content '.\start.ps1' | Where-Object {$_ -notmatch 'Start-Sleep'} | Out-File '.\new_start.ps1';}"

CMD ["powershell", ".\\entrypoint.ps1", "-sa_password $env:MSDTC_SQLSERVER_PASSWORD", "-attach_dbs \"$env:attach_dbs\"", "-Verbose"]

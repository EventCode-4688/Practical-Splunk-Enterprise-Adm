# Resources

## Windows UF
To install the Windows UF on your local computer, run the following commands in PowerShell:
```
PS> Start-Process powershell -Verb runAs    #will open an admin powershell session
PS C:\Windows\System32> msiexec.exe /i <fullpathto>Practical-Splunk-Enterprise-Adm\resources\splunkforwarder-9.1.0.1-77f73c9edb85-x64-release.msi USE_LOCAL_SYSTEM=1 PRIVILEGESECURITY=1 AGREETOLICENSE=yes /quiet  # make sure to include the full path to your cloned class repo
```


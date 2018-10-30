# Build

```bash
msbuild kbfiltr.vcxproj /p:Configuration="Win8.1 Release" /property:Platform=x64

## Sign
# 1. Create test signing certificate
makecert -r -pe -ss PrivateCertStore -n CN=Contoso.com(Test) ContosoTest.cer
# 2. Sign driver
signtool sign /v /s PrivateCertStore /n Contoso.com(Test) /t http://timestamp.verisign.com/scripts/timestamp.dll build/x64-Win8.1Release/kbfiltr.sys
```

# Install

1. On the target system, run `bcdedit /set testsigning on` and install the test cert
2. Copy `build/x64-Win8.1Release/kbfiltr.sys` to `c:/Windows/System32/drivers` on the target system
3. Create service on target system
   ```
   sc create kbfiltr binPath= C:\Windows\System32\drivers\kbfiltr.sys type= kernel
   ```
4. Install driver as an upper device filter below `kbdclass`
   ```
   REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Class\{4D36E96B-E325-11CE-BFC1-08002BE10318} /v UpperFilters /t REG_MULTI_SZ /d kbfiltr\0kbdclass /f
   ```
5. Reboot target system (TODO: find a more immediate way of installing and updating)

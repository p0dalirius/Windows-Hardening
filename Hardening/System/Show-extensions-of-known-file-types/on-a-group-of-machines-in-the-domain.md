# Setup on a group of machines in the domain

To prioritise IPv4 over IPv6 on a group of machines in the domain, we need to create a Group Policy that will add a registry entry to the machines. The change will take effect at reboot.

First, we need to login to the domain controller with a domain administrator. And then we run the Group Policy editor (`regedt32.exe`):

![](./imgs/)

Run the registry editor (`regedt32.exe`)

Add a registry key per the screenshot below then reboot for the change to take effect. To undo this change, simply delete the registry entry, then reboot again.

|   |   |
|---|---|
| **Location** | `HKLM\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters` | 
| **Key**      | `DisabledComponents`                                       | 
| **Type**     | `REG_DWORD`                                                | 
| **Value**    | `0x20`                                                     |

## References
 - https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/configure-ipv6-in-windows
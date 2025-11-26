# Disabling Credential Guard, VBS, and Core Isolation in Windows

This guide outlines the steps required to disable **Credential Guard**, **Virtualization Based Security (VBS)**, and **Core Isolation** in Windows. Please follow the instructions carefully.

## 1. Disable Credential Guard with Registry Settings

To disable Credential Guard, modify the following registry settings:

* **Key path:** `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa`
  **Key name:** `LsaCfgFlags`
  **Type:** `REG_DWORD`
  **Value:** `0`

* **Key path:** `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DeviceGuard`
  **Key name:** `LsaCfgFlags`
  **Type:** `REG_DWORD`
  **Value:** `0`

## 2. Disable Credential Guard with UEFI Lock

To disable Credential Guard with a UEFI lock, follow these steps in **Windows Command Prompt** (Run as Administrator):

1. Mount the EFI partition:

   ```
   mountvol X: /s
   ```

2. Copy the `SecConfig.efi` file to the EFI partition:

   ```
   copy %WINDIR%\System32\SecConfig.efi X:\EFI\Microsoft\Boot\SecConfig.efi /Y
   ```

3. Create a new boot entry:

   ```
   bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
   ```

4. Set the path for the new boot entry:

   ```
   bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
   ```

5. Set the boot sequence to the new entry:

   ```
   bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
   ```

6. Disable LSA-ISO for the new boot entry:

   ```
   bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO
   ```

7. Set the device partition:

   ```
   bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=X:
   ```

8. Unmount the EFI partition:

   ```
   mountvol X: /d
   ```

## 3. Disable VBS with Registry Settings

To disable Virtualization-Based Security (VBS), delete the following registry keys:

* **Key path:** `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard`
  **Key name:** `EnableVirtualizationBasedSecurity`

* **Key path:** `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard`
  **Key name:** `RequirePlatformSecurityFeatures`

## 4. Disable LSA-ISO and VBS in Boot Configuration

Run the following commands in **Windows Command Prompt** (Run as Administrator) to disable LSA-ISO and VBS:

1. Disable LSA-ISO and VBS:

   ```
   bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
   ```

2. Set VSM launch type to off:

   ```
   bcdedit /set vsmlaunchtype off
   ```

## 5. Disable Virtualization-Based Security via Group Policies

Follow these steps to disable Virtualization-Based Security (VBS) using the Group Policy Editor:

1. Open **Group Policy Editor** (`gpedit.msc`).
2. Navigate to:

   ```
   Computer Configuration -> Administrative Templates -> System -> Device Guard
   ```
3. Select **"Turn ON Virtualization Based Security"** and choose the **"Disable"** option.

## 6. Turn Off Core Isolation in Windows 11 24H2

To turn off **Core Isolation** in Windows 11 (24H2):

1. Open **Windows Settings**.
2. Navigate to:

   ```
   Windows Start -> Core Isolation
   ```
3. Turn off all available options.

## 7. Disable Hyper-V, Virtual Machine Platform, and WSL

In Windows 11, disable the following features:

1. Open **Windows Features**.
2. Uncheck the following:

   * **Hyper-V**
   * **Virtual Machine Platform**
   * **Windows Subsystem for Linux (WSL)**

## 8. Restart the PC

After completing the above steps, restart your PC. Before the OS boots, you will be prompted that the UEFI was modified. Press **F3** and press **Enter** to continue. [1]

---

[1]: https://community.broadcom.com/vmware-cloud-foundation/discussion/windows-11-24h2-hsot-how-to-disable-virtual-based-security?utm_source=chatgpt.com "Windows 11 24h2 hsot - how to disable Virtual Based Security"

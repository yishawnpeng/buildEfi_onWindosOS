# buildEfi_onWindosOS
Use EDK2 to build eif under Windows OS

# Set up enviroment
## 1.Down load UEFI open sourse--EDK2

下載intel在git上維護的專案[tianocore/edk2](https://github.com/tianocore/edk2.git)

或用git指令 ```git https://github.com/tianocore/edk2.git <你的新創資料夾>``` (<你的新創資料夾>以下用EDKII代替)

EDK2是Intel根據[UEFI規範(wiki)](https://zh.wikipedia.org/wiki/%E7%B5%B1%E4%B8%80%E5%8F%AF%E5%BB%B6%E4%BC%B8%E9%9F%8C%E9%AB%94%E4%BB%8B%E9%9D%A2)所開發的開源專案 已經包含一些sample code和UEIF BIOS開發工具 是目前最多開發者所依照使用的方法


## 2.Install ASL complier

下載並安裝[ASL編譯器(.zip)](https://acpica.org/sites/acpica/files/iasl-win-20190405.zip) (建議解壓到c:\asl)

ASL 是用來編譯ACPI(Advanced Configuration and Power Interface)的編譯器 編譯後為AML(是一種bytecode 在OS解釋後執行)

(利用RW.exe 開啟ACPI table的畫面 *註1)

![ACPI table on RW tool](https://user-images.githubusercontent.com/29775017/208063984-d8b1c20f-4e9d-4da5-9f73-6dd99fe1041c.png)


## 3.Intsall NASM complier

下載並安裝[NASM組譯器(.zip)](https://www.nasm.us/pub/nasm/releasebuilds/2.14.02/win64/nasm-2.14.02-win64.zip) (建議解壓到c:\nasm)

NASM (Netwide Assembler) 基於intel x86架構的組譯和反組譯工具 [wiki補充](https://zh.wikipedia.org/zh-tw/Netwide_Assembler)


## 4.Modify target.txt

在EDKII 新創Test\test.dsc (下1)

並在EDKII執行```edksetup.bat``` 他會建立一些暫時的環境變數還有建立並複製build所需的相關檔案

這時EDKII\Conf\ 會長出target.txt 

修改target.txt :

```ACTIVE_PLATFORM = Test/test.dsc```

```TARGET = RELEASE```

```TARGET_ARCH = X64```


# Coding and build 

## 1.Add new .dsc/.inf/.c in \Test
![image](https://user-images.githubusercontent.com/29775017/208332408-517820bb-38fd-49d6-b8c5-d90cc3be68de.png)

(1) test.dsc *註2
```[Defines]
  PLATFORM_NAME                  = test
  PLATFORM_GUID                  = a7af8bac-1291-453b-ac50-9edc786b41e2
  PLATFORM_VERSION               = 0.98
  DSC_SPECIFICATION              = 0x00010005
  OUTPUT_DIRECTORY               = Build/testOutput
  SUPPORTED_ARCHITECTURES        = IA32|X64
  BUILD_TARGETS                  = DEBUG|RELEASE|NOOPT
  SKUID_IDENTIFIER               = DEFAULT

  
[LibraryClasses]
  UefiApplicationEntryPoint|MdePkg/Library/UefiApplicationEntryPoint/UefiApplicationEntryPoint.inf
  UefiLib|MdePkg/Library/UefiLib/UefiLib.inf
  PcdLib|MdePkg/Library/BasePcdLibNull/BasePcdLibNull.inf
  DebugLib|MdePkg/Library/BaseDebugLibNull/BaseDebugLibNull.inf
  BaseMemoryLib|MdePkg/Library/BaseMemoryLib/BaseMemoryLib.inf
  BaseLib|MdePkg/Library/BaseLib/BaseLib.inf
  RegisterFilterLib|MdePkg/Library/RegisterFilterLibNull/RegisterFilterLibNull.inf
  PrintLib|MdePkg/Library/BasePrintLib/BasePrintLib.inf
  MemoryAllocationLib|MdePkg/Library/UefiMemoryAllocationLib/UefiMemoryAllocationLib.inf
  UefiBootServicesTableLib|MdePkg/Library/UefiBootServicesTableLib/UefiBootServicesTableLib.inf
  DevicePathLib|MdePkg/Library/UefiDevicePathLib/UefiDevicePathLib.inf
  UefiRuntimeServicesTableLib|MdePkg/Library/UefiRuntimeServicesTableLib/UefiRuntimeServicesTableLib.inf
  
  
[Components]
  Test\HelloWorld.inf
  ```


## 2.Setup and build

## 3.Output file

# Testing
1.Make a UEFI shell USB

2.Put build .efi into file

3.Run .efi

# Remark
1.isolate memary

2.PLATFORM_GUID OUTPUT_DIRECTORY

.F9

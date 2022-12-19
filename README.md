# buildEfi_onWindosOS
Use EDK2 to build eif under Windows OS

# Set up enviroment
## 1.Down load UEFI open sourse--EDK2

下載 intel 在 git 上維護的專案[tianocore/edk2](https://github.com/tianocore/edk2.git)

或用 git 指令 ```git https://github.com/tianocore/edk2.git <你的新創資料夾>``` (<你的新創資料夾>以下用 EDKII 代替)

EDK2 是 Intel 根據[UEFI規範(wiki)](https://zh.wikipedia.org/wiki/%E7%B5%B1%E4%B8%80%E5%8F%AF%E5%BB%B6%E4%BC%B8%E9%9F%8C%E9%AB%94%E4%BB%8B%E9%9D%A2)所開發的開源專案 已經包含一些 sample code 和 UEIF BIOS 開發工具 是目前最多開發者所依照使用的方法


## 2.Install ASL complier

下載並安裝[ASL編譯器(.zip)](https://acpica.org/sites/acpica/files/iasl-win-20190405.zip) (建議解壓到 c:\asl)

ASL 是用來編譯 ACPI(Advanced Configuration and Power Interface)的編譯器 編譯後為 AML(是一種 bytecode 在 OS 解釋後執行)

(利用 RW.exe 開啟 ACPI table 的畫面 *註1)

![ACPI table on RW tool](https://user-images.githubusercontent.com/29775017/208063984-d8b1c20f-4e9d-4da5-9f73-6dd99fe1041c.png)


## 3.Intsall NASM complier

下載並安裝[NASM組譯器(.zip)](https://www.nasm.us/pub/nasm/releasebuilds/2.14.02/win64/nasm-2.14.02-win64.zip) (建議解壓到 c:\nasm)

NASM (Netwide Assembler) 基於 intel x86 架構的組譯和反組譯工具 [wiki補充](https://zh.wikipedia.org/zh-tw/Netwide_Assembler)


## 4.Modify target.txt

在 EDKII 新創Test\test.dsc (下1.)

並在 EDKII 執行```edksetup.bat``` 他會建立一些暫時的環境變數還有建立並複製 build 所需的相關檔案

這時 EDKII\Conf\ 會長出 target.txt 

修改 target.txt :

```ACTIVE_PLATFORM = Test/test.dsc```

```TARGET = RELEASE```

```TARGET_ARCH = X64```


# Coding and build 

## 1.Add new .dsc/.inf/.c in \Test
![image](https://user-images.githubusercontent.com/29775017/208332408-517820bb-38fd-49d6-b8c5-d90cc3be68de.png)

(1) test.dsc 
```
[Defines]
  PLATFORM_NAME                  = test
  PLATFORM_GUID                  = a7af8bac-1291-453b-ac50-9edc786b41e2  #到網路上新建一個 GUID
  PLATFORM_VERSION               = 0.98
  DSC_SPECIFICATION              = 0x00010005
  OUTPUT_DIRECTORY               = Build/testOutput                      #testOutput 可以自己取
  SUPPORTED_ARCHITECTURES        = IA32|X64                              #可以只要 X64
  BUILD_TARGETS                  = DEBUG|RELEASE|NOOPT                   #可以只要 release
  SKUID_IDENTIFIER               = DEFAULT

  
[LibraryClasses]  #必須的lib 可以試看看 少一個都會叫你安裝
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
  
  
[Components]  #你的inf 
  Test\HelloWorld.inf
  ```
  
(2) HelloWorld.inf
```
[Defines]
  INF_VERSION                    = 0x00010005
  BASE_NAME                      = HelloWorld
  FILE_GUID                      = d47f3064-f65b-4779-8f57-114fe12df518 #到網路上新建一個 GUID
  MODULE_TYPE                    = UEFI_APPLICATION                     #我們用 UEFI APP 即可
  VERSION_STRING                 = 1.0                                  #非必要
  ENTRY_POINT                    = UefiMain                             #你的.c的入口函數(即你要執行的函數 通常用 main)

[Sources]             #你的.c
  HelloWorld.c

[Packages]
  MdePkg/MdePkg.dec
  MdeModulePkg/MdeModulePkg.dec

[LibraryClasses]
  UefiApplicationEntryPoint
  UefiLib
```

(3) HelloWorld.c
```
#include <Uefi.h>
#include <Library/UefiLib.h>

//
// String token ID of help message text.
// Shell supports to find help message in the resource section of an application image if
// .MAN file is not found. This global variable is added to make build tool recognizes
// that the help string is consumed by user and then build tool will add the string into
// the resource section. Thus the application can use '-?' option to show help message in
// Shell.
//

/**
  The user Entry Point for Application. The user code starts with this function
  as the real entry point for the application.

  @retval EFI_SUCCESS       The entry point is executed successfully.
  @retval other             Some error occurs when executing this entry point.

**/
EFI_STATUS
EFIAPI
UefiMain (
  )
{
  UINT32  Index;
  Index = 0;
  Print(L"\n%02x", Index);    //Print()是 EDKII 的函數   %x印出通用   字串前面用L代表
  
  Print(L"\nHellowWord");     //如果沒有/n 前面幾個字會被 shell>>會被吃掉

  return EFI_SUCCESS;
}
```

## 2.Setup and build

重新在 EDKII 執行```edksetup.bat``` 因為我們有修改.inf/.dsc

就可以```build```直接 build (若後續只有改.c檔也可以直接 build 不用重新 setup )

若 setup 有 error 通常是相對路徑或是lib有缺

## 3.Output file

build 出來的檔案會在你 test.dsc 的```OUTPUT_DIRECTORY = Build/testOutput```的位置

以我的舉例是\EDKII\Build\testOutput\RELEASE_VS2015x86\X64\Test\HelloWorld\OUTPUT\HelloWorld.efi

(但其實 \EDKII\Build\testOutput\RELEASE_VS2015x86\X64\ 底下也會有 我目前還不確認有兩個相同的檔案用意)

# Testing
## 1.Make a UEFI shell USB

製作方法可以參考[其一](https://twgreatdaily.com/eQQf6GwBJleJMoPMFhsQ.html)或[其二](https://storage-asset.msi.com/file/pdf/Win8_BIOS_Update_Step_by_Step_Guide_ch.pdf)的前半部分

重點是(1)格式化 USB 為 fat32 格式和(2)USB有/EFI/BOOT/Bootx64.efi 

## 2.Put build .efi into file

將 HelloWorld.efi 放到 usb 裡面 (建議根目錄就好)

## 3.Run .efi

依據不同BIOS/主機板開發商 進入 boot menu 方法不同

進入後執行```HelloWorld.efi```

(下圖為HelloWorld.c 裡面只有 Print(L"HellowWord/n"); 可以發現前幾個字被吃掉了)
![image](https://user-images.githubusercontent.com/29775017/208379215-1a50411b-1aef-46a1-b22b-94cac053997d.png)

# Remark

1.isolate memary

若 OS 是 Win10 需要關掉核心隔離(Core isolation)並重新開機 ,而 Win11 還需 win+R 輸入 regedit 修改 ```HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\CI\Config\VulnerableDriverBlocklistEnable``` 為0

才能執行RW.exe

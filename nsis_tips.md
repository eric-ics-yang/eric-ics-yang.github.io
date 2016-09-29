NSIS Nullsoft Scriptable Installation System
=================
###写文件
```
    Name "WTEST"
    OutFile "Setup.exe"
    RequestExecutionLevel user

    Section "Main"
    FileOpen $0 "C:\test\test.txt" w
    FileWrite $0 'load("test.il" "test")\r$\n'
    FileClose $0
    SectionEnd
```

###修改商标信息
> BrandingText "COMPANY"

###添加背景音乐（未验证）
```
Var hmci 
Function .OnInit 
InitPluginsDir 
SetOutPath $PLUGINSDIR 
File "G:\Temp\Nsisres\music.mp3"             [说明：源MP3的存放路径] 
System::Call msvfw32.dll::MCIWndCreate(i 0, i 0,i 0x0070, t "$PLUGINSDIR\music.mp3") i .r0 
SendMessage $0 0x0465 0 "STR:play repeat" 
System::Call user32.dll::ShowWindow(i r0, i 0) 
StrCpy $hmci $0 
Functionend 

Section 
;add your codes 
SendMessage $hmci 0x0010 0 0 
Sectionend
```
from [blog] [1].

  [1]: http://blog.csdn.net/u010434924/article/details/46011721 

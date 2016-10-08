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

###获取绝对路径名称
```
4.9.3.9 GetFullPathName

[/SHORT] user_var(output) path_or_file

Assign to the user variable $x, the full path of the file specified. If the path portion of the parameter is not found, the error flag will be set and $x will be empty. If /SHORT is specified, the path is converted to the short filename form. However, if /SHORT is not specified, the path isn't converted to its long filename form. To get the long filename, call GetLongPathName using the System plug-in. Note that GetLongPathName is only available on Windows 98, Windows 2000 and above.

StrCpy $INSTDIR $PROGRAMFILES\NSIS
SetOutPath $INSTDIR
GetFullPathName $0 ..
DetailPrint $0 # will print C:\Program Files
GetFullPathName /SHORT $0 $INSTDIR
DetailPrint $0 # will print C:\Progra~1\NSIS

StrCpy $0 C:\Progra~1\NSIS
System::Call 'kernel32::GetLongPathName(t r0, t .r1, i ${NSIS_MAX_STRLEN}) i .r2'
StrCmp $2 error +2
StrCpy $0 $1
DetailPrint $0 # will print C:\Program Files\NSIS, where supported

```
ref: [window api][1]

  [1]: https://msdn.microsoft.com/en-us/library/windows/desktop/aa364963%28v=vs.85%29.aspx
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

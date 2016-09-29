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

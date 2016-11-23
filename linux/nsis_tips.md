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
  
###变量
4.2.1 User Variables

$VARNAME

User variables can be declared with the Var command. You can use these variables to store values, work with string manipulation etc.
4.2.1.1 Var

[/GLOBAL] var_name

Declare a user variable. Allowed characters for variables names: [a-z][A-Z][0-9] and '_'. All defined variables are global, even if defined in a section or a function. To make this clear, variables defined in a section or a function must use the /GLOBAL flag. The /GLOBAL flag is not required outside of sections and functions.

Var example

Function testVar
  Var /GLOBAL example2

  StrCpy $example "example value"
  StrCpy $example2 "another example value"
FunctionEnd

4.2.2 Other Writable Variables

$0, $1, $2, $3, $4, $5, $6, $7, $8, $9, $R0, $R1, $R2, $R3, $R4, $R5, $R6, $R7, $R8, $R9

Registers. These variables can be used just like user variables, but are usually used in shared functions or macros. You don't have to declare these variables, so you won't get any name conflicts when using them in shared code. When using these variables in shared code, it's recommended that you use the stack to save and restore their original values. These variables can also be used for communication with plug-ins, because they can be read and written by the plug-in DLLs.


4.9.2.7 ExpandEnvStrings

user_var(output) string

Expands environment variables in string into the user variable $x. If an environment variable doesn't exist, it will not be replaced. For example, if you use "%var%" and var doesn't exists, the result will be "%var". If there is an error, the variable is set to empty, and the error flag is set.

ExpandEnvStrings $0 "WINDIR=%WINDIR%$\nTEMP=%TEMP%"

###常量
4.2.4 Constants Used in Strings

$$

Use to represent $.

$\r

Use to represent a carriage return (\r).

$\n

Use to represent a newline (\n).

$\t

Use to represent a tab (\t).

###函数
4.7.1.1 Function

[function_name]

Begins and opens a new function. Function names beginning with "." (e.g. ".Whatever") are generally reserved for callback functions. Function names beginning with "un." are functions that will be generated in the Uninstaller. Hence, normal install Sections and functions cannot call uninstall functions, and the Uninstall Section and uninstall functions cannot call normal functions.

Function func
  # some commands
FunctionEnd

Section
  Call func
SectionEnd

4.7.2 Callback Functions

You can create callback functions which have special names, that will be called by the installer at certain points in the install. Below is a list of currently available callbacks:
4.7.2.1 Install Callbacks
4.7.2.1.1 .onGUIInit

This callback will be called just before the first page is loaded and the installer dialog is shown, allowing you to tweak the user interface.

Example:

 !include "WinMessages.nsh"

 Function .onGUIInit
   # 1028 is the id of the branding text control
   GetDlgItem $R0 $HWNDPARENT 1028
   CreateFont $R1 "Tahoma" 10 700
   SendMessage $R0 ${WM_SETFONT} $R1 0
   # set background color to white and text color to red
   SetCtlColors $R0 FFFFFF FF0000
 FunctionEnd

4.7.2.1.2 .onInit

This callback will be called when the installer is nearly finished initializing. If the '.onInit' function calls Abort, the installer will quit instantly.

Here are two examples of how this might be used:

 Function .onInit
   MessageBox MB_YESNO "This will install. Continue?" IDYES NoAbort
     Abort ; causes installer to quit.
   NoAbort:
 FunctionEnd

###执行
4.9.1.2 Exec

command

Execute the specified program and continue immediately. Note that the file specified must exist on the target system, not the compiling system. $OUTDIR is used for the working directory. The error flag is set if the process could not be launched. Note, if the command could have spaces, you should put it in quotes to delimit it from parameters. e.g.: Exec '"$INSTDIR\command.exe" parameters'. If you don't put it in quotes it will not work on Windows 9x with or without parameters.

Exec '"$INSTDIR\someprogram.exe"'
Exec '"$INSTDIR\someprogram.exe" some parameters'

4.9.1.3 ExecShell

action command [parameters] [SW_SHOWDEFAULT | SW_SHOWNORMAL | SW_SHOWMAXIMIZED | SW_SHOWMINIMIZED | SW_HIDE]

Execute the specified program using ShellExecute. Note that action is usually "open", "print", etc, but can be an empty string to use the default action. Parameters and the show type are optional. $OUTDIR is used for the working directory. The error flag is set if the process could not be launched.

ExecShell "open" "http://nsis.sf.net/"
ExecShell "open" "$INSTDIR\readme.txt"
ExecShell "print" "$INSTDIR\readme.txt"

4.9.1.4 ExecWait

command [user_var(exit code)]

Execute the specified program and wait for the executed process to quit. See Exec for more information. If no output variable is specified ExecWait sets the error flag if the program executed returns a nonzero error code, or if there is an error. If an output variable is specified, ExecWait sets the variable with the exit code (and only sets the error flag if an error occurs; if an error occurs the contents of the user variable are undefined). Note, if the command could have spaces, you should put it in quotes to delimit it from parameters. e.g.: ExecWait '"$INSTDIR\command.exe" parameters'. If you don't put it in quotes it will not work on Windows 9x with or without parameters.

ExecWait '"$INSTDIR\someprogram.exe"'
ExecWait '"$INSTDIR\someprogram.exe"' $0
DetailPrint "some program returned $0"

###文件操作

4.9.5.2 FileOpen

user_var(handle output) filename openmode

Opens a file named "filename", and sets the handle output variable with the handle. The openmode should be one of "r" (read) "w" (write, all contents of file are destroyed) or "a" (append, meaning opened for both read and write, contents preserved). In all open modes, the file pointer is placed at the beginning of the file. If the file cannot be opened, the handle output is set to empty, and the error flag is set.

If no absolute path is specified the current folder will be used. The current folder is the folder set using the last SetOutPath instruction. If you have not used SetOutPath the current folder is $EXEDIR.

FileOpen $0 $INSTDIR\file.dat r
FileClose $0

4.9.5.3 FileRead

handle user_var(output) [maxlen]

Reads a string (ANSI characters) from a file opened with FileOpen. The string is read until either a newline (or carriage return newline pair) occurs, or until a null byte is read, or until maxlen is met (if specified). By default, strings are limited to 1024 characters (a special build with larger NSIS_MAX_STRLEN can be compiled or downloaded). If the end of file is read and no more data is available, the output string will be empty, and the error flag will be set.

(If you are building a Unicode installer, the function reads an ANSI string and makes the adequate conversion)

ClearErrors
FileOpen $0 $INSTDIR\file.dat r
IfErrors done
FileRead $0 $1
DetailPrint $1
FileClose $0
done:

4.9.5.8 FileWrite

handle string

Writes an ANSI string to a file opened with FileOpen. If an error occurs writing, the error flag will be set.

(If you are building a Unicode installer, the function makes the adequate conversion and writes an ANSI string)

ClearErrors
FileOpen $0 $INSTDIR\file.dat w
IfErrors done
FileWrite $0 "some text"
FileClose $0
done:

4.9.5.9 FileWriteUTF16LE

handle string

This function is only available when building a Unicode installer.

Writes a Unicode (UTF-16LE) string to a file opened with FileOpen. If an error occurs writing, the error flag will be set.

ClearErrors
FileOpen $0 $INSTDIR\file.dat w
IfErrors done
FileWriteUTF16LE $0 "some text"
FileClose $0
done:

4.9.5.10 FileWriteByte

handle string

Writes the integer interpretation of 'string' to a file opened with FileOpen. Of course you can enter the integer value directly. The following code writes a "Carriage Return / Line Feed" - Enter to the file.

FileWriteByte file_handle "13"
FileWriteByte file_handle "10"

If an error occurs while writing, the error flag will be set. Note that the low byte of the integer is used, i.e. writing 256 is the same as writing 0, etc.

4.9.1.6 Rename

[/REBOOTOK] source_file dest_file

Rename source_file to dest_file. You can use it to move a file from anywhere on the system to anywhere else and you can move a directory to somewhere else on the same drive. The destination file must not exist or the move will fail (unless you are using /REBOOTOK). If /REBOOTOK is specified, and the file cannot be moved (if, for example, the destination exists), then the file is moved when the system reboots. If the file will be moved on a reboot, the reboot flag will be set. The error flag is set if the file cannot be renamed (and /REBOOTOK is not used) or if the source file does not exist.

If no absolute path is specified the current folder will be used. The current folder is the folder set using the last SetOutPath instruction. If you have not used SetOutPath the current folder is $EXEDIR.

Rename $INSTDIR\file.ext $INSTDIR\file.dat


4.9.3.2 CopyFiles

[/SILENT] [/FILESONLY] filespec_on_destsys destination_path [size_of_files_in_kb]

Copies files from the source to the destination on the installing system. Useful with $EXEDIR if you want to copy from installation media, or to copy from one place to another on the system. You might see a Windows status window of the copy operation if the operation takes a lot of time (to disable this, use /SILENT). The last parameter can be used to specify the size of the files that will be copied (in kilobytes), so that the installer can approximate the disk space requirements. On error, or if the user cancels the copy (only possible when /SILENT was omitted), the error flag is set. If /FILESONLY is specified, only files are copied.

Fully-qualified path names should always be used with this instruction. Using relative paths will have unpredictable results.

CreateDirectory $INSTDIR\backup
CopyFiles $INSTDIR\*.dat $INSTDIR\backup

4.9.3.3 CreateDirectory

path_to_create

Creates (recursively if necessary) the specified directory. The error flag is set if the directory couldn't be created.

You should always specify an absolute path.

CreateDirectory $INSTDIR\some\directory

4.9.3.4 CreateShortCut

link.lnk target.file [parameters [icon.file [icon_index_number [start_options [keyboard_shortcut [description]]]]]]

Creates a shortcut 'link.lnk' that links to 'target.file', with optional parameters 'parameters'. The icon used for the shortcut is 'icon.file,icon_index_number'; for default icon settings use empty strings for both icon.file and icon_index_number. start_options should be one of: SW_SHOWNORMAL, SW_SHOWMAXIMIZED, SW_SHOWMINIMIZED, or an empty string. keyboard_shortcut should be in the form of 'flag|c' where flag can be a combination (using |) of: ALT, CONTROL, EXT, or SHIFT. c is the character to use (a-z, A-Z, 0-9, F1-F24, etc). Note that no spaces are allowed in this string. A good example is "ALT|CONTROL|F8". $OUTDIR is used for the working directory. You can change it by using SetOutPath before creating the Shortcut. description should be the description of the shortcut, or comment as it is called under XP. The error flag is set if the shortcut cannot be created (i.e. either of the paths (link or target) does not exist, or some other error).

CreateDirectory "$SMPROGRAMS\My Company"
CreateShortCut "$SMPROGRAMS\My Company\My Program.lnk" "$INSTDIR\My Program.exe" \
  "some command line parameters" "$INSTDIR\My Program.exe" 2 SW_SHOWNORMAL \
  ALT|CONTROL|SHIFT|F5 "a description"


###DLL操作
4.9.3.13 RegDLL

dllfile [entrypoint_name]

Loads the specified DLL and calls DllRegisterServer (or entrypoint_name if specified). The error flag is set if an error occurs (i.e. it can't load the DLL, initialize OLE, find the entry point, or the function returned anything other than ERROR_SUCCESS (=0)).

Use SetOutPath to set the current directory for DLLs that depend on other DLLs that are now in the path or in the Windows directory. For example, if foo.dll depends on bar.dll which is located in $INSTDIR use:

 SetOutPath $INSTDIR
 RegDLL $INSTDIR\foo.dll

4.9.3.14 UnRegDLL

dllfile

Loads the specified DLL and calls DllUnregisterServer. The error flag is set if an error occurs (i.e. it can't load the DLL, initialize OLE, find the entry point, or the function returned anything other than ERROR_SUCCESS (=0)).


###Flow控制
4.9.4.8 IfAbort

label_to_goto_if_abort [label_to_goto_if_no_abort]

If abort is called it will "return" true. This can happen if the user chose abort on a file that failed to create (or overwrite) or if the user aborted by hand. This function can only be called from the leave function of the instfiles page.

Page instfiles "" "" instfilesLeave

Function instfilesLeave
  IfAbort 0 +2
    MessageBox MB_OK "user aborted"
FunctionEnd

4.9.4.9 IfErrors

jumpto_iferror [jumpto_ifnoerror]

Checks and clears the error flag, and if it is set, it will goto jumpto_iferror, otherwise it will goto jumpto_ifnoerror. The error flag is set by other instructions when a recoverable error (such as trying to delete a file that is in use) occurs.

ClearErrors
File file.dat
IfErrors 0 +2
  Call ErrorHandler

4.9.4.10 IfFileExists

file_to_check_for jump_if_present [jump_otherwise]

Checks for existence of file(s) file_to_check_for (which can be a wildcard, or a directory), and Gotos jump_if_present if the file exists, otherwise Gotos jump_otherwise. If you want to check to see if a file is a directory, use IfFileExists DIRECTORY\*.*

IfFileExists $WINDIR\notepad.exe 0 +2
  MessageBox MB_OK "notepad is installed"

4.9.4.11 IfRebootFlag

jump_if_set [jump_if_not_set]

Checks the reboot flag, and jumps to jump_if_set if the reboot flag is set, otherwise jumps to jump_if_not_set. The reboot flag can be set by Delete and Rename, or manually with SetRebootFlag.

IfRebootFlag 0 noreboot
  MessageBox MB_YESNO "A reboot is required to finish the installation. Do you wish to reboot now?" IDNO noreboot
    Reboot
noreboot:

4.9.4.12 IfSilent

jump_if_silent [jump_if_not]

Checks the silent flag, and jumps to jump_if_silent if the installer is silent, otherwise jumps to jump_if_not. The silent flag can be set by SilentInstall, SilentUninstall, SetSilent and by the user passing /S on the command line.

IfSilent +2
  ExecWait '"$INSTDIR\nonsilentprogram.exe"'

4.9.4.13 IntCmp

val1 val2 jump_if_equal [jump_if_val1_less] [jump_if_val1_more]

Compares two integers val1 and val2. If val1 and val2 are equal, Gotos jump_if_equal, otherwise if val1 < val2, Gotos jump_if_val1_less, otherwise if val1 > val2, Gotos jump_if_val1_more.

IntCmp $0 5 is5 lessthan5 morethan5
is5:
  DetailPrint "$$0 == 5"
  Goto done
lessthan5:
  DetailPrint "$$0 < 5"
  Goto done
morethan5:
  DetailPrint "$$0 > 5"
  Goto done
done:

4.9.4.14 IntCmpU

val1 val2 jump_if_equal [jump_if_val1_less] [jump_if_val1_more]

Compares two unsigned integers val1 and val2. If val1 and val2 are equal, Gotos jump_if_equal, otherwise if val1 < val2, Gotos jump_if_val1_less, otherwise if val1 > val2, Gotos jump_if_val1_more. Performs the comparison as unsigned integers.
4.9.4.15 MessageBox

mb_option_list messagebox_text [/SD return] [return_check jumpto] [return_check_2 jumpto_2]

Displays a MessageBox containing the text "messagebox_text". mb_option_list must be one or more of the following, delimited by |s (e.g. MB_YESNO|MB_ICONSTOP).

    MB_OK - Display with an OK button
    MB_OKCANCEL - Display with an OK and a cancel button
    MB_ABORTRETRYIGNORE - Display with abort, retry, ignore buttons
    MB_RETRYCANCEL - Display with retry and cancel buttons
    MB_YESNO - Display with yes and no buttons
    MB_YESNOCANCEL - Display with yes, no, cancel buttons
    MB_ICONEXCLAMATION - Display with exclamation icon
    MB_ICONINFORMATION - Display with information icon
    MB_ICONQUESTION - Display with question mark icon
    MB_ICONSTOP - Display with stop icon
    MB_USERICON - Display with installer's icon
    MB_TOPMOST - Make messagebox topmost
    MB_SETFOREGROUND - Set foreground
    MB_RIGHT - Right align text
    MB_RTLREADING - RTL reading order
    MB_DEFBUTTON1 - Button 1 is default
    MB_DEFBUTTON2 - Button 2 is default
    MB_DEFBUTTON3 - Button 3 is default
    MB_DEFBUTTON4 - Button 4 is default

Return_check can be 0 (or empty, or left off), or one of the following:

    IDABORT - Abort button
    IDCANCEL - Cancel button
    IDIGNORE - Ignore button
    IDNO - No button
    IDOK - OK button
    IDRETRY - Retry button
    IDYES - Yes button

If the return value of the MessageBox is return_check, the installer will Goto jumpto.

Use the /SD parameter with one of the return_check values above to specify the option that will be used when the installer is silent. See section 4.12 for more information.

MessageBox MB_OK "simple message box"
MessageBox MB_YESNO "is it true?" IDYES true IDNO false
true:
  DetailPrint "it's true!"
  Goto next
false:
  DetailPrint "it's false"
next:
MessageBox MB_YESNO "is it true? (defaults to yes on silent installations)" /SD IDYES IDNO false2
  DetailPrint "it's true (or silent)!"
  Goto next2
false2:
  DetailPrint "it's false"
next2:

4.9.4.16 Return

Returns from a function or section.

Function func
  StrCmp $0 "return now" 0 +2
    Return
  # do stuff
FunctionEnd

Section
  Call func
  ;"Return" will return here
SectionEnd

4.9.4.17 Quit

Causes the installer to exit as soon as possible. After Quit is called, the installer will exit (no callback functions will get a chance to run).
4.9.4.18 SetErrors

Sets the error flag.

SetErrors
IfErrors 0 +2
  MessageBox MB_OK "this message box will always show"

4.9.4.19 StrCmp

str1 str2 jump_if_equal [jump_if_not_equal]

Compares (case insensitively) str1 to str2. If str1 and str2 are equal, Gotos jump_if_equal, otherwise Gotos jump_if_not_equal.

StrCmp $0 "a string" 0 +3
  DetailPrint '$$0 == "a string"'
  Goto +2
  DetailPrint '$$0 != "a string"'

###整型运算
4.9.10.2 IntOp

user_var(output) value1 OP [value2]

Combines value1 and (depending on OP) value2 into the specified user variable (user_var). OP is defined as one of the following:

    + ADDs value1 and value2
    - SUBTRACTs value2 from value1
    * MULTIPLIEs value1 and value2
    / DIVIDEs value1 by value2
    % MODULUSs value1 by value2
    | BINARY ORs value1 and value2
    & BINARY ANDs value1 and value2
    ^ BINARY XORs value1 and value2
    >> RIGHT SHIFTs value1 by value2
    << LEFT SHIFTs value1 by value2
    ~ BITWISE NEGATEs value1 (i.e. 7 becomes 4294967288)
    ! LOGICALLY NEGATEs value1 (i.e. 7 becomes 0)
    || LOGICALLY ORs value1 and value2
    && LOGICALLY ANDs value1 and value2

IntOp $0 1 + 1
IntOp $0 $0 + 1
IntOp $0 $0 << 2
IntOp $0 $0 ~
IntOp $0 $0 & 0xF

###日志管理
4.9.12 Install Logging Instructions
4.9.12.1 LogSet

on|off

Sets whether install logging to $INSTDIR\install.log will happen. $INSTDIR must have a value before you call this function or it will not work. Note that the NSIS_CONFIG_LOG build setting must be set (scons NSIS_CONFIG_LOG=yes) on compile time (it is not by default) to support this. See Building NSIS for more information about recompiling NSIS.
4.9.12.2 LogText

text

If installer logging is enabled, inserts text "text" into the log file.

IfFileExists $WINDIR\notepad.exe 0 +2
  LogText "$$WINDIR\notepad.exe exists"


###Sleep
4.9.7.8 Sleep

sleeptime_in_ms

Pauses execution in the installer for sleeptime_in_ms milliseconds. sleeptime_in_ms can be a variable, e.g. "$0" or a number, i.e. "666".

DetailPrint "sleeping..."
Sleep 3000
DetailPrint "back to work"



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

<div align="center">

## HackerScan Routine


</div>

### Description

This code will scan for popular hacking tools: FileMon, RegMon and SoftICE (both Win 9x and NT versions). This code was inspired by the SoftICE detection routine by Joox (http://www.planetsourcecode.com/vb/scripts/ShowCode.asp?lngWId=1&txtCodeId=7600). If any of these programs are in memory an access violation is generated. You should call this routine before you read or write any sensitive information (ie license files) to files or the regsitry.

I'm certain that there are workarounds for this code, but its intent is to make things harder for the hacker.

I would love to see other methods added to this to detect other debuggers, tools, etc. so please leave whatever comments come to mind. Go ahead and vote too!

Enjoy!
 
### More Info
 
Simply call this routine before doing any sensitive reading or writing to files or the registry...ie license information.

Acces violations, but it is by design. See the comment in the code for details.


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[N/A](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/empty.md)
**Level**          |Advanced
**User Rating**    |5.0 (10 globes from 2 users)
**Compatibility**  |VB 5\.0, VB 6\.0
**Category**       |[Miscellaneous](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/miscellaneous__1-1.md)
**World**          |[Visual Basic](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/visual-basic.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/hackerscan-routine__1-10000/archive/master.zip)

### API Declarations

```
Public Declare Function FindWindow Lib "user32" Alias "FindWindowA" (ByVal lpClassName As String, ByVal lpWindowName As String) As Long
Public Declare Sub RaiseException Lib "kernel32" Alias "RaiseException" (ByVal dwExceptionCode As Long, ByVal dwExceptionFlags As Long, ByVal nNumberOfArguments As Long, lpArguments As Long)
Public Declare Function CreateFile Lib "kernel32" Alias "CreateFileA" (ByVal lpFileName As String, ByVal dwDesiredAccess As Long, ByVal dwShareMode As Long, lpSecurityAttributes As ANY, ByVal dwCreationDisposition As Long, ByVal dwFlagsAndAttributes As Long, ByVal hTemplateFile As Long) As Long
Public Declare Function CloseHandle Lib "kernel32" Alias "CloseHandle" (ByVal hObject As Long) As Long
Public Const GENERIC_WRITE = &H40000000
Public Const GENERIC_READ = &H80000000
Public Const FILE_SHARE_READ = &H1
Public Const FILE_SHARE_WRITE = &H2
Public Const OPEN_EXISTING = 3
Public Const FILE_ATTRIBUTE_NORMAL = &H80
Public Const EXCEPTION_ACCESS_VIOLATION = &HC0000005
```


### Source Code

```
Public Sub HackerScan()
Dim hFile As Long, retVal As Long
Dim sRegMonClass As String, sFileMonClass As String
'\\We break up the class names to avoid detection in a hex editor
sRegMonClass = "R" & "e" & "g" & "m" & "o" & "n" & "C" & "l" & "a" & "s" & "s"
sFileMonClass = "F" & "i" & "l" & "e" & "M" & "o" & "n" & "C" & "l" & "a" & "s" & "s"
'\\See if RegMon or FileMon are running
Select Case True
 Case FindWindow(sRegMonClass, vbNullString) <> 0
 'Regmon is running...throw an access violation
 RaiseException EXCEPTION_ACCESS_VIOLATION, 0, 0, 0
 Case FindWindow(sFileMonClass, vbNullString) <> 0
 'FileMon is running...throw an access violation
 RaiseException EXCEPTION_ACCESS_VIOLATION, 0, 0, 0
End Select
'\\So far so good...check for SoftICE in memory
hFile = CreateFile("\\.\SICE", GENERIC_WRITE Or GENERIC_READ, FILE_SHARE_READ Or FILE_SHARE_WRITE, 0, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL, 0)
If hFile <> -1 Then
 ' SoftICE is detected.
 retVal = CloseHandle(hFile) ' Close the file handle
 RaiseException EXCEPTION_ACCESS_VIOLATION, 0, 0, 0
Else
 ' SoftICE is not found for windows 9x, check for NT.
 hFile = CreateFile("\\.\NTICE", GENERIC_WRITE Or GENERIC_READ, FILE_SHARE_READ Or FILE_SHARE_WRITE, 0, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL, 0)
 If hFile <> -1 Then
 ' SoftICE is detected.
 retVal = CloseHandle(hFile) ' Close the file handle
 RaiseException EXCEPTION_ACCESS_VIOLATION, 0, 0, 0
 End If
End If
End Sub
```


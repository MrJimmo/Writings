
# Who is this Druid Coordinator, and what are they trying to DELETE!?

One day, I noticed the following sessions showing up in [Fiddler](https://www.telerik.com/fiddler)....

Screen snip of Fiddler:

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image1.png" style="width:6.5in;height:1.14514in"
alt="Table Description automatically generated with medium confidence" />

...even though fiddler capture was not enabled:

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image2.png" style="width:2.89583in;height:0.625in"
alt="Text Description automatically generated" />

When capturing, Fiddler tells you so...

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image3.png" style="width:2.47718in;height:0.74147in"
alt="Graphical user interface, text, application Description automatically generated" />

I know from years of using Fiddler, things like this show up when an
HTTP request is made against this machines IP.

Like if I did one of the following from another machine:

```
C:\> curl <http://18.137.85.168:8888>
```
Or via Powershell:
```
PS C:\> iwr -URL 18.137.85.168:8888
```
But wait a sec, that Host, it's... 127.0.0.1 ...the call is coming from
INSIDE THE HOUSE!!!!!!! :o)

On most systems, when Fiddler is running, some of the extra columns are
just outside the view, scrolling or widening the session area, we can
find the "Process" column....

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image4.png" style="width:6.5in;height:0.86528in"
alt="Graphical user interface, text, application, email Description automatically generated" />

...and in this case we can see that these DELETEs are coming from powershell.

Rough table view copied from Fiddler:
| **\#** | **Result** | **Protocol** | **Host**       | **URL**                                                                                    | **Body** | **Caching**               | **Content-Type**         | **Process**                                | **Comments** | **Custom** | **RequestMethod** | **SentTime** | **SentDate** | **ClientIP**           | **Method** | **ReqBody** |
|--------|------------|--------------|----------------|--------------------------------------------------------------------------------------------|----------|---------------------------|--------------------------|--------------------------------------------|--------------|------------|-------------------|--------------|--------------|------------------------|------------|-------------|
| 1      | 200        | HTTP         | 127.0.0.1:8888 | /druid/coordinator/v1/lookups/config/\${jndi:dns://MDEDiscovery0837810b8cApacheDruid-8888} | 892      | no-cache, must-revalidate | text/html; charset=UTF-8 | powershell:33236                           |              |            | DELETE            | 13:33:23     | 2022-04-05   | ::ffff:127.0.0.1:21641 | DELETE     | 0           |
| 2      | 200        | HTTP         | 127.0.0.1:8888 | /druid/coordinator/v1/lookups/config/\${jndi:dns://MDEDiscovery0837810b8cApacheDruid-8888} | 892      | no-cache, must-revalidate | text/html; charset=UTF-8 | powershell:34776                           |              |            | DELETE            | 13:33:50     | 2022-04-05   | ::ffff:127.0.0.1:21942 | DELETE     | 0           |
| 3      | 200        | HTTP         | 127.0.0.1:8888 | /druid/coordinator/v1/lookups/config/\${jndi:dns://MDEDiscovery0837810b8cApacheDruid-8888} | 892      | no-cache, must-revalidate | text/html; charset=UTF-8 | <span class="mark">powershell:22040</span> |              |            | DELETE            | 15:05:42     | 2022-04-05   | ::ffff:127.0.0.1:21231 | DELETE     | 0           |

Taking a look at the HTTP Request and Response details of that last
<span class="mark">session</span>...

<table>
<colgroup>
<col style="width: 100%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>HTTP Request:</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>DELETE <a
href="http://127.0.0.1:8888/druid/coordinator/v1/lookups/config/$%7bjndi:dns:/MDEDiscovery0837810b8cApacheDruid-8888%7d">http://127.0.0.1:8888/druid/coordinator/v1/lookups/config/${jndi:dns://MDEDiscovery0837810b8cApacheDruid-8888}</a>
HTTP/1.1</p>
<p>Host: 127.0.0.1:8888</p>
<p>User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110
Safari/537.36</p>
<p>Accept: */*</p>
<p>Pragma: no-cache</p></td>
</tr>
<tr class="even">
<td><strong>HTTP Response:</strong></td>
</tr>
<tr class="odd">
<td><p>HTTP/1.1 200 Fiddler Generated</p>
<p>Date: Tue, 05 Apr 2022 22:05:42 GMT</p>
<p>Content-Type: text/html; charset=UTF-8</p>
<p>Connection: close</p>
<p>Cache-Control: no-cache, must-revalidate</p>
<p>Timestamp: 15:05:42.019</p>
<p>&lt;!doctype html&gt;</p>
<p>&lt;html&gt;&lt;head&gt;&lt;meta http-equiv="Content-Type"
content="text/html; charset=UTF-8"&gt;&lt;title&gt;Fiddler Echo
Service&lt;/title&gt;&lt;/head&gt;&lt;body style="font-family:
arial,sans-serif;"&gt;&lt;h1&gt;Fiddler Echo Service&lt;/h1&gt;&lt;br
/&gt;&lt;pre&gt;DELETE
/druid/coordinator/v1/lookups/config/${jndi:dns://MDEDiscovery0837810b8cApacheDruid-8888}
HTTP/1.1</p>
<p>Host: 127.0.0.1:8888</p>
<p>User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110
Safari/537.36</p>
<p>Accept: */*</p>
<p>Pragma: no-cache</p>
<p>&lt;/pre&gt;This page returned a &lt;b&gt;HTTP/200&lt;/b&gt; response
&lt;br /&gt;Originating Process Information:
&lt;code&gt;powershell:22040&lt;/code&gt;&lt;br /&gt;&lt;hr
/&gt;&lt;ul&gt;&lt;li&gt;To configure Fiddler as a reverse proxy instead
of seeing this page, see &lt;a href='<a
href="http://fiddler2.com/r/?REVERSEPROXY&#39;%3eReverse">http://fiddler2.com/r/?REVERSEPROXY'&gt;Reverse</a>
Proxy Setup&lt;/a&gt;&lt;li&gt;You can download the &lt;a
href="FiddlerRoot.cer"&gt;FiddlerRoot
certificate&lt;/a&gt;&lt;/ul&gt;&lt;/body&gt;&lt;/html&gt;</p></td>
</tr>
</tbody>
</table>

That Response body is the standard reply page Fiddler sends, when you
make a request against \<IP\>:8888, of a machine running fiddler,
configured to monitor that port....

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image5.png" style="width:6.5in;height:2.8in"
alt="Graphical user interface, text, application, email Description automatically generated" />

**NOTE**: This page is also a way you can get access to the FiddlerRoot Cert
you need, if you want to capture/view HTTPS from a different machine.
More on that some other time (some details
[here](https://docs.telerik.com/fiddler/configure-fiddler/tasks/monitorremotemachine))

So I want to know what Powershell is doing (running) which means I need
to catch powershell in the act.

Looking at the SentDate and SentTime columns (see
[Appendix](#appendix-adding-sentdate-and-senttime-columns-to-fiddler)
for adding these columns to your Fiddler...they are not there by
default):

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image6.png" style="width:6.5in;height:1.26528in"
alt="Text Description automatically generated" />

...it doesn’t appear to be on some exact schedule (other sessions had
multiple in an hour).

[ProcMon](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon)
to the rescue!

I setup ProcMon to watch for Powershell.exe events and left it
capturing.

For me, it was a little noisy because I also had VSCode running, which
usually has several PS child processes loitering around.

But eventually it captured the one that matched up with what I saw in
the sessions list

This is a hacked up screen-snip (for readability) of the Process Tree
tool in ProcMon, showing that powershell.exe (22040) process, is a
child of **SenseIR.exe** (21156)

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image7.png" style="width:6.5in;height:2.83472in"
alt="Graphical user interface, text, application Description automatically generated" />

**SenseIR.exe** is part of Windows Defender components.

Looking a little deeper into that **SenseIR.exe** (21156) process:

<table>
<colgroup>
<col style="width: 13%" />
<col style="width: 86%" />
</colgroup>
<thead>
<tr class="header">
<th>Description</th>
<th>Windows Defender Advanced Threat Protection Sense IR module</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Company</td>
<td>Microsoft Corporation</td>
</tr>
<tr class="even">
<td>Path</td>
<td>C:\Program Files\Windows Defender Advanced Threat
Protection\SenseIR.exe</td>
</tr>
<tr class="odd">
<td>Command</td>
<td>"C:\Program Files\Windows Defender Advanced Threat
Protection\SenseIR.exe" "OfflineSenseIR" "4936"
"eyJDb21tYW5kSWQiOiIiLCJEb3dubG9hZEZpbGVBY3Rpb25Db25maWciOm51bGwsIkRvd25sb2FkVHJ1c3RlZENlcnRpZmljYXRlc0NoYWlucyI6bnVsbCwiRW5hYmxlU2xlZXBTdXNwZW5zaW9uIjowLCJNYXhXYWl0Rm9yTmV3QWN0aW9uc0luTXMiOjcyMDAwMCwiT3JnSWQiOiIiLCJSdW5Qc1NjcmlwdEFjdGlvbkNvbmZpZyI6eyJlbmFibGUiOnRydWV9LCJhY2NlcHRTaW11bGF0b3JTaWduaW5nIjowLCJvZmZsaW5lSXJQaXBlSGFuZGxlIjo1MDAwfQ=="</td>
</tr>
<tr class="even">
<td>That Base64 part decoded</td>
<td><p>{</p>
<p>"CommandId": "",</p>
<p>"DownloadFileActionConfig": null,</p>
<p>"DownloadTrustedCertificatesChains": null,</p>
<p>"EnableSleepSuspension": 0,</p>
<p>"MaxWaitForNewActionsInMs": 720000,</p>
<p>"OrgId": "",</p>
<p>"RunPsScriptActionConfig": {</p>
<p>"enable": true</p>
<p>},</p>
<p>"acceptSimulatorSigning": 0,</p>
<p>"offlineIrPipeHandle": 5000</p>
<p>}</p></td>
</tr>
</tbody>
</table>

And the Powershell.exe (22040) process:

| Description | Windows PowerShell                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Company     | MIcrosoft Corporation                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Path        | C:\WINDOWS\system32\WindowsPowerShell\v1.0\powershell.exe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Command     | C:\WINDOWS\system32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy AllSigned -NoProfile -NonInteractive -Command "& {\$OutputEncoding = \[Console\]::OutputEncoding =\[System.Text.Encoding\]::UTF8;\$scriptFileStream = \[System.IO.File\]::Open('C:\ProgramData\Microsoft\Windows Defender Advanced Threat Protection\DataCollection\8099.7178736.0.7008675.7171118-8e8b1f960e814c284066448d42be8fc539cee33c\36dc1ba5-2d7a-4cc0-8b07-8b26d1899492.ps1', \[System.IO.FileMode\]::Open, \[System.IO.FileAccess\]::Read, \[System.IO.FileShare\]::Read);\$calculatedHash = Get-FileHash 'C:\ProgramData\Microsoft\Windows Defender Advanced Threat Protection\DataCollection\8099.7178736.0.7008675.7171118-8e8b1f960e814c284066448d42be8fc539cee33c\36dc1ba5-2d7a-4cc0-8b07-8b26d1899492.ps1' -Algorithm SHA256;if (!(\$calculatedHash.Hash -eq '126237759e5c09df1efbb590fcdd776388a3a9e4a2857185d85a4dfe875e36e4')) { exit 323;}; . 'C:\ProgramData\Microsoft\Windows Defender Advanced Threat Protection\DataCollection\8099.7178736.0.7008675.7171118-8e8b1f960e814c284066448d42be8fc539cee33c\36dc1ba5-2d7a-4cc0-8b07-8b26d1899492.ps1' }" |

That's a lot of goo.  

What do we know so far?

We know SenseIR launched Powershell with a really long command line, to run a chunk of script (the block of text after the -Command param):
```
C:\WINDOWS\system32\WindowsPowerShell\v1.0\powershell.exe
-ExecutionPolicy AllSigned -NoProfile -NonInteractive -Command
"
& {\$OutputEncoding = \[Console\]::OutputEncoding
=\[System.Text.Encoding\]::UTF8;

\$scriptFileStream =
\[System.IO.File\]::Open('C:\ProgramData\Microsoft\Windows Defender
Advanced Threat
Protection\DataCollection\8099.7178736.0.7008675.7171118-8e8b1f960e814c284066448d42be8fc539cee33c\36dc1ba5-2d7a-4cc0-8b07-8b26d1899492.ps1',
\[System.IO.FileMode\]::Open, \[System.IO.FileAccess\]::Read,
\[System.IO.FileShare\]::Read);

\$calculatedHash = Get-FileHash
C:\ProgramData\Microsoft\Windows Defender Advanced
Threat
Protection\DataCollection\8099.7178736.0.7008675.7171118-8e8b1f960e814c284066448d42be8fc539cee33c\36dc1ba5-2d7a-4cc0-8b07-8b26d1899492.ps1'
-Algorithm SHA256;

if (!(\$calculatedHash.Hash -eq
'126237759e5c09df1efbb590fcdd776388a3a9e4a2857185d85a4dfe875e36e4')) {
exit 323;
};

. 'C:\ProgramData\Microsoft\Windows Defender Advanced Threat
Protection\DataCollection\8099.7178736.0.7008675.7171118-8e8b1f960e814c284066448d42be8fc539cee33c\36dc1ba5-2d7a-4cc0-8b07-8b26d1899492.ps1'
}
"
```
Key part of that line appears to be the script that get runs:
```
'C:\ProgramData\Microsoft\Windows Defender Advanced Threat
Protection\DataCollection\8099.7178736.0.7008675.7171118-8e8b1f960e814c284066448d42be8fc539cee33c\36dc1ba5-2d7a-4cc0-8b07-8b26d1899492.ps1'
```
...wonder what that does?

Trying to navigate to that `C:\ProgramData\Microsoft\Windows Defender
Advanced Threat Protection\DataCollection` folder or view that file, I
hit a permissions problem (even though I did from elevated cmd prompt).

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image8.png" style="width:3.66667in;height:1.92708in"
alt="Graphical user interface, text, application Description automatically generated" />

....clicking \[Continue\]:

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image9.png" style="width:3.66667in;height:1.92708in"
alt="Graphical user interface, text, application Description automatically generated" />

Hrmm... let's try a little hackeroo here:

I run **psexec.exe /s cmd** which starts a cmd shell as "nt
authority\system"

Doing a quick dump of the contents with:
```
C:\> more 'C:\ProgramData\Microsoft\Windows Defender Advanced Threat
Protection\DataCollection\8099.7178736.0.7008675.7171118-8e8b1f960e814c284066448d42be8fc539cee33c\36dc1ba5-2d7a-4cc0-8b07-8b26d1899492.ps1'"
```
That dumped the contents of the file to the console (not pasted here...I no longer have access to that script)

Now we were talking!

**NOTE**: If you try to launch something like notepad to view that file,
it will start notepad as “nt authority\system”, which usually means no visible
UI for the current workstation.

In this case, I just took the console output and copy/pasted into a file.

Use the /I param to **psexec.exe /s cmd** should allow for interaction for UI.

With the script contents now in a file, I found part of that "druid/coordinator..." URI
declared as entry in an array of **ExploitScanTarget**, which is a
private member of the **CVE202144228** class, which is defined in this
**long CVE202144228ScannerSource** string.
```
\$CVE202144228ScannerSource = @"
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net;
using System.Net.Security;
using System.Net.Sockets;
using System.Security.Authentication;
using System.Security.Cryptography.X509Certificates;
using System.Text;
using System.Threading;

public class <span class="mark">CVE202144228</span>

....snip...

        new ExploitScanTarget() { Uris = new string\[\] {
"/druid/coordinator/v1/lookups/config/{0}" }, HttpCommand = "DELETE",
Ports = new ushort\[\] { 8888 }, ExploitIndicator = "ApacheDruid" },
```
Searching elsewhere in the script, I found that
**$CVE202144228ScannerSource** string is used in an [Add-Type](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/add-type?view=powershell-7.3) call....

```
Add-Type -TypeDefinition \$CVE202144228ScannerSource -Language CSharp
-IgnoreWarnings

Write-Host "Initiating local log4j vulnerability scan \$ScriptName
\$ScriptVersion"

\$StartTime = Get-Date
```
"**Log4J**"? that looked very familiar and a quick Web search shows it's a fairly recent and wide spread
vulnerability (Much more detail on CVE-2021-44228
[here](https://msrc-blog.microsoft.com/2021/12/11/microsofts-response-to-cve-2021-44228-apache-log4j2/)).

Right now, I can guess this is a Windows Defender thing that is meant to
scan for the recent Log4J vulnerability and I can stop
investigating....but what's the fun in that? There are more interesting
things to learn here!

That "Add-Type" basically creates (in the scope of this script
execution), the **CVE202144228** class.

And we find that class used in the **Scan-CVE-2021-44228** function,
where it calls the **ScanExploitTargets** method, with the specified
params....
```
function Scan-CVE-2021-44228
{
    param(
    \[Parameter(Mandatory=\$true)\]
    \[string\]\$RemoteIP,
    \[string\]\$RemoteMac,
    \[string\]\$LocalIP
    )
    try
    {
        \$result =
\[<span class="mark">CVE202144228</span>\]::ScanExploitTargets(\$LocalIP,
\$RemoteIP, \[CVE202144228+JndiPayloadType\]::Dns,
\$MachineIdLastCharacters)
    }
....snip.....
}
```
And elsewhere in the script, I found it called as:

`Scan-CVE-2021-44228 -LocalIP \$null -RemoteIP "127.0.0.1" -RemoteMac
\$null`

So this explains the session Fiddler.

That **Scan-CVE-2021-44228** call specifies 127.0.0.1, and the Log4J
ExploitScanTarget object, specifies the 8888 port, which gives us the
magic **127.0.0.1:8888** URI we see in the sessions and explains why Fiddler
captures it, which shows up as the resulting DELETE session going to
`/druid/coordinator/v1/lookups/config/{0}`.

For reference:
<https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-44228>

“Apache Log4j Remote Code Execution Vulnerability”

As part of the on-the-fly compilation of that class, Powershell spawns a
couple csc.exe/cvtres.exe processes:

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image10.png" style="width:2.32292in;height:1.07292in"
alt="Text Description automatically generated" />

Here's the cmd lines for those processes:

- Conhost.exe (21364): \\?\C:\WINDOWS\system32\conhost.exe 0xffffffff
  -ForceV1

- csc.exe (20072):
  "C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe" /noconfig
  /fullpaths
  @"C:\Windows\ServiceProfiles\LocalService\AppData\Local\Temp\nhw52cij.cmdline"

  - cvtres.exe (22908):
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\cvtres.exe /NOLOGO
    /READONLY /MACHINE:IX86
    "/OUT:C:\WINDOWS\SERVIC~1\LOCALS~1\AppData\Local\Temp\RES1354.tmp"
    "c:\Windows\ServiceProfiles\LocalService\AppData\Local\Temp\CSCE99ADD7D39BD46279D85B5254EEA5534.TMP"

- csc.exe (35396):
  "C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe" /noconfig
  /fullpaths
  @"C:\Windows\ServiceProfiles\LocalService\AppData\Local\Temp\3efj3jqa.cmdline"

  - cvtres.exe (18096):
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\cvtres.exe /NOLOGO
    /READONLY /MACHINE:IX86
    "/OUT:C:\WINDOWS\SERVIC~1\LOCALS~1\AppData\Local\Temp\RES1DD3.tmp"
    "c:\Windows\ServiceProfiles\LocalService\AppData\Local\Temp\CSC14AD5F42EB6247699EA698FD57CEE8D.TMP"

There are quite a few other PS scripts in
"C:\ProgramData\Microsoft\Windows Defender Advanced Threat
Protection\DataCollection" which might be interesting viewing.

But that’s it for this investigation.

# Appendix: Adding SentDate and SentTime columns to Fiddler

There's a lot of untapped power in Fiddler via
[FiddlerScript](https://docs.telerik.com/fiddler/extend-fiddler/addrules).

I've used fiddlerscript to modify requests and responses, for all sorts
of investigative and hacking purposes.

Adding columns is really straight forward, and this is just one of
them...

Go to the FiddlerScript tab, find the class Handlers section, and add
the following:
```
// Add SentTime  
public static BindUIColumn("SentTime", 60)  
function FillSentColumn(oS: Session){  
if ((oS.oRequest != null) && (oS.oRequest.headers != null))  
return oS.Timers.ClientBeginRequest.ToString("HH:mm:ss");  
else  
return String.Format("----");  
}

// Add SentDate column  
public static BindUIColumn("SentDate", 60)  
function FillSentDateColumn(oS: Session){  
if ((oS.oRequest != null) && (oS.oRequest.headers != null))  
return oS.Timers.ClientBeginRequest.ToString("yyyy-MM-dd");  
else  
return String.Format("----");  
}
```
Might look something like this:

<img src=".\/FiddlerInvestigationOfDRUIDMedia/image11.png" style="width:5.58333in;height:3.5in"
alt="Text Description automatically generated" />

(C) 2023 Jim Moore
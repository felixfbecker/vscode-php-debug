PHP Debug adapter for Visual Studio Code
========================================

![Demo GIF](images/demo.gif)

How to install
--------------

### Install extension:
Press `F1`, type `ext install php-debug`.

### Install XDebug:
[Download and install the XDebug extension](http://xdebug.org/download.php).
Then, in addition to `zend_extension=path/to/xdebug`, add these lines to your php.ini to enable remote debugging:

```ini
[XDebug]
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
```
Also, if you haven't already, point your webserver's webroot to your project.

Don't forget to restart your webserver after you made these changes.
Now, everytime you do a request to a PHP file, XDebug will automatically try to connect to port 9000 for debugging.

### Start debugging:
In your project, go to the debugger and hit the little gear icon. Choose PHP. A new launch configuration will be created for you.
Now, if you select this configuration and hit `F5`, VS Code will listen on port 9000 for incoming XDebug requests.
Now, when you make a request to `localhost` with your webbrowser, XDebug will connect to VS Code and you can debug your PHP.

What is supported?
------------------
 - Line breakpoints
 - Step over, step in, step out
 - Break on entry
 - Breaking on uncaught exceptions and errors / warnings / notices
 - Multiple, parallel requests (still a bit buggy but I think these are bugs in VS Code)
 - Stack traces, scope variables, superglobals, user defined constants
 - Arrays & objects (including classname, private and static properties)
 - Debug console
 - Watches

What is not supported?
----------------------
 - Conditional breakpoints (not yet implemented)
 - Breaking on _caught_ exceptions, this is not supported by XDebug and the setting is ignored
 - Attach requests, there is no such thing because the lifespan of PHP scripts is short

Remote Host Debugging
---------------------
If you want to debug a running application on a remote host, you have to set the `localSourceRoot` and `serverSourceRoot` settings in your launch.json.
Example:
```json
"serverSourceRoot": "/var/www/myproject",
"localSourceRoot": "./src"
```
`localSourceRoot` is resolved relative to the project root (the currently opened folder in VS Code).
Both paths are normalized, so you can use slashes or backslashes no matter of the OS you're running.
If no `localSourceRoot` is specified, the project root is assumed.

CLI Debugging
-------------
Triggers the debugger on a single file.
Windows (setup):
```batch
SET XDEBUG_CONFIG="idekey=VSCODE"
```
Linux (setup):
```bash
export XDEBUG_CONFIG="idekey=VSCODE"
```
Debug your file:
```cli
php -d xdebug.idekey="VSCODE" yourFileToDebug.php
```
Tip: Create a VSCode runnable [task](https://code.visualstudio.com/docs/editor/tasks) to execute currently open file.

Troubleshooting
---------------
When you are facing problems, please don't send me private emails, instead ask on
[Gitter](https://gitter.im/felixfbecker/vscode-php-debug) or if you think there is a bug in the adapter, [open an issue](https://github.com/felixfbecker/vscode-php-debug/issues).
If it fails with your ultra-awesome MVC app, please first try it on a dead-simple test.php (like the one in the [testproject](https://github.com/felixfbecker/vscode-php-debug/tree/master/testproject)). Please provide some info by setting `xdebug.remote_log = /path/to/logfile` in your php.ini (you will need to restart your webserver), `"log": true` in your launch.json and posting the two logs.

FAQ
---

#### How can I get the error message when breaking on an error/warning?
Set a watch for `error_get_last()`

#### Where are the variables of the parent scope?
In opposite to Javascript, PHP does not have closures.
A scope contains only variables that have been declared, parameters and imported globals with `global` or `use`.
If you want to see the variables of the scope of the callee, click on it in the stacktrace.

Contributing
------------
To hack on this adapter, clone the repository and open it in VS Code.
You can debug it (run it in "server mode") by selecting the "Debug adapter" launch configuration and hitting `F5`.
Then, open a terminal inside the project, and open the included testproject with VS Code while specifying the current directory as `extensionDevelopmentPath`.
As an example, for Powershell on Windows it could look like this:

```powershell
PS C:\Users\felix\github\vscode-php-debug> code .\testproject\ --extensionDevelopmentPath=$pwd
```

VS Code will open an "Extension Development Host" with the debug adapter running. Open `.vscode/launch.json` and
uncomment the `debugServer` configuration line. Hit `F5` to start a debugging session.
Now, you can debug the testproject like specified above and set breakpoints inside your first VS Code instance to step through the adapter code.

[![Gitter](https://badges.gitter.im/felixfbecker/vscode-php-debug.svg)](https://gitter.im/felixfbecker/vscode-php-debug?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

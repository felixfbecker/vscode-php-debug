
[![Latest Release](https://vsmarketplacebadge.apphb.com/version-short/felixfbecker.php-debug.svg)](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug) [![Installs](https://vsmarketplacebadge.apphb.com/installs/felixfbecker.php-debug.svg)](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug) [![Rating](https://vsmarketplacebadge.apphb.com/rating-short/felixfbecker.php-debug.svg)](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug) [![Build Status](https://travis-ci.org/felixfbecker/vscode-php-debug.svg?branch=master)](https://travis-ci.org/felixfbecker/vscode-php-debug) [![Build Status Windows](https://ci.appveyor.com/api/projects/status/hda6n2umfdt6eyms/branch/master?svg=true)](https://ci.appveyor.com/project/felixfbecker/vscode-php-debug/branch/master) [![Coverage](https://codecov.io/gh/felixfbecker/vscode-php-debug/branch/master/graph/badge.svg)](https://codecov.io/gh/felixfbecker/vscode-php-debug) [![Dependency Status](https://gemnasium.com/felixfbecker/vscode-php-debug.svg)](https://gemnasium.com/felixfbecker/vscode-php-debug) [![Gitter](https://badges.gitter.im/felixfbecker/vscode-php-debug.svg)](https://gitter.im/felixfbecker/vscode-php-debug?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

![Demo GIF](images/demo.gif)

Installation
------------

Install the extension: Press `F1`, type `ext install php-debug`.

This extension is a debug adapter between VS Code and [XDebug](https://xdebug.org/) by Derick Rethan. XDebug is a PHP extension (a `.so` file on Linux and a `.dll` on Windows) that needs to be installed on your server.

 1. [Install XDebug](https://xdebug.org/docs/install)  
  ***I highly recommend you make a simple `test.php` file, put a `phpinfo();` statement in there, then copy the output and paste it into the [XDebug installation wizard](https://xdebug.org/wizard.php). It will analyze it and give you tailored installation instructions for your environment.***  
  In short:
   - On Windows: [Download](https://xdebug.org/download.php) the appropiate precompiled DLL for your PHP version, architecture (64/32 Bit), thread safety (TS/NTS) and Visual Studio compiler version and place it in your PHP extension folder.
   - On Linux: Either download the source code as a tarball or [clone it with git](https://xdebug.org/docs/install#source), then [compile it](https://xdebug.org/docs/install#compile).
 2. [Configure PHP to use XDebug](https://xdebug.org/docs/install#configure-php) by adding `zend_extension=path/to/xdebug` to your php.ini.  
  The path of your php.ini is shown in your `phpinfo()` output under "Loaded Configuration File".
 3. Enable remote debugging in your php.ini:

  ```ini
  [XDebug]
  xdebug.remote_enable = 1
  xdebug.remote_autostart = 1
  ```
  There are other ways to tell XDebug to connect to a remote debugger than `remote_autostart`, like cookies, query parameters or browser extensions. I recommend `remote_autostart` because it "just works". There are also a variety of other options, like the port (by default 9000), please see the [XDebug documentation on remote debugging](https://xdebug.org/docs/remote#starting) for more information.
 4. If you are doing web development, don't forget to restart your webserver to reload the settings
 5. Verify your installation by checking your `phpinfo()` output for an XDebug section.

### VS Code Configuration
In your project, go to the debugger and hit the little gear icon and choose _PHP_. A new launch configuration will be created for you with two configurations:
 - **Listen for XDebug**  
   This setting will simply start listening on the specified port (by default 9000) for XDebug. If you configured XDebug like recommended above, everytime you make a request with a browser to your webserver or launch a CLI script XDebug will connect and you can stop on breakpoints, exceptions etc.
 - **Launch currently open script**  
   This setting is an example of CLI debugging. It will launch the currently opened script as a CLI, show all stdout/stderr output in the debug console and end the debug session once the script exits.

#### Supported launch.json settings:
 - `request`: Always `"launch"`
 - `port`: The port on which to listen for XDebug (default: `9000`)
 - `stopOnEntry`: Wether to break at the beginning of the script (default: `false`)
 - `localSourceRoot`: The path to the folder that is being served by your webserver and maps to `serverSourceRoot` (for example `"${workspaceRoot}/public"`)
 - `serverSourceRoot`: The path on the remote host where your webroot is located (for example `"/var/www"`)
 - `log`: Wether to log all communication between VS Code and the adapter to the debug console. See _Troubleshooting_ further down.
 - `xdebugSettings`: Allows you to override XDebug's remote debugging settings to fine tuning XDebug to your needs. For example, you can play with `max_children` and `max_depth` to change the max number of array and object children that are retrieved and the max depth in structures like arrays and objects. This can speed up the debugger on slow machines.
   For a full list of feature names that can be set please refer to the [XDebug documentation](https://xdebug.org/docs-dbgp.php#feature-names).
    - `max_children`: max number of array or object children to initially retrieve
    - `max_data`: max amount of variable data to initially retrieve.
    - `max_depth`: maximum depth that the debugger engine may return when sending arrays, hashs or object structures to the IDE.
    - `show_hidden`: This feature can get set by the IDE if it wants to have more detailed internal information on properties (eg. private members of classes, etc.) Zero means that hidden members are not shown to the IDE.

Options specific to CLI debugging:
 - `program`: Path to the script that should be launched
 - `args`: Arguments passed to the script
 - `cwd`: The current working directory to use when launching the script
 - `runtimeExecutable`: Path to the PHP binary used for launching the script. By default the one on the PATH.
 - `runtimeArgs`: Additional arguments to pass to the PHP binary
 - `externalConsole`: Launches the script in an external console window instead of the debug console (default: `false`)
 - `env`: Environment variables to pass to the script

Features
--------
 - Line breakpoints
 - Conditional breakpoints
 - Function breakpoints
 - Step over, step in, step out
 - Break on entry
 - Breaking on uncaught exceptions and errors / warnings / notices
 - Multiple, parallel requests
 - Stack traces, scope variables, superglobals, user defined constants
 - Arrays & objects (including classname, private and static properties)
 - Debug console
 - Watches
 - Run as CLI
 - Run without debugging

Remote Host Debugging
---------------------
To debug a running application on a remote host, you need to tell XDebug to connect to a different IP than `localhost`. This can either be done by setting [`xdebug.remote_host`](https://xdebug.org/docs/remote#remote_host) to your IP or by setting [`xdebug.remote_connect_back = 1`](https://xdebug.org/docs/remote#remote_connect_back) to make XDebug always connect back to the machine who did the web request. The latter is the only setting that supports multiple users debugging the same server and "just works" for web projects. Again, please see the [XDebug documentation](https://xdebug.org/docs/remote#communcation) on the subject for more information.

To make VS Code map the files on the server to the right files on your local machine, you have to set the `localSourceRoot` and `serverSourceRoot` settings in your launch.json. Example:
```json
"serverSourceRoot": "/var/www/myproject",
"localSourceRoot": "${workspaceRoot}/public"
```
Please also note that setting any of the CLI debugging options will not work with remote host debugging, because the script is always launched locally. If you want to debug a CLI script on a remote host, you need to launch it manually from the command line.

Docker Debugging
----------------
Here are some considerations when using Visual Code and XDebug with Docker:
- In your Dockerfile, you will need to install the XDebug extension and configure it:
````
RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=1" >> /usr/local/etc/php/conf.d/xdebug.ini 
````
- Debugging PHP running in a container on your local machine is similar to remote host debugging.  By default, Docker will run its containers in its "bridge" network mode, which requires you to configure Docker to connect to a specific IP address other than localhost (127.0.0.1).  If you have a fixed IP address for your network adapter (i.e. you do not use DHCP), that address will work fine.  Otherwise, in Windows, you can install Microsoft's loopback network adapter (see [this article](https://social.technet.microsoft.com/Forums/windows/en-US/259c7ef2-3770-4212-8fca-c58936979851/how-to-install-microsoft-loopback-adapter?forum=w7itpronetworking) to see how, one small change for, in Windows 10 the adapture was called "Microsoft KM-TEST-Loopback Adapter"), give it an IP address like 10.254.254.254 (anything unique and reserved) with a subnet of 255.255.255.0.  For Linux systems, youc an set up an IP alias for your loopback address 
````
ifconfig lo:0 127.0.0.2 netmask 255.0.0.0 up
````
- When you launch your PHP container, you will need to provide your IP address to XDebug in docker via an environment variable.  If you are doing a Docker run:
````
docker run -e XDEBUG_CONFIG="remote_host={{YOUR_IP_ADDRESS}}" your-image
```` 

````
# docker-compose.yml
foo:
  build: path/to/Dockerfile
  environment:
    XDEBUG_CONFIG: remote_host={{YOUR_IP_ADDRESS}}
````
- Set up your launch.json localSourceRoot, serverSourceRoot and port (for the sources, make sure you get the capitalization correct)
- If you are running multiple Docker containers on your machine, each can have its own port defined in its Dockerfile, and you can set up individual debug configurations in Visual Studio Code specifying those ports.

Thanks to [Hadrien de Cuzey](https://gist.github.com/chadrien/c90927ec2d160ffea9c4) for his info on setting up Docker with XDebug.

Troubleshooting
---------------
 - Ask a question on [Gitter](https://gitter.im/felixfbecker/vscode-php-debug)
 - If you think you found a bug, [open an issue](https://github.com/felixfbecker/vscode-php-debug/issues)
 - Make sure you have the latest version of this extension and XDebug installed
 - Try out a simple PHP file to recreate the issue, for example from the [testproject](https://github.com/felixfbecker/vscode-php-debug/tree/master/testproject)
 - If the Visual Code debugger breaks when there is an exception or error, but does not stop on your breakpoints, carefully check your localSourceRoot setting, capitalization is important if you are debugging PHP running on a VM or Docker container
 - In your php.ini, set [`xdebug.remote_log = /path/to/logfile`](https://xdebug.org/docs/remote#remote_log)
   (make sure your webserver has write permissions to the file)
 - Set `"log": true` in your launch.json

Contributing
------------
To hack on this adapter, clone the repository and open it in VS Code. You need NodeJS and typings installed (`npm install -g typings`). Install dependencies by running `npm install` and `typings install`.

You can debug the extension (run it in "server mode") by selecting the "Debug adapter" launch configuration and hitting `F5`. Then, open a terminal inside the project, and open the included testproject with VS Code while specifying the current directory as `extensionDevelopmentPath`:

```sh
code testproject --extensionDevelopmentPath=.
```

VS Code will open an "Extension Development Host" with the debug adapter running. Open `.vscode/launch.json` and uncomment the `debugServer` configuration line. Hit `F5` to start a debugging session. Now you can debug the testproject like specified above and set breakpoints inside your first VS Code instance to step through the adapter code.

The extension is written in TypeScript and compiled using a Gulpfile that first transpiles to ES6 and then uses Babel to specifically target VS Code's Node version. You can run the compile task through `npm run compile`, `gulp compile` or from VS Code with `Ctrl`+`Shift`+`B`. `npm run watch` / `gulp watch` enables incremental compilation.

Tests are written with Mocha and can be run with `npm test`. The tests are run in CI on Linux and Windows against PHP 5.4, 5.6, 7.0 and XDebug 2.3, 2.4.
=======
PHP Debug Adapter for Visual Studio Code
========================================

[![vs marketplace](https://img.shields.io/vscode-marketplace/v/felixfbecker.php-debug.svg?label=vs%20marketplace)](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug) [![downloads](https://img.shields.io/vscode-marketplace/d/felixfbecker.php-debug.svg)](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug) [![rating](https://img.shields.io/vscode-marketplace/r/felixfbecker.php-debug.svg)](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug) [![windows build](https://img.shields.io/appveyor/ci/felixfbecker/vscode-php-debug/master.svg?label=windows+build)](https://ci.appveyor.com/project/felixfbecker/vscode-php-debug)
[![macos/linux build](https://img.shields.io/travis/felixfbecker/vscode-php-debug/master.svg?label=macos/linux+build)](https://travis-ci.org/felixfbecker/vscode-php-debug) [![codecov](https://codecov.io/gh/felixfbecker/vscode-php-debug/branch/master/graph/badge.svg)](https://codecov.io/gh/felixfbecker/vscode-php-debug) [![dependencies](https://gemnasium.com/felixfbecker/vscode-php-debug.svg)](https://gemnasium.com/felixfbecker/vscode-php-debug) [![code style: prettier](https://img.shields.io/badge/code_style-prettier-ff69b4.svg)](https://github.com/prettier/prettier) [![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release) [![chat: on gitter](https://badges.gitter.im/felixfbecker/vscode-php-debug.svg)](https://gitter.im/felixfbecker/vscode-php-debug?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

![Demo GIF](images/demo.gif)

Installation
------------

Install the extension: Press `F1`, type `ext install php-debug`.

This extension is a debug adapter between VS Code and [XDebug](https://xdebug.org/) by Derick Rethan. XDebug is a PHP extension (a `.so` file on Linux and a `.dll` on Windows) that needs to be installed on your server.

 1. [Install XDebug](https://xdebug.org/docs/install)
  ***I highly recommend you make a simple `test.php` file, put a `phpinfo();` statement in there, then copy the output and paste it into the [XDebug installation wizard](https://xdebug.org/wizard.php). It will analyze it and give you tailored installation instructions for your environment.*** In short:
    - On Windows: [Download](https://xdebug.org/download.php) the appropiate precompiled DLL for your PHP version, architecture (64/32 Bit), thread safety (TS/NTS) and Visual Studio compiler version and place it in your PHP extension folder.
    - On Linux: Either download the source code as a tarball or [clone it with git](https://xdebug.org/docs/install#source), then [compile it](https://xdebug.org/docs/install#compile).
 2. [Configure PHP to use XDebug](https://xdebug.org/docs/install#configure-php) by adding `zend_extension=path/to/xdebug` to your php.ini. The path of your php.ini is shown in your `phpinfo()` output under "Loaded Configuration File".
 3. Enable remote debugging in your php.ini:

  ```ini
  [XDebug]
  xdebug.remote_enable = 1
  xdebug.remote_autostart = 1
  ```
  There are other ways to tell XDebug to connect to a remote debugger than `remote_autostart`, like cookies, query parameters or browser extensions. I recommend `remote_autostart` because it "just works". There are also a variety of other options, like the port (by default 9000), please see the [XDebug documentation on remote debugging](https://xdebug.org/docs/remote#starting) for more information.

 4. If you are doing web development, don't forget to restart your webserver to reload the settings
 5. Verify your installation by checking your `phpinfo()` output for an XDebug section.

### VS Code Configuration
In your project, go to the debugger and hit the little gear icon and choose _PHP_. A new launch configuration will be created for you with two configurations:
 - **Listen for XDebug**
   This setting will simply start listening on the specified port (by default 9000) for XDebug. If you configured XDebug like recommended above, everytime you make a request with a browser to your webserver or launch a CLI script XDebug will connect and you can stop on breakpoints, exceptions etc.
 - **Launch currently open script**
   This setting is an example of CLI debugging. It will launch the currently opened script as a CLI, show all stdout/stderr output in the debug console and end the debug session once the script exits.

#### Supported launch.json settings:
 - `request`: Always `"launch"`
 - `port`: The port on which to listen for XDebug (default: `9000`)
 - `stopOnEntry`: Wether to break at the beginning of the script (default: `false`)
 - `pathMappings`: A list of server paths mapping to the local source paths on your machine, see "Remote Host Debugging" below
 - `log`: Wether to log all communication between VS Code and the adapter to the debug console. See _Troubleshooting_ further down.
 - `ignore`: An optional array of glob patterns that errors should be ignored from (for example `**/vendor/**/*.php`)
 - `xdebugSettings`: Allows you to override XDebug's remote debugging settings to fine tuning XDebug to your needs. For example, you can play with `max_children` and `max_depth` to change the max number of array and object children that are retrieved and the max depth in structures like arrays and objects. This can speed up the debugger on slow machines.
   For a full list of feature names that can be set please refer to the [XDebug documentation](https://xdebug.org/docs-dbgp.php#feature-names).
    - `max_children`: max number of array or object children to initially retrieve
    - `max_data`: max amount of variable data to initially retrieve.
    - `max_depth`: maximum depth that the debugger engine may return when sending arrays, hashs or object structures to the IDE.
    - `show_hidden`: This feature can get set by the IDE if it wants to have more detailed internal information on properties (eg. private members of classes, etc.) Zero means that hidden members are not shown to the IDE.

Options specific to CLI debugging:
 - `program`: Path to the script that should be launched
 - `args`: Arguments passed to the script
 - `cwd`: The current working directory to use when launching the script
 - `runtimeExecutable`: Path to the PHP binary used for launching the script. By default the one on the PATH.
 - `runtimeArgs`: Additional arguments to pass to the PHP binary
 - `externalConsole`: Launches the script in an external console window instead of the debug console (default: `false`)
 - `env`: Environment variables to pass to the script

Features
--------
 - Line breakpoints
 - Conditional breakpoints
 - Function breakpoints
 - Step over, step in, step out
 - Break on entry
 - Breaking on uncaught exceptions and errors / warnings / notices
 - Multiple, parallel requests
 - Stack traces, scope variables, superglobals, user defined constants
 - Arrays & objects (including classname, private and static properties)
 - Debug console
 - Watches
 - Run as CLI
 - Run without debugging

Remote Host Debugging
---------------------
To debug a running application on a remote host, you need to tell XDebug to connect to a different IP than `localhost`. This can either be done by setting [`xdebug.remote_host`](https://xdebug.org/docs/remote#remote_host) to your IP or by setting [`xdebug.remote_connect_back = 1`](https://xdebug.org/docs/remote#remote_connect_back) to make XDebug always connect back to the machine who did the web request. The latter is the only setting that supports multiple users debugging the same server and "just works" for web projects. Again, please see the [XDebug documentation](https://xdebug.org/docs/remote#communcation) on the subject for more information.

To make VS Code map the files on the server to the right files on your local machine, you have to set the `pathMappings` settings in your launch.json. Example:
```json
// server -> local
"pathMappings": {
  "/var/www/html": "${workspaceRoot}/www",
  "/app": "${workspaceRoot}/app"
}
```
Please also note that setting any of the CLI debugging options will not work with remote host debugging, because the script is always launched locally. If you want to debug a CLI script on a remote host, you need to launch it manually from the command line.

Using XDebug With Docker
------------------------
Here are some considerations when using Visual Code and XDebug with Docker:

In your Dockerfile, you will need to install the XDebug extension and configure it:
````
RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=1" >> /usr/local/etc/php/conf.d/xdebug.ini 
````
If your Docker base image stores PHP configuration files in a different place, you will need to substitute that directory in the above blurb.

Debugging PHP running in a container on your local machine is similar to remote host debugging.  By default, Docker will run its containers in its "bridge" network mode, which requires you to configure Docker to connect to a specific IP address other than localhost (127.0.0.1), because you want XDEBUG to connect to your host, not the container itself.  If you have a fixed IP address for your network adapter (i.e. you do not use DHCP), that address will work fine (ex. 192.168.100.4).  Otherwise, in Windows, you can use the host name alias  ````docker.for.win.localhost````, and on a Mac, you can use the host name ````docker.for.mac.localhost````.  On Linux, you will need to set up an IP alias.  Substitute these values for {{YOUR_IP_ADDRESS}} as shown below.


When you launch your PHP container, you will need to provide your IP address to XDebug in docker via an environment variable.  If you are doing a Docker run:
````
docker run -e XDEBUG_CONFIG="remote_host={{YOUR_IP_ADDRESS}}" your-image
```` 
If you are using Docker Compose:
````
# docker-compose.yml
foo:
  build: path/to/Dockerfile
  environment:
    XDEBUG_CONFIG: remote_host={{YOUR_IP_ADDRESS}}
````
You can also override the remote_port in this manner.

Set up your launch.json localSourceRoot, serverSourceRoot and port (for the sources, make sure you get the capitalization correct)

If you are running multiple Docker containers on your machine, each can have its own port defined in its Dockerfile, and you can set up individual debug configurations in Visual Studio Code specifying those ports.

It is important that you carefully set the pathMappings in your launch.json.  The "key" must be the location of your project files, as it would be seen *inside of the container*, and the "value" must be that same directory as accessed from your host (in VSCode).  If you get this wrong, XDebug will stop when there is an exception, but not on your breakpoints.

If XDebug isn't working, you can shell into your container (````docker exec -it container_name bash````) and do the following:

- ````php -i | grep xdebug```` - this will tell you if you got XDebug installed correctly
- ````telnet {{YOUR_IP_ADDRESS}} 9000```` - this will tell you if the container can connct to the host (you may have to ````apt-get install telnet```` if you are using the base PHP image to get this to work, also, make sure to use the correct port)
- ````chdir```` to the directory you set up as a "value" in the pathMappings, make sure it corresponds to the directoryon your host specified in the "key"

Troubleshooting
---------------
 - Ask a question on [Gitter](https://gitter.im/felixfbecker/vscode-php-debug)
 - If you think you found a bug, [open an issue](https://github.com/felixfbecker/vscode-php-debug/issues)
 - Make sure you have the latest version of this extension and XDebug installed
 - Try out a simple PHP file to recreate the issue, for example from the [testproject](https://github.com/felixfbecker/vscode-php-debug/tree/master/testproject)
 - In your php.ini, set [`xdebug.remote_log = /path/to/logfile`](https://xdebug.org/docs/remote#remote_log)
   (make sure your webserver has write permissions to the file)
 - Set `"log": true` in your launch.json

Contributing
------------
To hack on this adapter, clone the repository and open it in VS Code. You need NodeJS and typings installed (`npm install -g typings`). Install dependencies by running `npm install` and `typings install`.

You can debug the extension (run it in "server mode") by selecting the "Debug adapter" launch configuration and hitting `F5`. Then, open a terminal inside the project, and open the included testproject with VS Code while specifying the current directory as `extensionDevelopmentPath`:

```sh
code testproject --extensionDevelopmentPath=.
```

VS Code will open an "Extension Development Host" with the debug adapter running. Open `.vscode/launch.json` and uncomment the `debugServer` configuration line. Hit `F5` to start a debugging session. Now you can debug the testproject like specified above and set breakpoints inside your first VS Code instance to step through the adapter code.

The extension is written in TypeScript and compiled using a Gulpfile that first transpiles to ES6 and then uses Babel to specifically target VS Code's Node version. You can run the compile task through `npm run compile`, `gulp compile` or from VS Code with `Ctrl`+`Shift`+`B`. `npm run watch` / `gulp watch` enables incremental compilation.

Tests are written with Mocha and can be run with `npm test`. The tests are run in CI on Linux and Windows against PHP 5.4, 5.6, 7.0 and XDebug 2.3, 2.4.

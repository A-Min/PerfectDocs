# File Logging

Using the `PerfectLogger` module, events can be logged to a specfied file, in addition to the console.

## Using in your project

Add the dependancy to your project's Package.swift file:

``` swift
.Package(url: "https://github.com/PerfectlySoft/Perfect-Logger.git", majorVersion: 0, minor: 0),
```

Now add the `import` directive to the file you wish to use the logging in:

``` swift
import PerfectLogger
```

To log events to the local console as well as a file:

``` swift
LogFile.debug("debug message", "test.txt")
LogFile.info("info message", "test.txt")
LogFile.warning("warning message", "test.txt")
LogFile.error("error message", "test.txt")
LogFile.critical("critical message", "test.txt")
LogFile.terminal("terminal message", "test.txt")
```

To log to the default file, omit the file name parameter.

## Setting a custom Logfile location

The default logfile location is `./log.log`. To set a custom logfile location, set the `logFileLocation` variable in `main.swift`:

``` swift
logFileLocation = "/var/log/myLog.log"
```

Messages can now be logged directly to the file as set by using:

``` swift
LogFile.debug("debug message")
LogFile.info("info message")
LogFile.warning("warning message")
LogFile.error("error message")
LogFile.critical("critical message")
LogFile.terminal("terminal message")
```

## Sample output

```
[DEBUG] [2016-11-16 15:18:02 GMT-05:00] a debug message
[INFO] [2016-11-16 15:18:02 GMT-05:00] an informational message
[WARNING] [2016-11-16 15:18:02 GMT-05:00] a warning message
[ERROR] [2016-11-16 15:18:02 GMT-05:00] an error message
[CRITICAL] [2016-11-16 15:18:02 GMT-05:00] a critical message
[EMERG] [2016-11-16 15:18:02 GMT-05:00] an emergency message
```

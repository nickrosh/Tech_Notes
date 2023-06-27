
Logging is the process of tracking and recording key events that occur in our applications for the purpose of inspection, debugging, etc. They're a whole lot more powerful than `print` statements because they allow us to send specific pieces of information to specific locations with custom formatting, shared interfaces, etc. This makes logging a key proponent in being able to surface insightful information from the internal processes of our application.

There are a few overarching concepts to be aware of:
- **Logger**: emits the log messages from our application. 
- **Handler**: sends log records to a specific location.
- **Formatter**: formats and styles the log records.


## Levels

There are different levels to log events:
- `INFO` - Informational messages that do not indicate any fault or error.
- `WARN` - Indicates that there is a potential problem, but with no user experience impact.
- `ERROR` - Indicates a serious problem, with some user experience impact
- `FATAL` - Indicates fatal errors, user experience is majorly impacted
- `DEBUG` - Used for debugging. The messaging targets specifically the app's developers

For example, logging an `ERROR` as `INFO` will interfere with troubleshooting. Also, if an exception is expected behavior and does not indicate degraded behavior, consider logging that exception as `INFO` instead of `ERROR`. 
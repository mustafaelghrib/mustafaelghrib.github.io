---
title: "Logging System with Singleton Pattern using Python"
date: 2023-02-27T00:51:01+02:00
author: Mustafa Abdallah
description:
---

### Introduction
Logging is an important aspect of software development, as it allows developers to track what their applications are doing and to identify and fix issues as they arise. One design pattern that can be useful in implementing a logging system is the Singleton pattern, which ensures that there is only one instance of a class in the system.

### UML Diagram of the Logging System
![UML Diagram of the Logging System](/images/logger_uml.png)

### Code Implementation
Implementing a logging system with the Singleton pattern is to define a Logger class that will handle all the logging functionality. The Logger class should have a private constructor and a static method that returns the single instance of the class.

```python
class Logger(object):
    
    _instance = None
    _logs = ""

    def __init__(self):
        raise RuntimeError('Call get_instance() instead')

    @classmethod
    def get_instance(cls):
        if cls._instance is None:
            cls._instance = cls.__new__(cls)
        return cls._instance

    def debug(self, message):
        self._logs += f"[DEBUG] {message}\n"

    def info(self, message):
        self._logs += f"[INFO] {message}\n"

    def warning(self, message):
        self._logs += f"[WARNING] {message}\n"

    def error(self, message):
        self._logs += f"[ERROR] {message}\n"

    def get_logs(self):
        print(self._logs)
```

This is a just a simple example of logging system, where it has four methods for each log level and one more method for getting the logs.

### Testing the logger
We could test the logger as follows:
```python
logger = Logger.get_instance()
logger.debug("my error")
logger.get_logs()

logger2 = Logger.get_instance()
logger2.info("my error2")
logger2.get_logs()
```

And here is the result:
```shell
[DEBUG] my error

[DEBUG] my error
[INFO] my error2
```
As you can see the new logs added to the old logs which means that the logger is instanced only once.

### Conclusion
Implementing a logging system using the Singleton pattern can be a useful approach for managing logging functionality in a Python application. By defining a Logger class with a private constructor and a static method that returns the single instance of the class, we can ensure that there is only one instance of the class in the system, and simplify the process of logging messages.

### References
- [Head First Design Patterns: A Brain-Friendly Guide](https://www.amazon.com/Head-First-Design-Patterns-Brain-Friendly/dp/0596007124)
- [Singleton Pattern – Design Patterns (ep 6) by Christopher Okhravi](https://www.youtube.com/watch?v=hUE_j6q0LTQ)
- I used [draw.io](https://www.draw.io/) for drawing the UML diagram

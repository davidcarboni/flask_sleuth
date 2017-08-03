# Logging standard

## Purpose

Standardise logging format to support interoperability. 

## What's in the box?

This project provides:

 * Logging in Spring Boot format using standard Python logging.
 * Including distributed trace information (B3) in Spring Cloud Sleuth format.
 * `regex.regex` Regexes that document the logging standard, which is based on Spring Boot and Spring Cloud Sleuth.
 * `demo` Python code to generate log messages in this standard format.

NB: this repo includes a copy of the [B3](https://gitlab.ros.gov.uk/CarbonD/flask_b3) 
code in order to log tracing information.

## Usage

Careful design has gone in to making this code as simple as possible to use.
To add this to your project, you'll need to copy `logging_standard/__init__.py` and `b3/__init__.py`.
Once imported and initialised, you should be able to use completely standard Python logging:

    import logging
    import logging_standard
    
    app = Flask("My app")
    logging_standard.init(app)

That should be all you need.
NB the `init` function calls `logging.basicConfig`. 
The `basicConfig` function does nothing if the logging system is already initialised
so you'll need to ensure you call `logging_standard.init(app)` 
before you make any other logging calls, otherwise the log format won't actually be applied.

You can run `demo.py` to see logging in action, or here are the two intended use cases:

### Basic Spring Boot format

    logger = logging.getLogger("demo_logger")
    logger.setLevel(logging.DEBUG)
    logger.debug("Logging without tracing information")

### With B3 tracing information (Spring Cloud Sleuth format)

    logger = logging.getLogger("demo_logger")
    logger.setLevel(logging.DEBUG)
    b3.start_span()
    logger.debug("Logging with added tracing information")
    b3.end_span()

NB You'll most likely want to call `start_span()` and `end_span()`
using `Flask.before_request()` and `Flask.after_request()`.
See the [flask_b3 README](https://gitlab.ros.gov.uk/CarbonD/flask_b3/blob/master/README.md)
for details.

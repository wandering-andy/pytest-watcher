# Welcome to pytest-watcher

[![PyPI](https://img.shields.io/pypi/v/pytest-watcher)](https://pypi.org/project/pytest-watcher/)
[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/pytest-watcher)](https://pypi.org/project/pytest-watcher/)
[![GitHub](https://img.shields.io/github/license/olzhasar/pytest-watcher)](https://github.com/olzhasar/pytest-watcher/blob/master/LICENSE)

## Overview

**pytest-watcher** is a tool to automatically rerun tests (using `pytest` by default) whenever your code changes.

Works on Unix (Linux, MacOS, BSD) and Windows.

Example:

![Preview](_static/preview.gif)

## Motivation

### Why not general tools

- Easy to use and remember
- Works for most python projects out of the box
- Uses native system monitoring API instead of polling on supported systems (see [watchdog documentation](https://python-watchdog.readthedocs.io/en/stable/installation.html#supported-platforms-and-caveats))
- Listens for new file, delete file, change and move events
- Runs your tests with latest changes in case of post-processing events (see [delay](#delay))

### What about pytest-watch

[pytest-watch](https://github.com/joeyespo/pytest-watch) has been around for a long time and used to address exactly this problem. Unfortunately, pytest-watch is no longer maintained and doesn't work for many users. This project provides an alternative for it.

See also: [Differences with pytest-watch](#differences-with-pytest-watch)

## File events

By default `pytest-watcher` looks for the following events:

- New `*.py` file created
- Existing `*.py` file modified
- Existing `*.py` file deleted
- A `*.py` file moved either from or to the watched path

You can specify alternative file patterns to watch. See [Watching different patterns](#watching-different-patterns)

## Installation

```sh
pip install pytest-watcher
```

## Usage

Specify the path that you want to watch:

```sh
ptw .
```

or

```sh
ptw /home/repos/project
```

`pytest-watcher` will pass any arguments after `<path>` to the test runner (which is `pytest` by default). For example:

```sh
ptw . -x --lf --nf
```

will call `pytest` with the following arguments:

```sh
pytest -x --lf --nf
```

### Using a different test runner

You can specify an alternative test runner using the `--runner` flag:

```sh
ptw . --runner tox
```

### Watching different patterns

You can use the `--patterns` flag to specify file patterns that you want to watch. It accepts a list of Unix-style patterns separated by a comma. The default value is "\*.py"

Example:

```sh
ptw . --patterns '*.py,pyproject.toml'
```

You can also **ignore** certain patterns using the `--ignore-patterns` flag:

```sh
ptw . --ignore-patterns 'settings.py,db.py'
```

### Delay

`pytest-watcher` uses a short delay (0.2 seconds by default) before triggering the actual test run. The main motivation for this is post-processors that can run after you save the file (for example, `black` plugin in your IDE). This ensures that tests will run with the latest version of your code.

You can control the actual delay value with the `--delay` flag:

`ptw . --delay 0.2`

To disable the delay altogether, you can set zero as a value:

`ptw . --delay 0`

### Differences with `pytest-watch`

Even though this project was inspired by [`pytest-watch`](https://github.com/joeyespo/pytest-watch), it's not a fork of it. Therefore, there are **differences** in behavior:

- `pytest-watch` needs you to specify a path to watch as a first argument:

```
ptw .
```

- `pytest-watch` doesn't start tests immediately by default. You can customize this behavior using `--now` flag.

## Configuring

You can configure `pytest-watcher` via `pyproject.toml` file. Here is the default configuration:

```toml
[tool.pytest-watcher]
now = false
delay = 0.2
runner = "pytest"
runner_args = []
patterns = ["*.py"]
ignore_patterns = []
```

## Compatibility

The code is compatible with Python versions 3.7+

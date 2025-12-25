.. LifecycleLogging documentation master file
   Lifecycle-aware logging utilities for Python

========================================
LifecycleLogging Documentation
========================================

**Lifecycle-aware logging utilities for Python applications.**

LifecycleLogging provides a comprehensive logging solution that combines Python's standard logging with rich output formatting, message storage, and lifecycle event tracking.

Features
--------

* Configurable console and file logging outputs
* Rich formatting for enhanced readability  
* Message storage with context and storage markers
* Verbosity controls with bypass markers
* JSON data attachment support
* Type-safe implementation
* Seamless integration with existing logging systems
* Automatic Gunicorn logger integration

Quick Start
-----------

Install from PyPI:

.. code-block:: bash

   pip install lifecyclelogging

Basic usage:

.. code-block:: python

   from lifecyclelogging import Logging

   logger = Logging(enable_console=True)
   logger.logged_statement("Hello, world!", log_level="info")

.. toctree::
   :maxdepth: 2
   :caption: Getting Started

   getting-started/installation
   getting-started/quickstart

.. toctree::
   :maxdepth: 2
   :caption: API Reference

   api/index

.. toctree::
   :maxdepth: 1
   :caption: Development

   development/contributing


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

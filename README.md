# pyvicp
Pure Python client-side implementation of VICP network communications protocol used to control LeCroy Digital Oscilloscopes (DSOs)

## Why is it licensed with LGPL2.1?

This package is a Python rewrite of the LeCroyVICP Client Library, which was written in C++.  That project can be found at https://sourceforge.net/projects/lecroyvicp/.

Because that project was licensed with LGPL2.1 and this is considered a derived work, it's not possible to release this package with a less restrictive license.  Sorry.

## Comparison with C++ version

In comparison with the C++ version, the Python version is ~500 lines of code smaller.  Because the Python version will raise an exception whenever an error condition is detected, the error handling is cleaner and more robust than the C++ version (which returns False on error and depends on the caller to check the return value).

The performance of the Python version is on par with the C++ version.  This is accomplished by using the low level socket interface and eliminating unnecessary data copies.

An additional benefit of the Python version is that it's platform independent.  The C++ version as released only ran on Windows and required modifications to run on any other OS.

## Installation

```
$ pip install pyvicp
```

## Usage
It's expected that this package won't be invoked directly, but rather will be used with the pyvisa package.  Because this package uses the more restrictive LGPL2.1 license, it can't be bundled with the pyvisa package (which uses the more permissive MIT license).

Below is an example of the low level interface provided by this package:

```
In [1]: from pyvicp import Client
   ...: scope = Client("10.11.12.42")
   ...: scope.send(b"*idn?")
   ...: scope.receive()
Out[1]: bytearray(b'LECROY,WR8208HD,LCRY5003N60179,9.8.0\n')
```

## Async Client
 The biggest difference from sync version, other from being async, is that client is not connected upon initiation. You can either call `connect` explicitly or use context manager

```python
# Without context manager
import asyncio
from pyvicp import AsyncClient

async def main():
   scope = AsyncClient("10.11.12.42")
   try:
      await scope.connect()
      await scope.send(b"*idn?")
      res = await scope.receive()
      print(res.decode())
   finally:
      await scope.close()

asyncio.run(main())
```

```python
# With Context manager
import asyncio
from pyvicp import AsyncClient

async def main():
   async with AsyncClient("10.11.12.42") as scope:
      await scope.send(b"*idn?")
      res = await scope.receive()
      print(res.decode())

asyncio.run(main())
```
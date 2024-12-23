:orphan:

Hostprobe api
===========================

The api of hostprobe

.. note::
    
    The function titles only show the required parameters, while the subsection "Optional" shows **all the other** parameters; 
    The "Optional" parameters have their default value listed next to them (ex: "port (80): <info>")

general hostprobe
-----------------------------------

.. function:: hostprobe.netprobe(network: str) -> list | None

    ``netprobe`` is a tool used to scrape all the online 
    hosts off of a network.
    
    Required Parameters:

    - **network** : the ip network (with netmask bits) that is scanned

    Optional Paremeters:

    - **timeout** (5): the timeout for how long a host will be checked until it is considered offline
    - **verbose** (False): prints extraneous info (works if output is enabled)
    - **retry** (False): retries all the hosts that rendered offline
    - **port** (80): the port scanned
    - **maxthreads** (0): the maximum number of individual threads (0 or less signifies no maximum)
    - **info** (False): prints runtime stats while the program is running (works if output is enabled, and verbose is disabled)
    - **r** (True): returns the output list
    - **output** (False): enables output (either verbose or info)
    - **threshold** (True): enables memory threshold, set in mebibytes, which won't be exceded by the number of threads
    - **maxthreshold** (DEFAULTTHRESHOLD): the maximum memory threshold value

    Example:

    >>> import hostprobe
    >>> onlinehosts = hostprobe.netprobe("192.168.1.1/24")
    >>> print(f"some online hosts: {onlinehosts}"")
    some online hosts: ["192.168.1.1", "192.168.1.7", "192.168.1.32"]

.. function:: hostprobe.check_host(host:str) -> Queue | None

    ``check_host`` is a tool designed for netprobe, but it is also available for public api.
    If no result queue is supplied, output will be printed to stdout.
    *Note on result queue:* the returned values were designed for the usage of netprobe;
    **True- host is online**, **2- host is offline**, **0-SIGINT was requested by the user**.
    *result_queue format:* "(host, status)" (ex. output: "('192.168. 123.132', True)").

    Required Parameters:

    - host: the ip adress of the host to be checked

    Optional Parameters:

    - port (80): the port scanned
    - timeout (1): the timeout for how long a host will be checked until it is considered offline
    - result_queue (None): a queue for the returned values (see ``check_host`` description for more details)

    Example without queue:

    >>> import hostprobe
    >>> hostprobe.check_host("192.168.1.1", timeout=2)
    192.168.1.1 is online

    Example with queue:

    >>> from queue import Queue
    >>> import hostprobe
    >>> result_queue = Queue()
    >>> host_status = hostprobe.check_host("192.168.1.1", timeout=2, result_queue=result_queue)
    >>> host, status = host_status.get()
    >>> if status is True:
    ...     print(f"the host {host} is online")
    ... elif status == 2:
    ...     print(f"the host {host} is offline")
    ...
    the host 192.168.1.1 is online

hostprobe constants
-------------------------------------------

.. py:data:: DEFAULTTHRESHOLD

    Hostprobe default value for maximum threshold (100mib)

    Example:

    >>> import hostprobe
    >>> online = hostprobe.netprobe("192.168.1.1/24", threshold=True, maxthreshold=hostprobe.DEFAULTTHRESHOLD)
    >>> #sets the max RAM used to the default (100mib)


.. py:data:: MINTHRESHOLD

    A dynamic value (depending on system) representing the approximate minimum threshold, which is the amount
    of ram the program uses (plus 1 for a grace amount, allowing at least 1 thread to be created).
    This is used to ensure that the program does not try to stop ram at a 
    value less then the ram the program generally uses.

    Example:

    >>> import hostprobe
    >>> from hostprobe.utils import mebibyte
    >>> hostprobe.netprobe("192.168.1.1/24", threshold=True, maxthreshold=(hostprobe.MINTHRESHOLD + mebibyte(1)))
    >>> #sets the max RAM usage to the near smallest value, without causing the program to crash


hostprobe submodule options
--------------------------------------------

.. function:: hostprobe.utils.mebibyte() -> int

    used to represent mebibytes in integer format

    Required Parameters:

    - mbval: the mebibyte value to be converted to int

    Example:

    >>> from hostprobe.utils import mebibyte
    >>> onemib = mebibyte(1)
    >>> print(onemib)
    1048576


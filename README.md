# ha-old-ping
A historic version of the HA ping integration which uses icmplib==1.2.2

Based off HomeAssistant core at this commit: https://github.com/home-assistant/core/tree/5742db630851cb7b4c4c1d6f8cbc4c58bde86b8c/homeassistant/components/ping

As of 14/04/2021 (HA 2021.4.0) the builtin ping integration uses icmblib==2.2.1, which fails when run as 'homeassistant' user in TrueNAS iocage plugin jail. Issue reported: https://github.com/tprelog/iocage-homeassistant/issues/39

------------

As of HomeAssistant 2021.4.1, the inbuilt `ping` integration uses `icmplib==2.1.1`: https://github.com/home-assistant/core/pull/43868/files

Ping in this version of icmplib fails in the HA iocage jail when run as the `homeassistant` user, but works when run as root. I don't understand the exact problem, but this seems to suggest that there is some type of network permission issue in the jails config.

This can be verified using the jail shell, updating to HA 2021.4.*, then entering the python venv: `source /usr/local/share/homeassistant/bin/activate`
then running python interactively: `python`
and pasting the following code:
```
from functools import lru_cache

from icmplib import SocketPermissionError, ping as icmp_ping

# In python 3.9 and later, this can be converted to just be `cache`
@lru_cache(maxsize=None)
def can_create_raw_socket():
    """Verify we can create a raw socket."""
    try:
        icmp_ping("127.0.0.1", count=0, timeout=0)
        return True
    except SocketPermissionError:
        return False


print (can_create_raw_socket())
```
(test code sourced from a similar issue: https://github.com/home-assistant/core/issues/43188)

If you enter the venv as root, this code prints `True`.
If you `su homeassistant` before entering the venv, then it returns `False`.

If you manually roll back the python icmplib to v1.2.2, then it will return `True` regardless of user.

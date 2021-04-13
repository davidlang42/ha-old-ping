# ha-old-ping
A historic version of the HA ping integration which uses icmplib==1.2.2

Based off HomeAssistant core at this commit: https://github.com/home-assistant/core/tree/5742db630851cb7b4c4c1d6f8cbc4c58bde86b8c/homeassistant/components/ping

As of 14/04/2021 (HA 2021.4.0) the builtin ping integration uses icmblib==2.2.1, which fails when run as 'homeassistant' user in TrueNAS iocage plugin jail. Issue reported: https://github.com/tprelog/iocage-homeassistant/issues/39

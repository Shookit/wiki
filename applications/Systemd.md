Systemd
=======

Overview
--------

Systemd is quickly becoming the new standard service manager for linux. It uses greatly simplified service descriptor files to handle daemon launching and management, and also has the advantage of using cgroups to silo everything.

Service files
-------------

Service files are of the following general format:

```
[Unit]
Description=VirtualBox Guest Service

[Service]
Type=simple
ExecStartPre=-/usr/bin/modprobe vboxguest
ExecStartPre=-/usr/bin/modprobe vboxvideo
ExecStartPre=-/usr/bin/modprobe vboxsf
ExecStart=/usr/bin/VBoxService -f
PIDFile= /var/run/vbox/vbox.pid

[Install]
WantedBy=multi-user.target
```

- Unit describes service metadata and dependencies on other services.
- Service describes daemon execution commands and types, pre-post execution commands, restart handling, PID file locations, and file handle limits, among others.
- Install describes what happens during enable/disable. `WantedBy=multi-user.target` is the default multiuser environment.

Service types
-------------

- simple: Should be configured with ExecStart and is the main process of the service.
- forking: Should be configured with ExecStart, and  will call fork() as part of its start-up. The parent process is expected to exit when start-up is complete and all communication channels are set up. The child continues to run as the main daemon process. It is recommended to also use the PIDFile option, so that systemd can identify the main process of the daemon.
- oneshot: Similar to simple, but it is expected that the process has to exit before systemd starts follow-up units.
- dbus
- notify
- idle

PID File
--------

Use of PIDFile under Service is recommended for services where Type is set to forking. systemd will read the PID of the main process of the daemon after start-up of the service. systemd will not write to the file configured here, although it will remove the file after the service has shut down if it still exists.


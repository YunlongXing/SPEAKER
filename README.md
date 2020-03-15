# SPEAKER: Split-Phase Execution of Application Containers

## Prerequisites
* **OS**: Ubuntu 16.04 with kernel version 4.15.0
* **Docker**: 19.03.6 or higher (can be installed by using [this script](./install-docker.sh)).


## Using the SPEAKER
The SPEAKER includes two modules: **Tracing Module** and **Slimming Module**. The Tracing Module is implemented as a Python script, which would trace all the system calls invoked in booting, running and shutdown phases of a container application and generate the corresponding system call lists. The Slimming Module takes the outputs of Tracing Module as inputs to build and automatically enforce the corresponding Seccomp Filters during the different execution phases of a container.

### Tracing Module
The Tracing Module utilizes the Linux audit log where the invoked syscall could be recorded if it does not match any of the configured Seccomp Filter rules. We set the Seccomp Filter null so that all the syscalls invoked by the container application will be collected by analyzing the audit log. To make sure all the audit log during tracing will be kept, set ``num_logs`` and ``max_log_file`` to appropriate values in ``/etc/audit/auditd.conf`` (refer [this](https://linux.die.net/man/5/auditd.conf) for more about the audit configuration).

The Tracing Module could be executed with the following command. In ``speaker/TracingModule``:
```
sudo python tracing.py
```
Then, follow the output instruction of the script to run a docker container, wait at least 120 seconds for container to warm up, perform normal operations as much as possible, and gracefully shutdown the container.

After that, three syscall lists will be generated for booting, running, and shutdown phases. You can also prepare the syscall lists by yourself (refer [syscall list examples](./ProfileExample) for format).

Using the following cmd to get audit log
``tail -f /var/log/audit/audit.log | grep SECCOMP``
``ausearch -m 1326``


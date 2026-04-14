# OS Jackfruit - Lightweight Container Runtime

## 1. Team Information

* Dhanush Rao
* SRN: PES2UG24CS153
* Chetan
* SRN: PES2UG24CS131

---

## 2. Build, Load, and Run Instructions

### 🔹 Build the Project

```bash
make
```

### 🔹 Load Kernel Module

```bash
sudo insmod monitor.ko
```

### 🔹 Verify Control Device

```bash
ls -l /dev/container_monitor
```

---

### 🔹 Start Supervisor

```bash
sudo ./engine supervisor ./rootfs-base
```

---

### 🔹 Create Writable Root Filesystems

```bash
cp -a ./rootfs-base ./rootfs-alpha
cp -a ./rootfs-base ./rootfs-beta
```

---

### 🔹 Start Containers (in another terminal)

```bash
sudo ./engine start alpha ./rootfs-alpha /bin/sh --soft-mib 48 --hard-mib 80
sudo ./engine start beta ./rootfs-beta /bin/sh --soft-mib 64 --hard-mib 96
```

---

### 🔹 List Running Containers

```bash
sudo ./engine ps
```

---

### 🔹 View Logs

```bash
sudo ./engine logs alpha
```

---

### 🔹 Run Workloads

#### Memory Test

```bash
sudo ./engine start mem1 rootfs "./memory_hog 1000" --soft-mib 10 --hard-mib 20
```

#### CPU Scheduling Test

```bash
sudo ./engine start cpu1 rootfs "/bin/sh -c \"while true; do ./cpu_hog; done\"" --nice 0
sudo ./engine start cpu2 rootfs "/bin/sh -c \"while true; do ./cpu_hog; done\"" --nice 10
```

---

### 🔹 Stop Containers

```bash
sudo ./engine stop alpha
sudo ./engine stop beta
```

---

### 🔹 Check Kernel Logs

```bash
dmesg | tail
```

---

### 🔹 Cleanup

```bash
sudo rm -f /tmp/mini_runtime.sock
sudo rmmod monitor
```

---

### 🔹 Running Helper Binaries

```bash
cp cpu_hog ./rootfs-alpha/
cp memory_hog ./rootfs-alpha/
```

---

## 3. Demo with Screenshots

Each screenshot includes a brief caption:

1. Multi-container supervision
   <img width="838" height="66" alt="1" src="https://github.com/user-attachments/assets/96559cf7-5818-4a27-9e8f-3754f3069f30" />
   <br>
   Screenshot showing multiple containers managed by the supervisor using the engine ps command.

2. Metadata tracking (`engine ps`)
   <img width="828" height="282" alt="2" src="https://github.com/user-attachments/assets/09e0841c-c70c-4797-a6d9-504a4510466f" />
   <br>
   Screenshot showing container metadata such as PID, state, and exit status.

3. Bounded-buffer logging (`engine logs`)
   <img width="830" height="123" alt="3" src="https://github.com/user-attachments/assets/8094cc5a-586e-44e7-b104-3926e1e98866" />
   <br>
   Screenshot showing container logs capturing output (“Hello”), demonstrating the logging system.

4. CLI and IPC interaction
   <img width="823" height="251" alt="4" src="https://github.com/user-attachments/assets/ca5b1368-e5dc-4237-b811-f6881948b761" />
   <br>
   Screenshot showing CLI command execution and response from supervisor.

5. Soft-limit warning (dmesg/logs)
   <img width="1600" height="625" alt="5 6" src="https://github.com/user-attachments/assets/e65d43aa-f44c-4183-ac48-fa98c9c4b162" />
   <br>
   Screenshot showing gradual memory allocation before reaching limit.

6. Hard-limit enforcement (OOM kill)
   <img width="1600" height="625" alt="5 6" src="https://github.com/user-attachments/assets/adc08fe4-c827-4656-bec1-fe964f47ad53" />
   <br>
   Screenshot showing kernel OOM kill message after memory limit is exceeded.

7. Scheduling experiment (nice values comparison)
   <img width="1447" height="711" alt="7a" src="https://github.com/user-attachments/assets/8457bf42-a2c3-4b51-8c14-daa15142d359" />
   <br>
   Screenshot showing two CPU-bound containers running with different nice values (0 and 10).

   <img width="1031" height="305" alt="7b" src="https://github.com/user-attachments/assets/b451e9c7-5870-44f0-b4c7-c60703bcbcf4" />
   <br>
   Screenshot showing CPU usage of two processes with different nice values. The process with lower 
   nice value has higher priority.

8. Clean teardown (no defunct processes)
   <img width="877" height="141" alt="8" src="https://github.com/user-attachments/assets/35cf9c4c-5bb1-4a2d-b700-659eca0a3d2d" />
   <br>
   CLI Screenshot showing no defunct processes and successful cleanup of resources, confirming proper 
   teardown. 

---

## 4. Engineering Analysis

This project demonstrates key OS concepts such as process isolation, inter-process communication, scheduling, and memory management. The system uses user-space and kernel-space interaction to enforce constraints. The kernel module monitors memory usage and enforces limits, while the runtime manages containers and communication.

---

## 5. Design Decisions and Tradeoffs

* **Isolation using chroot**

  * Simple and easy to implement
  * Tradeoff: weaker than namespaces
  * Justification: sufficient for learning purposes

* **Centralized supervisor**

  * Easy management of containers
  * Tradeoff: scalability limitations
  * Justification: simplifies design

* **Bounded buffer logging**

  * Demonstrates producer-consumer model
  * Tradeoff: added complexity
  * Justification: educational value

* **Kernel module monitoring**

  * Strong enforcement of limits
  * Tradeoff: kernel-level complexity
  * Justification: demonstrates OS internals

* **Nice-based scheduling**

  * Simple way to show priority
  * Tradeoff: limited control
  * Justification: aligns with Linux scheduler

---

## 6. Scheduler Experiment Results

### 🔹 Observed Data

| PID  | Nice Value | Process | CPU Usage (%) |
| ---- | ---------- | ------- | ------------- |
| 5249 | 0          | cpu_hog | 98.4          |
| 5250 | 10         | cpu_hog | 97.9          |

### 🔹 Analysis

The process with lower nice value (higher priority) received slightly more CPU time. This demonstrates that the Linux scheduler is priority-aware while still maintaining fairness. Lower-priority processes are not starved, but higher-priority processes get preference.

---

## GitHub Repository

👉 https://github.com/dhanushrao-max/OS-Jackfruit

---

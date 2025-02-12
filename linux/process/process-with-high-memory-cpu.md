# Troubleshooting High CPU/Memory Processes – Cheat Sheet

## 1. Monitor Usage
1. **Identify High-Resource Processes:**
   - Run `top` or `htop` to see processes consuming high CPU or memory.
   - Use:
     ```bash
     ps aux --sort=-%cpu | head -n 10   # Top CPU consumers
     ps aux --sort=-%mem | head -n 10    # Top memory consumers
     ```
2. **Check System Memory & Swap:**
   - Run:
     ```bash
     free -m
     vmstat 1 5
     ```

## 2. Check System Logs
3. **Kernel Messages:**
   - Check for OOM events:
     ```bash
     dmesg -T | grep -i oom
     ```
4. **System Log Files:**
   - Review `/var/log/syslog` or use:
     ```bash
     journalctl -xe
     ```

## 3. Inspect Process Details
5. **Process Information:**
   - Check `/proc` entries:
     ```bash
     cat /proc/<pid>/status
     cat /proc/<pid>/stat
     cat /proc/<pid>/cmdline
     ```
6. **Kernel Stack:**
   - (Requires root) Run:
     ```bash
     sudo cat /proc/<pid>/stack
     ```

## 4. Review Open Files & Network Connections
7. **List Open Files:**
   - Run:
     ```bash
     lsof -p <pid>
     ```

## 5. Profile the Process
8. **CPU Profiling:**
   - Use:
     ```bash
     perf top -p <pid>
     pidstat -p <pid> 1
     ```
9. **Memory Leak Detection:**
   - Run tools like:
     ```bash
     valgrind --leak-check=full ./your_app
     ```

## 6. Trace System and Library Calls
10. **System Calls:**
    - Run:
      ```bash
      strace -p <pid>
      ```
11. **Library Calls:**
    - Run:
      ```bash
      ltrace -p <pid>
      ```

## 7. Check cgroup/Container Limits (if applicable)
12. **Memory Limits:**
    - Run:
      ```bash
      cat /sys/fs/cgroup/memory/memory.limit_in_bytes
      cat /sys/fs/cgroup/memory/memory.usage_in_bytes
      ```

## 8. Advanced Tracing (Optional)
13. **Low-Overhead Tracing:**
    - Consider tools like `bpftrace` or `SystemTap` for custom instrumentation.

## 9. Attach a Debugger (For Deep Investigation)
14. **Using gdb:**
    - Run:
      ```bash
      sudo gdb -p <pid>
      ```
    - In gdb, type:
      ```gdb
      (gdb) bt
      ```
      to see the backtrace.

## 10. Remediate
15. **Adjust Resources or Optimize Code:**
    - Increase resource allocations via cgroups or container limits.
    - Optimize application code if inefficient.
    - Throttle process priority with:
      ```bash
      renice +10 -p <pid>
      ```

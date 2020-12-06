Android platform
================

Building platform
-----------------

### Environment setup

Easiest w/ Ubuntu (14.04 LTS); requires 64-bit

```bash
sudo apt-get install git gnupg flex bison gperf build-essential zip curl libc6-dev libncurses5-dev:i386 \
x11proto-core-dev libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-glx:i386 libgl1-mesa-dev \
g++-multilib mingw32 tofrodos  python-markdown libxml2-utils xsltproc zlib1g-dev:i386 lib32ncurses5-dev libssl0.9.8:i386 libqtgui4:i386 libxml2:i386 libswitch-perl

#For android 4.x:
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java6-installer
sudo update-java-alternatives -s java-6-oracle
sudo apt-get install oracle-java6-set-default

#For android 5.x:
sudo apt-get install openjdk-7
```

### Building

Must find android version which has compatible driver binaries before building

Install 'repo' program from google (refer to <http://source.android.com/source/downloading.html>)

```bash
repo init -u <manifest URL> -b <proper branch version>
```

For Mako/Nexus 4:

```bash
repo init -u https://android.googlesource.com/platform/manifest -b android-4.4.2_r1
repo sync -j8
```

Then download binary drivers, run the scripts to extract to vendor/ (refer to <https://developers.google.com/android/nexus/drivers>)

```bash
source build/envsetup.sh
lunch
make -j16
# ~/android_plat/out/target/product/model contains .img files
```

Boot the device into fastboot mode:

```bash
fastboot flash whatever1 whatever1.img
fastboot flash whatever2 whatever2.img
fastboot flash whatever3 whatever3.img
```


Building kernel
---------------

### Compiling the kernel

Must build the kernel with the same kernel source version as that being used in the platform build you're using.

Get the kernel sources (this below repo is just for for N4 and a few other qualcomm-based devices):

```bash
git clone https://android.googlesource.com/kernel/msm.git

export ARCH=arm
export SUBARCH=arm
export CROSS_COMPILE=arm-eabi-
export PATH=~/android_plat/prebuilts/gcc/linux-x86/arm/arm-eabi-4.6/bin:$PATH

cd msm
git branch -a to see branches
git checkout proper branch
make mako_defconfig
make menuconfig -> general setup -> local version - append to kernel release (to add an identifier)
make -j16
```

### Adding the kernel to the phone

Two options from here to create the boot.img:

Copy into an old boot.img (couldn't get to work on Lollipop)

```bash
apt-get install abootimg
get the original boot.img (android_platform/out/target/product/mako/boot.img)
get the new zImage (android_kern/arch/arm/boot/zImage)
abootimg --create boot.img -k zImage -r android_plat/out/target/product/mako/ramdisk.img
```

Build as part of the platform:

```bash
cd android_platform
rm device/lge/mako-kernel/kernel
cp zImage device/lge/mako-kernel/kernel
make -j8 bootimage (or just make -j8)
```

Test with:

```bash
adb reboot bootloader
fastboot boot boot.img
```

Tracing
-------

### Overview

- strace - system call tracer (for a single process)
- ltrace - dynamic library call tracer (for a single process)
- ftrace - kernel function tracer (system-wide; not an actual program; manually set up via file handlers in debugfs)
- atrace - wrapper around ftrace for android
- systrace - Android tool, based on atrace which in turn is based on ftrace. If on windows, don't use the systrace python command; use monitor.bat in the tools sdk folder.
- DDMS - port-forwarding services, screen capture on the device, thread and heap information on the device, logcat, process, and radio state information, incoming call and SMS spoofing, location data spoofing, and more. Only works on applications w/ debugging enabled(?). Can only get to work on my applications.
- traceview - graphical viewer for execution logs that you create by using either the Debug class or DDMS. Good to find amount of time spent in various java methods in.

### ftrace

Function tracing not enabled by default on many production builds. To get working, compile a new kernel:

```
*disable* CONFIG_STRICT_MEMORY_RWX in .config on msm-based phones
Kernel Hacking -> Tracers -> Kernel FUNCTION_TRACER
Kernel Hacking -> Tracers -> Interrupts-off Latency Tracer
Kernel Hacking -> Tracers -> Preemption-off Latency Tracer
Kernel Hacking -> Tracers -> Trace max stack
Kernel Hacking -> Tracers -> Enable kprobes-based dynamic events
Kernel Hacking -> Tracers -> enable/disable ftrace tracepoints dynamically
```

For function tracing:

```bash
echo function > current_tracer
cat trace
```

For event tracing:

```bash
echo 16384 > /d/tracing/buffer_size_kb && echo "" > /d/tracing/set_event && echo "" > /d/tracing/trace && echo "irq:* sched:* power:cpu_frequency" > /d/tracing/set_event && sleep 60 && cat /d/tracing/trace > /data/local/ftrace.txt &
```

NDK
---

The NDK has the option of using a cross-compiler (directly to a Linux binary) instead of compiling to a APK with embedded machine code.

```bash
# Download the NDK
./build/tools/make-standalone-toolchain.sh --toolchain=x86-4.6 --install-dir=/home/rayoub1/dkadjox/android/toolchain
# You can now compile a program and load the program into the device
i686-linux-android-gcc -o hello hello.c
adb push hello /data/scl/programs/.

# On the device
cd /data/scl/programs/
chmod 777 hello
./hello
```


Data sources
------------

### Getting battery drain

Current drain given in uA

```bash
cat /sys/class/power_supply/battery/current_now
```


### Get FPS data in logcat

```bash
setprop debug.gr.calcfps 1
setprop debug.gr.calcfps.period 1
stop
start
adb logcat time -v
```


TGID information in ftrace
--------------------------

Only new versions of the kernel have TGID info in ftrace. Apply the patch below and recompile the kernel to add this information.

```
trace: Add an option to show tgids in trace output

The tgids are tracked along side the saved_cmdlines tracking, and can be
included in trace output by enabling the 'print-tgid' trace option. This is
useful when doing post-processing of the trace data, as it allows events to be
grouped by tgid.

Change-Id: I52ed04c3a8ca7fddbb868b792ce5d21ceb76250e
Signed-off-by: Jamie Gennis <jgennis@google.com>
diff --git a/kernel/trace/trace.c b/kernel/trace/trace.c
index 66338c4..0308e57 100644
--- a/kernel/trace/trace.c
+++ b/kernel/trace/trace.c
@@ -479,6 +479,7 @@
    "disable_on_free",
    "irq-info",
    "markers",
+   "print-tgid",
    NULL
 };

@@ -983,6 +984,7 @@
 static unsigned map_pid_to_cmdline[PID_MAX_DEFAULT+1];
 static unsigned map_cmdline_to_pid[SAVED_CMDLINES];
 static char saved_cmdlines[SAVED_CMDLINES][TASK_COMM_LEN];
+static unsigned saved_tgids[SAVED_CMDLINES];
 static int cmdline_idx;
 static arch_spinlock_t trace_cmdline_lock = __ARCH_SPIN_LOCK_UNLOCKED;

@@ -1130,6 +1132,7 @@
    }

    memcpy(&saved_cmdlines[idx], tsk->comm, TASK_COMM_LEN);
+   saved_tgids[idx] = tsk->tgid;

    arch_spin_unlock(&trace_cmdline_lock);
 }
@@ -1163,6 +1166,25 @@

    arch_spin_unlock(&trace_cmdline_lock);
    preempt_enable();
+}
+
+int trace_find_tgid(int pid)
+{
+   unsigned map;
+   int tgid;
+
+   preempt_disable();
+   arch_spin_lock(&trace_cmdline_lock);
+   map = map_pid_to_cmdline[pid];
+   if (map != NO_CMDLINE_MAP)
+       tgid = saved_tgids[map];
+   else
+       tgid = -1;
+
+   arch_spin_unlock(&trace_cmdline_lock);
+   preempt_enable();
+
+   return tgid;
 }

 void tracing_record_cmdline(struct task_struct *tsk)
@@ -2082,6 +2104,13 @@
    seq_puts(m, "#              | |       |          |         |\n");
 }

+static void print_func_help_header_tgid(struct trace_array *tr, struct seq_file *m)
+{
+   print_event_info(tr, m);
+   seq_puts(m, "#           TASK-PID    TGID   CPU#      TIMESTAMP  FUNCTION\n");
+   seq_puts(m, "#              | |        |      |          |         |\n");
+}
+
 static void print_func_help_header_irq(struct trace_array *tr, struct seq_file *m)
 {
    print_event_info(tr, m);
@@ -2092,6 +2121,18 @@
    seq_puts(m, "#                            ||| /     delay\n");
    seq_puts(m, "#           TASK-PID   CPU#  ||||    TIMESTAMP  FUNCTION\n");
    seq_puts(m, "#              | |       |   ||||       |         |\n");
+}
+
+static void print_func_help_header_irq_tgid(struct trace_array *tr, struct seq_file *m)
+{
+   print_event_info(tr, m);
+   seq_puts(m, "#                                      _-----=> irqs-off\n");
+   seq_puts(m, "#                                     / _----=> need-resched\n");
+   seq_puts(m, "#                                    | / _---=> hardirq/softirq\n");
+   seq_puts(m, "#                                    || / _--=> preempt-depth\n");
+   seq_puts(m, "#                                    ||| /     delay\n");
+   seq_puts(m, "#           TASK-PID    TGID   CPU#  ||||    TIMESTAMP  FUNCTION\n");
+   seq_puts(m, "#              | |        |      |   ||||       |         |\n");
 }

 void
@@ -2389,9 +2430,15 @@
    } else {
        if (!(trace_flags & TRACE_ITER_VERBOSE)) {
            if (trace_flags & TRACE_ITER_IRQ_INFO)
-             print_func_help_header_irq(iter->tr, m);
+               if (trace_flags & TRACE_ITER_TGID)
+                   print_func_help_header_irq_tgid(iter->tr, m);
+               else
+                   print_func_help_header_irq(iter->tr, m);
            else
-             print_func_help_header(iter->tr, m);
+               if (trace_flags & TRACE_ITER_TGID)
+                   print_func_help_header_tgid(iter->tr, m);
+               else
+                   print_func_help_header(iter->tr, m);
        }
    }
 }
@@ -3074,9 +3121,53 @@
 }

 static const struct file_operations tracing_saved_cmdlines_fops = {
-  .open       = tracing_open_generic,
-  .read       = tracing_saved_cmdlines_read,
-  .llseek    = generic_file_llseek,
+   .open   = tracing_open_generic,
+   .read   = tracing_saved_cmdlines_read,
+   .llseek = generic_file_llseek,
+};
+
+static ssize_t
+tracing_saved_tgids_read(struct file *file, char __user *ubuf,
+               size_t cnt, loff_t *ppos)
+{
+   char *file_buf;
+   char *buf;
+   int len = 0;
+   int pid;
+   int i;
+
+   file_buf = kmalloc(SAVED_CMDLINES*(16+1+16), GFP_KERNEL);
+   if (!file_buf)
+       return -ENOMEM;
+
+   buf = file_buf;
+
+   for (i = 0; i < SAVED_CMDLINES; i++) {
+       int tgid;
+       int r;
+
+       pid = map_cmdline_to_pid[i];
+       if (pid == -1 || pid == NO_CMDLINE_MAP)
+           continue;
+
+       tgid = trace_find_tgid(pid);
+       r = sprintf(buf, "%d %d\n", pid, tgid);
+       buf += r;
+       len += r;
+   }
+
+   len = simple_read_from_buffer(ubuf, cnt, ppos,
+                     file_buf, len);
+
+   kfree(file_buf);
+
+   return len;
+}
+
+static const struct file_operations tracing_saved_tgids_fops = {
+   .open   = tracing_open_generic,
+   .read   = tracing_saved_tgids_read,
+   .llseek = generic_file_llseek,
 };

 static ssize_t
@@ -5073,6 +5164,9 @@
    trace_create_file("saved_cmdlines", 0444, d_tracer,
            NULL, &tracing_saved_cmdlines_fops);

+   trace_create_file("saved_tgids", 0444, d_tracer,
+           NULL, &tracing_saved_tgids_fops);
+
    trace_create_file("trace_clock", 0644, d_tracer, NULL,
              &trace_clock_fops);

diff --git a/kernel/trace/trace.h b/kernel/trace/trace.h
index 2081971..183cfe8 100644
--- a/kernel/trace/trace.h
+++ b/kernel/trace/trace.h
@@ -574,6 +574,7 @@
 extern cycle_t ftrace_now(int cpu);

 extern void trace_find_cmdline(int pid, char comm[]);
+extern int trace_find_tgid(int pid);

 #ifdef CONFIG_DYNAMIC_FTRACE
 extern unsigned long ftrace_update_tot_cnt;
@@ -786,6 +787,7 @@
    TRACE_ITER_STOP_ON_FREE     = 0x400000,
    TRACE_ITER_IRQ_INFO     = 0x800000,
    TRACE_ITER_MARKERS      = 0x1000000,
+   TRACE_ITER_TGID         = 0x2000000,
 };

 /*
diff --git a/kernel/trace/trace_output.c b/kernel/trace/trace_output.c
index 697e88d..249e1e9 100644
--- a/kernel/trace/trace_output.c
+++ b/kernel/trace/trace_output.c
@@ -660,11 +660,25 @@
    unsigned long secs, usec_rem;
    char comm[TASK_COMM_LEN];
    int ret;
+   int tgid;

    trace_find_cmdline(entry->pid, comm);

- ret = trace_seq_printf(s, "%16s-%-5d [%03d] ",
-                comm, entry->pid, iter->cpu);
+   ret = trace_seq_printf(s, "%16s-%-5d ", comm, entry->pid);
+   if (!ret)
+       return 0;
+
+   if (trace_flags & TRACE_ITER_TGID) {
+       tgid = trace_find_tgid(entry->pid);
+       if (tgid < 0)
+           ret = trace_seq_puts(s, "(-----) ");
+       else
+           ret = trace_seq_printf(s, "(%5d) ", tgid);
+       if (!ret)
+           return 0;
+   }
+
+   ret = trace_seq_printf(s, "[%03d] ", iter->cpu);
    if (!ret)
        return 0;
```

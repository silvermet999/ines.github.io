---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T20:03:40Z"
Emoji: "\U0001F41D"
id: bafyreie4btdaoxk6wndxlputocup3r6ltuqfifq3gefe6ksargmuzsaayu
---
# eBPF/XDP   
## Increasing Throughput   
- 7000 bps throughput (sm passing through sm) of ML-based IDS. Need 1Gbps. How to increase using XDP/eBPF   
   
→ Offload and accelerate some pkt processing to run in the kernel before reaching userspace   
- **Why slow:** Bottleneck from kernel userspace data transfer / pkt copying / per-pkt overhead / ML exec time   
- **XDP to filter in kernel: **runs at the earliest pnt in the kernel ntw stack: drop unwanted traffic / filter to only relevant pkts / flow labeling or header feature extraction => reduce pkt rate to userspace and ML   
- **XDP/eBPF strategy for IDS:**   
    - XDP program: attached to NIC (filters...)   
    - eBPF maps: share extracted mtd with userspace (zero copy)   
    - AF-XDP socket: bypass kernel stack to directly receive raw pkts   
- **Implementation options:**   
    - XDP+eBPF: XDP filters pkts (exp. only TCP, specific ports) / parses headers and store features in eBPF hash map / userspace ML polls map, read features, classifies   
    - AF-XDP with batching: receive interesting pkts in userspace / pypass kernel overhead / batch pkts and run ML on batched tensors   
    - ML inference in eBPF (simple): if model is lightweight, a compiled C version can run inside eBPF   
- **Feature extraction in XDP:** headers you can extract (IP ptc/ source and best IP or port / TCP flags…)   
   
=> for header-based analysis   
- **Attach XDP program using IP or XDP-loader**   
- **Performance boost potential:** drop noise in XDP / header preprocessing / AF-XDP + batching   
- Increase from 7000 to 10G   
    - pkt filtering done by XDP / ML inference outside critical path / pkt parsing & mtd extraction in kernel / batch ML inference / AF-XDP / high perf NIC / NUMACPU   
    - Full DL inference on every pkt / use Python in hot path (freq exec code)   
    - Multi-core parallelism: 1RX queue per core / core pinning (task set, isolCPUs) / per core XDP workers or polling threads   
    - Benchmarking according to pkt rates / perf op to find bottleneck / eBPF run time profiling   
- 10 cores achieving 1G with XDP → XDP + multi-scaling can multiply throughput   
- Assuming 1 core = 1 Gbps: no single bottleneck in NIC, mem, PCIe / traffic is load balanced / no shared resources contention (demand > supply / you can linearly scaled perf with XDP + AF-XDP   
- **Achieve core level parallelism with XDP**   
    - Enable RSS (Receive Side Scaling), let NIC hash pkts flow to diff Rx queues / each RX queue maps to a CPU core / XDP prog will run per queue per core   
    - run AF\_XDP sockets per core (RX queue) to avoid lack contention and max CPU usage   
    - Pin processes and queus   
    - Use batching + zero copy model in AF\_XDP / ring buffers to read / write pkts in batches   
- **Bottlenecks that bill scaling:** poor NICs, IRQ (interrupt request), contention (→ pinning)/ NUMA (Non Uniformal Memory Access) mismatch.   
   
## Definition   
- **eBPF (Extended Berkeley Pkt Filter)** is an abstract VM that runs on Linux. Can exec user-defined progs inside a sandbox in the kernel. Enable writing low level monitoring, tracing or ntw progs   
- **XDP (eXpress Data Path)** is a framework to perform high speed pkt processing within BPF. Runs immediately as a pkt is received by the ntw ops.   
- XDP allows attaching eBPF to low-level hooks, implemented by ntw device drivers.   
- XDP include generic hooks that run after the device driver. Primarly uses kernel bypass for high perf processing → reduce overhead needed for the kernel bc it doesn’t need to process etc.   
- NIC (Control of the Ntw Interface) is transferred to eBPF when working at high ntw speeds (10 Gbps +)   
- eBPF write their own drivers   
- XDP run before pkt parsing   
   
=> eBPF must implement functionalities without relying on the kernel.   
- XDP can be attached to ntw interface, when a new pkt is received, XDP receive a callback and perform ops on the pkt.   
- **XDP can be connected using:**   
    - Generic: loaded into the kernel as part of the ordinary ntw path.   
    - Native: as part of its initial receive path. Require support from the ntw card driver   
    - Offload: loaded on the NIC and exec without using CPU. Require support from the ntw interface device.   
- **XDP ops:**   
    - XDP\_DROP   
    - XDP\_PASS: Fwd without processing   
    - XDP\_TX: fwd with/out notif   
    - XDP\_REDIRECT: bypass normal ntw stack & redirect via another NIC   
- XDP in eBPF can be used for DDOS mitigation & firewalling / fwding & load balancing / monitoring & flow sampling etc.   
- Steps in implementing XDP:   
    - Simple XDP prog   
    - Build eBPF prog   
    - Load eBPF prog   
    - Display status for running eBPF   
    - Unload XDP   
    - Analyze code for traffic & pkts   
- **XDP/eBPF linked to the subj:** design high perf, low latency secure data pipeline in IDS   
- Data filtering at kernel level   
- Adversarial input detection before model inference   
- Mitigation of adversarial traffic   
- In-kernel feature store or caching   
- **Architecture: **NIC → kernel space - fast path : XDP/eBPF + feature extraction → userspace bridge : eBPF collector daemon to read maps & emit JSON and feature vectors → feature vector preprocessor for normalization & transformation → DBSCAN clustering for outlier detection → Response & mitigation to block IP, alert & log   
- **eBPF maps:**   
    - Hash map BPF\_MAP\_TYPE\_HASH: kernel (C) / userspace (bcc)   
    - Array map (for fixed arrays) BPF\_MAP\_TYPE\_ARRAY: kernel (C) / userspace (C)   
    - Per-CPU hash map (each CPU has a copy → reduce contention) BPF\_MAP\_TYPE\_PERCPU\_HASH: kernel (C)/ userspace (bpf map lookup elem())   
    - LRU hash map (auto evicting for flow tracking) BPF\_MAP\_TYPE\_LRU\_HASH   
    - Ring buffer (send event from kernel to user space) BPF\_MAP\_TYPE\_RINGBUF: kernel (C)/ userspace (libbpf/bcc) => Load eBPF maps with bpf tools   
   
   
*Credits: [https://www.tigera.io/learn/guides/ebpf/ebpf-xdp/](https://www.tigera.io/learn/guides/ebpf/ebpf-xdp/) *   
   
- DPDK allows to define traffic patterns using CLI, LUA or PCAP   
- Traffic gen is what's running DPDK packaging with 2 ports; port 0 → eno 2 → eno 4 → port 1   
- Packet going out of port 0 = pkt going in port 1    
- Check device under test for CPU load, cores used   
- NIC given a large receive buffer (ethtool)   
- Problem: CPUs are not getting faster, vendors put more CPUs & cores instead => multithreading & multicore archi   
- How to load balance multiple pkt received   
- Receive queues: # cores = # received queues bc it has an interrupt handler served by a dedicated core   
- Testing one flow bc it's served on the same queue, handled by the same interrupt handler, served by one core   
- Exp: elephant flow in ipsec; one big flow (also for budget)   
- SoftIRQ during DDOS attack: increase in interrupts → pkt / connection analysis   
- The principle of Userland ntw is that the ntw stack is no longer handled by the kernel => DPDK is a Userland NIC driver: NIC is no longer visible to the kernel   
- Fast for sending/receiving pkts & pkt gen but you need to do everything in Userland   
- Exp: can't use nginx or ssh etc. bc they need sockets to bind on → DPDK is just a driver   
- DPDK is fast bc it's bypassing codes in the kernel: PMD (Pull Mode Driver) ≠ IRQ interrupt bc you give it 1 or 2 CPUs → DPDK is always pulling for traffic to build ntw specific apps   
- On top of DPDK (the pkt put in mem): packaging → gen the pkt & printing stat   
- To build a router using Linux, we need fwding btw 2 DPDK NICs, exp: VPP (Vector Packet Processing) → can do tunneling ptc, netting etc.   
- XDP solves the FIB problem; bpf\_fib\_lookup() → return a nxt hop interface idx num according to the routing table → solves control plane problem   
- XDP is slightly slower   
   
   
*Credits: [https://youtu.be/hO2tlxURXJ0?si=99jFULYu2fY9Wrbn](https://youtu.be/hO2tlxURXJ0?si=99jFULYu2fY9Wrbn) *    
   
- LLVM is a collection of modular and reusable compiler tech   
- Clang is an LLVM native, C's compiler    
- The kernel contains JIT compiler that translates eBPF bytes into native machine code for perf   
- (limited) C → eBPF bytecode → machine code   
   
**(Limited) C**   
**ELF obj file**   
——————-   
eBPF opcode ——————————————⟶   
eBPF maps ——-———————-—————⟶   
**User space**   
eBPF syscall   
BPF\_PROG\_LOAD ————————-—⟶   
   
BPF\_MAP\_CREATE —————————⟶   
Attach BPF prog to an event   
Read / write maps   
BPF\_MAP\_GET\_NEXT\_KEY   
BPF\_MAP\_LOOKUP\_ELEM ←——————   
BPF\_MAP\_UPDATE\_ELEM   
BPF\_MAP\_DELETE\_ELEM   
**Kernel**   
——————-   
Verifier -   
→ BPF VM ←   
→ Maps ←   
\|   
\|   
\|   
-   
- seccomp-bpf: blacklisting / whitelisting syscalls, exp: Docker's default seccomp profile    
   
→ uses classic BPF → can't deref syscalls & can't use maps   
=> Landlock: uses eBPF (in dev)   
   
*Credits: [https://youtu.be/4SiWL5tULnQ?si=TF9Zkt0f8xR0Qhou](https://youtu.be/4SiWL5tULnQ?si=TF9Zkt0f8xR0Qhou) *   
   

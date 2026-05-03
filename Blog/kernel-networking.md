---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T20:03:40Z"
Emoji: "\U0001F427"
id: bafyreic5zvwph3jubwbtabkpciamxfxyvp27owz6gllaptsoljsvr66lwe
---
# Kernel Networking   
## Pkt Tracing   
- SYN, SYN ACK, ACK, ACK, PSH ACK len=6 (hello ), ACK ack=7 (len+1), RST ACK   
   
Syscall: user program request the kernel to perform privileged operation → in the cxt of syscall, its processor privilege (x86's ring 0 vs ring 3)   
## Type of privileges:   
- **Processor / hdw:**   
    - kernel runs in kernel mode (ring 0 on x86) → can exec any CPY instruction   
    - User prog run in user mode (ring 3)   
   
=> restricted instruction set   
  - Syscalls switch from user mode to kernel mode   
- **User account privileges (root vs regular)**: enforced by the kernel   
- **Exp: read()**   
    - CPU switches from user mode to kernel mode (processor privilege)   
    - Kernel mode exec with full hdw access   
    - Kernel check permission   
   
## Server Thread: process: ncat   
- In blue: exec in kernel => syscall, exec, socket, bind, listen, select etc.   
- In green: user mode   
- Family = 2 => IPV4   
- **File descriptor (fd):** ret = 3 (return)   
    - Linux assigns int to every file it opens for a process   
    - ret = 0 → std input   
    - ret = 1 → std output   
    - ret = 2 → std error   
    - ret > 3 → file   
   
## Syscalls   
- Set the socket option then bind the socket (data structure with buffer) to a certain ntw interface + port   
- Listens on the socket created for any SYN req running on the same fd   
- Otherflags sets (gets)   
- **Select: **wait on a number of fd for events (read(), write()…)   
- fd that user is waiting on for reading: read\_fds = [0x8] ← value of 8 bitmask   
- Bitmask: seq of bits used to manipulate, test or represent a set of bool flags/options   
- After select: wait blocked   
- Before select: wait for CPU   
   
## Steps   
1. Wait for SYN req   
2. Wait for CPU => ret = 1 (something being typed on server side, kernel unblock thread) => read\_fds=[0x1]   
3. Select (syscall)   
4. User mode   
5. Accept (syscall) → fd = 3, ret = 4 (non negative = accepted) => Connected socket created, assigned fd 4   
6. Close the listen socket & going to work on the data socket   
7. Other flags   
8. Select: wait for user to do something → read\_fds = [0x11] => waiting on more fds than before   
9. 11 hex means that fd 4 has the bitmap   
10. LSB (Least Significant Bit) = 1: bit 0 (LSB) =1 so it monitors the std input (11=1011)   
11. Wait for SYN req   
   
## Unblocking (2.):   
- soft IRQ ntw being triggered (hard IRQ is the actual interrupt) => telling us that something is being received   
- IRQ 198 is a hard interrupt before (close to soft interrupt) → actual hdw interrupt for wireless interface   
- Kernel thread triggered after, related to wifi-interface   
- IRQ\_handler\_entry: hdw interrupt service routine is being exec   
- IRQ\_handler\_exit   
   
## Ntw receiver analysis:   
- Read syscalls: fd = 0 → read from std input   
- Sendto syscall: send char typed over ntw (fd=4)   
- Before the select syscall, there’s a pkt coming from client: it’s a RST ACK → call the server to close the connection → the select syscall is unblocked   
- recvfrom syscall on fd=4   
- Close syscall   
- exit\_group   
- Gray: quitting   
   
=> When client hit ctrl Q to quit (or similar)   
- fd can be a socket or a normal file → how to know: using lttng   
- lttng shows events of diff traces   
   
## ncat Analysis:   
- In lttng\_state\_dump, you can find whether or not it’s a socket   
- In proc/PID/fd, you can find all fds currently open for a process: stdin, stdout, stderror, thrid fd (exp: socket[inode], inode is an index into md for the file sys)   
   
## lttg state dump events:   
- One for each process   
- fds have a process scope → 2 diff processes can have the same   
   
## fd number but assigned diff files   
- The scope of a fd is the process   
- All the threads of a certain process have the same fd naming → fd 10 in thread 1 = fd 10 in thread 2   
   
## sendto write events: (filter by CPU in select syscall)   
1. syscall\_entry\_sendto   
2. net\_dev\_queue: a pkt being queued into the ntw device   
3. skb\_consume: skb = socket buffer   
4. net\_dev\_xmit: ntw device transmitted   
5. syscall\_exit\_sendto   
   
→ the pkt capture what happens right btw queue & consume   
## net\_dev\_queue   
- Look for trace\_net\_dev\_queue in dev.c file in the kernel code   
- trace\_net\_dev\_queue is triggered when you trace for net\_‘\*’ in lttng in the ptc stack & you see net\_dev\_queue in ncat   
- The md of the pkt is being passed along the ptc stack   
   
## Accept   
- Accept the SYN packet after the select syscall   
   
→ Tricky to find bc it’s not happening sync   
- ncat thread is blocked, an interrupt comes and async triggers a chain of exec that is responsible for handling the received pkt (send or data)   
- The interrupt happens when CPU is unblocked & waiting   
- sched\_waking: context\_procname = irq/198-iwlwifi. Context\_procname is a kernel thread responsible for handling interrupts coming from wifi interface, the thread is waking the server up   
- sched\_wakeup   
   
## Resource View   
- Hdw interrupt then sfw interrupt in CPU resource   
- Interrupt earlier by IRQ 198 → result in interrupt by softIRQ ntw (for ntw receive to be triggered)   
- Waiting for SYN   
   
## Accept Steps   
1. Before waking up, there’s irq\_handler\_entry → the IRQ is the hard IRQ 198 (iwlwifi)   
2. irq\_handler\_exit   
3. sched\_waking: of the kernel thread with IRQ 198   
4. sched\_wakeup   
5. sched\_switch: to the iwlwifi thread → the pkt needs to be processed & handled by the kernel thread before handing it out to ncat   
6. irq\_softirq\_raise: where the bulk of the service routine happens =>Why there are soft IRQ and hard IRQ: handling interrupts runs on privileged state => highest priority goes to hdw: we don’t want the CPU & resources busy during hdw interrupt, we delegate soft interrupts to sfw   
7. irq\_softirq\_entry   
8. net\_napi\_gro\_receive\_entry: sfw interrupt routine when doing ntw receive   
9. net\_napi\_gro\_receive\_exit   
10. net\_if\_receive\_skb (if=interface)   
11. skb\_consume   
12. napi\_poll: ntw interface goes back into pulling mode to see if there’s more pkt   
13. irq\_softirq\_exit   
14. sched\_switch: end of thread   
   
## In CPU   
1. Hdw IRQ   
2. Service it   
3. Delegate to sfw IRQ   
4. Service it: what it does is that it receives pkt & guide it (fwd it) & process it through the ntw stack from bottom up → copies it into the fd of the socket of the app (ncat in this case), that’s waiting on it   
5. Sched out → expect the select to return   
   
**3-way handshake & CPU unblocking: **3-way handshake happens while waiting on the CPU to unblock => waiting forconfirmation on the client’s commitment → finish with select()   
When the SYN happens in the kernel:   
- pkt from the same trigger the same hdw interrupt line   
- Exp: interrupt IRQ 198 → another IRQ 198 around the time that is before receiving the SYN   
- in net\_if\_receive\_skb: ntw\_header = IPV4 & ptc=TCP, the client address, the server address, etc.   
- Flag: 0x002 = SYN   
- SYN consuming (skb consume) → net\_dev\_xmit → [SYN, ACK]   
- IRQ\_handler\_entry after exit & switch is the ACK   
- After exit: ncat : select(), accept(), recvfrom()   
- When sentto(), there’s an interrupt bc there’s the std input in fd   
- Receive from   
- After sched\_switch, syscall\_entry\_recvfrom   
- In CPU, after sched\_wakeup: net\_napi\_gra\_receive\_entry, net\_napi\_gra\_receive\_exit, net\_if\_receive\_skb   
- Flag 0x014 → of [RST, ACK], ACK   
- Interrupt before ACK   
- Flag 0x010 → of AC   
- The TCP implementation is inside the kernel, which takes care of the ACK   
- If the app had any pkt to send, it would’ve been notified   
- It didn’t because it wasn’t blocked on sending   
- Relate fd to what it is (the socket)   
- You can find src-dest pair: IP & port belonging to the socket using cat proc/net/tcp (the IP & port in HEX)   
   
   
*Credits: [https://www.youtube.com/watch?v=ck4WvYM9V4c](https://www.youtube.com/watch?v=ck4WvYM9V4c) *   
   

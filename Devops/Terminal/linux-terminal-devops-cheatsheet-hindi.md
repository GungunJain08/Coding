# Linux Terminal DevOps Cheat Sheet 

## Table of Contents
1. [Process Monitoring (Processes ko Track aur Manage Karna)](#1-process-monitoring-processes-ko-track-aur-manage-karna)
2. [Performance Monitoring (System Health & Resources Check Karna)](#2-performance-monitoring-system-health--resources-check-karna)
3. [Networking Tools (Network Issues & Ports Troubleshooting)](#3-networking-tools-network-issues--ports-troubleshooting)
4. [Text Manipulation (Logs Analysis & Text Processing)](#4-text-manipulation-logs-analysis--text-processing)
5. [Real-World DevOps Troubleshooting Workflows (Pro Scenarios)](#5-real-world-devops-troubleshooting-workflows-pro-scenarios)

---

## 1. Process Monitoring (Processes ko Track aur Manage Karna)

Production server par kaunsa application run ho raha hai, kitna memory le raha hai, aur agar koi process stuck ho jaye to use kill kaise karein—yeh sab process monitoring me aata hai.

### Commands & Explanations:

#### A. `ps` (Process Status)
Current running processes ka snapshot dekhne ke liye.
```bash
# System ke saare running processes ko detailed view me dekhne ke liye (Sabse zyada use hota hai)
ps aux

# Details check karne ke liye (e: Every process, f: Full-format listing)
ps -ef

# Kisi specific user ke running processes check karne ke liye
ps -u gunnu

# Process tree format me hierarchy dekhne ke liye (Parent-Child relationship)
ps -ef --forest
```

#### B. `top` & `htop` (Real-Time Process Monitoring)
System resource utilization ko live dekhne ke liye.
```bash
# Default interactive resource monitor (har Linux system par pre-installed hota hai)
top

# Modern, colorized aur interactive process manager (highly recommended in industry)
htop
```
> **`htop` Shortcuts (Pro Tips):**
> * **`F6`**: Sort processes by CPU, Memory, PID, etc.
> * **`F9` / `k`**: Kill a process directly from the UI.
> * **`F5`**: Tree view structure dekhne ke liye.
> * **`u`**: Kisi specific user ke processes filter karne ke liye.

#### C. `lsof` (List Open Files)
Linux me *"Everything is a file"*. Yeh command batata hai ki kaunsi process kis file, folder ya network port ko use kar rahi hai.
```bash
# Pata karne ke liye ki port 8080 par kaunsi process listen kar rahi hai (Very Important!)
lsof -i :8080

# Kisi specific user dwara open kiye gaye saare files/sockets dekhne ke liye
lsof -u username

# Kisi specific directory ke andar open files check karne ke liye
lsof +D /var/log/nginx
```

#### D. `fuser` (Find User Processes)
Specific file, directory, ya port ko use karne wale processes ki PID find aur kill karne ke liye.
```bash
# Port 8080 ko use karne wali process ki PID dekhne ke liye
fuser 8080/tcp

# Port 8080 ko consume karne wali process ko forcefully kill karne ke liye (Immediate release)
fuser -k 8080/tcp
```

#### E. `kill`, `pkill`, & `killall` (Process Terminate Signals)
Stuck ya unresponsive processes ko terminate karne ke liye signals send karna.
```bash
# Standard/Graceful termination (SIGTERM - Signal 15). Application ko cleanly exit hone ka time deta hai.
kill <PID>

# Forceful termination (SIGKILL - Signal 9). Application ko instantly kill kar deta hai. (Use only when SIGTERM fails)
kill -9 <PID>

# Process ke naam se process kill karne ke liye (PID ki zaroorat nahi hai)
pkill nginx

# Ek hi naam ke saare processes ko ek sath kill karne ke liye
killall node
```

#### F. `pgrep` & `pstree`
```bash
# Nginx process ki sirf PID(s) list karne ke liye
pgrep nginx

# Parent and child processes ka process hierarchy tree dekhne ke liye
pstree -p
```

#### G. Background & Daemon Process Management (`bg`, `fg`, `jobs`, `nohup`)
```bash
# Apne script ko backgroud me run karne ke liye (Command ke end me & lagayein)
./script.sh &

# Running background jobs ki list dekhne ke liye
jobs

# Background job number 1 ko foreground (active terminal screen) me lane ke liye
fg %1

# Background me paused job ko run state me dalne ke liye
bg %1

# Terminal session close hone ke baad bhi script/process ko background me chalte rehne ke liye (Outputs written to nohup.out)
nohup ./long-task.sh &
```

---

## 2. Performance Monitoring (System Health & Resources Check Karna)

Server performance issues troubleshoot karte waqt CPU, Memory, Disk Space aur IO bottlenecks check karne ke essential commands.

### Commands & Explanations:

#### A. `free` (Memory / RAM Status)
System ki physical RAM and Swap memory usage dekhne ke liye.
```bash
# Human-readable format me RAM status dekhne ke liye (GB/MB - Best practice)
free -h

# Swapping block status aur memory counters real-time updates ke liye (har 2 seconds par refresh)
free -h -s 2
```

#### B. `df` (Disk Free Space)
Storage disks (Filesystems) ki available space aur mount points check karne ke liye.
```bash
# Human-readable format me disk usage report
df -h

# Sirf local physical disks show karne ke liye (Virtual network mounts like NFS skip karne ke liye)
df -lh
```

#### C. `du` (Disk Usage by Directory)
Pata karne ke liye ki kis file ya folder ne sabse zyada space occupy kiya hai.
```bash
# Current directory ke har file/folder ka size summary show karein
du -sh *

# Size ke descending order me top 10 largest folders/files filter karna (Highly Used!)
du -ah . | sort -rh | head -n 10
```

#### D. `uptime` (Server Load Average)
Server kitne time se up hai aur uspar kitna workload load hai.
```bash
uptime
```
> **Load Average Interpretation:**
> output me 3 values milti hain: **Last 1 min, 5 min, aur 15 min** ka CPU workload.
> * Agar load average CPU cores se zyada ho (e.g., 4 Core CPU par load 8.00 hai), iska matlab system CPU resource bottlenecks face kar raha hai.

#### E. `vmstat` (Virtual Memory Statistics)
System ke processes, memory, swap, I/O, system activity, aur CPU activity ke real-time details ke liye.
```bash
# Har 1 second par updates display karein, total 5 times run hoga
vmstat 1 5
```

#### F. `iostat` (Input/Output Storage Statistics)
Storage disks ke input/output load aur CPU utilization ko analyze karne ke liye. Useful jab server slow storage read/write limits face kar raha ho.
```bash
# Disk read/write throughput aur metrics 2-2 seconds ke interval par check karein
iostat -xz 2
```

#### G. `sar` (System Activity Reporter)
Pichle kuch dino ya ghanto ki historical system performance reports retrieve karne ke liye (packages like `sysstat` required).
```bash
# Aaj ki historical CPU usage reports dekhne ke liye
sar 1 3

# RAM memory usage status ki history report
sar -r
```

#### H. `mpstat` (Multi-Processor Statistics)
Har single CPU core ki utilization check karne ke liye.
```bash
# Check CPU core load independently
mpstat -P ALL 1
```

#### I. `dmesg` (Kernel Ring Buffer logs)
Hardware failures, memory issues, OOM (Out of Memory) kills, ya driver errors debug karne ke liye.
```bash
# Pata karne ke liye ki kya kernel ne kisi process ko OOM Killer ke zariye force kill kiya hai
dmesg -T | grep -i "oom"
```

---

## 3. Networking Tools (Network Issues & Ports Troubleshooting)

Microservices architectures me API connectivity failures, DNS resolution issues, aur remote server reachability check karne ke scripts.

### Commands & Explanations:

#### A. `ping`
Check karein ki target host alive hai ya nahi.
```bash
# Google DNS connection verify karne ke liye (sirf 4 packets send karein)
ping -c 4 8.8.8.8
```

#### B. `traceroute` & `mtr` (My Traceroute)
Packets ka network route trace karne ke liye. Pata lagayein ki latency kis hop/router par badh rahi hai.
```bash
# Traceroute network path
traceroute google.com

# Live diagnostic tool jo ping aur traceroute dono ka kaam real-time stats ke sath karta hai
mtr google.com
```

#### C. `ss` & `netstat` (Socket / Port Status)
Server par kaun-kaun se ports open hain aur unke current state kya hain. Note: `ss` is the modern and faster replacement for `netstat`.
```bash
# Active listening sockets (TCP and UDP) details unke PID aur Process names ke sath
# t: TCP, u: UDP, l: Listening, p: Process, n: Numeric ports
ss -tulpn

# Netstat alternative (purane servers par useful)
netstat -tulpn
```

#### D. `curl` & `wget` (Web Requests and Downloads)
Terminal se APIs trigger karne ya files pull karne ke liye.
```bash
# Target API ke headers inspect karne ke liye (Bina complete body pull kiye response status code check karna)
curl -I https://api.github.com

# Detailed verbose mode connection logs check karne ke liye (SSL certificates, DNS resolution delays, handshake check)
curl -v https://example.com

# HTTP Method specifier ke sath API call
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' https://api.example.com/data

# Remote source se directly local file download karna
wget -O setup.sh https://get.docker.com
```

#### E. `dig` & `nslookup` (DNS Query Troubleshooting)
Check karein ki local/public DNS servers par dynamic domain name correctly IP address me translate ho rahe hain ya nahi.
```bash
# DNS Record verification
dig example.com

# Fast lookup details clean structure me target IP nikalne ke liye
dig example.com +short

# Specific DNS record type query (e.g. TXT, MX records lookup)
dig example.com MX
```

#### F. `ip` & `ifconfig` (Network Interface Setup)
```bash
# System ke saare network interfaces aur unke assigned IP addresses show karein
ip addr

# Linux Routing table lookup (Default gateway check karne ke liye)
ip route
```

#### G. `tcpdump` (Network Packet Sniffer)
Production interface levels par transparent network packet tracing and packet inspection.
```bash
# Interface eth0 ke port 80 ke dynamic web traffic packets capture and print karein
sudo tcpdump -i eth0 port 80

# Captured bytes limits capture file (.pcap) me save karein taaki use Wireshark par analyze kiya ja sake
sudo tcpdump -i eth0 -w traffic.pcap
```

#### H. `nc` / `netcat` & `telnet` (Network Swiss Army Knife)
Remote port test karne ke liye fast connections.
```bash
# Port open/listening check (z: zero-I/O mode, v: verbose)
nc -zv 192.168.1.50 22

# Basic raw TCP listener start karein (Port 9000 testing ke liye)
nc -l 9000

# Telnet alternative remote service validation
telnet 192.168.1.50 80
```

---

## 4. Text Manipulation (Logs Analysis & Text Processing)

Nginx access logs, application error logs, ya config files analyze aur parse karne ke tools. DevOps me in tools ki command command-chaining pipeline (`|`) ke sath commands run kiye jaate hain.

### Commands & Explanations:

#### A. `grep` & `egrep` (Global Regular Expression Print)
Files me pattern search karne ke liye.
```bash
# Case-insensitive search inside file
grep -i "error" app.log

# Current directory aur subdirectories ke andar recursively pattern search karna (with line number)
grep -rnw "." -e "exception"

# Matched lines ko invert karna (jis line me "INFO" na ho, use search karein)
grep -v "INFO" app.log

# Multiple pattern search (Egrep capability)
egrep -i "error|critical|fatal" app.log
```

#### B. `awk` (Pattern Scanning & Columns Processing)
Table format structured data (CSV, logs, processes) me se column-based manipulation aur filtering.
```bash
# Nginx access log file se sirf first column (Client IPs) print karne ke liye
awk '{print $1}' /var/log/nginx/access.log

# Agar condition match kare to dynamic actions print karein (Memory total column filter)
free | awk '/Mem:/ {print "Used Memory: " $3 " MB"}'
```

#### C. `sed` (Stream Editor)
In-place search-and-replace ya specific line operations bina file open kiye.
```bash
# config.json ke pehle occurrence "debug: false" ko "debug: true" se badle (Dry Run)
sed 's/debug: false/debug: true/' config.json

# File me directly overwrite/update karne ke liye (-i: in-place modification)
sed -i 's/db_port: 3306/db_port: 3307/g' config.yaml

# 5th line se lekar 10th line tak delete karne ke liye
sed '5,10d' input.txt
```

#### D. `cut` & `tr`
```bash
# Delimiter system ke column fields extraction (e.g. CSV fields extraction comma bases)
cut -d ',' -f 1-3 list.csv

# Uppercase string conversion inputs
echo "hello world" | tr '[:lower:]' '[:upper:]'

# Carriage returns/Windows spaces remove karna Linux execution error avoidance ke liye
tr -d '\r' < windows_file.sh > linux_file.sh
```

#### E. `sort` & `uniq`
```bash
# Numeric sorting (n) along with reversal order (r)
sort -nr numbers.txt

# File se duplicate lines remove karke unique lines count output show karna
sort file.txt | uniq -c

# Count duplicates and sort them by highest occurrence count (Highly Used in parsing logs!)
sort file.txt | uniq -c | sort -nr
```

#### F. `wc` (Word Count)
```bash
# Total lines count find karna (Useful to count total error logs matches)
grep "ERROR" app.log | wc -l
```

#### G. `head` & `tail` (Top & Bottom file views)
```bash
# File ke first 15 lines read karein
head -n 15 app.log

# File ke last 20 lines print karein
tail -n 20 app.log

# Live real-time stream check (Log Tailing)
tail -f /var/log/syslog
```

#### H. `xargs` (Argument List Builder)
Standard output inputs ko parameters ke tarah pick karke dynamic executions flow banana.
```bash
# Saare '.log' files find karke unhe delete karne ke liye
find . -name "*.log" | xargs rm -f

# Docker stack clean: saare stopped containers delete karne ke liye
docker ps -aq | xargs docker rm
```

#### I. `tee` (T-pipe output redirect)
Stdout data capture and redirection files mapping.
```bash
# Build task triggers outputs screen par dikhate hue terminal validation file me persist karna
./build.sh | tee build_history.log
```

---

## 5. Real-World DevOps Troubleshooting Workflows (Pro Scenarios)

Daily operations me standard pipelines commands kaise combine hoti hain uske live cases:

### Scenario A: Disk space 100% full ho chuka hai. Mujhe check karna hai kis directory ne memory block ki hai.
```bash
# 1. Mount allocation and system level blocks summary check
df -h

# 2. Main slash (root) level se human readable sizes and reverse sort
sudo du -h --max-depth=1 / | sort -hr | head -n 10
```

### Scenario B: Nginx application page 502 error de raha hai. Port connection checks?
```bash
# 1. Nginx service process alive status verify
ps aux | grep nginx

# 2. Listen block check (Port 80/443 ports listen validation)
ss -tulpn | grep -E '80|443'

# 3. Last 100 lines logs debug error tracking
tail -n 100 /var/log/nginx/error.log
```

### Scenario C: Nginx Access Log analyze karke top 10 unique Client IPs extraction.
```bash
# 1st column client IP extracts -> sorted -> unique counts matched -> reverse sorted -> top 10 listed
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr | head -n 10
```

### Scenario D: System RAM usage bohot high dikha raha hai, top 5 memory-hungry processes list kijiye.
```bash
# ps aux memory sorting logic mapping
ps aux --sort=-%mem | head -n 6
```

### Scenario E: Backend microservice port busy error de rahi hai. Port terminate workflow.
```bash
# 1. Port listener PID lookup
lsof -i :8082

# 2. Signal level clean termination
kill -15 <PID_extracted>

# 3. Force exit (if doesn't terminate automatically)
kill -9 <PID_extracted>
```

---

> **Pro Tip**: Agar kabhi command options aur details bhool jao, to direct terminal me `man <command>` (e.g. `man awk`) use karo. Us command ke details and documentation system par hi load ho jayenge.

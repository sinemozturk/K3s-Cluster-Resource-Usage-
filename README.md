# k3s Cluster Setup on EC2

## We will launch following instances

|EC2 AMI Type|Architecture|Instance Type| VCPUS |Memory (GiB)|
|---|---|---|---|---|
|Ubuntu Server 22.04| 64-bit (x86)| t2.nano| 1  | 0.5|
|Ubuntu Server 22.04| 64-bit (x86)| t2.micro| 1  | 1 |
|Ubuntu Server 22.04| 64-bit (x86)| t2.small| 1  | 2 |
|Ubuntu Server 22.04| 64-bit (x86)| t3a.nano| 2  | 0.5|
|Ubuntu Server 22.04| 64-bit (x86)| t3a.micro| 2  | 1 |
|Ubuntu Server 22.04| 64-bit (x86)| t3a.small| 2  | 2  |

### t2.micro

- Before installing k3s on the instaces we need to check resources current status with `top` command on `master node`

```bash
top
```

- ouput of `top` command

```bash
...
Tasks:  96 total,   1 running,  95 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :    967.9 total,    402.4 free,    178.5 used,    386.9 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.    640.5 avail Mem

PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                               
   1298 ubuntu    20   0   17296   7984   5580 S   0.3   0.8   0:00.02 sshd
      1 root      20   0  101828  12760   8256 S   0.0   1.3   0:03.64 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 netns
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H-events_highpri
      8 root      20   0       0      0      0 I   0.0   0.0   0:00.14 kworker/0:1-cgroup_destroy
      9 root      20   0       0      0      0 I   0.0   0.0   0:00.05 kworker/u30:0-events_unbound
     10 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percpu_wq
     11 root      20   0       0      0      0 S   0.0   0.0   0:00.00 rcu_tasks_rude_
     12 root      20   0       0      0      0 S   0.0   0.0   0:00.00 rcu_tasks_trace
     13 root      20   0       0      0      0 S   0.0   0.0   0:00.05 ksoftirqd/0
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.37 rcu_sched
     15 root      rt   0       0      0      0 S   0.0   0.0   0:00.01 migration/0
     16 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     17 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0
     18 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kdevtmpfs
     19 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 inet_frag_wq
     20 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 khungtaskd
     22 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
     23 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 writeback
     24 root      20   0       0      0      0 S   0.0   0.0   0:00.04 kcompactd0
     25 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd
     26 root      39  19       0      0      0 S   0.0   0.0   0:00.00 khugepaged
     72 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kintegrityd
     73 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kblockd
     74 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 blkcg_punt_bio
     75 root      20   0       0      0      0 S   0.0   0.0   0:00.00 xen-balloon
     76 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 tpm_dev_wq
     77 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 ata_sff
```

- or we can use `free -` command to see specific resource usage. To see usage of command;

```bash
free - 
```

- output;

```bash
...

Usage:
 free [options]

Options:
 -b, --bytes         show output in bytes
     --kilo          show output in kilobytes
     --mega          show output in megabytes
     --giga          show output in gigabytes
     --tera          show output in terabytes
     --peta          show output in petabytes
 -k, --kibi          show output in kibibytes
 -m, --mebi          show output in mebibytes
 -g, --gibi          show output in gibibytes
     --tebi          show output in tebibytes
     --pebi          show output in pebibytes
 -h, --human         show human-readable output
     --si            use powers of 1000 not 1024
 -l, --lohi          show detailed low and high memory statistics
 -t, --total         show total for RAM + swap
 -s N, --seconds N   repeat printing every N seconds
 -c N, --count N     repeat printing N times, then exit
 -w, --wide          wide output

     --help     display this help and exit
 -V, --version  output version information and exit
 ```

- example usage of free command

```bash
free -b
```

- output Note; b refers to `bytes`

```bash
...
               total        used        free      shared  buff/cache   available
Mem:      1014915072   185806848   421994496      851968   407113728   672935936
Swap:              0           0           0
```

------------------------------------------------------------------------------------

## K3s Installation

- Switching ubuntu account to root account in both instances

```bash
sudo su - 
```

- Hostname change of the nodes, so we can discern the roles of each nodes. For example, you can name the nodes (instances) like `k3s-master, k3s-worker`

```bash
hostnamectl set-hostname <node-name-master-or-worker>
hostnamectl set-hostname k3s-worker `example of worker node name `
```

```bash
bash
```

- Update the instances with following bash commands apply seperately

```bash
apt-get install -y
apt-get update -y
```

- Now we need to install k3s on `master node`

```bash
curl -sfL https://get.k3s.io | sh -
```

- Check the status of k3s on `master node`

```bash
systemctl status k3s
```

- or following code

```bash
systemctl status k3s.service
```

- Check the nodes of k3s on `master node`

```bash
kubectl get node
```

- We need TOKEN to create a cluster and connect the `master` and `worker` node. Run following command. Save the TOKEN we will use.

```bash
cat /var/lib/rancher/k3s/server/node-token
```

- We need to export some variables on `worker node` so go worker node (worker ec2) we need to provide the master node's `private IP` address and `TOKEN` Note; API server is listening of 6443 port

```bash
curl -sfL https://get.k3s.io | K3S_URL=https://private-Ip-of-master-ec2:6443 K3S_TOKEN="paste here the token" sh -
```

- example of usage;

```bash
curl -sfL https://get.k3s.io | K3S_URL=https://172.31.1.164:6443 K3S_TOKEN=K10491ae28afd8a4e6da888119bec526d8434baf94fa009f6eeb3b7214fb52091b4::server:6fc923688e802b57080295cc140b29dd sh -
```

- Check the status of k3s on `worker node`

```bash
systemctl status k3s-agent
```

- Check the nodes of k3s on `master node`

```bash
kubectl get node
```

- Output;

```bash
...
NAME     STATUS   ROLES                  AGE     VERSION  
master   Ready    control-plane,master   18m     v1.24.3+k3s1
worker   Ready    <none>                 9m57s   v1.24.3+k3s1
```

------------------------------------------------------------------------------------

## k3s Cluster Resource use on EC2

- To check the CPU and MEMORY (resources) usage of the K3s nodes

```bash
kubectl top node <node name>
```

- example:

```bash
kubectl top node master
```

- output of the above bash command

```bash
...
NAME     CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
master   105m         5%     807Mi           85%
```


------------------------------------------------------------------------------------

- Resource usage `before` installing k3s on `t2.small` instance

```bash
...
Tasks: 105 total,   1 running, 104 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.0 sy,  0.0 ni, 95.0 id,  4.7 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   1974.4 total,   1410.9 free,    183.2 used,    380.3 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   1638.9 avail Mem
```


- Resource usage `after` installing k3s on `t2.small` instance

|NAME    | CPU(cores)   |CPU%   |MEMORY(bytes) |  MEMORY%   |
|---|---|---|---|---|
|master  | 42m      |    4%    | 1387Mi         | 70%      |
|worker  | 9m        |   0%  |   728Mi  |         36% |


------------------------------------------------------------------------------------

- Resource usage `before` installing k3s on `t3a.micro` instance

```bash
...
Tasks: 101 total,   1 running, 100 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 97.7 id,  2.3 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :    946.9 total,    380.6 free,    180.5 used,    385.9 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.    618.7 avail Mem
```

- Resource usage `after` installing k3s on `t3a.micro` instance

|NAME    | CPU(cores)   |CPU%   |MEMORY(bytes) |  MEMORY%   |
|---|---|---|---|---|
|master  | 105m      |     5%   | 807Mi          | 85%      |
|worker  |  24m      |    1% |   602Mi  |         63%|

------------------------------------------------------------------------------------

- Resource usage `before` installing k3s on `t2.micro` instance

```bash
...
Tasks:  96 total,   1 running,  95 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :    967.9 total,    402.4 free,    178.5 used,    386.9 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.    640.5 avail Mem
```

- Resource usage `after` installing k3s on `t2.micro` instance

|NAME    | CPU(cores)   |CPU%   |MEMORY(bytes) |  MEMORY%   |
|---|---|---|---|---|
|master  |    88m     |    8%      |    829Mi        |    85%    |
|worker  |    9m    |    0%       |    496Mi      |   51%   |

------------------------------------------------------------------------------------

- Resource usage `before` installing k3s on `t2.nano` instance

```bash
...
Tasks:  99 total,   1 running,  98 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :    464.6 total,     16.1 free,    157.1 used,    291.5 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.    285.7 avail Mem
````

- Resource usage `after` installing k3s on `t2.nano` instance

```bash
...
root@master:~# systemctl status k3s
k3s.service - Lightweight Kubernetes
     Loaded: loaded (/etc/systemd/system/k3s.service; enabled; vendor preset: enabled)
     Active: activating (start) since Fri 2022-08-19 00:21:40 UTC; 16s ago
       Docs: https://k3s.io
```

- I got an error. installation failed with `t2.nano` type of instance

|NAME    | CPU(cores)   |CPU%   |MEMORY(bytes) |  MEMORY%   |
|---|---|---|---|---|
|master  |      |       |        |      |
|worker  |        |       |         |     |

------------------------------------------------------------------------------------

- Resource usage `before` installing k3s on `t3a.nano` instance

```bash
...
Tasks: 114 total,   1 running, 113 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.2 us,  0.2 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :    447.7 total,     21.5 free,    159.7 used,    266.5 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.    265.2 avail Mem
```

- Resource usage `after` installing k3s on `t3a.nano` instance

- instance failed to install k3s

```bash
...
Job for k3s.service failed because a fatal signal was delivered to the control process.
See "systemctl status k3s.service" and "journalctl -xeu k3s.service" for details.
```

------------------------------------------------------------------------------------

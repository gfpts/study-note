## Amazon EC2 T1.micro 增加 SWAP ##

### 檢查目前的磁碟空間  
[root@~]# df -h  
FilesystemSize  Used Avail Use% Mounted on  
/dev/sda1 9.9G  6.2G  **3.3G**  66% /  
none  312M 0  312M   0% /dev/shm

### 建立 SWAP 空間-依據自己的需求建立    

* dd if=/dev/zero of=/swapfile bs=1M count=1024
* mkswap /swapfile
* swapon /swapfile

### 掛載後檢查磁碟空間-原本剩餘 3.3G目前剩 2.3G  
[root@ ~]# df -h  
FilesystemSize  Used Avail Use% Mounted on  
/dev/sda1 9.9G  7.2G  **2.3G**  77% /  
none  312M 0  312M   0% /dev/shm  

### 檢查 SWAP 是否正常掛載與運作  

* swapon -s
* free -m

[root@ ~]# free -m  
 total   used   free sharedbuffers cached  
Mem:   622607 14  0  2452  
-/+ buffers/cache:152469  
Swap: 1023  0   1023
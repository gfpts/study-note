AWS EC2 硬碟空間擴大 Linux
--------------------

> 問題：如何調整 EBS Boot的 EC2雲主機的Root Disk大小
> 
> 環境：CentOS 6.X x86_64
> 
> 目標：Root Disk 容量由 30G 延展到 40G

 1. 將需要擴充硬碟空間的伺服器關機
    AWS EC2 console > INSTANCES > instances > 右方選擇伺服器
    按右鍵 > Actions > Stop

 2. 建立硬碟快照
    AWS EC2 console > ELASTIC BLOCK STORE > Volumes > 右方選擇伺服器的
    **Volumes** > Create Snapshot 於 Create Snapshot > Name 填入
    Snapshot的名稱,Description 與說明 > Create 
> **重點說明**
如何找尋Instances 對應哪一個 Volumes點擊該 Volumes 右下方會有詳細資料，找尋 Attachment information 後面會對應 instances 的 ID
    
    AWS EC2 console > ELASTIC BLOCK STORE > Snapshots > 右方選擇剛才建立的
    Snapshot 按右鍵 > Create Volume > 依據需要的需求填入 [2] > Create
> **重點說明**
**Type:** 硬碟的讀取類型
**Size (GiB)：** 需要擴大的空間
**Availability Zone:** 該區域必須跟 instances在同一區域才能掛載使用

 3. 卸載與掛載 Volumes AWS EC2 console > ELASTIC BLOCK STORE > Volumes >
    右方選擇要卸載的 **Volumes** Name 按右鍵選擇 > Detach Volume > Yes, Detach 
> **重點說明**
**Volumes** 下方可查出原本掛載的 **instances** 與 Volumes 資訊，並記下該資訊 **Attachment information**  `instance information:device name` 卸載後 右方的 Volumes State 狀態會變更為 **available**
例 `i-75685f72 (centos65_xx_test):/dev/sda`
    
    AWS EC2 console > ELASTIC BLOCK STORE > Volumes > 右方選擇要掛載的 Volumes
    Name 按右鍵選擇 > Attach Volume > Attach Volume 對話框填入要掛載的 Instance 與
    Device 資訊 [4]> Attach 
> **非常重要**
Instance 與 Device 資訊必須跟，步驟 **3.**的資訊一樣。 
例：i-75685f72 (centos65_xx_test):/dev/sda
**Instance** 填入 `i-75685f72`
**Device** 填入 `/dev/sda`

 4. 重新啟動 Instance AWS EC2 console > INSTANCES > instances > 右方選擇伺服器 按右鍵
    \> Actions > Start


----------


### Linux 系統磁碟延展方法

 1. 使用 SSH工具登入 EC2 - 檢查磁碟的資料格式
    資料格式必須是 **ext2, ext3, and ext4 ** file systems
    > [root@~]# file -s /dev/xvde
    > /dev/xvde: Linux rev 1.0 **ext4** filesystem data (needs journal recovery) (extents)
    > (large files) (huge files)

 2. 檢查原本的硬碟大小
    > [root@~]# df -h 
    > Filesystem Size Used Avail Use% Mounted on 
    > /dev/xvde **30G** 5.7G 23G 21% /
    > tmpfs 296M     0  296M   0% /dev/shm

 3. 硬碟延展尚未使用的總容量
    
    > [root@~]# lsblk
    > NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
    >xvde 202:64   0  **40G**  0 disk /

 4. 使用 Resize2fs 命令延展硬碟容量
    
    > [root@~]# **resize2fs /dev/xvde **
    > resize2fs 1.41.12 (17-May-2010)
    > Filesystem at /dev/xvde is mounted on /; on-line resizing required
    > old desc_blocks = 2, new_desc_blocks = 3
    > Performing an on-line resize of /dev/xvde to 10485760 (4k) blocks.
    > The filesystem on /dev/xvde is now 10485760 blocks long.

 5. 延展硬碟完成，檢查硬碟容量是否正確
    
    > [root@~]# df -h
    > Filesystem      Size  Used Avail Use% Mounted on
    > /dev/xvde        **40G**  5.7G   32G  16% /
    > tmpfs           296M     0  296M   0% /dev/shm

**註: 如果檢查正確即可刪掉舊的 Volume，建議確定檢查無誤後再刪除。**

> *待驗證* 另一種使用 AMIS延展硬碟的方法，硬碟 30G 擴大到 150G resize2fs 的時間大約 52分鐘

VIA
[AWS DOC to extend a Linux file system][1]
[1]: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-expand-volume.html
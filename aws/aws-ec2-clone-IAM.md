## AWS EC2 複製一台新的主機

#### 建立印象檔，類似`Ghost`
AWS Console > Instances > 選擇要複製的主機 按右鍵 > Create Image >填入相關資訊

 * Image name ：要儲存的映像檔名稱，好記憶的名稱即可
 * Image description：填寫該映像檔的敘述
 * No reboot：不重新開機
 * Instance Volumes：是否要變更儲存空間大小 (EC2的硬碟大小)

填寫完成後按下 Create Image

#### 開始複製一台新的主機
AWS Console > IMAGES >AMIs > 選擇剛才建立的映像檔 按右鍵 > Launch > 依據需求填入要開啟的新主機規格
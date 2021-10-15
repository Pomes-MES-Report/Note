# RTR020A - 進站開工
標籤 : #報工系統 #進站開工
備註 : 
## 一、資料庫
主要資料庫 : 報工數據收集- [工單狀態 MED02_0000](MED02_0000.md) 、報工數據收集-[工單時間 MED08_0000](MED08_0000)
紀錄資料庫 : 即時報工統計檔-[機器別 MEM01_0000](MEM01_0000.md)
更新狀態資料庫: [製令主檔 MET01_0000](MET01_0000.md) 、 [派工單主檔 MET03_0000](MET03_0000)

---

## 二、操作
1. 當人員完成變更機台、上下工，可以進行進站開工。通常沒有人員以及機台系統會顯示請使用者進行登入機台、上下工。
2. 使用者可以從table 裡面選擇單子，同時可以使用條碼槍掃。
3. 完成後，系統會跳出是否要進行生產作業，讓使用者自己選擇。

---
## 三、資料流向
1. 顯示進站開工的sql 如下:
```cs
SELECT 
	isnull(a.mac_code,'') as mac_code
	,a.mo_code ,a.wrk_code
	,a.pro_code,c.pro_name
	,a.pro_qty AS plan_qty,a.mo_status
	,isnull(a.sch_date_s+' '+a.sch_time_s,d.sch_date_s+' '+d.sch_time_s) as sch_datetime_s
FROM MET03_0000 a
	LEFT JOIN MEB30_0100 b On a.work_code=b.work_code
	LEFT JOIN MEB29_0200 e ON b.station_code=e.station_code
	LEFT JOIN MEB20_0000 c ON a.pro_code=c.pro_code
	LEFT JOIN MET01_0000 d ON a.mo_code=d.mo_code
WHERE e.mac_code= @mac_code and (a.mac_code = @mac_code or isnull(a.mac_code,'') ='')
	AND d.mo_status IN ('20','30')
	AND a.mo_status IN ('STOP','NONE')
	AND a.wrk_date<= @wrk_date
order by 
	case when (a.sch_date_s+' '+a.sch_time_s is null and d.sch_date_s+' '+d.sch_time_s is null ) 
		or a.sch_date_s='' 
		or d.sch_date_s='' then 1 else 0 end 
		, sch_datetime_s

```

2. 選擇工單後，並傳送工單號碼。資料庫最先會[[MED08_0000]]後，記錄 [[MED02_0000]] 寫入工單狀態、時間。若選擇暫停的工單會清空 [[MEM01_0000]].work_time_e (WHERE mo_code、work_code)。

3. 工單紀錄創建後，[[MET03_0000]]會更新mo_status以及[[MET01_0000]] mo_status。

4. [[MEM01_0000]]會同步(Update)以下訊息，(work_time_S、mac_code、station_code、usr_code)

5. 若[[MET01_0000]]中有開工時間，則不更新時間，其餘沒開工時間會更新時間。 
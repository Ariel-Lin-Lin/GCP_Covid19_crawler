# GCP_Covid19_crawler
### 動態網頁呈現如下連結
https://datastudio.google.com/reporting/0fba186c-6215-4d7a-8aa8-8187a6e2be9e/page/M9lSB

![image](https://github.com/Ariel-Lin-Lin/GCP_Covid19_crawler/blob/main/process.jpg)
### 資料來源
1. URL : https://github.com/CSSEGISandData/COVID-19
2. 約翰·霍普金斯大學系統科學與工程中心（CSSE）提供的COVID-19數據存儲庫，並每日更新
3. COVID-19/csse_covid_19_data/csse_covid_19_time_series/
![image](https://github.com/Ariel-Lin-Lin/GCP_Covid19_crawler/blob/main/covid19_opendata.png)

##### 利用Cloud Functions將爬蟲程式部屬至雲端
1. 使用pandas抓取covid19資料，建立DataFrame
2. 由於此資料是每日更新，相同欄位非常多，因此將原始資料相同欄位合併，變成可作圖的Tidy Data
##### 利用Cloud Storage將爬蟲程式的資料儲存至雲端

""" 
from google.cloud import storage
def upload_blob(bucket_name, source_file_name, destination_blob_name):
    storage_client = storage.Client()
    bucket = storage_client.bucket(bucket_name)
    blob = bucket.blob(destination_blob_name)
    blob.upload_from_filename(source_file_name)
    print(
        "File {} uploaded to {}.".format(
            source_file_name, destination_blob_name
        )
    )
""" 


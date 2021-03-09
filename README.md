# GCP_Covid19_crawler
### 動態網頁呈現如下連結
https://datastudio.google.com/reporting/0fba186c-6215-4d7a-8aa8-8187a6e2be9e/page/M9lSB
### 流程圖
![image](https://github.com/Ariel-Lin-Lin/GCP_Covid19_crawler/blob/main/process.jpg)
### 資料來源
1. URL : https://github.com/CSSEGISandData/COVID-19
2. 約翰·霍普金斯大學系統科學與工程中心（CSSE）提供的COVID-19數據存儲庫，此存儲庫每日更新
3. COVID-19/csse_covid_19_data/csse_covid_19_time_series/
![image](https://github.com/Ariel-Lin-Lin/GCP_Covid19_crawler/blob/main/covid19_opendata.png)

### 利用Cloud Functions將爬蟲程式部屬至雲端
1. 使用pandas抓取covid19資料，建立DataFrame
2. 由於此資料是每日更新，相同欄位非常多，因此將原始資料的相同欄位合併，變成可作圖的Tidy Data
3. 程式碼如下所示:(同main.py)
4. 爬蟲程式被呼叫後，會去github上爬取最新的COVID-19資料，並把資料儲存到cloud storage，每天更新並覆寫檔案
```
def crawler(request):
    df = pd.read_csv("https://github.com/CSSEGISandData/COVID-19/raw/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_confirmed_global.csv")
    df = pd.melt(df, id_vars=df.columns[:4], value_vars=df.columns[4:], var_name="date", value_name="count")
    return df.to_csv(index=False)
```
### 利用Cloud Storage將爬蟲程式的資料儲存至雲端
1. 建立自動上傳檔案的upload_blob
2. upload_blob是將一個檔案，上傳到cloud storage去
3. 程式碼如下所示:(同main.py)
```
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
```
### 利用cloud scheduler設定為自動排程
1. 頻率設定為:每12小時更新一次(0 */12 * * *)
2. 時區: 台灣
3. 每天的0點及12點0分，cloud schduler會啟動排程(COVID-19爬蟲程式)

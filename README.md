# GCP_Covid19_crawler
### 動態網頁呈現如下連結
https://datastudio.google.com/reporting/0fba186c-6215-4d7a-8aa8-8187a6e2be9e/page/M9lSB

![image](https://github.com/Ariel-Lin-Lin/GCP_Covid19_crawler/blob/main/process.jpg)
### 資料來源
1. URL : https://github.com/CSSEGISandData/COVID-19
2. 約翰·霍普金斯大學系統科學與工程中心（CSSE）提供的COVID-19數據存儲庫，並每日更新
3. COVID-19/csse_covid_19_data/csse_covid_19_time_series/
![image](https://github.com/Ariel-Lin-Lin/GCP_Covid19_crawler/blob/main/covid19_opendata.png)
### 資料清洗
1. 使用
"""
import pandas as pd

from google.cloud import storage


def upload_blob(bucket_name, source_file_name, destination_blob_name):
    """Uploads a file to the bucket."""
    # bucket_name = "your-bucket-name"
    # source_file_name = "local/path/to/file"
    # destination_blob_name = "storage-object-name"

    storage_client = storage.Client()
    bucket = storage_client.bucket(bucket_name)
    blob = bucket.blob(destination_blob_name)

    blob.upload_from_filename(source_file_name)

    print(
        "File {} uploaded to {}.".format(
            source_file_name, destination_blob_name
        )
    )

def crawler(request):
  files = ["confirmed_global.csv", "deaths_global.csv", "recovered_global.csv"]

  for f in files:
    df = pd.read_csv("https://github.com/CSSEGISandData/COVID-19/raw/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_" + f)
    df = pd.melt(df, id_vars=df.columns[:4], value_vars=df.columns[4:], var_name="date", value_name="count")
    df.to_csv("/tmp/"+f, index=False) #存檔案在/tmp底下，GCP限制
    upload_blob("covid19-data-01", "/tmp/"+f, f) #上傳檔案到storage我自己新建的covid19-data-01
    #upload_blob(storage,檔案,檔名)
"""

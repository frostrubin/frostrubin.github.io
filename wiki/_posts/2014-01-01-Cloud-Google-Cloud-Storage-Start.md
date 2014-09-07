---
title: Cloud Google Cloud Storage Start

layout: page
category: wiki
---

### Commands
* Create a durable reduced availability bucket in the EU
`gsutil mb -c DRA -l "EU" gs://<bucketname>/`

* Upload all the files _inside_ a folder (without the folder name) to google storage
`gsutil cp ~/my_folder/folder/*  gs://dst_uri/`

### Script
```
status=1
while [ $status -ne 0 ]; do
  gsutil cp -c -L ~/Unix/backup/gsTest.log ~/Desktop/Test/* gs://test/
  status=$?
  if [ $status -ne 0 ]; then
    sleep 5 # Time for Ctrl+C
  fi
done
```

### Links
* https://developers.google.com/storage/docs/signup#activate
* https://developers.google.com/storage/docs/overview#using
* https://developers.google.com/storage/docs/gsutil_install
* https://developers.google.com/storage/docs/gsutil/addlhelp/ScriptingProductionTransfers
* https://developers.google.com/storage/docs/gsutil/commands/cp#options


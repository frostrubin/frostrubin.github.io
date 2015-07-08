---
title: Cloud Amazon Cloud Storage Start

layout: page
category: wiki
---

### Script
    
    status=1
    while [ $status -ne 0 ]; do
      s3cmd sync --server-side-encryption --no-delete-removed  --progress -v --exclude '*.DS_Store' \
         ~/Desktop/Uploads/Folder/  s3://my_bucket/Folder/
      status=$?
      if [ $status -ne 0 ]; then
        sleep 5 # Time for Ctrl+C
      fi
    done

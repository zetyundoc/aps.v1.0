# Ingestion

首先，创建"demo_table"。

    python ingestion_client.py create \
      -h 52.27.67.237 \
      -u demo -a 'pdnYRsPauS3O4fmHrFPGEJRNJEdGDbAGPdKImZQpMUo2EgiMxlGCNzkdkGH5T3Gs' \
      -d demodb \
      -t demo_table

然后，检查Ingestion状态是否up.

    python ingestion_client.py status \
      -h 52.27.67.237 \
      -u demo -a 'pdnYRsPauS3O4fmHrFPGEJRNJEdGDbAGPdKImZQpMUo2EgiMxlGCNzkdkGH5T3Gs' \
      -d demodb \
      -t demo_table

Now, we can POST the data chunk by chunk. Here, we use 10 lines for each request.

    python ingestion_client.py postfile \
      -h 52.27.67.237 \
      -u demo -a 'pdnYRsPauS3O4fmHrFPGEJRNJEdGDbAGPdKImZQpMUo2EgiMxlGCNzkdkGH5T3Gs' \
      -d demodb \
      -t demo_table \
      -f wc.sample.json \
      --lines 10


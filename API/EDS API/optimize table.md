# 表优化

表优化用于将表格转换成columnar format。
例如：源表名称为"demo_table"，表优化将会将表名称转换成"opt_demo_table"。
This will convert table to optimized columnar format. For example, the source table
name is "demo_table", the optimized table name will be "opt_demo_table".


    python optimize_table.py \
      -h 52.27.67.237 \
      -u demo -a 'pdnYRsPauS3O4fmHrFPGEJRNJEdGDbAGPdKImZQpMUo2EgiMxlGCNzkdkGH5T3Gs' \
      -d demodb \
      -t demo_table

    ===============================
    jobid 13, iter 1, status = running
    jobid 13, iter 2, status = running
    jobid 13, iter 3, status = running
    jobid 13, iter 4, status = running
    jobid 13, iter 5, status = running
    jobid 13, iter 6, status = running
    jobid 13, iter 7, status = running
    jobid 13, iter 8, status = running
    jobid 13, iter 9, status = running
    jobid 13, iter 10, status = running
    jobid 13, iter 11, status = running
    jobid 13, iter 12, status = running
    jobid 13, iter 13, status = running
    jobid 13, iter 14, status = running
    jobid 13, iter 15, status = running
    jobid 13, iter 16, status = running
    jobid 13, iter 17, status = running
    jobid 13, iter 18, status = done
    ===============================
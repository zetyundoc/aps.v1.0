#使用Screwjack(Hive)

这里用于说明screwjack hive的运行环境。本节将创建一个EMR（Elastic Map-Reduce）hive的模块用 来得到搜索查询数据中词频最高的单词及出现的频率。

非常感谢AOL的共享。示例数据可以在这里下载，以下为数据格式：

|AnonID |   Query |    QueryTime |   ItemRank  |  ClickURL|
|--|--|--|--|--|
|142 |rentdirect.com | 2006-03-01 07:17:12| | |
|142 |www.prescriptionfortime.com |2006-03-12 12:31:06| | |
|142 |staple.com  |2006-03-17 21:19:29| | |
|142| staple.com | 2006-03-17 21:19:45| | |
|142| www.newyorklawyersite.com|   2006-03-18 08:02:58| | |
|142| www.newyorklawyersite.com  | 2006-03-18 08:03:09| | |
|142| westchester.gov |2006-03-20 03:55:57 |1 |  http://www.westchestergov.com|
|142| space.comhttp |  2006-03-24 20:51:24| | | 
|142| dfdf |   2006-03-24 22:23:07| | |
|142| dfdf |   2006-03-24 22:23:14| | |
|142| vaniqa.comh |2006-03-25 23:27:12| | | 
|142| www.collegeucla.edu |2006-04-03 21:12:14| | |
|142| www.elaorg|  2006-04-03 21:25:20| | |
|142| 207 ad2d 530 |   2006-04-08 01:31:04| | |
|142| 207 ad2d 530   | 2006-04-08 01:31:14 |1  |http://www.courts.state.ny.us|
|142| broadway.vera.org | 2006-04-08 08:38:23| | |
|142| broadway.vera.org |  2006-04-08 08:38:31| | |
|142| vera.org  |  2006-04-08 08:38:42| 1  | http://www.vera.org|
|142| broadway.vera.org |  2006-04-08 08:39:30| | |
|142| frankmellace.com  |  2006-04-09 02:19:24| | |
|142 |ucs.ljx.com| 2006-04-09 02:20:44| | |
|142 |attornyleslie.com  | 2006-04-13 00:25:27| | |
|142| merit release appearance |   2006-04-22 23:51:18| | | |

###<a name="id1">步骤1：初始化hive模块</a>

```
screwjack init emr_hive -n hot_token_topN_on_emr -d "Get hotest token in search engine query log."
```
当提示 `Module Version` 和其他选项时，可以输入回车选择默认选项进行下一步操作。
```
Module Version [0.1]:
Module Entry Command [/usr/bin/python main.py]:
Base Image [zetdata/ubuntu:trusty]:
init emr_hive
Sucessfully created 'hot_token_topn_on_emr'
```
之后，将创建一个名称是 `hot_token_topn_on_emr` 的目录。

###<a name="id2">步骤2：在模块中添加输入/输出和参数</a>

```
screwjack input_add query_log_s3_dir hive.s3.id_query_querytime
screwjack output_add hot_token_topN_s3_dir hive.s3.table.token_count
screwjack param_add topN string
```
`query_log_dir` 是HDFS中的目录，其中包含原始数据。数据的结构为ID，查询和查询时间。 `hot_token_topN` 是hive的表名，结果存储在此表里。

###<a name="id3">步骤3：（可选）使UDF帮助浏览查询到令牌</a>

此步骤为可选项，当模块中需要UDF时，参考以下实例说明： [example-modules](https://github.com/DataCanvasIO/example-modules/tree/master/tutorials/emr_hive/udft). 创建UDF的jar包后，将文件放在 `./resource/udfs`, HiveRuntime 会自动将文件上传到AWS的S3中。

###<a name="id4">步骤4：编写Hive脚本.</a>

可以打开 `main.hql` 文件，将代码写入：
```
set hive.base.inputformat=org.apache.hadoop.hive.ql.io.HiveInputFormat;

CREATE TEMPORARY FUNCTION splitword AS 'com.your_company.hive.udtf.SplitWord';

--CREATE OUTPUT TABLE

DROP TABLE IF EXISTS hot_token_topN_table;
CREATE EXTERNAL  TABLE hot_token_topN_table
(
        token STRING,
        freq  INT
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION '${OUTPUT_hot_token_topN_s3_dir}';

--CREATE AN EXTERNAL TABLE TO LOAD THE QUERY DATA
DROP TABLE IF EXISTS query;
CREATE EXTERNAL TABLE query
(
        id STRING,
        site STRING,
        timestp TIMESTAMP
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
LOCATION '${INPUT_query_log_dir_s3_dir}';

INSERT OVERWRITE TABLE hot_token_topN_table
SELECT token, freq FROM
(
        SELECT token,count(1) AS freq FROM
        (
                SELECT splitword(site) AS token FROM query
        )token_table
        GROUP BY token
)token_frep
ORDER BY freq DESC LIMIT ${PARAM_topN};
```
代码中可以直接引用screwjack的输入/输出/参数 `${INPUT_inputname} ` 和 `${OUTPUT_outputname}`  `${PARAM_paramname} `，在本例为 `${INPUT_query_log_s3_dir}` ， 输出为 `${OUTPUT_hot_token_topN_s3_dir}` ，参数为 `${PARAM_topN}`。

###<a name="id5">步骤5：本地测试</a>

测试前，需要将样本数据上传到S3存储空间。本例中我们放在了 `s3://get-hot-token-kk/input/query` 。 模块的输入来自于上一模块的输出，在测试中，需要自己传入输入值。我们使用输入参数文件和输出参数文件来承载输入和输出结果 。本例中输入参数是放在S3目录下的一个查询日志文件。创建输入文件` ./input.param `并存在 `s3://get-hot-token-kk/input/query` 下。 创建输出文件接收输出结果。
```
screwjack run local
```
然后输入相应的参数来运行测试。
```
Param 'FILE_DIR' [./resources/files]:
Param 'UDF_DIR' [./resources/udfs]:
Param 'AWS_ACCESS_KEY_ID' []: YOUR_AWS_ACCESS_KEY
Param 'AWS_ACCESS_KEY_SECRET' []: YOUR_AWS_ACCESS_KEY_SECRET
Param 'S3_BUCKET' []: get-hot-token-kk
Param 'AWS_Region' []: us-east-1
Param 'EMR_jobFlowId' []: YOUR_EMR_JOB_FLOW_ID
Param 'topN' []: 10
Input 'query_log_s3_dir': input.param
Output 'hot_token_topN_s3_dir': output.param
```
在测试过程中，如果有任何错误或缺陷出现，可以根据日志修改UDTF和hive脚本。如果调试完毕， 定义好的输出参数将被创建和写入 `output.param` 。如果测试成功完成，我们在S3目录中可以得到一个 在output.param文件。会得到 `s3://get-hot-token-kk/zetjob/your_username/job456/blk789/OUTPUT_hot_token_topN_s3_dir`. 打开S3文件会看到单词频率的排序前十位的结果如下：
```
of,110575
-,104052
in,91521
the,82961
for,70107
and,66675
to,45168
free,45149
a,36220
google,34970
```
###<a name="id6">步骤6：在 docker 中进行测试</a>

```
screwjack run docker
```
这一步是测试模块能否在docker镜像中运行。起初，screwjack 将在hive运行环境下建立一个特定的映像 然后将在UDFS映像下测试脚本。 如果运行结果成功，证明将此模块已通过测试，可以上线部署运行。

[1]	G. Pass, A. Chowdhury, C. Torgeson, “A Picture of Search” The First International Conference on Scalable Information Systems, Hong Kong, June, 2006.
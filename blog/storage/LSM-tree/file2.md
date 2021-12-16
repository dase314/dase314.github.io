# PostGreSQL的WAL日志与恢复

source: `{{ page.path }}`


## 9.1 Overview

#### 9.1.1

What is WAL Log

#### 9.1.2 

In PG, the history data are known as XLOG

LSN (Log Sequence Number) of XLOG record represents the location where its record is written on the transaction log. LSN of record is used as the unique id of XLOG record.

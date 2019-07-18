빅데이터 통합실습 (07/17)

PART1 - 빅데이터 클러스터 구축
PART2 - 빅데이터 테스트

#### hive tip
--업데이트 대신 테이블 reload
insert overwrite table cc_data select * from cc_data where sv_acnt_num <> 'SV_ACNT_NUM';

--drop table
Drop TABLE IF EXISTS MVNO_REQ_DATA;

--create table에 partition 옵션
CREATE EXTERNAL TABLE IF NOT EXISTS MVNO_REQ_DATA(
  req_id varchar(10),
  req_dtm varchar(14),
  table_nm1 varchar(100),
  column_nm1 varchar(100),
  item_nm1 varchar(100),
  category_grp_dtl_id1 varchar(100),
  table_nm2 varchar(100),
  column_nm2 varchar(100),
  item_nm2 varchar(100),
  category_grp_dtl_id2 varchar(100),
  table_nm3 varchar(100),
  column_nm3 varchar(100),
  item_nm3 varchar(100),
  category_grp_dtl_id3 varchar(100),
  sta_ym varchar(6),
  end_ym varchar(6)
  )
PARTITIONED BY (now char(10))
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION '/user/mobig01/mvno_req_data';

--add partition
alter table orders add partition (state="CA") location '/tables/CA';
ALTER TABLE COMMON ADD PARTITION IF NOT EXISTS (setkey='/user/common/external') LOCATION '/user/common/external';

--drop partition
alter table orders drop if exists partition (state="CA");

--show partition
show partitions orders;

--option(create table이나 export table할 때 첫줄 skip 옵션)
tblproperties("skip.header.line.count"="1");


--insert select문
INSERT OVERWRITE DIRECTORY '/user/mobig01/mvno_rslt_data/2018031822'
 ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
 SELECT CC_DATA.svc_scrb_yymm, CC_DATA.scrb_gb, count(*), BS_DATA.inv_yymm, sum(BS_DATA.inv_data_amnt), sum(BS_DATA.inv_total_amnt)	
 FROM CC_DATA
 JOIN BS_DATA
 ON (CC_DATA.sv_acnt_num = BS_DATA.sv_acnt_num AND CC_DATA.mvno_co_cd = BS_DATA.mvno_co_cd)
 WHERE 1=1
 AND CC_DATA.svc_scrb_yymm between '201712' and '201802'
 AND BS_DATA.inv_yymm between '201712' and '201802'
 AND CC_DATA.scrb_gb= "NEW"
 GROUP BY CC_DATA.svc_scrb_yymm, CC_DATA.svc_scrb_yymm, BS_DATA.inv_yymm
 ORDER BY CC_DATA.svc_scrb_yymm, CC_DATA.svc_scrb_yymm, BS_DATA.inv_yymm
;


--concat, length select
SELECT CONCAT(CONCAT('"',REQ_ID),'"'), REQ_DTM, STA_YM, END_YM, TABLE_NM1,
CONCAT(CONCAT(TABLE_NM1, '.'),COLUMN_NM1) AS COLUMN_NM1,
CASE WHEN LENGTH(ITEM_NM1)=0 THEN 'NA' ELSE CONCAT(CONCAT(CONCAT(CONCAT(CONCAT(TABLE_NM1, '.'),COLUMN_NM1),'= "'),ITEM_NM1),'"') END AS ITEM_NM1,
CASE WHEN LENGTH(TABLE_NM2)=0 THEN 'NA' ELSE TABLE_NM2 END TABLE_NM2,
CASE WHEN LENGTH(COLUMN_NM2)=0 THEN 'NA' ELSE CONCAT(CONCAT(TABLE_NM2, '.'),COLUMN_NM2) END COLUMN_NM2,
CASE WHEN LENGTH(ITEM_NM2)=0 THEN 'NA' ELSE CONCAT(CONCAT(CONCAT(CONCAT(CONCAT(TABLE_NM2, '.'),COLUMN_NM2),'= "'),ITEM_NM2),'"') END AS ITEM_NM2,
CASE WHEN LENGTH(TABLE_NM3)=0 THEN 'NA' ELSE TABLE_NM3 END TABLE_NM3,
CASE WHEN LENGTH(COLUMN_NM3)=0 THEN 'NA' ELSE CONCAT(CONCAT(TABLE_NM3, '.'),COLUMN_NM3) END COLUMN_NM3,
CASE WHEN LENGTH(ITEM_NM3)=0 THEN 'NA' ELSE CONCAT(CONCAT(CONCAT(CONCAT(CONCAT(TABLE_NM3, '.'),COLUMN_NM3),'= "'),ITEM_NM3),'"') END AS ITEM_NM3
FROM mvno_req_data
WHERE REQ_ID = '5'


--sqoop import (mysql --> hdfs)
 이때는 hdfs에 디렉토리 미리 생성되어 있으면 오류
--sqoop export (hdfs --> mysql)
이때는 mysql에 table 미리 생성해야 함


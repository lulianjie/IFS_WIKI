* 如果SYS,SYSTEM用户的密码都忘记或是丢失。
  这个密码是修改sys用户的密码。除sys其他用户的密码不会改变
```
   orapwd file=C:\app\Administrator\product\12.2.0\dbhome_1\database\PWDNMT.ORA password=newpassword
```
* IFS数据库恢复
  ```
  C:\>oradim -new -sid NMT  #创建一个orcl服务
  C:\>orapwd file=C:\app\Administrator\product\12.2.0\dbhome_1\database\PWDNMT.ORA password=chsc_2019
  C:\>sqlplus "sys/chsc_2019@NMT as sysdba"
  SQL>startup nomount pfile='C:\initNMT.ORA';
  SQL> CREATE CONTROLFILE set DATABASE "NMT" RESETLOGS  noARCHIVELOG
       MAXLOGFILES 16
       MAXLOGMEMBERS 3
       MAXDATAFILES 150
       MAXINSTANCES 8
       MAXLOGHISTORY 454
   LOGFILE
     GROUP 1 (
       'I:\ORADATA\NMT\REDO10.LOG',
       'I:\ORADATA\NMT\REDO11.LOG'
     ) SIZE 40M,
     GROUP 2 (
       'I:\ORADATA\NMT\REDO20.LOG',
       'I:\ORADATA\NMT\REDO21.LOG'
     ) SIZE 40M,
     GROUP 3 (
       'I:\ORADATA\NMT\REDO30.LOG',
       'I:\ORADATA\NMT\REDO31.LOG'
     ) SIZE 40M
   DATAFILE
     'I:\ORADATA\NMT\SYSTEM01.DBF',
     'I:\ORADATA\NMT\UNDOTBS01.DBF',
     'I:\ORADATA\NMT\SYSAUX01.DBF',
     'I:\ORADATA\NMT\USERS01.DBF',
     'I:\ORADATA\NMT\IFSAPP_LOB01.DBF',
     'I:\ORADATA\NMT\IFSAPP_DATA01.DBF',
     'I:\ORADATA\NMT\IFSAPP_INDEX01.DBF',
     'I:\ORADATA\NMT\IFSAPP_REPORT_DATA01.DBF',
     'I:\ORADATA\NMT\IFSAPP_REPORT_INDEX01.DBF',
     'I:\ORADATA\NMT\IFSAPP_ARCHIVE_DATA01.DBF',
     'I:\ORADATA\NMT\IFSAPP_ARCHIVE_INDEX01.DBF'
   CHARACTER SET TH8TISASCII
   ;
  SQL> create spfile from pfile='C:\initNMT.ORA';
  SQL> shutdown immediate;
  SQL> startup mount;
  SQL> recover database using backup controlfile until cancel;
  I:\ORADATA\NMT\REDO031.LOG
  SQL> ALTER DATABASE OPEN RESETLOGS;
  SQL> create temporary tablespace temp1 tempfile 'I:\ORADATA\NMT\temp1.dbf' size 10240m reuse autoextend on;
  SQL> alter database default temporary tablespace temp1;
  SQL> drop tablespace temp;
  SQL> create temporary tablespace temp tempfile 'I:\ORADATA\NMT\temp.dbf' size 10240m reuse autoextend on;
  SQL> alter database default temporary tablespace temp;
  SQL> drop tablespace temp1;
  SQL> alter user IFSSYS identified by ifsapp;
  SQL> alter user IFSAPP identified by ifsapp;
  SQL> shutdown immediate;
  SQL> startup;
  ```

* 闪回查询
```
  SELECT * FROM BUSINESS_OPPORTUNITY_TAB
  AS OF TIMESTAMP TO_TIMESTAMP('2020-09-03 1:30:00', 'YYYY-MM-DD HH:MI:SS')
```
* 闪回误删除的表（drop）
```
SELECT * FROM user_recyclebin
WHERE original_name='SUPPLIER_BLANKET_LINE_TAB';
FLASHBACK TABLE SUPPLIER_BLANKET_LINE_TAB TO BEFORE DROP;
```
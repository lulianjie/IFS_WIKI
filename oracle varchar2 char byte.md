* ORA-06502: PL/SQL: numeric or value error: character string buffer too small
* If the NLS_LENGTH_SEMANTICS is set to BYTE, a declaration of a string VARCHAR2(10) means that the string can hold ten bytes. In a Unicode environment one character can be up to 1-4 bytes long. Therefore in a scenario where all characters take up 4 bytes, only two such characters can be put into that variable, not 10 which might be what was expected. If you try to assign a string which is larger than that (in bytes, not necessarily in characters) Oracle will raise the ORA-06502: PL/SQL: numeric or value error: character string buffer too small exception.
* 需要修改NLS_LENGTH_SEMANTICS配置到CHAR，则PLSQL中默认的VARCHAR2(10)默认为CHAR
* 错误代码示例：
```
DECLARE
   name1_ VARCHAR2(2 byte) := '你';
   name2_ VARCHAR2(2 byte) := '好';
   name_  VARCHAR2(2 byte);
   length_ NUMBER;
BEGIN
   name_ := SUBSTR(name1_ || name2_, 0, 1);
   length_ := LENGTH(name_);
   dbms_output.put_line(name_||'---'||length_);
END;
```
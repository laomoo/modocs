# 技术问题


## Clob/Blob导入导出

	时间：2018年12月02日

`Clob`转换为`Blob`
```sql
CREATE OR REPLACE FUNCTION C2B(B IN CLOB DEFAULT EMPTY_CLOB()) RETURN BLOB
-- typecasts BLOB to CLOB (binary conversion)
 IS
  RES          BLOB;
  B_LEN        NUMBER := DBMS_LOB.GETLENGTH(B);
  DEST_OFFSET1 NUMBER := 1;
  SRC_OFFSET1  NUMBER := 1;
  AMOUNT_C     INTEGER := DBMS_LOB.LOBMAXSIZE;
  BLOB_CSID    NUMBER := DBMS_LOB.DEFAULT_CSID;
  LANG_CTX     INTEGER := DBMS_LOB.DEFAULT_LANG_CTX;
  WARNING      INTEGER;
BEGIN


  IF B_LEN > 0 THEN
    DBMS_LOB.CREATETEMPORARY(RES, TRUE);
    DBMS_LOB.OPEN(RES, DBMS_LOB.LOB_READWRITE);
    DBMS_LOB.CONVERTTOBLOB(RES,
                           B,
                           AMOUNT_C,
                           DEST_OFFSET1,
                           SRC_OFFSET1,
                           BLOB_CSID,
                           LANG_CTX,
                           WARNING);
  ELSE
    SELECT EMPTY_BLOB() INTO RES FROM DUAL;
  END IF;
  RETURN RES; -- res is OPEN here
END C2B;
```
`BLOB`转换为`VARCHAR`
```sql
CREATE OR REPLACE FUNCTION BLOB_TO_VARCHAR(BLOB_IN IN BLOB) RETURN CLOB IS
  V_VARCHAR  VARCHAR2(32767);
  V_VARCHAR1 VARCHAR2(32767);
  V_START    PLS_INTEGER := 1;
  V_BUFFER   PLS_INTEGER := 4000;
BEGIN
  IF DBMS_LOB.GETLENGTH(BLOB_IN) IS NULL THEN
    RETURN '';
  END IF;
  V_VARCHAR1 := '';
  --return to_char(Ceil(Dbms_Lob.Getlength(Blob_In) / V_Buffer));
  FOR I IN 1 .. CEIL(DBMS_LOB.GETLENGTH(BLOB_IN) / V_BUFFER) LOOP
    --当转换出来的字符串乱码时，可尝试用注释掉的函数
    --V_Varchar := Utl_Raw.Cast_To_Varchar2(Utl_Raw.Convert(Dbms_Lob.Substr(Blob_In, V_Buffer, V_Start),'SIMPLIFIED CHINESE_CHINA.ZHS16GBK', 'AMERICAN_THE NETHERLANDS.UTF8'));
    V_VARCHAR  := UTL_RAW.CAST_TO_VARCHAR2(DBMS_LOB.SUBSTR(BLOB_IN,
                                                           V_BUFFER,
                                                           V_START));
    V_VARCHAR1 := V_VARCHAR1 || V_VARCHAR;
  
    V_START := V_START + V_BUFFER;
  END LOOP;
  RETURN V_VARCHAR1;
END BLOB_TO_VARCHAR;
```

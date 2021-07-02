# oracle_hacks - commands to save

### Get recents operations of table
```
SELECT VERSIONS_STARTSCN
     , VERSIONS_STARTTIME
     , VERSIONS_ENDSCN
     , VERSIONS_ENDTIME
     , VERSIONS_XID   
     , CASE VERSIONS_OPERATION 
            WHEN 'D' THEN 'DELETE' 
            WHEN 'U' THEN 'UPDATE'
            WHEN 'I' THEN 'INSERT'
       END AS VERSIONS_OPERATION 
     , <table_name>.*
  FROM <owner>.<table_name> VERSIONS BETWEEN TIMESTAMP MINVALUE AND MAXVALUE
```

### Easy method to back in the time
```
#data after
SELECT * FROM <owner>.<table_name>;
#data before
SELECT * FROM <owner>.<table_name> AS OF TIMESTAMP(TO_DATE('01/jul/2021 10:00:28','DD/MM/YYYY HH24:MI:SS'))
```

### Other method to back in the time
```
#data after change
SELECT * FROM <owner>.<table_name>;
/
BEGIN
DBMS_FLASHBACK.ENABLE_AT_TIME(TO_DATE('30/06/2021 17:04:28','DD/MM/YYYY HH24:MI:SS'));
END;
/
#data before change
SELECT * FROM <owner>.<table_name>;

```

### Last queue
```
SELECT *
  FROM V$SQL 
 ORDER BY FIRST_LOAD_TIME DESC
```

### Auto Calendar
```
 SELECT TRUNC(SYSDATE-LEVEL)                                          AS "DATE"
      , TO_CHAR(SYSDATE-LEVEL,'DD')                                   AS "DAY"
      , TO_CHAR(SYSDATE-LEVEL,'MM')                                   AS "MONTH"
      , TO_CHAR(SYSDATE-LEVEL,'YYYY')                                 AS "YEAR"
      , TRUNC(SYSDATE-LEVEL,'MM')                                     AS "FIRST_DAY_OF_MONTH"
      , LAST_DAY(TRUNC(SYSDATE-LEVEL))                                AS "LAST_DAY_OF_MONTH"
      , TO_CHAR(SYSDATE-LEVEL,'DAY','NLS_DATE_LANGUAGE=ENGLISH')      AS "NAME_DAY"
      , TO_CHAR(SYSDATE-LEVEL,'MONTH','NLS_DATE_LANGUAGE=ENGLISH')    AS "NAME_MONTH"
      , TO_CHAR(SYSDATE-LEVEL,'YEAR')                                 AS "NAME_YEAR"
      , TO_CHAR(SYSDATE-LEVEL,'DY','NLS_DATE_LANGUAGE=ENGLISH')       AS "ABB_NAME_DAY"
      , TO_CHAR(SYSDATE-LEVEL,'MON','NLS_DATE_LANGUAGE=ENGLISH')      AS "ABB_NAME_MONTH"
      , TO_CHAR(SYSDATE-LEVEL,'YY')                                   AS "ABB_NAME_YEAR"
      , TO_CHAR(SYSDATE-LEVEL,'D')                                    AS "NUMBER_DAY_OF_WEEK"
      , TO_CHAR(SYSDATE-LEVEL,'W')                                    AS "NUMBER_WEEK_OF_MONTH"
      , TO_CHAR(SYSDATE-LEVEL,'WW')                                   AS "NUMBER_WEEK_OF_YEAR"
   FROM DUAL
CONNECT BY TO_DATE('01/01/2020','DD/MM/YYYY') + LEVEL <= TRUNC(SYSDATE)
ORDER BY TRUNC(SYSDATE-LEVEL) ASC;
```

### Kill Session
```
SELECT OSUSER
     , USER#
     , USERNAME
     , SID
     , SERIAL#
     , STATUS
     , SERVER
     , 'ALTER SYSTEM KILL SESSION '''||SID||','||SERIAL#||''' IMMEDIATE;' AS RUN_COMMAND
  FROM V$SESSION
  ORDER BY STATUS, OSUSER;
  ```

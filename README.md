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

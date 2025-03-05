# Common Issues and Solutions

## Overview
This document provides a structured reference for troubleshooting common issues encountered with Oracle Cloud and Oracle Database services. Each issue is categorized for easy retrieval, with clear symptoms, possible causes, and step-by-step solutions.

---

## 1. Database Connection Issues

### **Issue: Unable to Connect to Oracle Database**
**Symptoms:**
- Connection timeout errors
- "ORA-12541: TNS:no listener" message
- "ORA-01017: Invalid username/password; logon denied"

**Possible Causes:**
- Incorrect connection string or credentials
- Oracle Listener not running or misconfigured
- Network firewall blocking access

**Solution:**
1. Verify that the Oracle Listener is running:
   ```bash
   lsnrctl status
   ```
2. Check `tnsnames.ora` and `listener.ora` configurations.
3. Ensure correct database user credentials.
4. If using Oracle Cloud, check security rules and VCN settings.
5. Test connection using SQL*Plus or SQL Developer.

---

### **Issue: ORA-28001: The Password Has Expired**
**Symptoms:**
- Login fails due to expired password
- "ORA-28001: The password has expired" message

**Possible Causes:**
- User password policy enforces expiration
- Password not reset within allowed period

**Solution:**
1. Log in as a DBA user and reset the password:
   ```sql
   ALTER USER username IDENTIFIED BY new_password;
   ```
2. Modify password expiration policy if necessary:
   ```sql
   ALTER PROFILE default LIMIT PASSWORD_LIFE_TIME UNLIMITED;
   ```

---

## 2. Performance Issues

### **Issue: Slow Query Performance**
**Symptoms:**
- Queries take longer than expected
- High CPU usage on the database server

**Possible Causes:**
- Missing indexes
- Poor execution plan
- High contention on database resources

**Solution:**
1. Analyze query execution:
   ```sql
   EXPLAIN PLAN FOR <your-query>;
   SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
   ```
2. Identify and create missing indexes:
   ```sql
   CREATE INDEX idx_column ON table_name(column_name);
   ```
3. Optimize schema design and consider partitioning large tables.
4. Review background processes causing CPU contention.

---

### **Issue: ORA-01555: Snapshot Too Old**
**Symptoms:**
- Queries fail with "ORA-01555: snapshot too old"

**Possible Causes:**
- Undo tablespace too small
- Long-running queries consuming too much undo data

**Solution:**
1. Increase undo tablespace size:
   ```sql
   ALTER DATABASE DATAFILE '/path_to_undo.dbf' RESIZE 2G;
   ```
2. Modify undo retention settings:
   ```sql
   ALTER SYSTEM SET UNDO_RETENTION = 900;
   ```
3. Tune long-running queries to reduce undo usage.

---

## 3. Backup and Recovery Failures

### **Issue: RMAN Backup Fails Due to Insufficient Space**
**Symptoms:**
- RMAN command fails with "ORA-19809: Exceeding DB_RECOVERY_FILE_DEST_SIZE"

**Possible Causes:**
- Recovery file destination is full
- Old backups not being cleared

**Solution:**
1. Increase recovery file destination size:
   ```sql
   ALTER SYSTEM SET DB_RECOVERY_FILE_DEST_SIZE = 20G;
   ```
2. Remove obsolete backups:
   ```sql
   RMAN> DELETE OBSOLETE;
   ```
3. Review backup retention policies.

---

## 4. Security & Access Control

### **Issue: User Lacks Privileges on Database Objects**
**Symptoms:**
- "ORA-00942: Table or view does not exist" error

**Possible Causes:**
- Insufficient permissions
- Object exists in a different schema

**Solution:**
1. Grant required privileges:
   ```sql
   GRANT SELECT, INSERT, UPDATE ON table_name TO username;
   ```
2. Check schema references when accessing objects:
   ```sql
   SELECT * FROM user_tables;
   ```

---

## 5. Cloud-Specific Issues

### **Issue: Oracle Cloud Compute Instance Fails to Start**
**Symptoms:**
- Instance stuck in "Starting" state
- Cannot SSH into the instance

**Possible Causes:**
- Insufficient compute resources
- Misconfigured VCN or security list

**Solution:**
1. Check instance logs in the Oracle Cloud Console.
2. Verify VCN security rules allow SSH (port 22).
3. Restart the instance manually.

---

## 6. Data Corruption & Recovery

### **Issue: ORA-01578: ORACLE Data Block Corrupted**
**Symptoms:**
- "ORA-01578: ORACLE data block corrupted" error

**Possible Causes:**
- Hardware or disk failure
- Logical corruption in data blocks

**Solution:**
1. Identify corrupted blocks:
   ```sql
   SELECT * FROM V$DATABASE_BLOCK_CORRUPTION;
   ```
2. Use RMAN to recover corrupted blocks:
   ```sql
   RMAN> BLOCKRECOVER CORRUPTION LIST;
   ```
3. Check hardware logs for disk failures.

---

## Conclusion
This document provides solutions to common Oracle Cloud and Database issues. It should be regularly updated to include newly discovered problems and their resolutions.


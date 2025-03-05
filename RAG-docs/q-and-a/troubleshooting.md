# Internal Troubleshooting Guides

## Overview
This document provides internal troubleshooting procedures for resolving common Oracle Cloud and Oracle Database issues encountered by support teams. It includes step-by-step diagnostics, recommended actions, and escalation procedures.

---

## 1. Database Connection Failures

### **Issue: ORA-12541: TNS No Listener**
**Symptoms:**
- Application unable to connect to database
- "ORA-12541: TNS:no listener" error in logs

**Troubleshooting Steps:**
1. Verify that the Oracle Listener is running:
   ```bash
   lsnrctl status
   ```
2. If the listener is not running, start it:
   ```bash
   lsnrctl start
   ```
3. Check the `listener.ora` file for misconfigurations.
4. Ensure the database service is registered with the listener:
   ```sql
   ALTER SYSTEM REGISTER;
   ```
5. Restart the listener and test the connection.

**Escalation:**
- If the issue persists, escalate to the database administrator (DBA) for further debugging.

---

### **Issue: ORA-01017: Invalid Username/Password**
**Symptoms:**
- User unable to log in
- "ORA-01017: Invalid username/password; logon denied" error

**Troubleshooting Steps:**
1. Verify the username and password by logging in manually:
   ```sql
   sqlplus username/password@database
   ```
2. Check if the user account is locked:
   ```sql
   SELECT username, account_status FROM dba_users WHERE username = 'USER';
   ```
3. If locked, unlock the account:
   ```sql
   ALTER USER username ACCOUNT UNLOCK;
   ```
4. If the password is expired, reset it:
   ```sql
   ALTER USER username IDENTIFIED BY new_password;
   ```

**Escalation:**
- If authentication issues persist, check authentication method (e.g., database authentication vs. LDAP).

---

## 2. Performance Issues

### **Issue: High CPU Utilization in Oracle Database**
**Symptoms:**
- Queries running slower than expected
- CPU usage consistently above 90%

**Troubleshooting Steps:**
1. Identify top CPU-consuming sessions:
   ```sql
   SELECT s.sid, s.serial#, p.spid, s.username, s.program, t.sql_id
   FROM v$session s JOIN v$process p ON s.paddr = p.addr
   JOIN v$sqltext t ON s.sql_id = t.sql_id
   ORDER BY p.spid;
   ```
2. Identify the SQL statement causing high CPU usage:
   ```sql
   SELECT sql_text FROM v$sql WHERE sql_id = 'SQL_ID';
   ```
3. Optimize the query by analyzing execution plan:
   ```sql
   EXPLAIN PLAN FOR <your-query>;
   SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
   ```
4. If necessary, kill the problematic session:
   ```sql
   ALTER SYSTEM KILL SESSION 'sid,serial#' IMMEDIATE;
   ```

**Escalation:**
- If the issue persists, escalate to the performance tuning team.

---

## 3. Backup and Recovery Issues

### **Issue: RMAN Backup Failure - ORA-19809: Exceeding Recovery File Destination Size**
**Symptoms:**
- RMAN backups fail with "ORA-19809: Exceeding DB_RECOVERY_FILE_DEST_SIZE"

**Troubleshooting Steps:**
1. Check the current recovery file destination size:
   ```sql
   SHOW PARAMETER DB_RECOVERY_FILE_DEST_SIZE;
   ```
2. Increase the size if possible:
   ```sql
   ALTER SYSTEM SET DB_RECOVERY_FILE_DEST_SIZE = 20G;
   ```
3. Remove obsolete backups:
   ```sql
   RMAN> DELETE OBSOLETE;
   ```
4. Run an RMAN crosscheck to ensure backup integrity:
   ```sql
   RMAN> CROSSCHECK BACKUP;
   ```

**Escalation:**
- If space constraints persist, escalate to storage administration.

---

## 4. Security & Access Control Issues

### **Issue: User Unable to Access Required Tables**
**Symptoms:**
- "ORA-00942: Table or view does not exist" error

**Troubleshooting Steps:**
1. Verify the user has the required privileges:
   ```sql
   SELECT * FROM user_tab_privs WHERE grantee = 'USERNAME';
   ```
2. Grant necessary privileges if missing:
   ```sql
   GRANT SELECT, INSERT, UPDATE ON schema.table TO username;
   ```
3. If using Oracle Cloud, check IAM policies and roles.

**Escalation:**
- If permissions appear correct but issue persists, escalate to the security team.

---

## 5. Cloud-Specific Issues

### **Issue: Oracle Cloud Compute Instance Not Responding**
**Symptoms:**
- Instance is unreachable via SSH
- Instance stuck in "Stopping" or "Starting" state

**Troubleshooting Steps:**
1. Check instance status in the Oracle Cloud Console.
2. Verify VCN security rules allow SSH traffic (port 22).
3. Use serial console to check for boot errors.
4. Restart the instance manually.
5. If the instance is unresponsive, attempt to restore from a backup.

**Escalation:**
- If restoration fails, escalate to Oracle Cloud Infrastructure support.

---

## 6. Escalation Matrix

| Issue Category | Initial Troubleshooting | Escalation Level 1 | Escalation Level 2 |
|---------------|----------------------|------------------|------------------|
| Connection Issues | Check listener, logs | DBA | Network Team |
| Performance Issues | Analyze SQL, check CPU | Performance Tuning Team | System Administrators |
| Backup Failures | Check RMAN logs, disk space | Storage Team | Oracle Support |
| Security Issues | Verify permissions | Security Team | IAM Administrator |
| Cloud Instance Failures | Check console, restart | Cloud Support Team | Oracle Cloud Support |

---

## Conclusion
This document provides internal troubleshooting steps for resolving common Oracle-related issues. It should be continuously updated as new troubleshooting techniques and best practices emerge.


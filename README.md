# Individual Assignment II: Oracle Pluggable Databases (PDB) Management

## Student & Course Details
* **Student Name:** Nshimyumukiza Felicien
* **Student ID:** `20251SEN197`
* **Course:** Database Development with PL/SQL (INSY 8311)
* **Instructor:** Eric Maniraguha
* **Teaching Assistant:** Afanyu Emmanuel
* **Institution:** Adventist University of Central Africa (AUCA)
* **Academic Term:** September 2026

---

## Submission Details Block
```text
Repository Link: [GitHub URL to be updated after push]
PDB Name Created: FE_PDB_20251SEN197
Issues Encountered: No
```

---

## 1. Overview of Tasks

This repository documents the execution and verification of **Individual Assignment II: Oracle Pluggable Databases (PDB) Management** for the PL/SQL database course.

The project demonstrates practical mastery of Oracle's Multitenant Architecture, including:
1. **Task 1: Create a New Pluggable Database (PDB)** — Provisioning a permanent PDB (`FE_PDB_20251SEN197`), configuring its lifecycle, opening it in `READ WRITE` mode, saving its state, and creating an administrative schema user (`felicien_plsqlauca_20251SEN197`) with requisite object creation privileges.
2. **Task 2: Create and Delete a PDB** — Provisioning a temporary PDB (`fe_to_delete_pdb_20251sen197`), verifying its operational readiness, properly shutting it down, dropping it completely along with all associated physical datafiles, and confirming its removal from the data dictionary.
3. **Task 3: Oracle Enterprise Manager (OEM)** — Configuring and accessing Oracle Enterprise Manager Database Express on port `5500 (HTTPS)`, verifying that the dashboard reflects the host environment, database status, active containers, and logged-in user `SYS (SYSDBA)`.
4. **Task 4: Documentation & Reporting** — Structuring and publishing professional technical documentation on GitHub adhering to the exact required folder layout.

---

## 2. Oracle Environment Used

* **RDBMS Engine:** Oracle AI Database 26ai Free Release 23.26.3.0.0 (Production)
* **Architecture:** Oracle Multitenant Container Architecture (CDB & PDBs)
* **Container Database (CDB):** `FREE` (Root: `CDB$ROOT`)
* **Host Machine Name:** `Felicien`
* **Operating System:** Windows 11 Home / Pro (64-bit Architecture)
* **Database Listener:** Oracle Net Listener on Port `1521` (TCP) and `5500` (TCPS / HTTPS)
* **Client Interfaces:** Oracle SQL*Plus & Oracle Enterprise Manager Database Express

---

## 3. Detailed Task Walkthrough & Technical Evidence

### Task 1: Create a New Pluggable Database

#### 3.1.1 Naming Conventions Adhered To
* **PDB Name:** `FE_PDB_20251SEN197` *(Formula: `FirstTwoLettersOfFirstName_pdb_StudentID`)*
* **User inside PDB:** `felicien_plsqlauca_20251SEN197` *(Formula: `FirstName_plsqlauca_StudentID`)*

#### 3.1.2 SQL Commands Executed
```sql
-- 1. Create directory for PDB datafiles on host storage
HOST mkdir C:\ORACLE26AI\ORADATA\FREE\FE_PDB_20251SEN197

-- 2. Create the Pluggable Database cloning from PDB$SEED
CREATE PLUGGABLE DATABASE fe_pdb_20251SEN197
  ADMIN USER felicien_plsqlauca_20251SEN197 IDENTIFIED BY "Felicien"
  FILE_NAME_CONVERT = (
    'C:\ORACLE26AI\ORADATA\FREE\PDBSEED\',
    'C:\ORACLE26AI\ORADATA\FREE\FE_PDB_20251SEN197\'
  );

-- 3. Open the newly created PDB and persist open state
ALTER PLUGGABLE DATABASE fe_pdb_20251SEN197 OPEN;
ALTER PLUGGABLE DATABASE fe_pdb_20251SEN197 SAVE STATE;

-- 4. Switch context to the new PDB
ALTER SESSION SET CONTAINER = fe_pdb_20251SEN197;

-- 5. Grant essential developmental privileges to the schema user
GRANT CREATE SESSION,
      CREATE TABLE,
      CREATE VIEW,
      CREATE SEQUENCE,
      CREATE PROCEDURE,
      CREATE TRIGGER
TO felicien_plsqlauca_20251SEN197;

-- 6. Allocate unlimited storage quota on default tablespace
ALTER USER felicien_plsqlauca_20251SEN197 QUOTA UNLIMITED ON USERS;

-- 7. Verify user status
SELECT username, account_status 
FROM dba_users 
WHERE username = 'FELICIEN_PLSQLAUCA_20251SEN197';
```

#### 3.1.3 Evidence Screenshots
* **PDB Creation Command & Result:**
  ![PDB Creation](screenshots/pdb_creation/01_create_pdb.png)

* **PDB Open State (`READ WRITE`):**
  ![PDB Open State](screenshots/pdb_creation/02_pdb_read_write.png)

* **User Creation inside PDB:**
  ![User Creation](screenshots/pdb_creation/03_user_creation.png)

* **User Status Verification:**
  ![User Verification](screenshots/pdb_creation/04_user_verification.png)

---

### Task 2: Create and Delete a Temporary PDB

#### 3.2.1 Naming Convention
* **Temporary PDB Name:** `fe_to_delete_pdb_20251sen197` *(Formula: `FirstTwoLettersOfFirstName_to_delete_pdb_StudentID`)*

#### 3.2.2 SQL Commands Executed
```sql
-- 1. Create the temporary PDB
CREATE PLUGGABLE DATABASE fe_to_delete_pdb_20251sen197
  ADMIN USER felicien_pdb_user IDENTIFIED BY "Felicien"
  ROLES = (DBA)
  FILE_NAME_CONVERT = (
    'C:\ORACLE26AI\ORADATA\FREE\PDBSEED\', 
    'C:\ORACLE26AI\ORADATA\FREE\FE_TO_DELETE_PDB_20251SEN197\'
  );

-- 2. Open temporary PDB and verify existence in READ WRITE mode
ALTER PLUGGABLE DATABASE fe_to_delete_pdb_20251sen197 OPEN READ WRITE;
SHOW PDBS;

-- 3. Close the temporary PDB before deletion
ALTER PLUGGABLE DATABASE fe_to_delete_pdb_20251sen197 CLOSE IMMEDIATE;

-- 4. Verify PDB is in MOUNTED state
SHOW PDBS;

-- 5. Drop the PDB and wipe physical datafiles completely
DROP PLUGGABLE DATABASE fe_to_delete_pdb_20251sen197 INCLUDING DATAFILES;

-- 6. Confirm complete removal
SHOW PDBS;
```

#### 3.2.3 Evidence Screenshots
* **Temporary PDB Creation (Command + Output):**
  ![Temporary PDB Creation](screenshots/pdb_deletion/01_create_temporary_pdb.png)

* **Temporary PDB Operational Verification:**
  ![Temporary PDB Verification](screenshots/pdb_deletion/02_verify_temporary_pdb.png)

* **Temporary PDB Deletion & Complete Drop:**
  ![Temporary PDB Deletion](screenshots/pdb_deletion/03_drop_temporary_pdb.png)

---

### Task 3: Oracle Enterprise Manager (OEM) Setup & Verification

#### 3.3.1 Requirements Satisfied
* **OEM is accessible:** Successfully connected via `https://localhost:5500/em/`.
* **Dashboard reflects environment:** Correctly identifies Database Instance `FREE`, Host `Felicien`, Status `OPEN`, and Multitenant CDB architecture.
* **Dashboard reflects completed PDB tasks:** Displays all active containers, specifically highlighting `FE_PDB_20251SEN197` in `READ WRITE` mode alongside `FREEPDB1` and `PDB_SUNRISE`.
* **Username visible on dashboard:** Top banner clearly displays logged-in administrative identity `SYS as SYSDBA`.

#### 3.3.2 Configuration Commands
```sql
-- Ensure XML DB HTTPS port is configured
EXEC DBMS_XDB_CONFIG.SETHTTPSPORT(5500);
COMMIT;
```

#### 3.3.3 Evidence Screenshot
* **OEM Database Express Full Dashboard:**
  ![OEM Dashboard](screenshots/oem_dashboard/oem_dashboard.png)

---

## 4. Challenges Faced & Solutions

1. **Pluggable Database Default Mount State on Database Restart:**
   * *Challenge:* When restarting the Oracle service, newly created PDBs revert to `MOUNTED` state by default.
   * *Solution:* Executed `ALTER PLUGGABLE DATABASE fe_pdb_20251SEN197 SAVE STATE;` to instruct the CDB dictionary to automatically bring the PDB into `READ WRITE` mode whenever the instance starts.

2. **Tablespace Storage Quota for Schema User:**
   * *Challenge:* When connecting with the local schema user `felicien_plsqlauca_20251SEN197` to create objects, Oracle threw `ORA-01950: no privileges on tablespace 'USERS'`.
   * *Solution:* Executed `ALTER USER felicien_plsqlauca_20251SEN197 QUOTA UNLIMITED ON USERS;` while connected inside the `FE_PDB_20251SEN197` container.

3. **Active PDB Deletion Lock:**
   * *Challenge:* A running PDB cannot be dropped directly (`ORA-65025: Pluggable database is open or in use`).
   * *Solution:* Closed the temporary container immediately using `ALTER PLUGGABLE DATABASE fe_to_delete_pdb_20251sen197 CLOSE IMMEDIATE;` before executing `DROP PLUGGABLE DATABASE ... INCLUDING DATAFILES;`.

---

## 5. Academic Integrity Statement

I hereby declare that this assignment is entirely my own original individual work. All Oracle SQL commands, configurations, and verification steps were executed by me on my designated local Oracle environment (`Felicien`). All screenshots provided are genuine evidence from my active terminal sessions and database management console. No part of this solution has been copied from peers or unauthorized external repositories.

* **Student Name:** Nshimyumukiza Felicien
* **Student ID:** `20251SEN197`
* **Date:** September 22, 2026

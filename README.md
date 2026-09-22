# Oracle PDB Assignment II

## Student Information

**Student Name:** Mugisha Kalisa Orion
**Student ID:** 27426
**Course:** PL/SQL / Oracle Database

---

## 1. Project Overview

This assignment focused on working with Oracle Pluggable Databases (PDBs). I created a new PDB, configured a database user, created a temporary PDB for testing, and then deleted the temporary PDB completely.

The main PDB created for this assignment is:

`MU_PDB_27426`

The database user created for the coursework is:

`MU_PLSQLAUCA_27426`

---

## 2. Oracle Environment

The assignment was completed using:

* Oracle Database Free 26AI
* Oracle SQL Developer
* Windows
* Host: `localhost`
* Port: `1521`
* Main PDB: `MU_PDB_27426`
* Oracle service: `FREEPDB1`

Administrative database operations were performed using the SYS account.

---

# Task 1 — Create a New Pluggable Database

## Objective

The first task was to create a new Pluggable Database (PDB) using the required naming format.

The PDB was created with the name:

`MU_PDB_27426`

The required coursework user was:

`MU_PLSQLAUCA_27426`

## PDB Creation

The Oracle installation required a `FILE_NAME_CONVERT` clause because Oracle Managed Files (OMF) was not enabled.

The PDB seed files were located in:

`C:\APP\KALIS\PRODUCT\26AI\ORADATA\FREE\PDBSEED\`

The PDB was therefore created using a separate destination directory.

Example command:

```sql
CREATE PLUGGABLE DATABASE MU_PDB_27426
ADMIN USER MU_PLSQLAUCA_27426
IDENTIFIED BY [password]
FILE_NAME_CONVERT = (
    'C:\APP\KALIS\PRODUCT\26AI\ORADATA\FREE\PDBSEED\',
    'C:\APP\KALIS\PRODUCT\26AI\ORADATA\FREE\MU_PDB_27426\'
);
```

The password is not included in this repository for security reasons.

## Verification

The PDB was checked using:

```sql
SHOW PDBS;
```

The final status was:

```text
MU_PDB_27426    READ WRITE
```

The PDB was also saved so that its open state is maintained:

```sql
ALTER PLUGGABLE DATABASE MU_PDB_27426 SAVE STATE;
```

---

# Task 2 — Create and Delete a PDB

## Objective

The second task required creating a temporary PDB and then deleting it completely.

The temporary PDB was named:

`MU_TO_DELETE_PDB_27426`

## Creating the Temporary PDB

The temporary PDB was created using:

```sql
CREATE PLUGGABLE DATABASE MU_TO_DELETE_PDB_27426
ADMIN USER temp_admin
IDENTIFIED BY [password]
FILE_NAME_CONVERT = (
    'C:\APP\KALIS\PRODUCT\26AI\ORADATA\FREE\PDBSEED\',
    'C:\APP\KALIS\PRODUCT\26AI\ORADATA\FREE\MU_TO_DELETE_PDB_27426\'
);
```

The PDB was then opened and verified using:

```sql
ALTER PLUGGABLE DATABASE MU_TO_DELETE_PDB_27426 OPEN;
```

and:

```sql
SHOW PDBS;
```

The temporary PDB appeared in the list.

## Deleting the Temporary PDB

Before deletion, the PDB had to be closed:

```sql
ALTER PLUGGABLE DATABASE MU_TO_DELETE_PDB_27426 CLOSE IMMEDIATE;
```

It was then completely removed using:

```sql
DROP PLUGGABLE DATABASE MU_TO_DELETE_PDB_27426 INCLUDING DATAFILES;
```

The `INCLUDING DATAFILES` option was used to remove the associated database files as part of the deletion.

The deletion was verified using:

```sql
SHOW PDBS;
```

The temporary PDB was no longer listed.

---

# Task 3 — User and PDB Verification

The main PDB was verified as:

```text
MU_PDB_27426
```

with the following status:

```text
READ WRITE
```

The coursework user was:

```text
MU_PLSQLAUCA_27426
```

The user was checked using:

```sql
SELECT USERNAME, ACCOUNT_STATUS
FROM DBA_USERS
WHERE USERNAME = 'MU_PLSQLAUCA_27426';
```

The user already existed in the PDB and was available for the coursework.

The user was granted the required basic privileges:

```sql
GRANT CREATE SESSION,
      CREATE TABLE,
      CREATE VIEW,
      CREATE SEQUENCE,
      CREATE PROCEDURE
TO MU_PLSQLAUCA_27426;
```

---

# 4. SQL Commands Used

## Check PDBs

```sql
SHOW PDBS;
```

This displays the PDBs in the container database and shows whether they are open, mounted, or read-only.

## Switch to the root container

```sql
ALTER SESSION SET CONTAINER = CDB$ROOT;
```

This was used when performing PDB administration from the root container.

## Switch to the main PDB

```sql
ALTER SESSION SET CONTAINER = MU_PDB_27426;
```

This changes the current session from the root container into the main PDB.

## Check the current container

```sql
SHOW CON_NAME;
```

This confirms which container the current SQL session is using.

## Save PDB State

```sql
ALTER PLUGGABLE DATABASE MU_PDB_27426 SAVE STATE;
```

This saves the PDB's open state.

## Verify the PDB

```sql
SELECT NAME, OPEN_MODE
FROM V$PDBS
WHERE NAME = 'MU_PDB_27426';
```

This confirms that the main PDB exists and shows its current open mode.

---

# 5. Results

The main PDB was successfully created and configured.

### Main PDB

```text
MU_PDB_27426
READ WRITE
```

### Coursework User

```text
MU_PLSQLAUCA_27426
OPEN
```

### Temporary PDB

```text
MU_TO_DELETE_PDB_27426
```

The temporary PDB was successfully created, opened, closed, and deleted.

After deletion, it no longer appeared in:

```sql
SHOW PDBS;
```

---

# 6. Business Scenario

A university database system may use multiple Pluggable Databases to separate different applications, departments, or student projects while still operating within one Oracle Container Database.

For this assignment, `MU_PDB_27426` represents a dedicated database environment for the student's PL/SQL coursework.

Using a separate PDB helps isolate the coursework database objects from other databases running in the same Oracle environment. A temporary PDB was also created to demonstrate how a database administrator can create and remove an isolated database environment when it is no longer required.

---

# 7. Challenges and Resolutions

## Challenge 1 — FILE_NAME_CONVERT Error

During PDB creation, Oracle returned:

```text
ORA-65016: FILE_NAME_CONVERT must be specified
```

The issue occurred because Oracle Managed Files were not enabled and no PDB file-name conversion setting was defined.

### Resolution

The location of the PDB seed files was identified and the `FILE_NAME_CONVERT` clause was added to the `CREATE PLUGGABLE DATABASE` command.

---

## Challenge 2 — Existing Database Files

An attempt to create the PDB produced:

```text
ORA-01537: cannot add file ... - file already part of database
```

This happened because the destination database files already existed and were already associated with the database.

### Resolution

The existing PDB was checked using:

```sql
SHOW PDBS;
```

It was found that the PDB had already been created and was mounted. The existing PDB was therefore used instead of attempting to create another copy.

---

## Challenge 3 — PDB Name

The PDB initially appeared under a different name.

The PDB was eventually configured as:

```text
MU_PDB_27426
```

The final configuration was verified with:

```sql
SHOW PDBS;
```

---

## Challenge 4 — Deleting the Temporary PDB

An attempt to delete the temporary PDB while it was open resulted in:

```text
ORA-65025: Pluggable database ... is not closed on all instances.
```

### Resolution

The temporary PDB was closed first:

```sql
ALTER PLUGGABLE DATABASE MU_TO_DELETE_PDB_27426 CLOSE IMMEDIATE;
```

It was then deleted using:

```sql
DROP PLUGGABLE DATABASE MU_TO_DELETE_PDB_27426 INCLUDING DATAFILES;
```

---

# 8. Screenshots

Screenshots are organized into the following folders:

```text
screenshots/
<img width="3840" height="2035" alt="pdb_creation" src="https://github.com/user-attachments/assets/e51e6574-c6af-4196-987e-99391a55340c" />
<img width="3840" height="2073" alt="2" src="https://github.com/user-attachments/assets/1d92f9ec-5f6f-49c2-882e-79d2e205bab2" />
<img width="3840" height="2055" alt="3" src="https://github.com/user-attachments/assets/dcaa6eda-2eea-4ede-a4c8-c55927c078b7" />
<img width="3814" height="2018" alt="4" src="https://github.com/user-attachments/assets/f782731c-8507-479a-a0f6-179de2512e98" />
<img width="3840" height="2062" alt="5" src="https://github.com/user-attachments/assets/6050fd8b-3686-4509-b6b9-744da4811bb1" />

```

The screenshots provide evidence of the PDB creation, PDB deletion, verification commands, and database management environment.

---

# 9. Integrity Statement

I confirm that the work submitted in this repository represents my own work. The SQL commands were executed and tested in my Oracle database environment, and the screenshots included in the repository represent my own database setup and results.

---

# 10. Submission Details

**Repository Link:** [Paste your public GitHub repository link here]

**PDB Name Created:** `MU_PDB_27426`

**Issues Encountered:** Yes

The main issues encountered were related to PDB file conversion, existing database files, PDB naming, and closing the temporary PDB before deletion. These issues were resolved during the setup and testing process.

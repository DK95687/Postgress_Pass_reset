# PostgreSQL 18 – Password Reset & Connection Guide

**Purpose:** Reset the PostgreSQL `postgres` user password and establish a successful connection using pgAdmin and Windows CMD.

**Environment:**
- Operating System: Windows 10
- PostgreSQL Version: 18.6
- PostgreSQL Port: 5432
- Database User: postgres
- Database: postgres
- Installation Directory: `C:\Program Files\PostgreSQL\18`
- Data Directory: `C:\Program Files\PostgreSQL\18\data`

---

## 1. Problem Description

Initially, PostgreSQL displayed the following error:

```text
Unable to connect to server:

FATAL: password authentication failed
for user "postgres"
```

**Root Cause:**

The password provided for the PostgreSQL `postgres` user was incorrect.

## 2. Verify PostgreSQL Installation

Open Windows Run:

1. Press `Windows + R`.
2. Enter `services.msc`.
3. Find `postgresql-x64-18`.
4. Right-click and select Properties.
5. Verify the executable path.

Confirmed executable:

```text
"C:\Program Files\PostgreSQL\18\bin\pg_ctl.exe"
runservice -N "postgresql-x64-18"
-D "C:\Program Files\PostgreSQL\18\data" -w
```

## 3. Locate PostgreSQL Authentication File

File name:

`pg_hba.conf`

Location:

```text
C:\Program Files\PostgreSQL\18\data\pg_hba.conf
```

This file controls client authentication and database access.

## 4. Temporarily Change Authentication Method

Open Notepad as Administrator and edit `pg_hba.conf`.

Find the following entries:

```text
host    all    all    127.0.0.1/32    scram-sha-256

host    all    all    ::1/128         scram-sha-256
```

Temporarily replace `scram-sha-256` with `trust`:

```text
host    all    all    127.0.0.1/32    trust

host    all    all    ::1/128         trust
```

Save the file.

**Important:** `trust` allows connections without password authentication. Use it only temporarily on a local development machine.

## 5. Restart PostgreSQL Service

Open Command Prompt as Administrator.

Execute:

```cmd
net stop postgresql-x64-18
```

Then:

```cmd
net start postgresql-x64-18
```

Verify that the service starts successfully.

## 6. Connect to PostgreSQL Without Password

Open a normal Windows CMD.

Execute:

```cmd
"C:\Program Files\PostgreSQL\18\bin\psql.exe" -h 127.0.0.1 -p 5432 -U postgres -d postgres
```

Expected output:

```text
psql (18.6)
Type "help" for help.

postgres=#
```

This confirms that the PostgreSQL command-line connection is established.

## 7. Reset PostgreSQL Password

Execute the following SQL command inside the `postgres=#` prompt:

```sql
ALTER USER postgres WITH PASSWORD 'Admin@123';
```

Expected output:

```text
ALTER ROLE
```

This confirms that the password has been updated.

**Note:** Replace the example password with a strong, unique password for actual use.

## 8. Restore Authentication Security

Open `pg_hba.conf` again.

Change both `trust` entries back to `scram-sha-256`.

Final configuration:

```text
host    all    all    127.0.0.1/32    scram-sha-256

host    all    all    ::1/128         scram-sha-256
```

Save the file.

Restart PostgreSQL:

```cmd
net stop postgresql-x64-18
net start postgresql-x64-18
```

**Security requirement:** Never leave `trust` enabled after completing the password reset.

## 9. Verify the New Password

Open Windows CMD and execute:

```cmd
"C:\Program Files\PostgreSQL\18\bin\psql.exe" -h localhost -p 5432 -U postgres -d postgres
```

Enter the newly configured password when prompted.

Successful output:

```text
psql (18.6)
Type "help" for help.

postgres=#
```

The successful connection confirms that password authentication is working.

## 10. Verify Database Connection

Inside the PostgreSQL command prompt, execute:

```sql
SELECT current_user, current_database();
```

Expected result:

```text
 current_user | current_database
--------------+-----------------
 postgres     | postgres
```

To exit PostgreSQL:

```sql
\q
```

## 11. Configure pgAdmin Connection

Open pgAdmin and configure the server connection.

| Field | Configuration |
|---|---|
| Name | PostgreSQL 18 |
| Host | localhost |
| Port | 5432 |
| Maintenance Database | postgres |
| Username | postgres |
| Password | Newly configured password |

Click Save.

The PostgreSQL server should now connect successfully.

## 12. Important Commands for Future Reference

| Purpose | Command |
|---|---|
| Stop PostgreSQL | `net stop postgresql-x64-18` |
| Start PostgreSQL | `net start postgresql-x64-18` |
| Connect using CMD | `psql.exe -h localhost -p 5432 -U postgres -d postgres` |
| Change password | `ALTER USER postgres WITH PASSWORD 'NewPassword';` |
| Check current user | `SELECT current_user;` |
| Check current database | `SELECT current_database();` |
| List databases | `\l` |
| List tables | `\dt` |
| Exit PostgreSQL | `\q` |

## 13. Troubleshooting Reference

| Error | Possible Solution |
|---|---|
| Failed to resolve host | Verify hostname and DNS configuration |
| Password authentication failed | Verify username and password |
| Connection refused | Check PostgreSQL service and port 5432 |
| Database does not exist | Verify database name |
| Could not open pg_hba.conf | Check installation and data directory permissions |
| Port already in use | Check whether another PostgreSQL instance is using port 5432 |

---

## 14. Final Status

**PostgreSQL 18 Password Reset and Connection Setup**

- PostgreSQL installation verified.
- PostgreSQL service identified.
- Authentication configuration located.
- Password reset procedure completed.
- New password authentication tested.
- pgAdmin connection configuration documented.

**Important:** Keep this document for future PostgreSQL password recovery and connection troubleshooting. Do not store your actual database password in this document or commit it to GitHub.

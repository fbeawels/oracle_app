# SQLScript.sql

## Review

## 1. Summary  
The script creates three relational tables that together model a simple banking application.  
* **`GROUP1_ACCOUNTREQUEST`** – stores a customer’s initial application details.  
* **`GRP1_REGISTEREDINFO`** – holds the fully registered account once the request is approved.  
* **`GRP1_TRANSACTIONINFO`** – records individual transactions against registered accounts.  

The tables are linked via foreign keys (`RequestId` → `Account_Number`).  The design uses standard Oracle SQL constructs (VARCHAR2, NUMBER, DATE), includes basic data validation (CHECK constraints, NOT NULL, DEFAULT values), and sets up primary/foreign key relationships to enforce referential integrity.

---

## 2. Detailed Description  

| Table | Purpose | Key columns | Constraints |
|-------|---------|-------------|-------------|
| `GROUP1_ACCOUNTREQUEST` | Capture a new account application before it is fully processed. | `RequestId`, `Branch`, `AccountType`, `Title`, `FirstName`, `LastName`, `DOB`, `WorkPhone`, `HomePhone`, `Address`, `State`, `Zip`, `Email`, `Status` | Primary key on `RequestId`; CHECK on phone lengths; NOT NULL on all columns; default status “ENTERED”. |
| `GRP1_REGISTEREDINFO` | Persist the registered customer/account once approved. | `Account_Number`, `RequestId`, `Branch`, `Account_Type`, `Title`, `FirstName`, `LastName`, `DOB`, `WorkPhone`, `HomePhone`, `Address`, `State`, `Zip`, `Email`, `Online_registration`, `password` | Primary key on `Account_Number`; foreign key `RequestId` referencing `GROUP1_ACCOUNTREQUEST`; CHECKs on phone lengths; default “N” for online registration. |
| `GRP1_TRANSACTIONINFO` | Store transactional activity per account. | `TransactionId`, `TransactionDate`, `Account_Number`, `Amount`, `ChequeNo`, `Transaction_Type` | Primary key on `TransactionId`; foreign key `Account_Number` referencing `GRP1_REGISTEREDINFO`; CHECK that `Amount` > 0; default `TransactionDate` to current system date. |

### Execution Flow  
1. **Schema Setup** – Running the script in an Oracle environment will create the three tables with the constraints defined above.  
2. **Application Flow** –  
   * A user submits a request → a row is inserted into `GROUP1_ACCOUNTREQUEST`.  
   * Once approved, a new row is inserted into `GRP1_REGISTEREDINFO` (usually copying the request data plus generating an `Account_Number`).  
   * Any debit/credit operations insert rows into `GRP1_TRANSACTIONINFO`.  
3. **Referential Integrity** – Foreign keys prevent orphaned transaction or registration records.  
4. **Cleanup** – Not applicable; standard DDL, no runtime processes.  

### Assumptions & Constraints  
* Phone numbers are stored as numeric types, implying no formatting or leading zeros; the CHECK ensures 10 digits.  
* No uniqueness constraint on `Email` – multiple customers may share an email.  
* `password` is stored as plain text (`VARCHAR2(10)`), which is insecure for production use.  
* `ChequeNo` is marked NOT NULL; any electronic transaction would need to handle a null placeholder or use a different flag.  

### Architecture & Design Choices  
* **Normalized Structure** – Each table serves a distinct logical entity.  
* **Primary/Foreign Keys** – Enforce relationships at the database level.  
* **CHECK Constraints** – Basic data validation in the schema.  
* **Minimal Business Logic** – No triggers, stored procedures, or functions defined in the script; the comment hints that these will be added separately (`plsql_trigger_func.pdf`).  

---

## 3. Functions/Methods  
The provided snippet contains only DDL; there are no PL/SQL functions or procedures to review.  However, the comment indicates that triggers will be added in a separate file (`plsql_trigger_func.pdf`).  

If triggers are created, typical responsibilities might include:  
* **Auto‑generating primary keys** (e.g., using sequences).  
* **Updating `Status` in `ACCOUNTREQUEST`** when a registration is created.  
* **Validating `Amount`** beyond the CHECK (e.g., ensuring sufficient balance).  

---

## 4. Dependencies  
| Item | Type | Notes |
|------|------|-------|
| Oracle Database (12c/19c etc.) | RDBMS | All syntax is Oracle‑specific (VARCHAR2, NUMBER, CHECK). |
| Sequences (assumed for PKs) | DB object | Not shown but likely needed for auto‑generation of IDs. |
| `plsql_trigger_func.pdf` | Documentation | Provides trigger code; not part of the execution. |
| None other |  | No external libraries or APIs used. |

---

## 5. Additional Notes & Recommendations  

### 1. Data Types & Validation  
* **Phone Numbers** – Storing as `NUMBER` removes leading zeros and can cause rounding issues. Prefer `VARCHAR2(10)` and use a format mask or regular expression for validation.  
* **Email** – Add a `CHECK` for a basic email pattern or use a constraint that enforces uniqueness if required.  
* **Password** – Never store plain text. Use a hashed value (`VARCHAR2(64)`) and a proper hashing algorithm (e.g., SHA‑256).  

### 2. Primary Key Generation  
The script defines primary keys but does not provide a mechanism for auto‑generation.  
* **Sequences + Triggers** – Create a sequence per table and a BEFORE INSERT trigger that sets the PK if null.  
* **Identity Columns** – Oracle 12c+ supports `GENERATED AS IDENTITY`.  

### 3. Normalization & Redundancy  
* `GROUP1_ACCOUNTREQUEST` and `GRP1_REGISTEREDINFO` share many columns.  
  * Option: store only the fields that differ (e.g., status, registration date) in the request table, and keep the rest in a single `CUSTOMER` table.  
* `Transaction_Type` is `VARCHAR2(2)` – consider an ENUM or lookup table for clarity.

### 4. Constraints & Business Rules  
* **Balance Calculation** – The schema does not track balances. Consider adding a `BALANCE` column to `GRP1_REGISTEREDINFO` updated via triggers or an application layer.  
* **ChequeNo** – If a transaction can be electronic, make this field nullable or add a `Transaction_Method` flag.  

### 5. Performance & Indexing  
* Create indexes on foreign key columns (`RequestId`, `Account_Number`) for faster joins.  
* Consider a composite index on `(Account_Number, TransactionDate)` to support balance reports.  

### 6. Security & Auditing  
* Add `CREATED_BY`, `CREATED_AT`, `LAST_UPDATED_BY`, `LAST_UPDATED_AT` columns to each table.  
* Use Oracle’s Fine‑Grained Auditing (FGA) or triggers to log changes.  

### 7. Documentation & Naming Conventions  
* Table names mix prefixes (`GROUP1_`, `GRP1_`) – standardize (e.g., `ACCOUNT_REQUEST`, `REGISTERED_INFO`).  
* Column names use mixed case and underscores inconsistently; adopt a single convention.  

### 8. Future Enhancements  
* **Multi‑branch support** – Add a `BRANCH` lookup table.  
* **Account Types** – Use a lookup table for `Account_Type` with attributes like interest rate.  
* **Transaction Types** – Lookup for `DEBIT`, `CREDIT`, `TRANSFER`.  
* **Soft Deletes** – Add a `DELETED` flag instead of hard dropping rows.  

---  

**Bottom Line:**  
The script provides a solid, normalized foundation for a basic banking data model.  Enhancements around key generation, data validation, security (especially for passwords), and additional business logic (balance management, auditing) would be necessary to move from a prototype to a production‑ready system.

## Code Critique



## Code Preview

```sql
-- TABLE CREATION

-- 1.	ACCOUNTREQUEST

create table GROUP1_ACCOUNTREQUEST
(
   RequestId Number(10) PRIMARY KEY,
        Branch varchar2(15) NOT NULL,
        AccountType varchar2(15) NOT NULL,
   Title varchar2(4) NOT NULL,
   FirstName varchar2(15) NOT NULL,
   LastName varchar2(15) NOT NULL,
        DOB  date NOT NULL,
   WorkPhone number(10) NOT NULL,
        CONSTRAINT wtel_length CHECK (LENGTH(WorkPhone)= 10),
   HomePhone number(10) NOT NULL,
        CONSTRAINT htel_length CHECK (LENGTH(HomePhone) =10),
   Address varchar2(30) NOT NULL,
   State varchar2(15) NOT NULL,
   Zip number(10) NOT NULL,
   Email varchar2(30) NOT NULL,
   Status varchar2(10) DEFAULT 'ENTERED'
);
 
-- 2.  REGISTEREDINFO

create table GRP1_REGISTEREDINFO
(
   RequestId Number(10),
   Account_Number Number(6) PRIMARY KEY,
   Branch varchar2(15) NOT NULL,
   Account_Type varchar2(15) NOT NULL,
   Title varchar2(4) NOT NULL,
   FirstName varchar2(15) NOT NULL,
   LastName varchar2(15) NOT NULL,
        DOB  date NOT NULL,
        WorkPhone number(10) NOT NULL,
        CONSTRAINT wtel_length1 CHECK (LENGTH(WorkPhone)= 10),
        HomePhone number(10) NOT NULL,
        CONSTRAINT htel_length1 CHECK (LENGTH(HomePhone) =10),
        Address varchar2(30) NOT NULL,
        State varchar2(15) NOT NULL,
        Zip number(10) NOT NULL,
        Email varchar2(30) NOT NULL,
   Online_registration varchar2(1) DEFAULT 'N',
   password varchar2(10),
   FOREIGN KEY (RequestId) REFERENCES GRP1_ACCOUNTREQUEST(RequestId)
);

-- 3.  TRANSACTIONINFO

 create table GRP1_TRANSACTIONINFO
 (
    TransactionId Number(10) PRIMARY KEY,
    TransactionDate Date DEFAULT Sysdate,
    Account_Number Number(6),
    Amount Number(8) CONSTRAINT amt_value_check CHECK(Amount>0),
    ChequeNo Number(10) NOT NULL,
    Transaction_Type varchar2(2),
    FOREIGN KEY (Account_Number) REFERENCES  GRP1_REGISTEREDINFO(Account_Number)
 );

-- Trigger codes corresponding to each form mentioned in plsql_trigger_func.pdf file with demo


```

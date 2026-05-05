# MODULE 7: ADVANCED OPERATIONS

## Overview

This module covers the remaining advanced topics from the Informatica PowerCenter course that weren't addressed in earlier modules. These topics appear in scenario-based MCQs and are important for understanding how Informatica works in real team environments.

**Topics Covered:**
- **Topic 29:** Version Control — Check In, Check Out, Compare & Export
- **Topic 30:** Flagging Strategy for SCD & Incremental Loads
- **Topic 31:** Stored Procedure Transformation — Advanced Scenarios
- **Topic 32:** Parameter Variables in Mapplets

---

## Topic 29: Version Control — Check In, Check Out, Compare & Export 🔥

### What is Version Control in Informatica?

**Version Control** = A system that tracks changes to repository objects (mappings, sessions, workflows) over time, allowing teams to manage concurrent development safely.

**Think of it as:** Google Docs version history for your ETL objects — you can see who changed what, when, and revert if needed.

**Why it matters:**
- Multiple developers working on same repository
- Need to track changes for audit purposes
- Ability to rollback to previous working version
- Manage migration between environments (Dev → Test → Prod)

---

### Versioned vs Non-Versioned Repository

**Non-Versioned Repository:**
- Default mode
- Objects saved directly, no history
- Last save overwrites previous
- No locking mechanism
- Suitable for single developer

**Versioned Repository:**
- Full version history maintained
- Objects must be checked out before editing
- Locked to prevent concurrent edits
- Every save creates a new version
- Suitable for teams

**How to enable versioning:**
Repository Manager → Repository → Enable Version Control

---

### Check Out ⭐ (EXAM IMPORTANT!)

**What it is:** The action of reserving an object so only you can edit it. Like taking a library book — while you have it checked out, no one else can modify it.

**Why needed:**
- Prevents two developers editing the same mapping simultaneously
- Without checkout, changes could overwrite each other
- Creates a "lock" on the object

**How to Check Out:**

**Method 1: From Repository Manager**
1. Open Repository Manager
2. Navigate to folder → object
3. Right-click → **Check Out**
4. Add a comment (optional but recommended — explains why you're editing)
5. Object now shows lock icon → yours exclusively

**Method 2: From Designer (automatic)**
- When you open a mapping in Designer and start editing
- Informatica prompts: "Object is not checked out. Check out now?"
- Click Yes → automatic checkout

**Method 3: Explicit from Designer**
- Right-click mapping → Check Out
- Or: Version menu → Check Out

**What you see after checkout:**
```
Object status indicators:
🔒 Lock icon = Checked out by someone
👤 Your icon = Checked out by YOU
📄 No icon   = Available (not checked out)
```

---

### Check In ⭐

**What it is:** Saving your changes back to the repository and releasing the lock so others can edit.

**Important:** Check In ≠ just saving. Checking in:
1. Creates a new version in the repository
2. Releases the lock (others can now edit)
3. Records who made changes and when
4. Allows you to add a comment/label describing changes

**How to Check In:**

**From Designer:**
1. Complete your edits and validate mapping
2. Right-click mapping → **Check In**
3. Enter check-in comment (describe what changed)
   - Example: "Added error handling for NULL customer IDs"
4. Click OK
5. New version created, lock released

**From Repository Manager:**
1. Right-click checked-out object
2. **Check In**
3. Add comment → OK

**Check In Options:**

| Option | Behavior |
|--------|---------|
| **Keep Checked Out** | Creates new version but keeps your lock — continue editing |
| **Check In** | Creates new version AND releases lock |
| **Undo Checkout** | Discards all your changes, releases lock, reverts to previous version |

**Undo Checkout = Discard changes (like git reset hard)**

---

### Version History ⭐

**Viewing all versions of an object:**

1. Right-click object → **View History**
2. Shows table:

```
Version | Modified By | Modified Date  | Comment
1       | admin       | 2025-01-10     | Initial creation
2       | john_dev    | 2025-03-15     | Added filter logic
3       | mary_dev    | 2025-06-20     | Performance optimization
4       | john_dev    | 2025-11-05     | Added error handling
```

**Actions from version history:**
- **Open version:** View old version (read-only)
- **Recover:** Restore a specific old version
- **Compare:** Diff two versions (covered below)

---

### Comparing Objects ⭐

**Purpose:** See exactly what changed between two versions of an object.

**Why useful:**
- Understand what a colleague changed
- Debug regression — find what broke after last change
- Code review before promoting to production
- Audit trail for compliance

**How to Compare Two Versions:**
1. Right-click object → **View History**
2. Select version 1 (Ctrl+Click version 2)
3. Click **Compare**
4. Diff view shows:
   - **Green:** Added elements
   - **Red:** Removed elements
   - **Yellow:** Modified elements

**How to Compare Two Objects (Different Mappings):**
1. Repository Manager → select first object
2. Ctrl+Click second object
3. Right-click → **Compare**
4. Shows differences between the two objects

**Common use case:**
- Compare Dev mapping vs Prod mapping before deployment
- Confirm only intended changes were made

---

### Export and Import Objects ⭐⭐

**Purpose:** Move objects between repositories — typically Dev → Test → Prod migration.

**What can be exported:**
- Mappings
- Sessions and Workflows
- Transformations and Mapplets
- Source and Target definitions
- Connection objects

---

#### Exporting Objects

**From Repository Manager:**
1. Select the objects to export (Ctrl+Click for multiple)
2. Repository → **Export Objects** (or right-click → Export)
3. Configure export options:

**Export Dialog Options:**

| Option | Description |
|--------|-------------|
| **Export referenced objects** | Also exports all objects the selected ones depend on (sources, targets, lookups, reusable transformations) |
| **Include dependencies** | Ensures nothing is missing in target repo |
| **File location** | Where to save the .xml export file |

4. Click Export → generates `.xml` file

**Export file format:** XML — human-readable, can be version-controlled in Git

**Best Practice:** Always check "Export referenced objects" — ensures the receiving repository has all dependencies.

---

#### Importing Objects

**In Target Repository (e.g., Production):**
1. Repository Manager → Connect to target repository
2. Repository → **Import Objects**
3. Browse to the exported `.xml` file
4. Configure import options:

**Import Dialog Options:**

| Option | Behavior |
|--------|---------|
| **Replace existing objects** | Overwrites if object already exists in target |
| **Rename conflicting objects** | Keeps existing, renames incoming with suffix |
| **Import referenced objects** | Also imports dependencies from file |
| **Retain object labels** | Preserves version labels from source |

5. Review import log for errors
6. Click Import

**Common Issues During Import:**

| Issue | Cause | Solution |
|-------|-------|---------|
| "Connection not found" | Source connection doesn't exist in target | Create connection in target repo first |
| "Folder doesn't exist" | Target folder missing | Create folder before import |
| "Object already exists" | Same name object in target | Choose Replace or Rename option |
| "Missing referenced object" | Dependency not in export file | Re-export with "Include dependencies" |

---

### Labels and Deployment Groups ⭐

**Labels:** Tags applied to a specific version of an object at a point in time.

**Use case:** Mark "PROD_RELEASE_2025_NOV" on all objects currently in production. When you need to recover, you can retrieve exactly this state.

**How to create:**
1. Right-click object → **Apply Label**
2. Create new label or select existing
3. Label applied to current version

**Deployment Groups:**
- Named collection of objects that should be deployed together
- Example: "Nov_2025_Release" = all mappings/workflows for this sprint
- Export/import entire group as one unit

---

### Scenario-Based Questions:

**Q1:** Developer A is editing mapping m_Load_Customers. Developer B tries to open the same mapping. What happens?
- A) Both can edit simultaneously
- B) Developer B gets a warning that object is checked out by Developer A ✅
- C) Developer B's changes overwrite Developer A's
- D) An error occurs

**Why?** Checkout creates a lock. Developer B can open in read-only mode but cannot edit until Developer A checks in.

---

**Q2:** You made major changes to a workflow but realized it's wrong. You want to discard all changes and restore the previous version. What should you do?
- A) Delete and recreate the workflow
- B) Check In and then manually revert
- C) Undo Checkout ✅
- D) Import from backup

**Why?** Undo Checkout discards all changes since checkout and releases the lock — like git reset. The object reverts to its state before you checked it out.

---

**Q3:** You need to move a mapping from Dev to Prod repository. The mapping uses a Lookup transformation that references a shared dimension table. Which export option is critical?
- A) Replace existing objects
- B) Export referenced objects ✅
- C) Retain object labels
- D) Export to CSV format

**Why?** "Export referenced objects" ensures the Lookup's source definition and all other dependencies are included in the export file. Without this, the import will fail with missing dependency errors.

---

**Q4:** You want to find exactly what changed between version 3 and version 4 of a mapping. What feature?
- A) View History
- B) Compare ✅
- C) Export both versions
- D) Check In comment

**Why?** Compare shows a diff between two versions — additions, deletions, modifications highlighted visually.

---

**Q5:** After importing a mapping to the production repository, the session fails with "Connection not found." What is the most likely cause?
- A) Mapping has errors
- B) The database connection object wasn't created in the production repository ✅
- C) Wrong import option selected
- D) Version mismatch

**Why?** Connection objects are environment-specific (different credentials for Dev vs Prod). They must be manually created in the target repo or exported/imported separately.

---

---

## Topic 30: Flagging Strategy for SCD & Incremental Loads 🔥

### What is the Flagging Strategy?

**Flagging** = A method where source records are marked with a status flag to indicate what ETL action should be taken for each row.

**Think of it as:** A routing label on a package — before ETL processes a row, the flag tells it: "this one is new," "this one changed," "this one is deleted."

**Where flags come from:**
- Source system sets the flag (most common in practice)
- ETL process derives the flag by comparing source to target
- CDC mechanism populates the flag

---

### Why Use Flags?

**Problem without flags:**
```
Source feed arrives daily with 1M rows.
Without flags, ETL must:
1. Compare EVERY row against target to detect new/changed
2. Run expensive lookups on all 1M rows
3. Decision logic for every single row

With flags:
Source already tells ETL what happened.
Only process N/U/D rows — skip unchanged rows.
Much faster, less complex logic.
```

---

### Standard Flag Values ⭐

| Flag Value | Meaning | ETL Action |
|------------|---------|------------|
| **N** | New record (Insert) | `DD_INSERT` |
| **U** | Updated record (Update) | `DD_UPDATE` |
| **D** | Deleted record (Delete/Soft delete) | `DD_DELETE` or mark inactive |
| **' '** (space) or **NULL** | No change | Skip / `DD_REJECT` |

**Some systems use numeric flags:**

| Flag Value | Meaning |
|------------|---------|
| **1** | New |
| **2** | Updated |
| **3** | Deleted |
| **0** | No change |

---

### Complete Flagging Implementation ⭐⭐

#### Step 1: Source Table Setup

Source system provides a file/table with a FLAG column:

```
EMP_ID | EMP_NAME    | SALARY | DEPT   | FLAG
101    | John Smith  | 50000  | IT     | N    ← New employee
102    | Jane Doe    | 65000  | HR     | U    ← Salary changed
103    | Bob Lee     | 45000  | Sales  | D    ← Left company
104    | Alice Wang  | 70000  | IT     |      ← No change, skip
```

---

#### Step 2: Filter to Only Changed Records

**Filter Transformation: FLT_CHANGED_ONLY**
```
Condition: FLAG IN ('N', 'U', 'D')
```

This immediately removes all unchanged records (blank/NULL flag) from processing. No wasted lookups or comparisons on rows that don't need action.

```
Source (1M rows)
    ↓
Filter: FLAG IN ('N', 'U', 'D')
    ↓
Only 5000 rows continue (the changed ones)
```

---

#### Step 3: Route by Flag Value

**Router Transformation: RTR_FLAG_ROUTE**

```
Group NEW:     FLAG = 'N'   → Insert path
Group UPDATE:  FLAG = 'U'   → Update path
Group DELETE:  FLAG = 'D'   → Delete path
Default:                    → Error log (unexpected flag value)
```

---

#### Step 4: Apply Update Strategy Per Path

**For NEW records (Insert):**
```
Update Strategy: UPD_INSERT
Expression: DD_INSERT
Target: EMP_DWH (Data Driven)
```

**For UPDATE records (Update):**
```
Update Strategy: UPD_UPDATE
Expression: DD_UPDATE
Target: EMP_DWH (Data Driven)
Key Column: EMP_ID (used to find existing row)
```

**For DELETE records (Soft Delete — recommended):**
```
Expression: EXP_SOFT_DELETE
  O_IS_ACTIVE = 'N'
  O_DELETED_DATE = SYSDATE

Update Strategy: UPD_SOFT_DELETE
Expression: DD_UPDATE  ← Update, not delete
Target: EMP_DWH
(Sets IS_ACTIVE='N' instead of physically deleting)
```

**For DELETE records (Hard Delete — if required):**
```
Update Strategy: UPD_DELETE
Expression: DD_DELETE
Target: EMP_DWH
```

---

#### Step 5: Post-Load Flag Reset

**After successful load, reset flags in source:**

**Session Post-SQL on Source:**
```sql
UPDATE EMPLOYEE_FEED
SET FLAG = ' '
WHERE FLAG IN ('N', 'U', 'D')
  AND LOAD_DATE = SYSDATE - 1
```

**Why reset:** Prevents re-processing same records in next run. Source system then only sets flags again when new changes occur.

---

#### Complete Flagging Flow Diagram

```
Source (with FLAG column)
    ↓
Filter: FLAG IN ('N','U','D')      ← Skip unchanged rows
    ↓
Router: RTR_FLAG_ROUTE
    ├─ FLAG = 'N' ──────────────→ Update Strategy (DD_INSERT) → Target
    ├─ FLAG = 'U' ──────────────→ Update Strategy (DD_UPDATE) → Target
    ├─ FLAG = 'D' ──────────────→ Update Strategy (DD_UPDATE  → Target
    │                              or DD_DELETE)
    └─ Default  ─────────────────→ Error_Log
    
Post-Load:
Source → Reset FLAG = ' ' for processed rows
```

---

### Flagging with Sequence Generator (SRC Flag + SK Generation)

**Scenario:** New records need surrogate key, updated records use existing key.

```
Source (with FLAG)
    ↓
Filter: FLAG IN ('N', 'U', 'D')
    ↓
Lookup: LKP_TARGET (get existing EMP_SK)
  - Condition: EMP_ID = LKP_EMP_ID
  - Returns: LKP_EMP_SK
    ↓
Sequence Generator: SEQ_EMP_SK
  - NEXTVAL available for new records
    ↓
Expression: EXP_ASSIGN_SK
  O_EMP_SK = IIF(FLAG = 'N', SEQ_EMP_SK.NEXTVAL, LKP_EMP_SK)
  ← New records get new SK, updates reuse existing SK
    ↓
Router: Route by FLAG
    ├─ N → DD_INSERT
    ├─ U → DD_UPDATE
    └─ D → DD_DELETE / Soft Delete
```

---

### Flagging vs Timestamp-Based Incremental Load

| Aspect | Flagging | Timestamp-Based |
|--------|---------|-----------------|
| **Source requirement** | FLAG column in source | LAST_MODIFIED_DATE column |
| **Who sets change indicator** | Source system | Database/ETL compares dates |
| **Explicit delete tracking** | Yes (D flag) | No (timestamps don't capture deletes) |
| **Performance** | Faster (flag pre-identifies changes) | Slightly slower (date comparison) |
| **Complexity** | Source must maintain flags | Simpler, just needs timestamp |
| **Delete handling** | Built-in (D flag) | Requires separate delete detection |
| **When best** | Source system supports it | Source system doesn't have flags |

---

### Active Records Pattern ⭐

**Scenario:** Only process currently active records from source.

**Source has IS_ACTIVE flag:**
```
EMP_ID | NAME   | IS_ACTIVE | FLAG
101    | John   | Y         | U
102    | Jane   | N         | U    ← Terminated employee
103    | Bob    | Y         | N
```

**Filter combination:**
```
Filter: FLAG IN ('N', 'U') AND IS_ACTIVE = 'Y'
```

This processes only active employees with changes — ignores terminated employees even if their flag says U.

**Or use Router:**
```
Router: RTR_ACTIVE_CHANGES
  Group ACTIVE_NEW:    FLAG = 'N' AND IS_ACTIVE = 'Y' → Insert active
  Group ACTIVE_UPDATE: FLAG = 'U' AND IS_ACTIVE = 'Y' → Update active
  Group TERMINATED:    IS_ACTIVE = 'N'                → Soft delete path
  Default:             No change / skip
```

---

### Scenario-Based Questions:

**Q1:** Source file arrives with a FLAG column: N=New, U=Updated, space=No change. Which transformation should you use FIRST to improve performance?
- A) Router to separate N, U, and spaces
- B) Filter to exclude blank FLAG rows ✅
- C) Expression to evaluate flag
- D) Lookup to check if record exists

**Why?** Filter early — remove the unchanged rows (blank FLAG) immediately. This reduces data volume before any expensive Router or Lookup operations.

---

**Q2:** You use flagging strategy. FLAG = 'D' means the employee left the company. You want to mark them inactive in the target but keep the record. Which Update Strategy?
- A) DD_DELETE
- B) DD_REJECT
- C) DD_UPDATE with IS_ACTIVE = 'N' ✅
- D) DD_INSERT a new record

**Why?** Soft delete — update the existing record to mark inactive. Hard delete (DD_DELETE) would remove the row, losing historical reference.

---

**Q3:** After a successful load using flagging strategy, what should the Post-SQL do to the source?
- A) Delete all processed records
- B) Reset FLAG to blank/space for processed rows ✅
- C) Set FLAG = 'P' (processed)
- D) Nothing — source handles it

**Why?** Resetting flags prevents re-processing the same records in the next run. Without reset, the same N/U/D rows would be processed again.

---

**Q4:** Flagging vs Timestamp: Which method natively handles deleted records?
- A) Timestamp-based
- B) Flagging ✅
- C) Both equally
- D) Neither

**Why?** Timestamps track when records were modified, but a deleted row has no timestamp to query. Flagging explicitly marks rows with 'D' before deletion, making delete tracking straightforward.

---

**Q5:** Source has 1 million rows. 800 have FLAG='N', 200 have FLAG='U', and 999,000 have FLAG=space (no change). After the Filter transformation, how many rows continue to the Router?
- A) 1,000,000
- B) 999,000
- C) 1,000 ✅
- D) 800

**Why?** Filter condition `FLAG IN ('N', 'U')` passes only 800 + 200 = 1,000 rows. The 999,000 unchanged rows are removed by the filter.

---

---

## Topic 31: Stored Procedure Transformation — Advanced Scenarios

### Quick Recap

**Stored Procedure Transformation** = Calls a database stored procedure from within an Informatica mapping or session.

Two modes (covered in Module 3 briefly):
- **Connected** — In the mapping pipeline, passes/receives data row by row
- **Unconnected** — Called from Expression transformation like a function

This topic goes deeper into real-world usage patterns.

---

### Connected Stored Procedure — Advanced Use Cases ⭐

**When to use Connected Stored Procedure (in pipeline):**

**Use Case 1: Complex Row-Level Database Logic**
```
Scenario: Calculate shipping cost using complex database function
that queries 5 tables and applies business rules.

Easier to call existing SP than rebuild logic in Informatica.

Connected SP: SP_CALC_SHIPPING
  Input Port:  IN_WEIGHT, IN_DESTINATION, IN_PRIORITY
  Output Port: OUT_SHIPPING_COST, OUT_ESTIMATED_DAYS

Flow:
Source → Expression → Connected SP → Target
                      ↓ passes ports ↑
                  DB runs procedure
```

**Use Case 2: Row-Level Validation via Database**
```
Connected SP: SP_VALIDATE_CUSTOMER
  Input Port:  IN_CUST_ID
  Output Port: OUT_IS_VALID (1/0), OUT_ERROR_MSG

Flow:
Source → Connected SP → Router (is_valid=1 → Main, is_valid=0 → Error)
```

**Port Types in Connected Stored Procedure:**

| Port Type | Direction | Purpose |
|-----------|-----------|---------|
| **Input (IN)** | Mapping → SP | Values passed TO the procedure |
| **Output (OUT)** | SP → Mapping | Values returned FROM the procedure |
| **Input/Output (IN/OUT)** | Both | Value passed in, modified, returned |
| **Return** | SP → Mapping | Single return value |

---

### Unconnected Stored Procedure — Advanced Use Cases ⭐

**When to use Unconnected Stored Procedure:**

**Use Case 1: Pre-Session Database Preparation**
```
Called before session starts (Pre-session stored procedure):
SP_DISABLE_INDEXES → Disable target table indexes for faster load
SP_TRUNCATE_STAGING → Clear staging table
SP_ARCHIVE_OLD_DATA → Move old records to archive
```

**Use Case 2: Post-Session Cleanup**
```
Called after session completes (Post-session stored procedure):
SP_REBUILD_INDEXES → Rebuild indexes after bulk load
SP_UPDATE_STATISTICS → Refresh database statistics
SP_SEND_NOTIFICATION → Email notification from database
SP_LOG_LOAD_COMPLETION → Write audit record
```

**How to configure Pre/Post session SP:**
```
Session Properties → Components Tab:
  Pre-session stored procedure: SP_PREPARE_TARGET
  Post-session stored procedure: SP_FINALIZE_LOAD

These run at session level, not row level.
```

**Use Case 3: Conditional Call from Expression**
```
Only call SP when specific condition is met:

In Expression:
  O_DISCOUNT = IIF(CUST_TYPE = 'PREMIUM',
                :SP.SP_GET_PREMIUM_DISCOUNT(CUST_ID),
                0)

SP called only for premium customers — not every row.
```

---

### Connected vs Unconnected — Decision Guide

```
Need to process EVERY row → Connected
Need result for SOME rows (conditional) → Unconnected

Need MULTIPLE return values → Connected
Need SINGLE return value → Unconnected

Pre/Post session operations → Unconnected (session-level)
Row-level enrichment in pipeline → Connected
Reuse SP across multiple transformations → Unconnected

Performance consideration:
Unconnected = called only when needed (faster for conditional logic)
Connected = called for every row in pipeline (slower if SP is heavy)
```

---

### Error Handling for Stored Procedures

**Session Properties → Error Handling:**

| Setting | Options | Recommendation |
|---------|---------|---------------|
| **On Pre-Session SP Error** | Stop / Continue | Stop (preparation failed, don't proceed) |
| **On Post-Session SP Error** | Stop / Continue | Continue (load succeeded, post-cleanup failure shouldn't fail load) |
| **On SP Row Error** | Stop / Continue | Stop for critical SPs, Continue for optional enrichment |

**Best Practice:**
```
Pre-session SP fails → Stop session
  (If setup failed, loading would produce wrong results)

Post-session SP fails → Continue (or separate notification workflow)
  (Load already succeeded — don't mark it as failed for cleanup issue)
```

---

### Scenario-Based Questions:

**Q1:** You need to call a database function that validates a tax ID for EVERY row being processed. Which SP mode?
- A) Unconnected
- B) Connected ✅
- C) Pre-session
- D) Post-session

**Why?** Connected SP is in the pipeline and processes every row. Unconnected is called conditionally from expressions.

---

**Q2:** Before a bulk load session, you want to disable all indexes on the target table. Which is the best approach?
- A) Connected SP in mapping
- B) Pre-session Unconnected SP ✅
- C) Post-SQL in session
- D) Expression transformation

**Why?** Pre-session SP runs once before the session starts — ideal for setup tasks like disabling indexes. Post-SQL also works, but Stored Procedure transformation provides more structured error handling and reusability.

---

**Q3:** Your Connected SP fails for 5 rows out of 10,000. What session property controls whether the session stops or continues?
- A) Commit Interval
- B) DTM Buffer Size
- C) On Stored Procedure Error ✅
- D) Stop on Errors

**Why?** "On Stored Procedure Error" in session error handling properties controls whether row-level SP failures stop or continue the session.

---

**Q4:** An Unconnected SP returns customer discount percentage. You want to call it only for customers with TIER = 'GOLD'. Where do you call it?
- A) Filter transformation
- B) Expression transformation using :SP.SP_NAME() ✅
- C) Router transformation
- D) Directly in Source Qualifier

**Why?** Unconnected SPs are called from Expression transformations using `:SP.SP_NAME(input_param)` syntax — you can wrap it in an IIF to call conditionally.

---

---

## Topic 32: Parameter Variables in Mapplets

### Quick Recap of Mapplets

**Mapplet** = Reusable transformation logic packaged as a single object, used across multiple mappings.

Covered in Module 4: Mapplets contain Input/Output transformations, can include any transformations except Source Qualifier and Target definitions.

**This topic covers:** How to pass parameters INTO a mapplet and use variables WITHIN a mapplet.

---

### Why Pass Parameters to Mapplets? ⭐

**Problem with static mapplets:**
```
Mapplet: mpl_FormatDate
  Expression: TO_CHAR([Date], 'YYYY-MM-DD')  ← Hardcoded format

Mapping A needs:  'YYYY-MM-DD'
Mapping B needs:  'DD/MM/YYYY'
Mapping C needs:  'MM-DD-YYYY'

Without parameters: Need 3 different mapplets!
With parameters:    One mapplet, pass format as parameter!
```

**Benefit:** Single reusable mapplet that adapts behavior based on input parameters.

---

### Mapping Parameters Inside Mapplets ⭐⭐

**Key concept:** Parameters defined in a mapplet are separate from the mapping's parameters. Each level has its own scope.

**Mapplet Parameter vs Mapping Parameter:**

| Aspect | Mapplet Parameter | Mapping Parameter |
|--------|------------------|-------------------|
| **Scope** | Within mapplet only | Entire mapping |
| **Defined in** | Mapplet Designer | Mapping parameter list |
| **Set in** | Parameter file (mapplet section) | Parameter file (mapping section) |
| **Syntax** | `$$ParamName` (same syntax) | `$$ParamName` |
| **Conflict** | Separate namespace | Separate namespace |

---

### Creating Parameters in a Mapplet

**Step 1: Define parameter in Mapplet**
1. Open Mapplet Designer
2. Mapplets → Parameters and Variables
3. Click **Add** → Name: `$$DateFormat`, Type: String, Default: `YYYY-MM-DD`

**Step 2: Use parameter in mapplet expression**
```
Expression inside mapplet:
O_FORMATTED_DATE = TO_CHAR(I_DATE, $$DateFormat)
```

**Step 3: Set parameter value in parameter file**
```
# Parameter file: params.txt

[FolderName.wf_WorkflowName.s_SessionName.mpl_FormatDate]
$$DateFormat=DD/MM/YYYY
```

**Note:** The section header includes the mapplet name after the session name.

---

### Passing Values INTO Mapplet via Input Ports ⭐

**Most common pattern — pass data through ports, not parameters:**

```
Mapping has: ORDER_DATE field
Mapplet needs: I_DATE input port

Connection:
Mapping → [I_DATE port of mapplet] → Transformations → [O_FORMATTED_DATE] → Mapping
```

This is data-level passing (column values), not parameter-level.

**When to use ports vs parameters:**

| Use Ports When | Use Parameters When |
|----------------|---------------------|
| Passing row-level data (column values) | Configuring behavior (format strings, thresholds) |
| Every row has different value | Same value applied to all rows |
| Example: passing DATE column | Example: passing date format string |

---

### Mapplet Variables ⭐

**Mapping Variables inside mapplets:** Allow mapplets to maintain state across rows or sessions — similar to mapping variables but scoped to the mapplet.

**Use case:**
```
Mapplet: mpl_RunningTotal
  Variable: $$RunningSum (Integer, persistent)
  
  Expression inside:
  $$RunningSum = $$RunningSum + SALARY
  O_RUNNING_TOTAL = $$RunningSum

Each row: adds SALARY to the running total
Persists: $$RunningSum value saved between sessions
```

**Configuration:**
- In Mapplet Designer → Parameters and Variables → Add Variable
- Set: Name, Type, Default Value, Is Persistent (Yes/No)

---

### Practical Example: Reusable Threshold Mapplet ⭐

**Business Requirement:**
Multiple mappings need to classify employees by salary band, but the thresholds differ per region.

**Without parameters (bad):**
```
mpl_SalaryBand_US    → hardcoded US thresholds
mpl_SalaryBand_APAC  → hardcoded APAC thresholds
mpl_SalaryBand_EU    → hardcoded EU thresholds
(3 mapplets to maintain)
```

**With parameters (good):**
```
mpl_SalaryBand (one mapplet with parameters):
  $$HighThreshold  (e.g., 100000 for US, 80000 for APAC)
  $$MidThreshold   (e.g., 60000 for US, 45000 for APAC)

Expression inside mapplet:
  O_BAND = IIF(SALARY > $$HighThreshold, 'Senior',
             IIF(SALARY > $$MidThreshold, 'Mid', 'Junior'))

Parameter file - US mapping:
[US_Folder.wf_US_Load.s_US_Session.mpl_SalaryBand]
$$HighThreshold=100000
$$MidThreshold=60000

Parameter file - APAC mapping:
[APAC_Folder.wf_APAC_Load.s_APAC_Session.mpl_SalaryBand]
$$HighThreshold=80000
$$MidThreshold=45000

Result: One mapplet, two configurations, two behaviors.
```

---

### Parameter File Structure for Mapplets

**Full syntax:**
```
[FolderName.WorkflowName.SessionName.MappletName]
$$ParameterName=Value

Example:
[HR_Project.wf_LoadEmployees.s_LoadEmployees.mpl_FormatName]
$$NameFormat=LAST_FIRST
$$TrimSpaces=Y

[Finance_Project.wf_LoadSalary.s_LoadSalary.mpl_SalaryBand]
$$HighThreshold=90000
$$MidThreshold=55000
```

**Scope resolution rule:** Informatica looks for the most specific match first:
1. Mapplet-level entry (most specific)
2. Session-level entry
3. Workflow-level entry
4. Global section `[GLOBAL]` (least specific)

---

### Common Mistakes with Mapplet Parameters

❌ **Mistake 1: Using mapping parameters inside mapplet expecting them to work**
```
Mapping has: $$DateFormat = 'YYYY-MM-DD'
Mapplet uses: $$DateFormat → DOES NOT INHERIT from mapping!
Mapplet has its own namespace.

Fix: Define $$DateFormat separately inside the mapplet,
     set value in parameter file under mapplet section.
```

❌ **Mistake 2: Wrong parameter file section header**
```
Wrong:
[FolderName.wf_Workflow.s_Session]
$$MappletParam=value   ← Session-level, not mapplet-level

Correct:
[FolderName.wf_Workflow.s_Session.mpl_MappletName]
$$MappletParam=value
```

❌ **Mistake 3: Passing row data as parameters instead of ports**
```
Wrong: Trying to set $$CustomerName per row (can't — params are constants)
Correct: Use Input Port I_CustomerName to receive row-level data
```

---

### Scenario-Based Questions:

**Q1:** A mapplet uses `$$ThresholdValue` parameter. You have 3 mappings using this mapplet with different threshold values. How do you configure this?
- A) Create 3 separate mapplets with hardcoded values
- B) Define $$ThresholdValue in each mapplet section of the parameter file ✅
- C) Pass threshold as an input port
- D) Use a global parameter ($$ThresholdValue in [GLOBAL])

**Why?** Each mapping's session has its own `[Folder.Workflow.Session.MappletName]` section with its specific value. Global would give same value to all — defeating the purpose.

---

**Q2:** Inside a mapplet, you reference `$$RegionCode`. Where must this be defined?
- A) In the mapping's parameter list
- B) In the mapplet's own Parameters and Variables dialog ✅
- C) In the session properties
- D) In the workflow variables

**Why?** Mapplet parameters have their own namespace — separate from mapping or workflow parameters. Must be defined within the mapplet itself.

---

**Q3:** You need to pass a different CUSTOMER_ID to a mapplet for each row. What is the correct approach?
- A) Use a mapplet parameter `$$CustomerID`
- B) Use a mapplet variable
- C) Use an Input Port on the mapplet's Input transformation ✅
- D) Use a workflow variable

**Why?** Row-level data (different value per row) must pass through Input Ports. Parameters are constants — same value for all rows in a session.

---

**Q4:** A mapplet parameter file entry is not being picked up. The parameter file has section `[Folder.wf_Load.s_Session]` with `$$Format=YYYYMMDD`. The mapplet is `mpl_FormatDate`. What is wrong?
- A) Wrong parameter name
- B) Section header missing the mapplet name ✅
- C) Quotes needed around value
- D) Parameter should be in [GLOBAL] section

**Why?** Mapplet parameters need `[Folder.wf_Load.s_Session.mpl_FormatDate]` — the mapplet name must be in the section header.

---

---

## MODULE 7 SUMMARY

### Key Takeaways:

✅ **Version Control** — Objects must be Checked Out before editing (creates lock). Check In saves version and releases lock. Undo Checkout discards changes. Compare shows diff between versions. Export/Import moves objects between repositories (always include referenced objects).

✅ **Flagging Strategy** — FLAG column (N/U/D) pre-identifies what action each row needs. Filter first to remove unchanged rows (performance). Router to split by flag value. Update Strategy applies DD_INSERT / DD_UPDATE / DD_DELETE. Reset flags post-load to prevent reprocessing.

✅ **Stored Procedure — Advanced** — Connected SP processes every row in pipeline (multiple ports). Unconnected SP called conditionally from Expression (single return value). Pre/post session SPs for setup/cleanup. Configure error behavior independently per scenario.

✅ **Mapplet Parameters** — Parameters defined within mapplet (own namespace, not inherited from mapping). Set per-mapplet values in parameter file under `[Folder.Workflow.Session.MappletName]` section. Use ports for row-level data, use parameters for session-level configuration.

---

### Critical Exam Concepts 🎯

1. **Check Out / Check In order** — Check Out first, edit, Check In to release lock
2. **Undo Checkout** — Discards changes (not the same as Check In)
3. **Export referenced objects** — Critical option during export to include dependencies
4. **Flagging filter first** — Always filter to N/U/D rows before Router for performance
5. **Soft delete vs hard delete** — D flag usually means DD_UPDATE with IS_ACTIVE='N'
6. **Unconnected SP syntax** — `:SP.SP_NAME(input)` called from Expression
7. **Mapplet parameter scope** — Own namespace, set in parameter file with mapplet section header

---

### Common Mistakes to Avoid ❌

1. **Editing without checking out** — Changes may not save or conflict with others
2. **Forgetting "Export referenced objects"** — Import fails with missing dependency errors
3. **Not resetting flags post-load** — Same records processed again in next run
4. **Using Connected SP when Unconnected is appropriate** — Performance hit if heavy SP runs every row
5. **Expecting mapping parameters to pass into mapplet** — Mapplet has its own parameter namespace
6. **Wrong parameter file section for mapplet** — Must include mapplet name in header

---

### Self-Check Questions

Before completing, can you answer:

1. What is the difference between Check In and Undo Checkout?
2. Why is "Export referenced objects" important during migration?
3. In flagging strategy, why do you filter BEFORE routing by flag value?
4. When would you use a Connected vs Unconnected Stored Procedure?
5. Why can't a mapplet inherit parameters from its parent mapping directly?
6. What post-load step is essential when using the flagging strategy?

---

**🎯 MODULE 7 COMPLETE — ALL INFORMATICA GAPS COVERED!**

**Full Informatica Coverage:**
✅ Module 1: Fundamentals (Architecture, Tools, Repository)
✅ Module 2: Core Components (Sources, Targets, Mappings)
✅ Module 3: Transformations (All major transformations)
✅ Module 4: Advanced Techniques (Mapplets, Workflows, Parameters)
✅ Module 5: Performance & Optimization (Tuning, Partitioning, Caching)
✅ Module 6: Scenario-Based Concepts (SCD, Incremental, Data Quality)
✅ Module 7: Advanced Operations (Version Control, Flagging, SP Advanced, Mapplet Params)

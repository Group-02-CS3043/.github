## Database Structure `AspireTrust`

**Tables:**

| Table | Description |
|---|---|
| `account` | Stores information about bank accounts. |
| `auth` | Stores user authentication details. |
| `branch` | Stores information about bank branches. |
| `employee` | Stores information about bank employees. |
| `fixed_deposit` | Stores information about fixed deposit accounts. |
| `loan` | Stores information about loans. |
| `loan_installment` | Stores information about loan installments. |
| `loan_request` | Tracks loan requests made by users to employees. |
| `online_loan` | Stores information about loans linked to fixed deposits. |
| `operation` | Records financial operations on accounts (transactions, withdrawals, deposits). |
| `organization` | Stores information about organizations. |
| `organization_member` | Tracks the association of users with organizations and their roles. |
| `savings_account` | Stores specific details about savings accounts. |
| `transaction` | Stores details about money transfers between accounts. |
| `user` | Stores general information about users. |

**Views:**

| View | Description |
|---|---|
| `withdraw_report` | Provides a summary of withdrawal transactions. |

**Table Structures:**

### `account`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `account_id` | `int` | Primary Key, Auto Increment | Unique identifier for each account. |
| `account_number` | `varchar(20)` | Unique, Not Null | The account number. |
| `user_id` | `int` | Foreign Key (`user`.`user_id`), Primary Key, Not Null |  The user who owns the account. |
| `branch_id` | `int` | Foreign Key (`branch`.`branch_id`), Not Null | The branch where the account was opened. |
| `account_type` | `varchar(20)` |  Not Null, Check Constraint (`account_type` in ('SAVINGS', 'CURRENT')) | The type of account (SAVINGS or CURRENT). |
| `balance` | `decimal(10,2)` |  Not Null | The current balance of the account. |

### `auth`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `user_id` | `int` | Primary Key, Foreign Key (`user`.`user_id`) |  The user associated with these credentials. |
| `username` | `varchar(50)` | Unique, Not Null | The username for authentication. |
| `password` | `varchar(100)` |  Not Null | The hashed password for authentication. |

### `branch`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `branch_id` | `int` | Primary Key, Auto Increment | Unique identifier for each branch. |
| `city` | `varchar(20)` |  Not Null | The city where the branch is located. |
| `address` | `varchar(50)` |  | The address of the branch. |
| `manager_id` | `int` | Foreign Key (`employee`.`employee_id`) | The employee who manages this branch. |

### `employee`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `employee_id` | `int` | Primary Key, Auto Increment | Unique identifier for each employee. |
| `position` | `varchar(25)` | Not Null, Check Constraint (`position` in ('MANAGER', 'TAILOR', 'ACADEMIC-STAFF')) | The job position of the employee. |
| `branch_id` | `int` | Foreign Key (`branch`.`branch_id`), Not Null |  The branch where the employee works. |
| `user_id` | `int` | Foreign Key (`user`.`user_id`), Not Null | The user account associated with this employee. |

### `fixed_deposit`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `fixed_deposit_id` | `int` | Primary Key, Auto Increment | Unique identifier for each fixed deposit account. |
| `user_id` | `int` | Foreign Key (`user`.`user_id`), Not Null |  The user who owns this fixed deposit. |
| `amount` | `decimal(10,2)` |  Not Null | The amount of money deposited. |
| `duration` | `int` |  Not Null | The duration of the fixed deposit in months. |
| `savings_account_id` | `int` | Foreign Key (`savings_account`.`savings_account_id`), Not Null | The savings account associated with this fixed deposit. |

### `loan`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `loan_id` | `int` | Primary Key, Auto Increment | Unique identifier for each loan. |
| `user_id` | `int` |  Primary Key, Not Null | The user who applied for the loan. |
| `approved` | `tinyint(1)` |  Default: 0 | Loan approval status (0: Pending, 1: Approved). |
| `amount` | `decimal(10,2)` |  | The loan amount. |
| `duration` | `int` |  Not Null | The loan duration in months. |
| `branch_id` | `int` |  |  The branch that processes the loan. |
| `interest_rate` | `decimal(10,2)` |  | The interest rate for the loan. |
| `created_at` | `datetime` |  Default: CURRENT_TIMESTAMP | Timestamp of loan creation. |

### `loan_installment`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `loan_installment_id` | `int` | Primary Key, Auto Increment | Unique identifier for each loan installment. |
| `loan_id` | `int` | Foreign Key (`loan`.`loan_id`), Not Null | The loan associated with this installment. |
| `last_payment_date` | `datetime` |  | The date of the last payment. |
| `next_payment_date` | `datetime` |  | The due date of the next payment. |
| `paid` | `tinyint(1)` |  Default: 0 | Payment status (0: Unpaid, 1: Paid). |
| `paid_count` | `int` |  Default: 0 | Number of installments paid. |

### `loan_request`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `loan_request_id` | `int` | Primary Key, Auto Increment | Unique identifier for each loan request. |
| `user_id` | `int` | Foreign Key (`user`.`user_id`), Not Null | The user who requested the loan. |
| `employee_id` | `int` | Foreign Key (`employee`.`employee_id`), Not Null |  The employee who received the loan request. |
| `loan_id` | `int` | Foreign Key (`loan`.`loan_id`), Not Null | The loan associated with this request. |

### `online_loan`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `online_loan_id` | `int` | Primary Key, Auto Increment | Unique identifier for each online loan. |
| `loan_id` | `int` | Foreign Key (`loan`.`loan_id`), Not Null | The loan associated with this online loan. |
| `fixed_deposit_id` | `int` | Foreign Key (`fixed_deposit`.`fixed_deposit_id`), Not Null | The fixed deposit used as collateral for this loan. |

### `operation`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `operation_id` | `int` | Primary Key, Auto Increment | Unique identifier for each operation. |
| `account_id` | `int` | Foreign Key (`account`.`account_id`), Not Null |  The account on which the operation was performed. |
| `operation_type` | `varchar(20)` |  Not Null, Check Constraint (`operation_type` in ('TRANSACTION', 'WITHDRAW', 'DEPOSIT')) | The type of operation (TRANSACTION, WITHDRAW, DEPOSIT). |
| `amount` | `decimal(10,2)` |  Not Null | The amount of money involved in the operation. |
| `remark` | `varchar(50)` |  Not Null | A brief description or note for the operation. |
| `created_at` | `datetime` |  Default: CURRENT_TIMESTAMP | Timestamp of operation creation. |

### `organization`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `organization_id` | `int` | Primary Key, Auto Increment | Unique identifier for each organization. |
| `name` | `varchar(30)` |  Not Null | The name of the organization. |

### `organization_member`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `organization_member_id` | `int` | Primary Key, Auto Increment | Unique identifier for each organization member. |
| `organization_id` | `int` | Foreign Key (`organization`.`organization_id`), Not Null | The organization to which this member belongs. |
| `role` | `varchar(50)` |  Not Null | The role of the user in the organization. |
| `user_id` | `int` | Foreign Key (`user`.`user_id`), Not Null | The user who is a member of the organization. |

### `savings_account`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `savings_account_id` | `int` | Primary Key, Auto Increment | Unique identifier for each savings account. |
| `account_id` | `int` | Foreign Key (`account`.`account_id`), Primary Key, Not Null | The associated account details. |
| `plan` | `varchar(10)` |  Not Null, Check Constraint (`plan` in ('CHILDREN', 'TEEN', 'ADULT', 'SENIOR')) |  The savings plan associated with this account (CHILDREN, TEEN, ADULT, SENIOR). |

### `transaction`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `transaction_id` | `int` | Primary Key, Auto Increment | Unique identifier for each transaction. |
| `operation_id` | `int` | Foreign Key (`operation`.`operation_id`), Not Null | The operation associated with this transaction. |
| `to_acc` | `int` | Foreign Key (`account`.`account_id`), Not Null | The recipient account for this transaction. |

### `user`

| Column | Data Type | Constraints | Description |
|---|---|---|---|
| `user_id` | `int` | Primary Key, Auto Increment | Unique identifier for each user. |
| `nic` | `varchar(12)` |  | The National Identity Card number of the user. |
| `user_type` | `varchar(10)` |  Not Null, Check Constraint (`user_type` in ('CUSTOMER', 'EMPLOYEE')) | The type of user (CUSTOMER or EMPLOYEE). |
| `first_name` | `varchar(50)` |  Not Null | The first name of the user. |
| `last_name` | `varchar(50)` |  Not Null | The last name of the user. |
| `date_of_birth` | `date` |  Not Null | The date of birth of the user. |
| `telephone` | `varchar(12)` |  | The telephone number of the user. |
| `home_town` | `varchar(50)` |  Not Null | The hometown of the user. |
| `created_at` | `datetime` |  Default: CURRENT_TIMESTAMP | Timestamp of user creation. |

**Triggers:**

| Trigger | Table | Description |
|---|---|---|
| `check_operation_validity` | `operation` |  Before an operation is inserted, it checks if the account balance is sufficient for withdrawals based on the account plan. |

**Procedures:**

This database includes a variety of stored procedures for managing accounts, employees, loans, fixed deposits, and transactions. They enforce data integrity, streamline operations, and provide custom logic for various banking processes.

**Please note:** This structure is a representation based on the provided MySQL dump. It is recommended to consult additional documentation and understand the business logic for a comprehensive overview.

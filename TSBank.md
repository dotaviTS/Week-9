# TS Bank Project - Tauspace Developer Internship
## Week 9 - TSBank Project
### Project Overview

The project is a simple banking application that allows users to create accounts, deposit and withdraw money, and transfer money between accounts. The application will have two types of users: customers and staff. Customers can create accounts and perform transactions on their own accounts. Staff can perform all the same actions as customers, as well as create accounts for customers.

## Project Setup
### Prerequisites


- [Elixir](https://elixir-lang.org/install.html)
- [Phoenix](https://hexdocs.pm/phoenix/installation.html)
- [PostgreSQL](https://www.postgresql.org/download/)


### Sample Project Structure  

The project is structured as follows:

-  `lib/`: application code here

-  `tsbank/`: main application code.

-  `accounts/`: schemas and controllers for the `Account` model.

-  `auth/`: schemas and controllers for the `User` model.

-  `bank/`: contains the `BankRegistry` and `BankSupervisor` modules.

-  `customers/`: the schemas and controllers for the `Customer` model.

-  `staff/`: the schemas and controllers for the `StaffMember` model.

-  `transactions/`: the schemas and controllers for the `Transaction` model.

-  `tsbank.ex`: the application's main supervisor.

-  `tsbank_web/`: the web layer.

-  `channels/`: the channels.

-  `controllers/`: the controllers.

-  `templates/`: the templates.

-  `views/`: the views.

-  `tsbank_web.ex`: the web layer's main supervisor.

-  `tsbank.ex`: the application's main supervisor.

-  `test/`: the tests.

-  `config/`: the configuration files.

-  `mix.exs`: the project's dependencies and other metadata.

-  `README.md`: the project's README.

-  `router.ex`: the application's router.

-  `seeds.exs`: the seed data.

-  `tsbank.sql`: the SQL file for the database.

-  `tsbank_test.exs`: the test configuration.

-  `tsbank_web.ex`: the web layer's main supervisor.

  

## The project should be attempted in the following phases:

- Phase 1: Initial Setup
- Phase 2: Data Modelling and DB Integration
- Phase 3: Building Endpoints
- Phase 4: Error Handling
- Phase 5: Testing
- Phase 6: Documentation

## Background Information on TSBank

## Functions  

Currently TSBank exposes the following public functions:

#### Transaction Module
- No public functions are exposed; it defines a struct.

#### BankSupervisor Module
-  **`start_link/1`**: Starts the supervisor.
#### BankRegistry Module
-  **`start_link/0`**: Starts the unique registry for BankAccounts.
#### BankAccount Module

1.  **`start_link(account_number, initial_balance)`**: Starts the GenServer for a BankAccount with a given `account_number` and `initial_balance`.

2.  **`deposit(pid, amount)`**: Deposits the given `amount` into the bank account identified by the process ID `pid`.  

3.  **`withdraw(pid, amount)`**: Withdraws the given `amount` from the bank account identified by the process ID `pid`.

4.  **`balance(pid)`**: Fetches the current balance of the bank account identified by the process ID `pid`.

5.  **`transfer(from_account, to_account, amount)`**: Transfers the given `amount` from the account identified by `from_account` to the account identified by `to_account`.

---------------------------------------------------------------------------------------------------
## Phase 1: Initial Setup

1.  **Install Phoenix Framework**

```bash
mix  archive.install  hex  phx_new
```

2.  **Create a New Phoenix Project**

```bash
mix  phx.new  your_project_name (TSBANK)
```

3.  **Navigate to the New Project Directory**

```bash
cd  your_project_name
```

4.  **Initialize Postgres Database**
```bash
mix  ecto.create
mix  ecto.migrate
```

5.  **Copy Existing Modules**

- Move `BankSupervisor`, `BankRegistry`, `BankAccount`, and any other modules into the `lib/` directory of your Phoenix project.

6.  **Update `mix.exs` File**

```bash
Example:  Adding  a  dependency  in  mix.exs
defp  deps  do
[
{:some_dependency, "~> 1.0"}
]
end
```
- Open your `mix.exs` file and add any dependencies that your existing modules may require.

7.  **Edit Supervision Tree in `application.ex`**

- Add `BankRegistry` and `BankSupervisor` to the `children` list:

```elixir
children = [
# ...
BankRegistry,
	{
	BankSupervisor, arg_for_init
	},
# ...
]
```
---------------------------------------------------------------------------------------------------

## Phase 2: Data Modelling and Database Integration

#### Objective

To define the data model for the application and integrate it with a PostgreSQL database.

#### Strategies

1.  **Define Data Model**

- Define the data model for the application, including the relationships between different entities.

2.  **Integrate Database**

- Create a PostgreSQL database and integrate it with the application using Ecto.

#### Guidelines

-  **Data Model**: The data model should be defined in a way that is intuitive and easy to understand. It should also be flexible enough to accommodate future changes.
 
-  **Database Integration**: The database should be integrated using Ecto, which is a database wrapper and query generator that works seamlessly with Phoenix.

-  **Migrations**: Migrations are a way to create or alter database schema in a structured and organized manner. They should be used to create the necessary tables and columns in the database.

-  **Seeding**: Seeding a database is a process in which an initial set of data is provided to a database when it is being installed. This is useful for populating the database with some initial data for testing purposes.

-  **Testing**: After integrating the database, make sure to test all database operations to ensure they are working as expected.  

#### Steps

1.  **Define Data Model**

- Define the data model for the application, including the relationships between different entities.

-  **Example**: The data model for a banking application might include the following entities:
-  `User`: A user of the application.
-  `Customer`: A customer of the bank.
-  `StaffMember`: A staff member of the bank.
-  `Account`: A bank account.
-  `Transaction`: A transaction on a bank account.

-  **Relationships**: The relationships between these entities might look like this:
- A `User` can be either a `Customer` or a `StaffMember`
- A `Customer` can have multiple `Account`s.
- An `Account` can have multiple `Transactions`.
- etc..

-  **Data Types**: The data types for each entity should be defined as well. For example, a `User` might have an `email` and `password` field, both of which are strings.

-  **Associations**: The associations between entities should be defined as well. For example, a `User` might have a `has_one` association with `Customer` and `StaffMember`.

-  **Attributes**: The attributes for each entity should be defined as well. For example, a `User` might have a `role` attribute that can be either `customer` or `staff`.

-  **Validations**: The validations for each entity should be defined as well. For example, a `User` might have a `password` field that must be at least 8 characters long, contain letters and numbers, etc.


-  **Example**: Your data model for banking application should look similar to this:

```mermaid
erDiagram
User ||--o{ Customer : has
User ||--o{ Admin : has
Customer ||--o{ Account : owns
Account ||--o{ Transaction : has
Customer ||--o{ CustomerAddress : lives_at
Admin ||--o{ Staff : has
Address ||--o{ CustomerAddress : type_of

User {
int id
string email
string password
bool isActive
datetime lastLoginDate
datetime created_at
datetime updated_at
}

Customer {
int id
string firstName
string lastName
string phoneNumber
date dateOfBirth
string idNumber
string passportNumber
bool FICAComplete
datetime created_at
datetime updated_at
}

Admin {
int id
string role
datetime created_at
datetime updated_at
}
  
Staff {
int id
datetime created_at
datetime updated_at
string moreinfo
}
  
Account {
int id
string accountNumber
string status
date dateOpened
float interestRate
int overdraftLimit
string branchCode
string type
date lastTransactionDate
datetime created_at
datetime updated_at
}

Transaction {
int id
string type
string status
string method
int amount
date date
string description
int accountId
datetime created_at
datetime updated_at
}

Address {
int id
string unitNumber
string street
string city
string province
string country
string postalCode
datetime created_at
datetime updated_at
}

CustomerAddress {
int customerId
int addressId
string type
datetime created_at
datetime updated_at
}
```

2.  **Integrate Database**
- Create a PostgreSQL database and integrate it with the application using Ecto.
-  **Install Postgres**: If not already installed, ensure PostgreSQL is installed.
-  **Configure**: Update `/config/dev.exs` with the database credentials.
-  **Create Repo**: Create a new Elixir file within the `lib/your_app/repos/` folder.
-  **Implement Repo**: Write the necessary repo functions that your endpoints will use.
-  **Run Migrations**: Run `mix ecto.migrate` to create or update the tables in your database according to the schemas.
-  **Seed Data**: Optionally, you can add some seed data to your database by editing the `seeds.exs` file in the `priv/repo/seeds.exs` directory.
-  **Test Database Operations**: Once you've completed the above steps, run tests to make sure all database operations are working as expected.
-  **Commit Changes**: After testing, commit your changes to your version control system.

  
#### Steps

1.  **Environment Setup for Database**  
-  **Install Postgres**: If not already installed, ensure PostgreSQL is installed.
-  **Configure**: Update `/config/dev.exs` with the database credentials.  

2.  **Create or Modify Ecto Schemas**
-  **What is an Ecto Schema?**: Ecto schemas are used to map any data source into an Elixir struct. They define what the data looks like in a particular table.
-  **Location**: Create a new Elixir file within the `lib/your_app/schemas/` folder for each model.

```elixir
defmodule  YourApp.Account  do
use  Ecto.Schema
	schema "accounts"  do
	field :account_number, :integer
	field :balance, :integer
	field :user_id, :integer
	timestamps()
	end
end
```
-  -  **Associations**: If your models have associations like `has_many` or `belongs_to`, specify them in the schema.


2.  **Implement Repo Functions**

  

-  **What is a Repo?**: A repository in Ecto maps to an actual database. It’s the main way your application will interact with your database.

  

-  **Location**: Create a new Elixir file within `lib/your_app/repos/` folder.

  

-  **Common Functions**:

-  `Repo.get(Account, id)`: To fetch a record.
-  `Repo.insert!(%Account{params})`: To insert a new record.
-  `Repo.update!(account)`: To update a record.
-  `Repo.delete!(account)`: To delete a record.

  

-  **Implement**: Write the necessary repo functions that your endpoints will use.

  

3.  **Run Migrations**
 
-   *What are Migrations?**: Migrations are a way to create or alter database schema in a structured and organized manner.

-  **Command**: Run `mix ecto.migrate` to create or update the tables in your database according to the schemas.

-  **Rollback**: To rollback a migration, you can use `mix ecto.rollback`.

-  **Test Database Operations**: Once you've completed the above steps, run tests to make sure all database operations are working as expected.

-  **Commit Changes**: After testing, commit your changes to your version control system.

#### Steps  

1.  **Create or Modify Ecto Schema**:
- For each model, create an Ecto schema in a new file within the `schemas` folder.
- This schema will map to a table in the PostgreSQL database.
- For example, for the `Account` model, you might have fields like `account_number`, `balance`, and `user_id`.

2.  **Implement Repo Functions**:
- In a new file within the `repos` folder, implement functions for database operations such as `insert`, `update`, `delete`, and `get`.
- You can use Ecto's `Repo` module for this.

3.  **Migrate Database**: After creating or modifying Ecto schemas, run `mix ecto.migrate` to apply the changes to your database.


#### Additional Notes:

- [Phoenix Framework](https://hexdocs.pm/phoenix/overview.html)
- [Ecto](https://hexdocs.pm/ecto/Ecto.html)
- [Ecto Cheatsheet](https://devhints.io/phoenix-ecto)
- [Ecto Query Cheatsheet](https://devhints.io/phoenix-ecto-query)
- [Ecto Associations](https://hexdocs.pm/ecto/Ecto.html#module-associations)
- [Ecto Migrations](https://hexdocs.pm/ecto/Ecto.Migration.html)
- [Ecto Repo](https://hexdocs.pm/ecto/Ecto.Repo.html)
- [Ecto Schema](https://hexdocs.pm/ecto/Ecto.Schema.html)

--------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------


--------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------
## Phase 3: Building Endpoints

### Overview
In this phase, you will be building the API endpoints essential for the TSBank application. These endpoints will facilitate the core functionalities, such as customer and staff authentication, account management, and transaction handling.

```mermaid
graph  TD;
A[Start]  -->  B1[Plan API Resources]
B1  -->  B2[Data Model]
B1  -->  B3[Decide HTTP Verbs]
B1  -->  B4[Decide on Endpoint URLs]
B2  -->  B[Generate Resources]
B  -->  C1[Edit Controller Logic]
C1  -->  C2[Add CRUD Operations]
C1  -->  C3[Add Business Logic]
C1  -->  C4[Handle Errors]
C4  -->  C[Compile Controller]
C  -->  D1[Edit Routes]
D1  -->  D2[Configure Route Paths]
D1  -->  D3[Map to Controller Actions]
D3  -->  D[Compile Routes]
D  -->  E[Run Local Server]
E  -->  F{Test Endpoint}
F  -- Yes -->  G1[Documentation]
G1  -->  G2[Update API Docs]
G1  -->  G3[Add Example Requests and Responses]
G3  -->  G[Proceed to Next Endpoint]
F  -- No -->  H1[Debug]
H1  -->  H2[Check Logs]
H1  -->  H3[Check Error Messages]
H3  -->  H[Resolve Issue]
H  -->  F
G  -->  I[End]
```
Notes : Try to return a key value pair in your response payload, not just the result or data.

### API Categories
- #### Customer Management
- #### Staff/Banking Management
- #### Account Operations
- #### Transaction Management
- #### General Utility

### Customer Management Endpoints
 These endpoints will help in creating, authenticating, and managing customer accounts.

##### 1. Customer Login
- Endpoint: POST /api/v1/customers/login
- Description: Authenticates a customer and returns an access token.

Request Parameters:
- email: Email address of the customer
- password: Customer's password

Request Body:
```json
{
  "email": "customer@email.com",
  "password": "password123"
}
```

Response: JSON containing the access token or an error message.

2. Create a New Account
- Endpoint: POST /api/v1/accounts
- Description: Creates a new bank account for the authenticated customer.
Request Parameters:
- account_type: Type of account to create (e.g., Savings, Checking)

Request Body:
json
```
{
  "account_type": "Savings",
  "initial_balance": 1000
}
```

- Valid account_type values: "Savings", "Cheque", "Transmission"
Response: JSON containing the account details or an error message.

4. List All Accounts for a Customer
- Endpoint: GET /api/v1/customers/:customer_id/accounts
- Description: Lists all accounts associated with a particular customer.
Response: JSON array containing account details.

5. View Specific Account
- Endpoint: GET /api/v1/accounts/:account_id
- Description: Retrieves the details of a specific account.
Response: JSON containing the account details.

6. Deposit Money into Account
- Endpoint: POST /api/v1/accounts/:account_id/deposit
- Description: Deposits money into a specific account.
Request Parameters:
- amount: Amount to deposit
- Response: JSON containing the new account balance.

Sequence Diagram for Deposit:

```mermaid
sequenceDiagram
  participant Client as Client
  participant BankRegistry as BankRegistry
  participant BankAccount as BankAccount
  Client->>BankRegistry: Lookup PID for account
  BankRegistry->>Client: Return PID
  Client->>BankAccount: deposit(pid, amount)
  BankAccount->>BankAccount: handle_call({:deposit, amount}, _from, balance)
  BankAccount->>Client: Return {:ok, new_balance} or {:error, reason}
```

### Staff/Admin Management Endpoints

These endpoints will bwe used by internal bank staff to perform operations on customer accounts, as well as management and auth functions.

1. Staff Login
- Endpoint: POST /api/v1/admin/login
- Description: Authenticates a staff member and returns an access token.

Request Parameters:
- email: Email address of the staff
- password: user password

Response: JSON containing the access token or an error message.

2. View All Accounts (Admin)
- Endpoint: GET /api/v1/admin/accounts
- Description: Retrieves details of all accounts in the system.
Response: JSON array containing account details.

3. View Specific Account (Admin)
- Endpoint: GET /api/v1/admin/accounts/:account_id
- Description: Retrieves the details of a specific account.
Response: JSON containing the account details.

4. Create New Account for Customer (Admin)
- Endpoint: POST /api/v1/admin/accounts
- Description: Creates a new account for a specific customer.
Request Parameters:
- customer_id: ID of the customer
- account_type: Type of account (e.g., Savings, Checking)
Response: JSON containing the account details or an error message.

5. Update Account Details (Admin)
- Endpoint: PUT /api/v1/admin/accounts/:account_id
- Description: Updates the details of a specific account.
Request Parameters: Varies depending on what is being updated (e.g., account_type)
Response: JSON containing the updated account details or an error message.

6. Delete an Account (Admin)
- Endpoint: DELETE /api/v1/admin/accounts/:account_id
- Description: Deletes a specific account.
Response: JSON confirming the deletion or an error message.

### General Utility Endpoints

1. Logout
- Endpoint: POST /api/v1/logout
- Description: Logs out the currently authenticated user, invalidating their token.
Response: JSON confirming logout.

### Flow of Use Cases
#### Creating a Bank Account
#### User Signup / Staff Onboarding

First, a user (either a customer or staff member) needs to be registered in the system.
- Endpoint: POST /api/v1/signup
User Login

After signup, the user needs to log in to get an authentication token.
- Endpoint for customers: POST /api/v1/customers/login
- Endpoint for staff: POST /api/v1/staff/login (Check User Role)

The API needs to verify if the logged-in user has the permissions to create a bank account.
Create a Bank Account

Now the user or staff can create a bank account.
- Endpoint for customers: POST /api/v1/accounts
- Endpoint for staff (if they have permissions): POST /api/v1/admin/accounts

Confirmation
Once the bank account is created, a confirmation is sent to the user.

```mermaid
graph TD;
  A[Start] --> B[User Signup];
  B --> C[User Login];
  C --> D{Check Role};
  D -->|Customer| E[Create Customer Account];
  D -->|Staff| F[Create Account for Customer];
  E --> G[Confirmation];
  F --> G;
  G --> H[End];
```
--------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------


  

  

## Phase 4: Error Handling

  

  

#### Objective

  

  

To implement robust error-handling mechanisms in each controller method to manage unexpected scenarios and provide informative responses to the client.

  

  

#### Strategies

  

  

1.  **Use `with` Statements**: Utilize Elixir's `with` statement for cleaner error handling when dealing with multiple operations that could fail.

  

- The `with` statement allows you to chain multiple functions that return `{:ok, result}` or `{:error, reason}` tuples and handle errors in a single `else` block.

  

**Example**:

  

```elixir

  

with {:ok, user} <- find_user(params["user_id"]),

  

{:ok, account} <- create_account(user, params["account_type"]) do

  

{:ok, "Account successfully created"}

  

else

  

{:error, reason} -> {:error, "Failed to create account: #{reason}"}

  

end`

  

```

  

2.  **Use `case` Statements**: For simpler scenarios or when you need to pattern-match on multiple conditions, a `case` statement can be useful.

  

**Example**:

  

```elixir

  

case authenticate_user(params["username"], params["password"]) do

  

{:ok, user} -> {:ok, "Authenticated successfully"}

  

{:error, :user_not_found} -> {:error, "User not found"}

  

{:error, :invalid_password} -> {:error, "Invalid password"}

  

_ -> {:error, "An unknown error occurred"}

  

end

  

```

  

#### Guidelines

  

  

-  **HTTP Status Codes**: Always return appropriate HTTP status codes along with the error message. For example, return a 400 for bad requests, 401 for unauthorized access, etc.

  

-  **Logging**: Use Elixir's Logger module to log errors for debugging purposes.

  

-  **Consistency**: Maintain a consistent error response format. Typically, a JSON object with a `message` or `error` key is used.

  

**Example**:

  

```json

  

{

  

"error": "Invalid credentials provided",

  

"status": 401

  

}

  

```

  

-  **Test Error Scenarios**: While writing tests, make sure to cover error cases to validate that your error handling is working as expected.

  

  

----------

  

  

## Phase 5: Testing

  

  

#### Objective

  

  

To write comprehensive tests for each API endpoint, covering both successful operations and error scenarios, using Phoenix's extensive testing capabilities.

  

  

#### Strategies

  

  

1.  **Initialize Test Environment**

  

- Before writing tests, make sure your test environment is properly configured. This includes your test database and any necessary configurations in your `config/test.exs` file.

  

2.  **Write Test Cases for Each Endpoint**

  

- Use the `ExUnit` framework along with Phoenix's testing support to write tests for each controller action.

  

**Example**:

  

```elixir

  

`test "GET /api/v1/accounts/:id", %{conn: conn} do

  

conn = get(conn, "/api/v1/accounts/1")

  

assert json_response(conn, 200)["data"]["id"] == "1"

  

end

  

```

  

-  **Positive Tests**: Test the cases where the operation should succeed. Check the status code, response format, and any changes in the database.

  

-  **Negative Tests**: Test the cases where the operation should fail. For example, try to create an account with an already existing account number, or attempt an unauthorized access.

  

3.  **Cover Edge Cases**

  

- Ensure that you write tests for any edge cases you can think of. For example, what happens if a deposit amount is negative or if a user tries to withdraw more money than they have?

  

4.  **Utilize Fixtures or Factories**

  

- If your tests require specific conditions to be met in the database (e.g., a user must exist before an account can be created), use fixtures or factories to set up this data.

  

  

"Fixtures" and "Factories" are both techniques used in automated testing to manage and set up the initial state of your system before running the tests. They help you create the necessary data and conditions that your test needs to run correctly. Let's break down each term:

  

  

#### Fixtures

  

  

A fixture is a known set of data that the tests use as a baseline. This could be a pre-defined set of records in your database, like users, accounts, or transactions. Before running the tests, these records are loaded into the test database to mimic a real-world scenario.

  

  

**Example in Elixir/Phoenix:**

  

  

Let's say you have a file `test/fixtures/users_fixture.exs` with the following content:

  

```elixir

  

`alias  MyApp.Accounts

  

Accounts.create_user(%{email:  "test@example.com", password:  "test123"})`

  

```

  

  

You can run this file in your test setup to ensure that a user is present in the database:

  

```elixir

  

setup do

  

MyApp.Repo.delete_all(MyApp.User)

  

Code.eval_file("test/fixtures/users_fixture.exs")

  

:ok

  

end

  

```

  

  

#### Factories

  

  

Factories are more dynamic than fixtures. They are functions or modules that allow you to create objects on-the-fly, often with randomized or sequentially increasing data. Factories give you more control and flexibility, as you can easily override default values for specific tests.

  

  

A popular library for defining factories in Elixir is [ExMachina](https://github.com/thoughtbot/ex_machina).

  

  

**Example using ExMachina:**

  

  

First, you define a factory module:

  

  

```elixir

  

defmodule  MyApp.Factory  do

  

use  ExMachina.Ecto, repo:  MyApp.Repo

  

def  user_factory  do

  

%MyApp.User{

  

email: sequence(:email, &"test#{&1}@example.com"),

  

password:  "test123"

  

}

  

end

  

end

  

```

  

Then, in your tests, you can use this factory to insert a user:

  

  

```elixir

  

insert(:user) # Inserts a user with a unique email and password "test123"`

  

```

  

Or insert a user with overridden attributes:

  

```elixir

  

insert(:user, email:  "override@example.com") # Overrides the email

  

```

  

  

#### Which to Use?

  

  

-  **Fixtures**: Good for setting up a known, fixed environment. Easier to reason about but can be inflexible.

  

-  **Factories**: Better for tests that require unique or varied conditions. More flexible but can make tests a bit harder to reason about due to variability.

  

  

### Guidelines

  

  

-  **Run All Tests Regularly**: After writing a set of tests, run them to make sure they all pass. Keep an eye out for flaky tests that pass or fail intermittently.

  

bashCopy code

  

`mix test`

  

-  **Review Test Coverage**: While 100% test coverage is not always achievable or necessary, aim for a high percentage of code coverage. Tools like `excoveralls` can help analyze your test coverage.

  

bashCopy code

  

`mix coveralls.html`

  

-  **Continuous Integration**: Consider setting up a CI/CD pipeline that runs your test suite automatically when code changes are pushed to your repository.

  

-  **Documentation**: Document any non-obvious parts of your tests to make it easier for other developers to understand why a particular test exists.

  

  

----------

  

  

## Phase 6: Documentation

  

  

1. Update the `README.md` with setup instructions, how to use the API, and how to run tests.

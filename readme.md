# Trakko Generation Language DSL Specification (TGL DSL)

| schema version | specification |  status  |
| :------------: | :-----------: | :------: |
|    Arakesh     |   **below**   | proposed |

## **1. About the project**

The Trakko Generation Language (in short TGL) is a markup DSL inspired by JSON and Groovy. It is designed to generate api clients and documentation as well as data classes and database connections from a simple markup language format, stored in a `.tgl` file. The TGL executor is in charge of interpreting the instructions of a file and as such can generate source files in all supported programming languages and can be extended with custom instructions via a plugin system.

---
## **2. General structures**

| name                     | usage                      | example                |
| ------------------------ | -------------------------- | ---------------------- |
| task & struct            | NAME { }                   | `create { }`           |
| instruction              | NAME                       | `documentation`        |
| instruction with options | NAME: OPTION1, OPTION2     | `build-client: 'java'` |
| function                 | NAME(ARGUMENT1, ARGUMENT2) | `range(1, 5)`          |

---
## **2.1 Tasks**

- `create {}`
  - Task running when called via an executor's (CLI/GUI) create command.
  - Contains instructions used to generate code and/or documentation files. 

- `docs {}`
  - Task defining properties for generating documentation files, for example to include custom css files.

- `data {}`
  - Task defining the structure of data used to generate data classes/objects.
  - It specifies which variables should be created including their name and data type.

---
## **2.2 Instructions**

### `CREATE` & `DOCS`
- output-dir: '*PATH*'
  - Specifies the directory path the generated content should be written to.
  - **`PATH`**: Either a relative file path starting with `./` or an absolute file path. If the path doesn't exists it will be created when the task is executed.

- output-filename: '*NAME*'
  - Defines the filename the generated file will have.
  - **`NAME`**: A valid filename string. It doesn't need to have a `.*` extension as the needed extensions will be added nontheless automatically. 

### `CREATE`

- build-client: '*PLATFORM_1*', '*PLATFORM_2*', ...
  - Instructs the executor to generate clients for the described api for the given platform or platforms if more then one or `all` is specified.
  - **`PLATFORM`**: Platform descriptor or keyword e.g. `all` or `java`. The possible platforms are based on the executor and used plugins and, as such, may vary.

- build-data-object: '*PLATFORM_1*', '*PLATFORM_2*', ...
  - Instructs the executor to generate data classes/objects for the described data structure for the given platform or platforms if more then one or `all` is specified.
  - **`PLATFORM`**: Platform descriptor or keyword e.g. `all` or `java`. The possible platforms are based on the executor and used plugins and, as such, may vary.
  
- build-db-connector: '*DATABASE_1*', '*DATABASE_2*', ...
  - Calls the executor to create an abstract database access object (dao) and implementation of such in for the specified database types.
  - **`DATABASE`**: Database name or keyword e.g. `all`, `MySql` ,or `MongoDB`. The possible database types are based on the executor and used plugins and, as such, may vary.
  
- docs-api
  - Instruction guiding the executor to generate documentation for the defined api structure.
  
- docs-client: '*PLATFORM_1*', '*PLATFORM_2*', ...
  - Instructs the executor to generate documentation files for clients of the described api for the given platform or platform if more then one or `all` is specified.
  - **`PLATFORM`**: Platform descriptor or keyword e.g. `all` or `java`. The possible platforms are based on the executor and used plugins and, as such, may vary.

### `DOCS`

- type: '*TYPE*'
  - Specifing the file format which the documentation will be generated in.
  - **`TYPE`**: Valid types are `markdown` (short form: `md`) [**default**] or `html`.
  
- include-css: '*PATH*'
  - Defining the path of a custom css file which will be included in the generated documentation file. [**ONLY HTML**]
  - **`PATH`**: Either a relative file path starting with `./` or an absolute file path.

### `DATA`

- field {*OPTION_1*, *OPTION_2*}
  - Defining a field/variable to hold data.
  - **`OPTIONS`**:
    - name: '*NAME*'
      - Specifing the field's name/variable name.
      - **`NAME`**: String containing only letters.
    - type: '*TYPE*'
      - Declaring the data type the variable/field should hold.
      - **`TYPE`**: Identifier for type information, which is looked up in the mapping files in `/tgl/lang-maps/` folder.
    - get: 'default', return($+1)
      - Specifies the way the value of this field is returned.
      - TODO
    - set: 'default', range(1,5), min(0) & max(18), ifNull('default')
      - Defines the way and/or constrains used to asign a new value to the field.
      - TODO
  
- equals: '*STRATEGY*'
  - Specifies the way objects should be compared to equality.
  - **`STRATEGY`**: Either `recursive` [**default**] comparing values and object types recursively whether they are equal, or `$FIELD_NAME` to compare the given field or fields (if multiple) to check for equality (`FIELD_NAME` needs to be substituted with the name of the desired field).

---
## **2.3 Examples**

TODO

---
## **3. Plugins**

Plugins may extend the capabilities of the executor to further customise the behaviour of the code generation and include functionality not included in the overall standard already. For this purpose, plugins may define their own tasks, instructions and functions, which should integrate into standard functions without problems. Plugins are located in the `/tgl/` folder in the subdirectory `/plugins/`.

---
## **4. TGL Executor (CLI/GUI Tool)**

Runs through instructions listed in `.tgl` files and executes them. 
Plugins can supply custom instructions to include.

---
## **4.1 Commands**

### `create`
Runs through all `.tgl` files in the current working directory an executes the instructions given in the `create` task.

### `create-recursive`
Is equal to running the `create` command for the current working directory and all its subdirectories recursively.

### `plugins`
Shows a list of all detected and usable plugins found in the `/plugins/` subfolder of the executor (system global plugins) and the current working directory (project plugins).

### `version`
Prints the build version of the executor and which schema version it's implementing.
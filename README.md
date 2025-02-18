# OpenAlchemy
Translates an OpenAPI schema to SQLAlchemy models.

## Installation
```bash
python -m pip install OpenAlchemy
# To be able to load yaml file
python -m pip install PyYAML
```

## Example

For example, given the following OpenAPI specification:

```yaml
# ./examples/simple-example-spec.yml
openapi: "3.0.0"

info:
  title: Test Schema
  description: API to illustrate OpenAlchemy MVP.
  version: "0.1"

paths:
  /employee:
    get:
      summary: Used to retrieve all employees.
      responses:
        200:
          description: Return all employees from the database.
          content:
            application/json:
              schema:
                type: array
                items:
                  "$ref": "#/components/schemas/Employee"

components:
  schemas:
    Employee:
      description: Person that works for a company.
      type: object
      x-tablename: employee
      properties:
        id:
          type: integer
          description: Unique identifier for the employee.
          example: 0
          x-primary-key: true
          x-autoincrement: true
        name:
          type: string
          description: The name of the employee.
          example: David Andersson
          x-index: true
        division:
          type: string
          description: The part of the company the employee works in.
          example: Engineering
          x-index: true
        salary:
          type: number
          description: The amount of money the employee is paid.
          example: 1000000.00
      required:
        - id
        - name
        - division
```

The SQLALchemy models file then becomes:
```python
# models.py
from open_alchemy import init_yaml

init_yaml("./examples/simple-example-spec.yml")
```

The _Base_ and _Employee_ objects can be accessed:
```python
from open_alchemy.models import Base
from open_alchemy.models import Employee
```

An extensive set of examples with a range of features is here:

[examples for main features](examples)

An example API has been defined using connexion and Flask here:

[example connexion app](examples/app)

## Documentation
[Read the Docs](https://openapi-sqlalchemy.readthedocs.io/en/latest/)

## Features
### Supported
The following features are supported:
- `integer ` (32 and 64 bit),
- `number` (float only),
- `boolean`,
- `string`,
- `$ref` references for columns and models,
- foreign keys,
- many to one relationships and
- `allOf` inheritance for columns and models.

### Not Supported
The following features are on the backlog:
- one to many relationships.

## Contributing
Fork and checkout the repository. To install:
```bash
python -m venv venv
source ./venv/bin/activate
python -m pip install -e .[dev]
```
To run tests:
```bash
tox
```
Make your changes and raise a pull request.

## Compiling Docs
```bash
python -m venv venv
cd docs
make html
```
This creates the `index.html` file in `docs/build/html/index.html`.

## Release Commands
```bash
rm -r dist/*
python -m pip install --upgrade setuptools wheel
python setup.py sdist bdist_wheel
python -m pip install --upgrade twine
python -m twine upload dist/*
```

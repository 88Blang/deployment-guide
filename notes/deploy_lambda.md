

# AWS Lambda

## For Python

1. [Lambda_function](#1-lambda-function)
2. [Dependencies](#2-dependencies)
3. [Package](#3-package)
4. [Deploy](#4-deploy)
    - Python Version
    - URL
5. [Tests](#5-tests)




## 1. Lambda Function

lambda_function.py

```
import json

def lambda_handler(event, context):
    # My Code
    return {
        'statusCode': 200,
        'body': json.dumps('Hello!'),
    }

```


## 2. Dependencies
Venv / Requirements.txt


Make requirements.txt
Make dependices folder

```
mkdir dependencies
```

Pip install
```
pip3 install --target=dependencies requests
pip3 install --target=dependencies -r requirements.txt
```


## 3. Package

Zip

Create zip (in working directory with `lambda_function.py` and `venv` )
Add function (no dependencies)
```
zip lambda_deployment.zip lambda_function.py
```


```
cd dependencies
zip -r ../lambda_deployment.zip .
```
`../file` makes the zip in the main directory
`.` from current file (dependencies)

Add lambda
```
cd ..
zip lambda_deployment.zip lambda_function.py
```


## 4. Deploy

AWS

Add to AWS / Create API




lambda
create function
Add:
- name
- runtime: python 3.9
Then create function


Two sections:
- Function Overview
- Code source

On the right of the `Code source` section click `upload from` and select `.zip file` upload `lambda_deployment.zip` file.

Now got to the `Function overview` section and click `Add trigger`
- `Select a source` -> `API Gateway` -> `Create a new API` -> `HTTP API` -> Security `Open`, then Add
- Give it a second then click on `API Gateway`, under `Triggers` there will be `API endpoint`, this is the API url, copy and save it!
- Go back, click `Code`/`Code Source`, where you will see the python code, click `Deploy`
- Done!


## 5. Tests
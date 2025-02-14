# Nebulous serverless Google Sheets API app

## Description

This simple app uses the [Google Sheets API](https://developers.google.com/sheets) to read the display the contents of a [public spreadsheet](https://docs.google.com/spreadsheets/d/1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgvE2upms/view) in the [Sheets API documentation](https://developers.google.com/sheets/api/quickstart/python). The output looks like the following when running locally (or deployed to the cloud):
![students web app](https://user-images.githubusercontent.com/1102504/149280036-a5ed50cf-70e0-464f-95c1-3c81ddab92d6.png)


## Python 3 version (Python 2 compatible)

This sample is offered in Python 3, but the code itself is Python 2-compatible. Instructions on running it under 2.x will leverage the resources of the [Cloud API Python sample](cloud/python) which provides a more complete experiencce as it has all configuration available to support 2.x deployments.


## Security

The app uses [service account authentication](https://cloud.google.com/iam/docs/service-accounts) and [service account public/private key-pair with the appropriate IAM permissions](https://cloud.google.com/iam/docs/creating-managing-service-account-keys) for authorization. However, since Google Sheets documents are typically owned by human users, this can be changed to [end-use/Google account authentication](https://cloud.google.com/docs/authentication/end-user) with [OAuth client IDs](https://developers.google.com/identity/protocols/oauth2) where the data owner must grant the requested scope permissions to the app so it can access their private data.

To get an idea of what code changes are required, check the `alt` folder in the repo for this [multi-API codelab](http://goo.gle/3nPxmlc) featuring the Google Drive, Cloud Storage, Cloud Vision, and Google Sheets APIs. There are code samples demonstrating OAuth client ID as well as service accounts, old and new Python auth libraries, and the [Google APIs client library](https://developers.google.com/api-client-library) for Python as well as [Google Cloud API client libraries](https://cloud.google.com/apis/docs/cloud-client-libraries).


## Deployments and their files

File | Description
--- | ---
[`main.py`](main.py) | main application file
[`templates/index.html`](templates/index.html) | application HTML template
[`requirements.txt`](requirements.txt) | 3rd-party package requirements file
[`app.yaml`](app.yaml) | App Engine configuration file (only for App Engine deployments)
`credentials.json` | service account public/private key-pair (only for running locally)
[`.gcloudignore`](.gcloudignore) | files to exclude deploying to the cloud (administrative)
[`noxfile.py`](noxfile.py) |  unit tests `nox` tool setup file
[`test_sheets.py`](test_sheets.py) |  unit tests (`pytest`)
[`Procfile`](Procfile) |  "Entrypoint" to start app (only for Cloud Run [Cloud Buildpacks] deployments)
`README.md` | this file (administrative)

Below are the required settings and instructions for all documented deployments. The "**TL:DR;**" section at the top of each configuration summarizes the key files (see above) while the table beneath spells out the details. No administrative files are listed.


## **Local Flask server (Python 2 & 3)**

**TL;DR:** application files (`main.py` &amp; `requirements.txt`) plus `credentials.json`

1. **Create service account key**, download key file as `credentials.json` in working directory, and set `GOOGLE_APPLICATION_CREDENTIALS` environment variable pointing to it. Read more about this process in [the documentation](https://cloud.google.com/docs/authentication/production#manually)).
1. (2.x only) **Uncomment/enable** the line for `google-auth` in `requirements.txt`
1. **Run** `pip install -U pip -r requirements.txt` to install/update packages locally (`pip2` for Python 2 or `pip3` for Python 3 explicitly)
1. **Run** `python main.py` to run on local Flask server (`python2` or `python3` to be explicit)

**Troubleshooting**: if you're on a VM running `pip install`, you may get an error like this:

```
ERROR: Could not install packages due to an OSError: [Errno 13] Permission denied: 'PKG-INFO'
Consider using the `--user` option or check the permissions.
```

_Solution_: If this is the case:
- **Uupdate** the line for `flask` in `requirements.txt` to: `flask<2.0dev`


## **App Engine (Python 2)**

**TL;DR:** app files plus `app.yaml`, `appengine_config.py`, and `lib`

1. **Copy** the [Cloud API Python 2 `app.yaml` file](/cloud/python/app.yaml)
1. **Copy** the [Cloud API Python 2 `appengine_config.py` file](/cloud/python/appengine_config.py)
1. **Uncomment/enable** the line for `google-auth` in `requirements.txt`
1. **Uncomment** "<1.12.0" on the line for `google-api-python-client` in `requirements.txt`
1. **Run** `pip install -t lib -r requirements.txt` to populate `lib` folder (or `pip2`)
1. **Run** `gcloud app deploy`


## **App Engine (Python 3)**

**TL;DR:** app files plus `app.yaml`

1. **Run** `gcloud app deploy`

This deploy will work right out of the box but not if you just followed the above instructions to deploy to Python 2. (Either undo all the changes above or start with a clean copy of the repo folder.)


## **Cloud Functions (Python 3)**

**TL;DR:** app files only

1. **Run** `gcloud functions deploy students --runtime python39 --trigger-http --allow-unauthenticated` to deploy to Cloud Functions (or Python 3.7 or 3.8)

- The command above creates &amp; deploys a new HTTP-triggered Cloud Function (name `students` must match the function's name in `main.py`) else you need to use `--entrypoint`.
- Cloud Functions does not support Python 2.


## **Cloud Run (Python 2 via Docker)**

**TL;DR:** app files plus `Dockerfile`

1. **Copy** the [Cloud API Python 2 `Dockerfile`](/cloud/python/Dockerfile) and [`.dockerignore`](/cloud/python/.dockerignore) files.
1. **Run** `gcloud run deploy students --allow-unauthenticated --platform managed --source .` to deploy to Cloud Run; optionally add `--region REGION` for non-interactive deploy, or simply `gcloud run deploy` for a fully-interactive experience.

> **NOTE:** This sample uses the Flask development server by default for prototyping; for production, bundle and deploy a production server like `gunicorn`:
>    1. **Uncomment** `gunicorn` from `requirements.txt` (commented out for App Engine &amp; Cloud Functions)
>    1. **Uncomment** the `ENTRYPOINT` entry for `gunicorn` replacing the default entry in `Dockerfile`
>    1. Re-use the same deploy command above


## **Cloud Run (Python 3 via Docker)**

**TL;DR:** app files plus `Dockerfile` (nearly identical to Python 2 deployment)

1. **Copy** the [Cloud API Python 2 `Dockerfile`](/cloud/python/Dockerfile) and [`.dockerignore`](/cloud/python/.dockerignore) files.
1. **Edit** `Dockerfile` and switch the `FROM` entry to the `python:3-slim` base image
1. **Run** `gcloud run deploy students --allow-unauthenticated --platform managed --source .` to deploy to Cloud Run; optionally add `--region REGION` for non-interactive deploy, or simply `gcloud run deploy` for a fully-interactive experience.

- The sidebar above pertaining to `gunicorn` and its instructions also apply to Python 3.


## **Cloud Run (Python 3 via Cloud Buildpacks)**

**TL;DR:** app files plus [`Procfile`](https://devcenter.heroku.com/articles/procfile)

1. **Run** `gcloud run deploy students --allow-unauthenticated --platform managed --source .` to deploy to Cloud Run; optionally add `--region REGION` for non-interactive deploy, or simply `gcloud run deploy` for a fully-interactive experience.

- The sidebar above pertaining to `gunicorn` and its instructions also apply to Python 3.
- There is no support for Python 2 with Cloud Buildpacks (2.x developers must use Docker)
- You can also use this shortcut to create an ephemeral version of this repo to deploy to Cloud Run:
    [![Run on Google Cloud](https://deploy.cloud.run/button.svg)](https://deploy.cloud.run)


## References

These are relevant links only to the app in this folder (for all others, see the [README one level up](../README.md):

- **[App Engine](https://cloud.google.com/appengine)**
    - [Python 3 App Engine quickstart](https://cloud.google.com/appengine/docs/standard/python3/quickstart)
    - [Python 3 App Engine (standard environment) runtime](https://cloud.google.com/appengine/docs/standard/python3/runtime)
    - [Python 2 App Engine (standard environment) runtime](https://cloud.google.com/appengine/docs/standard/python/runtime)
    - [Differences between Python 2 &amp; 3 App Engine (standard environment) runtimes](https://cloud.google.com/appengine/docs/standard/runtimes)
    - [Python 2 to 3 App Engine (standard environment) migration guide](http://cloud.google.com/appengine/docs/standard/python/migrate-to-python3)
- **[Cloud Functions](https://cloud.google.com/functions)**
    - [Python Cloud Functions quickstart](https://cloud.google.com/functions/docs/quickstart-python)
- **[Cloud Run](https://cloud.run)**
    - [Python Cloud Run quickstart](https://cloud.google.com/run/docs/quickstarts/build-and-deploy/python)
- **[Google Sheets API](https://developers.google.com/sheets)**
    - [Google Sheets API Python Quickstart sample](https://developers.google.com/sheets/api/quickstart/python)
    - [Google Sheets API video library](https://developers.google.com/sheets/api/videos)
- **Others**
    - [Google APIs client libraries](https://developers.google.com/api-client-library)
    - [Flask](https://flask.palletsprojects.com)


## Testing

Testing is driven by [`nox`](http://nox.thea.codes) which uses [`pytest`](https://pytest.org) for testing and [`flake8`](https://flake8.pycqa.org) for linting, installing both in virtual environments along with application dependencies, `flask` and `google-api-python-client`, and finally, `blinker`, a signaling framework integrated into Flask. To run the lint and unit tests (testing `GET` and `POST` requests), install `nox` (with the expected `pip install -U nox`) and run it from the command line in the application folder and ensuring `noxfile.py` is present.


### Expected output

```
$ nox
nox > Running session tests-2.7
nox > Creating virtual environment (virtualenv) using python2.7 in .nox/tests-2-7
nox > Command /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -m virtualenv /private/tmp/sheets/.nox/tests-2-7 -p python2.7 failed with exit code 1:
/Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9: No module named virtualenv.__main__; 'virtualenv' is a package and cannot be directly executed
nox > Session tests-2.7 failed.
nox > Running session tests-3.8
nox > Creating virtual environment (virtualenv) using python3.8 in .nox/tests-3-8
nox > Command /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -m virtualenv /private/tmp/sheets/.nox/tests-3-8 -p python3.8 failed with exit code 1:
/Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9: No module named virtualenv.__main__; 'virtualenv' is a package and cannot be directly executed
nox > Session tests-3.8 failed.
nox > Running session tests-3.9
nox > Creating virtual environment (virtualenv) using python3.9 in .nox/tests-3-9
nox > Command /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -m virtualenv /private/tmp/sheets/.nox/tests-3-9 -p python3.9 failed with exit code 1:
/Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9: No module named virtualenv.__main__; 'virtualenv' is a package and cannot be directly executed
nox > Session tests-3.9 failed.
nox > Running session lint-2.7
nox > Creating virtual environment (virtualenv) using python2.7 in .nox/lint-2-7
nox > Command /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -m virtualenv /private/tmp/sheets/.nox/lint-2-7 -p python2.7 failed with exit code 1:
/Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9: No module named virtualenv.__main__; 'virtualenv' is a package and cannot be directly executed
nox > Session lint-2.7 failed.
nox > Running session lint-3.8
nox > Creating virtual environment (virtualenv) using python3.8 in .nox/lint-3-8
nox > Command /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -m virtualenv /private/tmp/sheets/.nox/lint-3-8 -p python3.8 failed with exit code 1:
/Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9: No module named virtualenv.__main__; 'virtualenv' is a package and cannot be directly executed
nox > Session lint-3.8 failed.
nox > Running session lint-3.9
nox > Creating virtual environment (virtualenv) using python3.9 in .nox/lint-3-9
nox > Command /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -m virtualenv /private/tmp/sheets/.nox/lint-3-9 -p python3.9 failed with exit code 1:
/Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9: No module named virtualenv.__main__; 'virtualenv' is a package and cannot be directly executed
nox > Session lint-3.9 failed.
nox > Ran multiple sessions:
nox > * tests-2.7: failed
nox > * tests-3.8: failed
nox > * tests-3.9: failed
nox > * lint-2.7: failed
nox > * lint-3.8: failed
nox > * lint-3.9: failed
[wesc@wesc-macbookpro1.roam.corp.google.com-330] MASTER, YOUR COMMAND? pip3 install -U virtualenv
Collecting virtualenv
  Using cached virtualenv-20.13.0-py2.py3-none-any.whl (6.5 MB)
Requirement already satisfied: distlib<1,>=0.3.1 in /Library/Frameworks/Python.framework/Versions/3.9/lib/python3.9/site-packages (from virtualenv) (0.3.1)
Requirement already satisfied: filelock<4,>=3.2 in /Library/Frameworks/Python.framework/Versions/3.9/lib/python3.9/site-packages (from virtualenv) (3.4.2)
Requirement already satisfied: six<2,>=1.9.0 in /Library/Frameworks/Python.framework/Versions/3.9/lib/python3.9/site-packages (from virtualenv) (1.15.0)
Requirement already satisfied: platformdirs<3,>=2 in /Library/Frameworks/Python.framework/Versions/3.9/lib/python3.9/site-packages (from virtualenv) (2.4.0)
Installing collected packages: virtualenv
Successfully installed virtualenv-20.13.0
[wesc@wesc-macbookpro1.roam.corp.google.com-331] MASTER, YOUR COMMAND? !no
nox
nox > Running session tests-2.7
nox > Creating virtual environment (virtualenv) using python2.7 in .nox/tests-2-7
nox > python -m pip install pytest blinker flask google-auth google-api-python-client
nox > pytest
=================================================== test session starts ====================================================
platform darwin -- Python 2.7.16, pytest-4.6.11, py-1.11.0, pluggy-0.13.1
rootdir: /private/tmp/sheets
collected 1 item

test_sheets.py .                                                                                                     [100%]

================================================= 1 passed in 2.01 seconds =================================================
nox > Session tests-2.7 was successful.
nox > Running session tests-3.8
nox > Creating virtual environment (virtualenv) using python3.8 in .nox/tests-3-8
nox > python -m pip install pytest blinker flask google-auth google-api-python-client
nox > pytest
=================================================== test session starts ====================================================
platform darwin -- Python 3.8.2, pytest-6.2.5, py-1.11.0, pluggy-1.0.0
rootdir: /private/tmp/sheets
collected 1 item

test_sheets.py .                                                                                                     [100%]

==================================================== 1 passed in 0.95s =====================================================
nox > Session tests-3.8 was successful.
nox > Running session tests-3.9
nox > Creating virtual environment (virtualenv) using python3.9 in .nox/tests-3-9
nox > python -m pip install pytest blinker flask google-auth google-api-python-client
nox > pytest
=================================================== test session starts ====================================================
platform darwin -- Python 3.9.9, pytest-6.2.5, py-1.11.0, pluggy-1.0.0
rootdir: /private/tmp/sheets
collected 1 item

test_sheets.py .                                                                                                     [100%]

==================================================== 1 passed in 0.90s =====================================================
nox > Session tests-3.9 was successful.
nox > Running session lint-2.7
nox > Creating virtual environment (virtualenv) using python2.7 in .nox/lint-2-7
nox > python -m pip install flake8
nox > flake8 --show-source --builtin=gettext --max-complexity=20 --exclude=.nox,.cache,env,lib,generated_pb2,*_pb2.py,*_pb2_grpc.py --ignore=E121,E123,E126,E203,E226,E24,E266,E501,E704,W503,W504,I202 --max-line-length=88 .
nox > Session lint-2.7 was successful.
nox > Running session lint-3.8
nox > Creating virtual environment (virtualenv) using python3.8 in .nox/lint-3-8
nox > python -m pip install flake8
nox > flake8 --show-source --builtin=gettext --max-complexity=20 --exclude=.nox,.cache,env,lib,generated_pb2,*_pb2.py,*_pb2_grpc.py --ignore=E121,E123,E126,E203,E226,E24,E266,E501,E704,W503,W504,I202 --max-line-length=88 .
nox > Session lint-3.8 was successful.
nox > Running session lint-3.9
nox > Creating virtual environment (virtualenv) using python3.9 in .nox/lint-3-9
nox > python -m pip install flake8
nox > flake8 --show-source --builtin=gettext --max-complexity=20 --exclude=.nox,.cache,env,lib,generated_pb2,*_pb2.py,*_pb2_grpc.py --ignore=E121,E123,E126,E203,E226,E24,E266,E501,E704,W503,W504,I202 --max-line-length=88 .
nox > Session lint-3.9 was successful.
nox > Ran multiple sessions:
nox > * tests-2.7: success
nox > * tests-3.8: success
nox > * tests-3.9: success
nox > * lint-2.7: success
nox > * lint-3.8: success
nox > * lint-3.9: success
```

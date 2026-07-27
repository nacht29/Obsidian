
## Local `gcloud` login

#### Normal login
```sh
gcloud auth login
gcloud config set project <YOUR_PROJECT_ID>
```

#### Application login (code)
```sh
gcloud auth application-default login
gcloud auth application-default set-quota-project <YOUR_PROJECT_ID>
```

#### All-in-one
```sh
gcloud init
```

## Check config

```sh
gcloud config list
```
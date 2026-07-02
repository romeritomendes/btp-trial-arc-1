# btp-trial-arc-1

## Create BTP Cockpit Trial Account

## Create a local config file for the ABAP Trial instance
```params.json
{
    "email": "youremail@mail.com"
}
```

## Create ABAP Trial instance

```bash
cf create-service abap-trial shared my-abap-instance -c .\params-trial.json
```
## 
## Create Service Key for ABAP Trial instance

```bash
cf create-service-key my-abap-instance default
```

## Create Destinations

### Basic Auth

Property	Value
Name	SAP_TRIAL (your choice)
Type	HTTP
URL	http://<sap-host>:<sap-port>
Authentication	BasicAuthentication
User	Technical SAP user
Password	Technical SAP password

### Propagation

# Clone ARC-1

```bash
git clone https://github.com/arc-mcp/arc-1.git

cd arc-1

cp mta-overrides.mtaext.example mta-overrides.mtaext   # edit your destinations + flags
```
## Edit MTA file (mta-overrides.mtaext)


```bash

```
## c

```bash
npm ci                                                 # mbt's before-all build needs deps

npm run btp:build-deploy-ext
```
## d
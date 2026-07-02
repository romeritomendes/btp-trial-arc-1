# btp-trial-arc-1

## 1.Create BTP Cockpit Trial Account

## 2.Create a local config file for the ABAP Trial instance
```json:params-trial.json
{
    "email": "youremail@mail.com"
}
```

## 3.Create ABAP Trial instance

```sh
cf create-service abap-trial shared my-abap-instance -c .\params-trial.json
```

## 4.Create Service Key for ABAP Trial instance

```sh
cf create-service-key my-abap-instance default
```

## 5.Read Service Key for ABAP Trial instance

```sh
cf service-key my-abap-instance default
```

## 6.Create Destinations

### 6.1.Basic Auth

| Property | Value | Example |
| ---------- | ---------- | ---------- |
| Name | ```SAP_TRIAL``` | |
| Type | ```HTTP``` | |
| URL | ```http://<instanceID>.abap.<location>.hana.ondemand.com``` | ServiceKey: default.url |
| Authentication | BasicAuthentication``` | |
| User | Technical SAP user | ServiceKey: default.uaa.clientid |
| Password | Technical SAP password | ServiceKey: default.uaa.clientsecret |

### 6.2.Propagation

| Property | Value | Example |
| ---------- | ---------- | ---------- |
| Name | ```SAP_TRIAL_PP``` | |
| Type | ```HTTP``` | |
| URL | ```http://<instanceID>.abap.<location>.hana.ondemand.com``` | ServiceKey: default.url |
| Authentication | ```OAuth2UserTokenExchange``` | |
| Client ID | ```Technical SAP user``` | ServiceKey: default.uaa.clientid |
| Client Secret | ```Technical SAP password``` | ServiceKey: default.uaa.clientsecret |
| Token Service URL | ```http://<subaccount>.authentication.<location>.hana.ondemand.com/oauth/token``` | ServiceKey: default.uaa.url |

# 7.Clone ARC-1

```sh
git clone https://github.com/arc-mcp/arc-1.git

cd arc-1
```

## 8.Copy and Edit the MTA file (mta-overrides.mtaext)

```sh
cp mta-overrides.mtaext.example mta-overrides.mtaext
```

```mta
_schema-version: "3.1"
ID: arc1-mcp-overrides
extends: arc1-mcp

modules:
  - name: arc1-mcp-server
    properties:
      # ── Destinations (REQUIRED override) ─────────────────────────────
      # BasicAuth destination for startup (feature probing, cache warmup)
      SAP_BTP_DESTINATION: "SAP_TRIAL"

      # PrincipalPropagation destination for per-user requests (with JWT)
      SAP_BTP_PP_DESTINATION: "SAP_TRIAL_PP"

      # ── Principal propagation behaviour ──────────────────────────────
      # Enable per-user principal propagation (default in mta.yaml: true)
      SAP_PP_ENABLED: "true"

      # ── Safety / Authorization ───────────────────────────────────────
      # Enable object mutations (create, update, delete)
      SAP_ALLOW_WRITES: "true"

      # Enable transport mutations (release, create, add to transport).
      # Also requires SAP_ALLOW_WRITES=true.
      SAP_ALLOW_TRANSPORT_WRITES: "true"

      # Restrict writes to these packages (comma-separated, supports
      # wildcards). Default in mta.yaml: "$TMP".
      SAP_ALLOWED_PACKAGES: "Z*,Y*,$TMP"
    requires:
      - name: arc1-xsuaa
```

## 9.Build and Deploy to BTP 

```sh
npm ci

npm run btp:build-deploy-ext
```

## 10.Assign Roles to your user
```sh
ARC-1 Admin (dev)
ARC-1 Developer + Data (dev)
ARC-1 Viewer + SQL (dev)
```

## 11. Add to Cloud Code

```sh
claude mcp add --transport http ARC-TRIAL https://<subaccount>-dev-arc1-mcp-server.cfapps.<location>.hana.ondemand.com/mcp
```
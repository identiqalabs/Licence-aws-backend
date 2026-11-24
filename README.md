# Licence-aws-backend

The **AWS Lambda deployment package** for the Identiqa license service.

This repository holds only `function.zip`, the built bundle that is uploaded to
AWS Lambda. **The source code is in the [`license_backend`](https://github.com/identiqalabs/license_backend)
repository.** Make code changes there, then rebuild the zip.

## What's in `function.zip`

| Path | Description |
|---|---|
| `lambda.js` | Lambda handler (`lambda.handler`). Routes are served under `/backend_api` |
| `server.js` | Express server for local runs |
| `src/` | Routes, controllers, Mongoose model and usage-sync job |
| `package.json`, `package-lock.json` | Dependencies |
| `node_modules/` | Installed dependencies (about 1,900 files) |

The code matches `license_backend` (apart from Windows line endings). For the API
endpoints, see that repository's README.

## Rebuilding the package

From a clone of `license_backend`:

```bash
npm install --omit=dev
npm run build        # zip -r function.zip .
```

Make sure `.env` and other local-only files are **not** included in the zip;
the build script zips the whole folder.

## Deploying

1. Upload `function.zip` to the Lambda function (console, or
   `aws lambda update-function-code --function-name <name> --zip-file fileb://function.zip`).
2. Handler: `lambda.handler` · Runtime: Node.js 18 or newer.
3. Set these as Lambda environment variables:

   | Variable | Description |
   |---|---|
   | `MONGO_URI` | MongoDB connection string |
   | `HMAC_SECRET` | Secret used to sign license responses |

4. Expose it with a Lambda Function URL. The license agent and proxy in
   `zabbix-docker` call `<function-url>/backend_api/check-license/...`.

## ⚠️ Note about the current zip

The `function.zip` currently committed includes a `.env` file with real
configuration values. Rebuild the package without it, and change the values it contained.

# AWS SAM Cognito Example

An example SAM project with one unauthenticate endpoint and one endpoint requiring username/password authentication.

This project adds to the `Hello World Example` quick start application.

## Usage
### 1. Create the Infrastructure

```
export COGNITO_USER_EMAIL='me@example.com'

sam build && sam deploy --guided --parameter-overrides CognitoUserEmail=$COGNITO_USER_EMAIL
```

Make note of all of the outputs from Cloudformation.

Check your email for a temporary password.

### 2. Start the Authentication Process

Replace `<TEMP-PASS>` and `<CLIENT-ID>`.

```
aws cognito-idp initiate-auth --auth-flow USER_PASSWORD_AUTH --auth-parameters "USERNAME=$COGNITO_USER_EMAIL,PASSWORD=<TEMP-PASS>" --client-id <CLIENT-ID> --query "Session" --output text
```

Use the output in place of `<SESSION>` in the next command.

### 3. Set a New Password

Replace `<USER-POOL>`, `<CLIENT-ID>`, `<NEW-PASS>`, and `<SESSION>` with your unique values.

```
aws cognito-idp admin-respond-to-auth-challenge --user-pool-id <USER-POOL> --client-id <CLIENT-ID> --challenge-responses "USERNAME=$COGNITO_USER_EMAIL,NEW_PASSWORD=<NEW-PASS>" --challenge-name NEW_PASSWORD_REQUIRED --session <SESSION>
```

Use the value of `IdToken` in the next command.

### 4. Send an Authenticated Request

Replace `<ID-TOKEN>` and `<API-ID>`.

```
curl -H "Authorization: Bearer <ID-TOKEN>" https://<API-ID>.execute-api.us-east-1.amazonaws.com/api/hellowithauth/
```
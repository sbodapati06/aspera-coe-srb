# Aspera on Cloud




# Interacting with AOC

NOTE: SCRIPT IS CREATED IN /Users/sbodapati/Downloads/sherwin-williams FOLDER.

<br>



## 1. REST API - Integration

You can use REST API's to interact with AoC. <br>


### a) Generate Private key. <br>
```
ssh-keygen -t rsa -b 4096 -m PEM -f jwtRS256.key 
```

### b) Generate Public key.<br>
```
openssl rsa -in jwtRS256.key -pubout -outform PEM -out jwtRS256.key.pub
```


### c) Create API Client
Logon to AoC
Admin Application > Integrations > API Clients > New <br>
```
sbodapati-test1
Redirect URL: https://localhost:12345
Enable JWT Grant Type: Check
Global key (PEM format) (optional) : cat jwtRS256.key.pub &&& Paste the Public key into this field.
```

### Below steps will retrieve Bearer token
### AFter receiving bearer token, then kick the manual transfer job.

```
AUTOMATION_WORKFLOW_ID=$1

if [[ -z "${AUTOMATION_WORKFLOW_ID// /}" ]]; then
  echo "Please pass AUTOMATION_WORKFLOW_ID. Example: $0 7946"
  exit 1
fi

# Constants
CERT_PATH="./jwtRS256.key" # The path could be relative or absolute
ORGANIZATION_NAME="sedemo"
EMAIL="sudhakar.bodapati@ibm.com"
API_CLIENT_ID="oNfm0yu0qg"
API_CLIENT_SECRET="ab4r-v-Z6vzPsfKGluParoWXtV9nnQo6GDNnJtgykmHfvI7CzPaJKd_-otU7AHdr7GUQukt8CqEI708iflXZ_ajxke_h3FHL"
SCOPE="user:all" # Change for any valid scope

# JWT Header
header=$(echo -n \
'{"typ":"JWT",
"alg":"RS256"}' | tr -d ' \n' | base64 | tr '+/' '-_' | tr -d '\n')

# JWT Payload
current_time=$(date +%s)
not_before=$(($current_time - 300)) # 5 minutes before now
expiration_time=$(($current_time + 300)) # 5 minutes from now

payload=$(echo -n '{
"iss": "'$API_CLIENT_ID'",
"sub": "'$EMAIL'",
"aud": "https://api.ibmaspera.com/api/v1/oauth2/token",
"nbf": '$not_before',
"exp": '$expiration_time'
}' | tr -d ' \n' | base64 | tr '+/' '-_' | tr -d '=\n')

# JWT Signature
signature=$(echo -n "$header.$payload" | openssl dgst -sha256 -sign $CERT_PATH | base64 | tr '+/' '-_' | tr -d '\n=')

# JWT
JWT=$(echo "$header.$payload.$signature")

echo \n
echo "Retrieving Bearer token............"
response=$(curl -u $API_CLIENT_ID:$API_CLIENT_SECRET "https://api.ibmaspera.com/api/v1/oauth2/${ORGANIZATION_NAME}/token" \
    --data-urlencode  "assertion=${JWT}" \
    --data-urlencode "grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer" \
    --data-urlencode  "scope=${SCOPE}" \
    -H "Content-Type: application/x-www-form-urlencoded"
    )
### echo $response

token=$(echo "Bearer $(echo $response | jq -r '.access_token')")
echo $token

echo \n
echo "Kickoff Automation Workflow " $AUTOMATION_WORKFLOW_ID
response=$(curl --request POST \
  --url https://api.ibmaspera.com/automation/v1/workflows/$AUTOMATION_WORKFLOW_ID/launch \
  --header 'accept: application/json' \
  --header "Authorization: ${token}" )

echo $response

echo \n
echo "Finish!!"
```


#### 2. test
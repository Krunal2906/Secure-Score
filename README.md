# Secure-Score
* In this readme file we will know that how to fetch Defender's Secure Score without login into azure account.

## Step-1: Create an Apllication inside MS Entra ID 
1. First of all we have to create an application inside Entra ID for that go to your azure account > Microsoft Entra ID > Manage > Click on App Registation > New 
   registration.
   
   ![image](https://github.com/user-attachments/assets/f5d7782a-a0b2-404a-bb37-ff8582dc1b45)


3. Now Give name to your application and click on register.

   ![image](https://github.com/user-attachments/assets/4ec59d98-740d-4497-b32a-f407c7b06ed9)


4. Now go to All Aplication and click on your application.

    ![image](https://github.com/user-attachments/assets/8a155bfb-6743-468a-9213-62b59dfe0ede)


6. Now note down Client ID and Tenant ID values it is imporatant in order to get secure score and then click in Certificates & secrets.

   ![image](https://github.com/user-attachments/assets/4341679e-3cdb-4927-94cc-7fe37ac9e768)


7. Now go to client secrets > New client secret and then give appropriate Description and click on Add.

   ![image](https://github.com/user-attachments/assets/02232203-a827-494a-9130-cb7ff6419ad5)


8. Note down the Secret value it is also important in order to get Secure score.
 
   ![image](https://github.com/user-attachments/assets/ebd48ee5-f125-4bdc-b1a4-dd8b75912edd)


   
## Step-2: Give required permission to Application  

1. Next go to your Subscription and note down subscription ID and then go to Access control (IAM).
 
   ![image](https://github.com/user-attachments/assets/4f7bac77-ae8c-4adc-8fdb-7c4ae96b2cad)


2. Now click on Add and then Add Role Assignment as shown below.
 
   ![image](https://github.com/user-attachments/assets/7b13aa03-ca08-4026-8e49-fc41b4f92a7d)


3. Search for Security Reader Role and select it and click on Next.
 
   ![image](https://github.com/user-attachments/assets/c8ac8d6e-ff51-49c3-a8e4-c8d1d11826e7)


4. Now click on select members and search for your application and click on select button and then click on Review and Create.
 
   ![image](https://github.com/user-attachments/assets/bc193bef-b427-4864-8179-206adf566731)



## Step-3 : Create an Item in Zabbix for secure score


### Azure Secure Score Retrieval Script

This Python script retrieves the Azure Secure Score for a specified subscription using Azure's REST API.

### Prerequisites

- Python 3.x
- `requests` library (install via `pip install requests`)

### Usage

To use this script, you need to provide your Azure tenant ID, client ID, client secret, and subscription ID as command-line arguments.

### Command-Line Arguments

1. `tenant_id` - Your Azure tenant ID
2. `client_id` - Your Azure client ID
3. `client_secret` - Your Azure client secret
4. `subscription_id` - Your Azure subscription ID

### Scipt

```python
import sys
import requests

# Check if all required arguments are provided
if len(sys.argv) != 5:
    print("Usage: python script.py <tenant_id> <client_id> <client_secret> <subscription_id>")
    sys.exit(1)

# Retrieve command-line arguments
tenant_id = sys.argv[1]
client_id = sys.argv[2]
client_secret = sys.argv[3]
subscription_id = sys.argv[4]
secure_score_name = 'ascScore'  # or the specific secure score name you need

# Get access token
token_url = f'https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/token'
token_data = {
    'grant_type': 'client_credentials',
    'client_id': client_id,
    'client_secret': client_secret,
    'scope': 'https://management.azure.com/.default'
}

token_r = requests.post(token_url, data=token_data)
token = token_r.json().get('access_token')

# Check if token retrieval was successful
if not token:
    print("Failed to retrieve access token. Check your credentials and network connectivity.")
    sys.exit(1)

# Get Azure Secure Score
secure_score_url = f'https://management.azure.com/subscriptions/{subscription_id}/providers/Microsoft.Security/secureScores/{secure_score_name}?api-version=2020-01-01'
headers = {
    'Authorization': f'Bearer {token}'
}

response = requests.get(secure_score_url, headers=headers)

# Check if response was successful
if response.status_code != 200:
    print(f"Failed to retrieve Azure Secure Score. Status code: {response.status_code}")
    sys.exit(1)

secure_score = response.json()

# Extract percentage value and print it
percentage_value = secure_score.get('properties', {}).get('score', {}).get('percentage')
if percentage_value is not None:
    print(f'{percentage_value}')
else:
    print("Failed to extract percentage value from the response.")
    sys.exit(1)
 ```
   

  
  
   
   


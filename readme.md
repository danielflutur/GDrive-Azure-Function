## Set-up Steps for the Google Drive API

### 1. Login to Google Cloud Console and Create a New Project
- Go to [Google Cloud Console](https://console.cloud.google.com/).
- Create a new project.

### 2. Enable the Google Drive API
- In your project, navigate to **API Library**.
- Enable the **Google Drive API**.

### 3. Set Up the Consent Screen
- Configure the consent screen by providing necessary details like:
  - Application name
  - Scopes
  - Support email, etc.

---

## Create the Service Account Credentials

### 1. Create a Service Account
- Go to the **Credentials** tab of your project.
- Click **Create credentials** → **Service account**.
- Enter the **Service account name**.
- The **Service account ID** (must be unique) is generated automatically.
- Add a brief description.
- Press **Create and Continue**.

> 💡 *Grant the service account access to the project.*
> - For downloading files, the **Viewer** permission will suffice.

- Click **Done**, or skip the *Grant users access* step.

### 2. Generate a Key for the Service Account
- Open the created service account and navigate to the **Keys** tab.
- Click **Add Key** → **Create new key**.
- Choose the **JSON** option and press **Create**.
- The key will be downloaded locally. **Store it securely**.
- Add the downloaded key to your project.

---

## Automating the Creation of the Service Account

> You can automate service account creation using tools like `gcloud CLI`, `Terraform`, etc. Azure Bicep is not suitable for Google Cloud resources.

---

## Steps to Accomplish the Automation Process in Azure DevOps Pipeline

### 1. Install Google Cloud SDK
Ensure that the agent running the Azure DevOps pipeline has the **Google Cloud SDK** installed (includes `gcloud` CLI).

### 2. Create the Service Account Command
This command creates a new service account named **my-service-account@YOUR_PROJECT_ID.iam.gserviceaccount.com**
```bash
gcloud iam service-accounts create my-service-account \
  --description="Service account for downloading files from Google Drive" \
  --display-name="GDrive Download Viewer"
```

### 3. Assign the Viewer Role
After creating the service account, assign it the Viewer role so it can read (download) files from Google Drive. Replace YOUR_PROJECT_ID with the actual Google Cloud project ID. This command updates the IAM policy for your project to grant the Viewer role to the new service account.
```bash
gcloud projects add-iam-policy-binding YOUR_PROJECT_ID \
  --member="serviceAccount:my-service-account@YOUR_PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/viewer"
```

### 4. Automate Key Generation
The following command is used to generate a key for the service account (to be used by the Azure function). This creates a JSON key file named key.json that you can securely upload to your secret store e.g., Azure Key Vault.
```bash
gcloud iam service-accounts keys create key.json \
  --iam-account=my-service-account@YOUR_PROJECT_ID.iam.gserviceaccount.com
```
# Content moderation application 

## Description

This app is a text moderation tool that can be used to analyze text in Finnish for harmful content. 
It uses the Google Cloud Natural Language API to identify potentially harmful categories in the text, such as violence, profanity, and hate speech. 

The app also uses the Google Cloud Translate API to translate the text into English if it is not already in English. 
This allows users to analyze text that is not in their native language.

**Usage**
- Enter the text you want to analyze into the text area and
- Click the "Analyze" button.

The app will then display the results of the analysis, including the categories that were identified and the confidence values for each category.

This app can be used to help protect users from harmful content online. It can also be used to help businesses and organizations moderate their own content.

![image](https://github.com/sandra-calvo/content_moderation/blob/main/ui.png)

![image](https://github.com/sandra-calvo/content_moderation/blob/main/categories.png)

## Components 

- **[Natural Language AI](https://cloud.google.com/natural-language?hl=en)**: Powerful tool that can be used to analyze text in a variety of ways. It can be used to entity extraction, sentiment analysis, or text moderation. This information can then be used to improve the user experience, provide better customer service, and make more informed decisions.
  - Check [language support](https://cloud.google.com/natural-language/docs/languages) from the documentation.
     
- **[Translation AI](https://cloud.google.com/translate?hl=en)**: Can be used to translate text from one language to another. It is easy to use and can be integrated into existing systems. The Translation API can be used for a variety of purposes, including: customer service, marketing, education and research. It can be integrated with existing systems.
  - Supports over 100 languages. Check [language support](https://cloud.google.com/translate?hl=en) from the documentation.
  - You can also train your own custom model using [AutoML](https://cloud.google.com/translate/automl/docs/). This allows you to fine-tune the model for your specific needs. 

   
## Run the Application locally (on Cloud Shell)
NOTE: Before you move forward, ensure that you have followed the instructions in SETUP.md. Additionally, ensure that you have cloned this repository and you are currently in the gemini-streamlit-cloudrun folder. This should be your active working directory for the rest of the commands.

To run the Streamlit Application locally (on cloud shell), we need to perform the following steps:

Setup the Python virtual environment and install the dependencies:

In Cloud Shell, execute the following commands:

```bash
python3 -m venv gemini-streamlit
source gemini-streamlit/bin/activate
pip install -r requirements.txt
```

Your application requires access to two environment variables:
- GCP_PROJECT : This the Google Cloud project ID.
- GCP_REGION : This is the region in which you are deploying your Cloud Run app. For e.g. us-central1.

These variables are needed since the Vertex AI initialization needs the Google Cloud project ID and the region. 
The specific code line from the app.py function is shown here: vertexai.init(project=PROJECT_ID, location=LOCATION)

In Cloud Shell, execute the following commands:

```bash
export GCP_PROJECT='<Your GCP Project Id>'  # Change this
export GCP_REGION='<Your region>'             # If you change this, make sure the region is supported.
```
To run the application locally, execute the following command:

In Cloud Shell, execute the following command:

```bash
streamlit run app.py \
  --browser.serverAddress=localhost \
  --server.enableCORS=false \
  --server.enableXsrfProtection=false \
  --server.port 8080
```

The application will startup and you will be provided a URL to the application. Use Cloud Shell's web preview function to launch the preview page. You may also visit that in the browser to view the application. Choose the functionality that you would like to check out and the application will prompt the Vertex AI Gemini API and display the responses.

## Build and Deploy the Application to Cloud Run
NOTE: Before you move forward, ensure that you have followed the instructions in SETUP.md. Additionally, ensure that you have cloned this repository and you are currently in the gemini-streamlit-cloudrun folder. This should be your active working directory for the rest of the commands.

To deploy the Streamlit Application in Cloud Run, we need to perform the following steps:

Your Cloud Run app requires access to two environment variables:

* GCP_PROJECT : This the Google Cloud project ID.
* GCP_REGION : This is the region in which you are deploying your Cloud Run app. For e.g. us-central1.

These variables are needed since the Vertex AI initialization needs the Google Cloud project ID and the region. 
The specific code line from the app.py function is shown here: vertexai.init(project=PROJECT_ID, location=LOCATION)

**1. In Cloud Shell, execute the following commands:**

```bash
export GCP_PROJECT='<Your GCP Project Id>'  # Change this
export GCP_REGION='<Your region>'           # If you change this, make sure the region is supported.
```
Now you can build the Docker image for the application and push it to Artifact Registry. To do this, you will need one environment variable set that will point to the Artifact Registry name. Included in the script below is a command that will create this Artifact Registry repository for you.

**2. Create an image and push to the Artifact Registry**

In Cloud Shell, execute the following commands:

```bash
export AR_REPO='<REPLACE_WITH_YOUR_AR_REPO_NAME>'  # Change this
export SERVICE_NAME='<REPLACE_WITH_YOUR_SERVICE_NAME>' # This is the name of our Application and Cloud Run service. Change it if you'd like.

#make sure you are in the active directory for 'gemini-streamlit-cloudrun'
gcloud artifacts repositories create "$AR_REPO" --location="$GCP_REGION" --repository-format=Docker
gcloud auth configure-docker "$GCP_REGION-docker.pkg.dev"
gcloud builds submit --tag "$GCP_REGION-docker.pkg.dev/$GCP_PROJECT/$AR_REPO/$SERVICE_NAME"
```

**3. Deploy to Cloud Run**

In Cloud Shell, execute the following command:
```bash
gcloud run deploy "$SERVICE_NAME" \
  --port=8080 \
  --image="$GCP_REGION-docker.pkg.dev/$GCP_PROJECT/$AR_REPO/$SERVICE_NAME" \
  --allow-unauthenticated \
  --region=$GCP_REGION \
  --platform=managed  \
  --project=$GCP_PROJECT \
  --set-env-vars=GCP_PROJECT=$GCP_PROJECT,GCP_REGION=$GCP_REGION
```
On successful deployment, you will be provided a URL to the Cloud Run service. You can visit that in the browser to view the Cloud Run application that you just deployed.
Congratulations!

### NOTE: You may need to open port 8080 in GCP. 


## License
This is not an officially supported Google product. The code in this repository is for demonstrative purposes only.

This project is licensed under the Apache License 2.0.

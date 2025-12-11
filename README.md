# Dataiku Project Bundle: Image Annotation and Model Training

This repository serves as the distribution hub for a Dataiku DSS project bundle for image annotation and model training within an ML pipeline.

This is a portable template designed to be easily imported into any Dataiku DSS instance.

## Import and Configuration

### Prerequisites

Before importing, please ensure you have the following configured on your Dataiku DSS instance:

1.  **Dataiku Version:** DSS 12.0+
2.  **Required Connections:** The Flow uses placeholder connections. You must have the following equivalent connections defined on your instance:
    * `[SOURCE_DB_NAME]`: Used for raw data extraction
    * `[OUTPUT_FILES_STORE]`: Used for final report storage

### Step 1: Download the Bundle

1.  Go to the **[Releases](https://github.com/evanguarnaccia/dss-yolo-train/releases)** tab of this repository.
2.  Download the `.zip` file

### Step 2: Import into Dataiku DSS

1.  Navigate to the **Dataiku DSS Homepage**.
2.  Click **`+ New Project`** $\rightarrow$ **`Import project`**.
3.  Upload the downloaded `.zip` archive.
4.  Choose a unique **Project Key** (e.g., `yolo-train-evan`).

### Step 3: Remapping Connections

The imported project may have broken links to external storage, and the connections will need to be remapped before the import can complete. 

### Step 4: Customize Environment Variable

This project requires a user-defined **Project Variable** to function correctly. This variable is the location in S3 of the images.

1.  Go to **Project Settings** (gear icon) $\rightarrow$ **Variables**.
2.  Under the **"Custom Project Variables"** section, click **`+ Add a new variable`**.
3.  Set the following Key/Value pair:

| Key (Variable Name) | Description | Example Value |
| :--- | :--- | :--- |
| **`file_path`** | This is the path in S3 for the images to be annotated | /data/dataiku/bbox_1/ |

> **How it's Used:** Change this value to have the project load images from other locations.

### Final Step: Build the Flow

Once the connections and the `file_path` variable are configured:

1.  Navigate to the **Flow** view.
2.  Select the final output dataset (e.g., `annotated_images_final`).
3.  Click **Build** and choose the datasets you wish to generate.

---

## Project Architecture Overview (Image Annotation & ML)

This project template is structured around the core phases of the Machine Learning lifecycle for computer vision tasks. The Flow is organized into distinct zones for data ingestion, labeling, model training, and deployment.

1.  **Extraction & Ingestion Zone (p1_INGEST):**
    * **Purpose:** Reads raw image files from the S3 location
    * **Output:** An initial dataset containing file paths and metadata, ready for the labeling phase.

2.  **Annotation & Preparation Zone (p2_LABEL):**
    * **Purpose:** This zone orchestrates the critical human-in-the-loop steps.
    * **Key Action:** Sends a subset of images to an **Annotation Task**
    * **Processes:** Reads the final human-verified **Annotation Labels (JSON/CSV)**

3.  **Model Development Zone (p3_TRAIN):**
    * **Purpose:** Handles feature engineering (if needed), splits the labeled data, and trains the computer vision model.
    * **Processes:** **Data Split** (Train/Validation/Test sets), uses a **Python/Deep Learning Recipe** with frameworks like PyTorch or TensorFlow, and outputs the trained model object. [Image of the typical machine learning workflow and lifecycle]
    * **Output:** The fully trained and versioned **Model** object stored in the Model Registry.

4.  **Evaluation & Deployment Zone (p4_EVAL):**
    * **Purpose:** Assesses the model's performance and prepares it for production use.
    * **Processes:** **Model Evaluation Recipe** (calculating metrics like accuracy, precision, and F1-score), and a **Deployment Recipe** that creates an API endpoint (if the model is served in real-time).
    * **Output:** A deployed **API Endpoint** or a batch scoring recipe that writes predictions back to the `[OUTPUT_DB_CONN]`.

## Troubleshooting

* **Error: Missing Connection:** You did not properly remap the connections in Project Settings.
* **Error: Variable Not Found:** The Python or SQL recipes failed because the `${ENV_SUFFIX}` variable was not defined in **Project Settings** $\rightarrow$ **Variables**.
* **Error: No Data:** The Flow was imported but has not been **Built**. You must manually trigger the Flow build to populate the output datasets.

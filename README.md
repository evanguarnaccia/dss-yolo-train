# Dataiku Project Bundle: Image Annotation Module

This repository serves as the distribution hub for a Dataiku DSS project bundle for image annotation within a ML pipeline.

This is a portable template designed to be easily imported into any Dataiku DSS instance. It contains only the Flow structure, recipes, and logic—no data or connection settings are included.

## Quick Start: Import and Configuration

### Prerequisites

Before importing, please ensure you have the following configured on your Dataiku DSS instance:

1.  **Dataiku Version:** DSS 12.0
2.  **Required Connections:** The Flow uses placeholder connections. You must have the following equivalent connections defined on your instance:
    * `[SOURCE_DB_NAME]`: Used for raw data extraction.
    * `[OUTPUT_FILES_STORE]`: Used for final report storage (e.g., S3, Azure Blob, or a Local Folder).

### Step 1: Download the Bundle

1.  Go to the **[Releases](https://github.com/YourOrg/repo-name/releases)** tab of this repository.
2.  Download the `.zip` file asset corresponding to the desired version (e.g., `[Project-Name]-v1.0.0.zip`).

### Step 2: Import into Dataiku DSS

1.  Navigate to the **Dataiku DSS Homepage**.
2.  Click **`+ New Project`** $\rightarrow$ **`Import project`**.
3.  Upload the downloaded `.zip` archive.
4.  Choose a unique **Project Key** (e.g., `SALES_RPT_DEV`).

### Step 3: Configure Connections (Remapping)

The imported project will have broken links to external storage. You must remap them:

1.  Go to **Project Settings** (gear icon) $\rightarrow$ **Connections**.
2.  For each placeholder connection listed in the project, click **"Remap"** and select the actual, valid connection from your DSS instance (e.g., map the placeholder `[SOURCE_DB_NAME]` to your `Snowflake_Dev_Database`).

## Step 4: Critical Customization (Environment Variable)

This project requires a user-defined **Project Variable** to function correctly. This variable tells the recipes which specific identifier to use in your environment.

1.  Go to **Project Settings** (gear icon) $\rightarrow$ **Variables**.
2.  Under the **"Custom Project Variables"** section, click **`+ Add a new variable`**.
3.  Set the following Key/Value pair:

| Key (Variable Name) | Description | Example Value |
| :--- | :--- | :--- |
| **`file_path`** | Unique identifier for your environment. Used in SQL queries/paths. | `_DEV` or `_TESTING` |

> **How it's Used:** Recipes within the project reference this variable using the syntax `${ENV_SUFFIX}` to dynamically adjust table names, schema paths, or output folder names to ensure isolation from other environments.

## Final Step: Build the Flow

Once the connections and the `ENV_SUFFIX` variable are configured:

1.  Navigate to the **Flow** view.
2.  Select the final output dataset (e.g., `p9_Final_Report_Summary`).
3.  Click **Build** and choose the datasets you wish to generate.

---

## Project Architecture Overview

This project template consists of three main Flow zones:

1.  **Extraction Zone (p1_RAW):** Reads raw data from the `[SOURCE_DB_NAME]` connection.
2.  **Preparation Zone (p2_CLEAN):** Standardizes date formats, handles missing values, and calculates initial metrics.
3.  **Reporting Zone (p3_FINAL):** Joins prepared data, applies final business logic, and writes the output to the `[OUTPUT_FILES_STORE]`.

## Troubleshooting

* **Error: Missing Connection:** You did not properly remap the connections in Project Settings.
* **Error: Variable Not Found:** The Python or SQL recipes failed because the `${ENV_SUFFIX}` variable was not defined in **Project Settings** $\rightarrow$ **Variables**.
* **Error: No Data:** The Flow was imported but has not been **Built**. You must manually trigger the Flow build to populate the output datasets.

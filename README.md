# Agent Workshop

A hands-on workshop demonstrating how to build AI Agents using Microsoft Fabric Data Agents (no-code) and Microsoft Foundry (code-first). This workshop uses a fictitious SaaS subscription analytics dataset modeled as a star schema.

---

## Prerequisites

- An Azure subscription with Owner or Contributor access
- A Microsoft 365 or Entra ID tenant with admin access
- A GitHub account (for repository creation)
- A web browser (Microsoft Edge or Chrome recommended)

---

## Workshop Steps

### Step 1: Enable Microsoft Fabric in Your Tenant

1. Sign in to the [Microsoft 365 Admin Center](https://admin.microsoft.com) as a Tenant Administrator.
2. Navigate to **Settings** → **Org settings** → **Services**.
3. Search for and select **Microsoft Fabric**.
4. Toggle **"Users can access data stored in OneLake with apps external to the Fabric environment"** to **Enabled**.
5. Under **Tenant Settings**, ensure the following are enabled:
   - "Users can create Fabric items"
   - "Users can create and use Data Agents (preview)" — this may be under **Admin Portal** → **Tenant settings** → **Copilot and Azure OpenAI Service**
6. Click **Save**.
7. Alternatively, navigate directly to the [Fabric Admin Portal](https://app.fabric.microsoft.com/admin-portal):
   - Go to **Tenant settings**
   - Under **Microsoft Fabric**, enable **"Users can use Microsoft Fabric items"**
   - Under **Copilot and Azure OpenAI Service**, enable **"Data Agents"** (if available as a separate toggle)

> **Note:** Tenant settings changes may take up to 15 minutes to propagate.

---

### Step 2: Create an F2 Fabric Capacity in Azure

1. Sign in to the [Azure Portal](https://portal.azure.com).
2. Click **+ Create a resource**.
3. Search for **"Microsoft Fabric"** and select **Microsoft Fabric**.
4. Click **Create**.
5. Fill in the required fields:
   - **Subscription:** Select your Azure subscription
   - **Resource group:** Create new or select existing (e.g., `rg-fabric-workshop`)
   - **Capacity name:** Enter a unique name (e.g., `fabric-workshop-f2`)
   - **Region:** Select a region close to you (e.g., `East US`)
   - **Size:** Select **F2** (2 Capacity Units)
   - **Fabric capacity administrator:** Add your user account
6. Click **Review + create**, then **Create**.
7. Wait for deployment to complete (typically 2-5 minutes).
8. Navigate to the resource and verify the status shows **Active**.

> **Cost Note:** F2 capacity costs approximately $0.36/hour. Remember to pause or delete the capacity after the workshop to avoid ongoing charges.

---

### Step 3: Create a Fabric Workspace

1. Navigate to [Microsoft Fabric](https://app.fabric.microsoft.com).
2. Click on **Workspaces** in the left navigation pane.
3. Click **+ New workspace**.
4. Configure the workspace:
   - **Name:** `Agent Workshop` (or your preferred name)
   - **Description:** "Workshop workspace for building AI Agents with Fabric and Foundry"
5. Click **Apply**.
6. Your new workspace is now created and you will be redirected to it.

---

### Step 4: Assign the F2 Capacity to Your Workspace

1. In your newly created workspace, click the **Workspace settings** gear icon (⚙️) in the top-right corner.
2. Select **License info** (or **Premium** tab, depending on your Fabric version).
3. Under **License mode**, select **Fabric capacity**.
4. In the **Capacity** dropdown, select the F2 capacity you created in Step 2 (e.g., `fabric-workshop-f2`).
5. Click **Apply**.
6. You should see a diamond (💎) icon next to your workspace name, confirming the capacity assignment.

> **Note:** If you don't see your capacity in the dropdown, ensure you were added as a Capacity Administrator in Step 2.

---

### Step 5: Create a Fabric Lakehouse

1. In your workspace, click **+ New item**.
2. Under the **Store data** section, select **Lakehouse**.
3. Enter a name for your Lakehouse: `SaaSAnalytics`
4. Click **Create**.
5. You will be taken to the Lakehouse explorer view showing:
   - **Tables** — for structured data (Delta tables)
   - **Files** — for raw file storage

---

### Step 6: Upload Star Schema Files to the Lakehouse

1. In the Lakehouse explorer, click on **Files** in the left panel.
2. Click **Upload** → **Upload files**.
3. Navigate to the `data/` folder in this repository and select all CSV files:
   - `fact_subscriptions.csv`
   - `dim_customer.csv`
   - `dim_product.csv`
   - `dim_subscription_plan.csv`
   - `dim_region.csv`
   - `dim_date.csv`
4. Click **Upload**. Wait for all files to upload successfully.
5. To convert CSVs to Delta tables (recommended for best performance):
   - Right-click on each uploaded CSV file
   - Select **Load to Tables** → **New table**
   - Keep the default table name (it will use the filename)
   - Click **Load**
   - Repeat for each CSV file
6. Verify all six tables appear under the **Tables** section in the Lakehouse explorer.

---

### Step 7: Create a Semantic Model on the Lakehouse Tables

1. In the Lakehouse explorer, click on **New semantic model** in the top ribbon (or navigate from workspace → New item → Semantic model).
2. Name the model: `SaaS Analytics Model`
3. Select the workspace Lakehouse as the data source.
4. Select all six tables:
   - ☑️ `fact_subscriptions`
   - ☑️ `dim_customer`
   - ☑️ `dim_product`
   - ☑️ `dim_subscription_plan`
   - ☑️ `dim_region`
   - ☑️ `dim_date`
5. Click **Confirm**.
6. Once the model is created, open it and navigate to the **Model view** to define relationships:
   - Drag `fact_subscriptions.customer_id` to `dim_customer.customer_id`
   - Drag `fact_subscriptions.product_id` to `dim_product.product_id`
   - Drag `fact_subscriptions.plan_id` to `dim_subscription_plan.plan_id`
   - Drag `fact_subscriptions.region_id` to `dim_region.region_id`
   - Drag `fact_subscriptions.date_id` to `dim_date.date_id`
7. For each relationship, ensure:
   - **Cardinality:** Many-to-one (many on the fact table side)
   - **Cross-filter direction:** Single
8. Click **Save** to persist the model.

---

### Step 8: Create a Fabric Data Agent and Test It

1. Navigate back to your workspace.
2. Click **+ New item**.
3. Under the **Analyze data** or **AI** section, select **Data Agent** (Preview).
4. Name the agent: `SaaS Insights Agent`
5. Click **Create**.
6. In the Data Agent configuration:
   - Under **Select data**, click **Add data source**
   - Choose **Semantic model** and select `SaaS Analytics Model`
   - Click **Add**
7. Under **Instructions**, paste the content from the [`data_agent_instructions.md`](./data_agent_instructions.md) file in this repository. This provides the agent with table descriptions, column definitions, and relationship context.
8. Click **Save**.
9. **Test the agent** by typing questions in the chat panel:
   - "What is the total revenue across all subscriptions?"
   - "Which product category generates the most revenue?"
   - "Show me the churn rate by customer segment"
   - "Which customers have expansion subscriptions?"
   - "What is the average NPS score by region?"
10. Verify the agent returns accurate, data-driven responses.

> **Tip:** See the [Data Agent Instructions](./data_agent_instructions.md) file for a full list of 28 sample questions to test with.

---

## Data Model Overview

This workshop uses a **star schema** representing a SaaS subscription analytics platform:

```
                    ┌──────────────┐
                    │  dim_date    │
                    └──────┬───────┘
                           │
┌──────────────┐    ┌──────┴───────────┐    ┌──────────────────────┐
│ dim_customer │────┤                  ├────│ dim_subscription_plan│
└──────────────┘    │                  │    └──────────────────────┘
                    │fact_subscriptions │
┌──────────────┐    │                  │    ┌──────────────┐
│ dim_product  │────┤                  ├────│  dim_region  │
└──────────────┘    └──────────────────┘    └──────────────┘
```

### Key Metrics Available

- **Revenue:** Total revenue, MRR, ARR
- **Usage:** Seats purchased/active, API calls, storage
- **Health:** NPS scores, churn status, support tickets
- **Growth:** Expansion revenue tracking

---

## Next Steps: Microsoft Foundry Integration

After completing the Fabric Data Agent setup, the next phase of this workshop covers:

1. **Creating a Foundry project** — Set up a Foundry resource to build code-first agents
2. **Connecting to the Fabric Data Agent** — Use the Fabric Data Agent as a tool within a Foundry agent
3. **Augmenting with Foundry capabilities** — Add custom tools, multi-agent orchestration, and advanced reasoning

Stay tuned for Part 2 of this workshop!

---

## Cleanup

To avoid ongoing Azure charges after the workshop:

1. **Pause or delete the F2 capacity** in the Azure Portal:
   - Navigate to your Fabric capacity resource
   - Click **Pause** (to temporarily stop) or **Delete** (to remove entirely)
2. **Delete the resource group** (optional): This removes all associated resources.

---

## License

This workshop content is provided for educational purposes.

---

## Administration - Push Assets to GitHub

1. Create a new repository on GitHub:
   - Go to [github.com/new](https://github.com/new)
   - **Owner:** `krepko7`
   - **Repository name:** `AgentWorkshop`
   - **Description:** "Workshop: Building AI Agents with Microsoft Fabric and Foundry"
   - **Visibility:** Public (or Private, depending on your preference)
   - Click **Create repository**

2. Push the workshop files using Git:

   ```bash
   cd AgentWorkshop
   git init
   git add .
   git commit -m "Initial commit: Agent Workshop assets"
   git branch -M main
   git remote add origin https://github.com/krepko7/AgentWorkshop.git
   git push -u origin main
   ```

3. Verify the repository contains:
   ```
   AgentWorkshop/
   ├── README.md                      (this file)
   ├── data_agent_instructions.md     (Fabric Data Agent configuration)
   └── data/
       ├── fact_subscriptions.csv     (fact table)
       ├── dim_customer.csv           (customer dimension)
       ├── dim_product.csv            (product dimension)
       ├── dim_subscription_plan.csv  (subscription plan dimension)
       ├── dim_region.csv             (region dimension)
       └── dim_date.csv              (date dimension)
   ```

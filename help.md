---
title: Notion data protection
author: HYCU, Inc.
pdf_filename: HYCU-for-Notion.pdf
---

Protecting your SaaS application data consists of the following tasks:

<!--HYCU_HELP:MINI_TOC_TASKS-->

# Preparing for SaaS application data protection

Before you start protecting your Notion data, complete the following steps:

<!--HYCU_HELP:MINI_TOC_STEPS-->

## Getting familiar with your SaaS application specifics

Before you start protecting your Notion data, you must get familiar with
all prerequisites, limitations, considerations, and/or recommendations in
this topic to make sure that your environment is prepared and configured
correctly.

#### Prerequisite

You must collect the following information in Notion:

- Workspace ID: the unique identifier associated with a specific
  Notion workspace.

- Workspace name: the name of a specific Notion workspace.

- Bearer token: the access token that grants access to the resources
  in Notion. For instructions on how to create a bearer token, see
  [Creating a bearer token](#creating-a-bearer-token).

- Restore workspace bearer token. The access token that enables you to
  restore:

  - A complete workspace

  - A page or a database that is directly connected to the backed-up
    workspace

  To generate the restore workspace bearer token, use the same procedure
  as for the bearer token. For instructions, see [Creating a bearer
  token](#creating-a-bearer-token).

  :::Note
  The restore bearer token can be the same as the backup bearer
  token if you want to restore to same workspace. If the workspace is
  deleted, it cannot be recreated using the same backup bearer token.
  :::

- Restore workspace page ID: the unique identifier associated with the
  page in a Notion workspace that will be used by R-Cloud to perform a
  complete or granular restore of:

  - A complete workspace

  - A page or a database that is directly connected to the backed-up
    workspace

  The restore workspace page ID can be found in the URL of the workspace.

  :::Example
  In the `https://www.notion.so/My-Page-Title-1234567890abcdef1234567890abcdef`
  URL, `1234567890abcdef1234567890abcdef` represents the restore workspace
  page ID.
  :::

#### Limitation

HYCU for Notion supports only Google Cloud Storage as a target.

#### Considerations

- After creating a new page or database in Notion, you must manually
  establish the connection between the newly created page or database
  and the integration. To establish the connection in Notion,
  click **Setting & Members** in your workspace and then click **My connections**.
  Enable access to the connection by selecting the resources to which you wish to grant
  access.

- Notion currently enforces a rate limit of 3 API calls per second. This
  limitation may cause the `HTTP 429 Too Many Requests` errors during the
  backup and restore procedures.

- HYCU for Notion backs up data by using a Google Cloud staging target. Select
  the preferred Google Cloud staging target when adding the module as a source
  to R-Cloud.

##### Creating a bearer token

To create a bearer token, in Notion, follow these steps:

1. Create a new Notion integration. For details, see Notion
   documentation about building you first integration with the Notion API.

2. Go to My integrations, and then click **Capabilities**.

3. Enable these options:

   - Under Content Capabilities, enable the **Read content**, **Update
     content** and **Insert Content** options to allow protecting data.

   - Under Comments Capabilities, enable the **Read comments** and
     **Insert comments** options to allow protecting comments.

   - Under User capabilities, enable the **Read user information** option
     including email addresses to allow protecting user mentions.

4. Click **Save**.

5. To make the created integration publicly accessible, navigate to
   Distribution, and then do the following:

   a. Enable the **Do you want to make this integration public?** option.

   b. Fill in your organization information.

6. Click **Submit**.

7. To manage the public integration secrets, navigate to Secrets, and
   then do the following:

   a. Copy the Authorization URL, and then open the URL in a browser that
      will redirect you to Notion.

   b. Select the required workspace from the header of the authorization
      dialog box.

   c. Select the pages or databases to which you want to provide access,
      and the click **Allow access**. By selecting the resources, Notion
      will grant the permissions to access the resource.

      :::Note
      To protect the subordinate pages or databases, also grant access
      to the parent pages or databases.
      :::

   d. The control is redirected to the redirection URL with the code. The
      code generates the access token.

      :::Note
      The code is valid only for a single use. If an error appears,
      repeat the procedure to generate a new code.
      :::

   e. Create a bearer token by using a Notion API call. The following is
      the API call structure:

      :::code-small-text

      ```None
      curl --location 'https://api.notion.com/v1/oauth/token' \
      --header 'Authorization: Basic BASE64_CLIENT_ID:BASE64_CLIENT_SECRET' \
      --header 'Content-Type: application/json' \
      --data '{
        "grant_type": "authorization_code",
        "code": "",
        "redirect_uri": "",
      }
      ```

      :::

     The API will respond with the workspace details with access token. Use
     the provided access token in R-Cloud as the bearer token.

<!--HYCU_HELP:INSERT_CONFIGURING_SAAS_APP_BACKUPS_SECTION-->

# Backing up data

R-Cloud enables you to back up your Notion data securely and efficiently.

## Configuring the SaaS application for backup operations

This topic includes all prerequisites, limitations, considerations, and/or recommendations that help you enable your SaaS application data backups on the module side.

#### Limitations

- The staging target that you add to R-Cloud when adding an R-Cloud module and
  the target that is defined in the policy assigned to the related SaaS
  application must reside on the same cloud platform.

- SaaS application data cannot be stored to the automatically created targets.

#### Considerations

- HYCU for Notion backs up data by using a staging target. Select the preferred
  staging target when adding the module as a source to R-Cloud.

- If you use an automatically created staging target, the HMSA must be
  configured to perform all operations on the target specified in the policy
  that is assigned to the related SaaS application. Alternatively, the same
  cloud account must be configured to perform all operations on both targets
  (the staging target and the target specified in the policy that is assigned to
  the related SaaS application).

- If you encounter a 404 Not Found error that can be found in the R-Cloud backup
  task reports, the cause could be that the integration is not enabled for the
  specific resource. To resolve this issue, enable the access to the resource
  manually in Notion as follows:

   1. Open the workspace settings.

   2. Select **My connections** from the Settings pop-up dialog box.

   3. Select the relevant resources or provide a new bearer token. For
     details, see the topic Creating a bearer token in Getting familiar with your SaaS application specifics.

<!--HYCU_HELP:INSERT_PERFORMING_BACKUPS_SECTION-->

# Restoring data

R-Cloud enables you to restore an entire SaaS application or its resources
to a specific point in time. Before restoring Notion data, you must get
familiar with all prerequisites, limitations, considerations, and/or
recommendations in this topic.

#### Prerequisite

To enable the file restore with R-Cloud, the storage bucket must be
publicly accessible.

#### Limitations

- Restoring pages and databases directly under the workspace is not
  supported. Instead, the pages and the databases are restored to a
  restore Notion page that you provide.

- The following page and database properties cannot be restored:

  - Created by

  - Created time

  - Last edited by

  - Last edited time

  - Unique ID

  - Rollup

  - Verification

  - Status

- The progress bar associated with the Number property cannot be
  restored.

- The maximum numbers of restored elements are:

  - Options in a Multi-select property: 100

  - Characters in the Email and Phone number properties: 200

  - Database heading characters: 2000

  - Page heading characters: 2000

  - URL characters: 2000

  The parent item property can be restored if the same naming
  convention is followed at both the page and database levels. If the
  naming convention is not maintained, the restore is not possible.

- Up to 25 page-related properties are supported for restore.

- Up to 25 person-related properties are supported for restore.

- *For pages:* The following items cannot be restored:

  - Attachments and reactions in the page comments

  - Draft page comments

  - Page cover image position

  - The template_mention property

  - Page properties if the relation between the parent and the sub-item
    is not preserved

- *For databases:* The following items cannot be restored:

  - Database configuration fields, such as filters, sub-items, and
    groups

  - Order of database rows and columns

  - Database cover image position

  - Linked databases

  - Custom database views

- *For blocks:*

  - The following types of blocks cannot be restored:

    - Unsupported

    - Audio

    - Link Preview

    - Duplicate synced block

    - Template

    - Button

    - Empty attachment/file blocks without content

  - Attachments and reactions inside the block comments cannot be
    restored.

  - Block comments cannot be restored to the same block. Instead, the
    comments will be restored to the parent page of the block.

  - The child database or child page of a block can be restored only to
    their parent page.

  - The column list, the column structure, and the hierarchy cannot be
    preserved. They will be restored at the page level and at the parent
    block level.

  - The draft comments cannot be restored.

  - Up to 100 text blocks that contain one of the listed styles can be
    restored:

    - Paragraph text

    - Heading (1, 2, 3)

    - Callout

    - Toggle

    - Bullet list

    - To-do

    - Numbered list text content

    - Captions of other blocks

- *For files:*

  - The attached files cannot be restored. Instead, a storage target
    file path is provided to Notion to restore the attached files.

  - If a storage target bucket is not publicly accessible, restoring the
    attachment will result in a permission denied warning showing up.

#### Considerations

- As a part of the restore you can perform the complete and granular
  restore of workspace, pages, databases, and blocks.

- R-Cloud allows you to restore the following video types:
  `.amv`, `.asf`, `.avi`, `.f4v`, `.flv`, `.gifv`, `.mkv`, `.mov`,
  `.mpg`, `.mpeg`, `.mpv`, `.mp4`,  
  `.m4v`, `.qt`, `.wmv`, and
  YouTube video links that include the embed or watch elements.

  :::Example
  `https://www.youtube.com/watch?v=\[id\]`  
  `https://www.youtube.com/embed/\[id\]`
  :::

- If you encounter a 404 Not Found error that can be found in the R-Cloud
  restore task reports, the cause could be that the integration
  is not enabled for the specific resource. To resolve this issue, enable
  the access to the resource manually in Notion as follows:

  1. Open the workspace settings.

  2. Select **My connections** from the Settings pop-up dialog box.

  3. Select the relevant resources or provide a new bearer token. For
     details, see see the topic Creating a bearer token in Getting familiar with your SaaS application specifics.

<!--HYCU_HELP:INSERT_PERFORMING_RESTORES_SECTION-->

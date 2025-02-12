# Migration Tools Configuration

This documentation contains configuration files for setting up and running migration tasks using the Migration Tools from [nkdAgility](https://nkdagility.com/learn/azure-devops-migration-tools/). For more details, refer to the [Azure DevOps Migration Tools documentation](https://nkdagility.com/learn/azure-devops-migration-tools/).

## Components That Can Be Migrated

Using the Migration Tools, you can migrate the following components from one Azure DevOps organization to another:

1. **Work Items**:
    - User Stories
    - Bugs
    - Epics
    - Features
    - Issues
    - Tasks
    - Test Cases
    - Test Plans
    - Test Suites

3. **Pipelines**:
    - Build Pipelines
    - Release Pipelines

4. **Service Connections**:
    - Service connections used in pipelines

5. **Task Groups**:
    - Task groups used in pipelines

6. **Variable Groups**:
    - Variable groups used in pipelines

7. **Team Settings**:
    - Team configurations and settings
    - Team capacities

8. **Area and Iteration Paths**:
    - Area paths
    - Iteration paths

9. **Work Item Links**:
    - Links between work items

10. **Attachments**:
     - Work item attachments

11. **Embedded Links and Images**:
     - Embedded links and images in work items

    ## Installation and Running Commands

    ### Installing the Utility via Chocolatey

    To install the Migration Tools utility using Chocolatey, follow these steps:

    1. **Install Chocolatey**:
        If you haven't installed Chocolatey yet, you can do so by running the following command in an elevated PowerShell prompt:

        ```powershell
        Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
        ```

    2. **Install Migration Tools**:
        Once Chocolatey is installed, you can install the Migration Tools utility by running the following command:

        ```powershell
        choco install vsts-sync-migrator --version 16.1.0-preview0003
        ```

    ### Running Commands Locally

    After installing the Migration Tools utility, you can run the following commands locally to perform migration tasks:

    1. **Navigate to the Directory**:
        Open a command prompt or PowerShell window and navigate to the directory containing your configuration files:

        ```powershell
        cd /C:/tools/MigrationTools/
        ```

    2. **Run the Migration Command**:
        Use the Migration Tools utility to run the migration command with the appropriate configuration file. For example, to run the migration using `configuration.json`, use the following command:

        ```powershell
        devopsmigration.exe execute --config configuration.json
        ```

        Similarly, to run the migration using `configuration-pipeline.json`, use the following command:

        ```powershell
        devopsmigration.exe execute --config configuration-pipeline.json
        ```

    These steps will help you install the Migration Tools utility via Chocolatey and run the necessary commands locally to perform your migration tasks.

These components can be configured and migrated using the provided configuration files (`configuration.json` and `configuration-pipeline.json`).

## Configuration Files

### configuration.json

The `configuration.json` file contains settings and parameters required for migrating work items, repositories, and other entities from a source project to a target project.

### configuration-pipeline.json

The `configuration-pipeline.json` file contains settings and parameters required for migrating build and release pipelines, task groups, variable groups, and service connections from a source project to a target project.

## Required Information

To use these configuration files, you need to provide the following information:

### For configuration.json

1. **Source Endpoint**:

   - `Collection`: The URL of the source Azure DevOps collection.
   - `Project`: The name of the source project.
   - `AccessToken`: The access token for authentication.
   - `ReflectedWorkItemIdField`: The field used to reflect work item IDs.
   - `LanguageMaps`: Mappings for area and iteration paths.

2. **Target Endpoint**:

   - `Collection`: The URL of the target Azure DevOps collection.
   - `Project`: The name of the target project.
   - `AccessToken`: The access token for authentication.
   - `ReflectedWorkItemIdField`: The field used to reflect work item IDs.
   - `LanguageMaps`: Mappings for area and iteration paths.

3. **Processors**:

   - `WIQLQuery`: The WIQL query to select work items for migration.
   - `SourceName`: The name of the source endpoint.
   - `TargetName`: The name of the target endpoint.
   - Other processor-specific settings.

4. **CommonTools**:
   - `Mappings`: Mappings for repositories, fields, and other entities.

### For configuration-pipeline.json

1. **Source Endpoint**:

   - `Organisation`: The URL of the source Azure DevOps organization.
   - `Project`: The name of the source project.
   - `AccessToken`: The access token for authentication.
   - `ReflectedWorkItemIdField`: The field used to reflect work item IDs.

2. **Target Endpoint**:

   - `Organisation`: The URL of the target Azure DevOps organization.
   - `Project`: The name of the target project.
   - `AccessToken`: The access token for authentication.
   - `ReflectedWorkItemIdField`: The field used to reflect work item IDs.

3. **Processors**:

   - `BuildPipelines`: The list of build pipelines to migrate.
   - `ReleasePipelines`: The list of release pipelines to migrate.
   - `SourceName`: The name of the source endpoint.
   - `TargetName`: The name of the target endpoint.
   - Other processor-specific settings.

4. **CommonTools**:
   - `Mappings`: Mappings for repositories.

## Example Configuration

Here is an example of the `configuration.json` file:

```json
{
  "Serilog": {
    "MinimumLevel": "verbose"
  },
  "MigrationTools": {
    "Version": "16.0",
    "Endpoints": {
      "Source": {
        "EndpointType": "TfsTeamProjectEndpoint",
        "Collection": "https://dev.azure.com/your-source-collection/",
        "Project": "sourceproject",
        "Authentication": {
          "AuthenticationMode": "AccessToken",
          "AccessToken": "your-source-access-token"
        },
        "ReflectedWorkItemIdField": "Custom.ReflectedWorkItemId",
        "LanguageMaps": {
          "AreaPath": "Area",
          "IterationPath": "Iteration"
        }
      },
      "Target": {
        "EndpointType": "TfsTeamProjectEndpoint",
        "Collection": "https://dev.azure.com/your-target-collection/",
        "Project": "destinationproject",
        "Authentication": {
          "AuthenticationMode": "AccessToken",
          "AccessToken": "your-target-access-token"
        },
        "ReflectedWorkItemIdField": "Custom.ReflectedWorkItemId",
        "LanguageMaps": {
          "AreaPath": "Area",
          "IterationPath": "Iteration"
        }
      }
    },
    "Processors": [
      {
        "ProcessorType": "TfsWorkItemMigrationProcessor",
        "Enabled": false,
        "WIQLQuery": "SELECT [System.Id] FROM WorkItems WHERE [System.TeamProject] = @TeamProject AND [System.WorkItemType] in ('User Story', 'Bug', 'Epic', 'Feature', 'Issue', 'Task') ORDER BY [System.ChangedDate] desc",
        "WorkItemCreateRetryLimit": 5,
        "FilterWorkItemsThatAlreadyExistInTarget": false,
        "SourceName": "Source",
        "TargetName": "Target",
        "LogLevel": "Verbose",
        "ReplayRevisions": true,
        "AttachRevisionHistory": true,
        "GenerateMigrationComment": true,
        "UpdateCreatedDate": true,
        "UpdateCreatedBy": true,
        "FixHtmlAttachmentLinks": true,
        "MaxGracefulFailures": 0,
        "PauseAfterEachWorkItem": false,
        "SkipRevisionWithInvalidAreaPath": false,
        "SkipRevisionWithInvalidIterationPath": false,
        "EnsureTargetWorkItemCreated": false
      },
      {
        "ProcessorType": "TfsTestVariablesMigrationProcessor",
        "Enabled": true,
        "Processor": "TestVariablesMigrationContext",
        "SourceName": "Source",
        "TargetName": "Target"
      },
      {
        "ProcessorType": "TfsTestConfigurationsMigrationProcessor",
        "Enabled": true,
        "SourceName": "Source",
        "TargetName": "Target"
      },
      {
        "ProcessorType": "TfsTestPlansAndSuitesMigrationProcessor",
        "Enabled": true,
        "OnlyElementsWithTag": "",
        "TestPlanQuery": "SELECT [System.Id] FROM TestPlans WHERE [System.TeamProject] = @TeamProject",
        "RemoveAllLinks": false,
        "MigrationDelay": 0,
        "RemoveInvalidTestSuiteLinks": false,
        "FilterCompleted": false,
        "SourceName": "Source",
        "TargetName": "Target"
      }
    ],
    "CommonTools": {
      "TfsGitRepositoryTool": {
        "Enabled": true,
        "Mappings": {
          "RepoInSource": "RepoInTarget"
        }
      },
      "FieldMappingTool": {
        "Enabled": true,
        "FieldMaps": [
          {
            "FieldMapType": "FieldValueMap",
            "ApplyTo": ["User Story"],
            "sourceField": "System.State",
            "targetField": "System.State",
            "valueMapping": {
              "Active": "Approved"
            }
          }
        ],
        "WorkItemTypeMapping": {
          "User Story": "Product Backlog Item"
        }
      },
      "TfsNodeStructureTool": {
        "Areas": {
          "Filters": [],
          "Mappings": {
            "^migrationSource1([\\\\]?.*)$": "MigrationTest5$1"
          }
        },
        "Enabled": true,
        "Iterations": {
          "Filters": [],
          "Mappings": {
            "^migrationSource1([\\\\]?.*)$": "MigrationTest5$1"
          }
        },
        "ReplicateAllExistingNodes": true,
        "ShouldCreateMissingRevisionPaths": true
      },
      "TfsTeamSettingsTool": {
        "Enabled": true,
        "MigrateTeamSettings": true,
        "UpdateTeamSettings": true,
        "MigrateTeamCapacities": true
      },
      "TfsWorkItemLinkTool": {
        "Enabled": true,
        "FilterIfLinkCountMatches": true,
        "SaveAfterEachLinkIsAdded": false
      },
      "TfsRevisionManagerTool": {
        "Enabled": true,
        "ReplayRevisions": true,
        "MaxRevisions": 0
      },
      "TfsAttachmentTool": {
        "RefName": "TfsAttachmentTool",
        "Enabled": true,
        "ExportBasePath": "c:\\temp\\WorkItemAttachmentExport",
        "MaxAttachmentSize": 480000000
      },
      "StringManipulatorTool": {
        "Enabled": true,
        "MaxStringLength": 1000000,
        "Manipulators": [
          {
            "$type": "RegexStringManipulator",
            "Enabled": true,
            "Pattern": "[^( -~)\n\r\t]+",
            "Replacement": "",
            "Description": "Remove invalid characters from the end of the string"
          }
        ]
      },
      "WorkItemTypeMappingTool": {
        "Enabled": true,
        "Mappings": {
          "User Story": "Product Backlog Item",
          "Bug": "Bug",
          "Epic": "Epic",
          "Feature": "Feature",
          "Issue": "Impediment",
          "Task": "Task",
          "Test Case": "Test Case",
          "Test Plan": "Test Plan",
          "Test Suite": "Test Suite"
        }
      },
      "TfsWorkItemEmbededLinkTool": {
        "Enabled": true
      },
      "TfsEmbededImagesTool": {
        "Enabled": true
      }
    }
  }
}
```

Here is an example of the `configuration-pipeline.json` file:

```json
{
  "Serilog": {
    "MinimumLevel": "verbose"
  },
  "MigrationTools": {
    "Version": "16.0",
    "Endpoints": {
      "Source": {
        "EndpointType": "AzureDevOpsEndpoint",
        "AccessToken": "your-source-access-token",
        "AuthenticationMode": "AccessToken",
        "Organisation": "https://dev.azure.com/your-source-organisation/",
        "Project": "sourceproject",
        "ReflectedWorkItemIdField": "Custom.ReflectedWorkItemId"
      },
      "Target": {
        "EndpointType": "AzureDevOpsEndpoint",
        "AccessToken": "your-target-access-token",
        "AuthenticationMode": "AccessToken",
        "Organisation": "https://dev.azure.com/your-target-organisation/",
        "Project": "destinationproject",
        "ReflectedWorkItemIdField": "Custom.ReflectedWorkItemId"
      }
    }
  },
  "Processors": [
    {
      "ProcessorType": "AzureDevOpsPipelineProcessor",
      "BuildPipelines": [],
      "Enabled": true,
      "MigrateBuildPipelines": true,
      "MigrateReleasePipelines": true,
      "MigrateServiceConnections": true,
      "MigrateTaskGroups": true,
      "MigrateVariableGroups": true,
      "ReleasePipelines": [],
      "SourceName": "Source",
      "TargetName": "Target"
    }
  ],
  "CommonTools": {
    "TfsGitRepositoryTool": {
      "Enabled": true,
      "Mappings": {
        "eShopOnWeb": "eShopOnWeb"
      }
    }
  }
}
```

## Troubleshooting

### Error: Value cannot be null. Parameter name: uriString

If you encounter the following error:

```
System.ArgumentNullException: Value cannot be null.
Parameter name: uriString
   at System.Uri..ctor(String uriString, UriKind uriKind)
   ...
```

This error occurs when a URI value is not correctly configured. To resolve this issue, ensure that all necessary URI values are provided in the configuration file.

### Error: ProcessorContainer: Of 0 configured Processors only 0 are enabled

If you encounter the following error:

```
ProcessorContainer: Of 0 configured Processors only 0 are enabled
```

This error occurs when no processors are enabled. To resolve this issue, ensure that the `Enabled` property for the processors is set to `true` in the configuration file.

### Error: TF401019: The Git repository with name or identifier eShopOnWeb does not exist or you do not have permissions for the operation you are attempting.

If you encounter the following error:

```
TF401019: The Git repository with name or identifier eShopOnWeb does not exist or you do not have permissions for the operation you are attempting.
```

This error occurs when the Git repository does not exist in the target project or you do not have the necessary permissions. To resolve this issue:

1. Ensure that the repository exists in the target project.
2. Verify that you have the necessary permissions to access the repository.

### Error: Expecting comparison operator. The error is caused by «\*».

If you encounter the following error:

```
Expecting comparison operator. The error is caused by «*».
```

This error occurs when the WIQL query is not correctly formatted. To resolve this issue, ensure that the WIQL query is valid and correctly formatted.

## Support

If you encounter any issues or have questions, please refer to the documentation or contact support for assistance.

# rcdp
{
  "Id": "DP-resource-uam",
  "Version": "1.2.10",
  "name": "DP Resoure UAM",
  "InputParameters": [
    {
      "name": "RequestNumber",
      "description": "Request number",
      "Type": 0,
      "required": true
    },
    {
      "name": "Environment",
      "description": "Environment of the resource (Production OR Non-Production)",
      "Type": 0,
      "required": true
    },
    {
      "name": "Business",
      "description": "Name of the business for whom this request should be processed.",
      "Type": 0,
      "required": true
    },
    {
      "name": "PrimaryRequestor",
      "description": "Email address of the Primary Requestor",
      "Type": 0,
      "required": true
    },
    {
      "name": "ResourceType",
      "description": "Resource type",
      "Type": 0,
      "required": true
    },
    {
      "name": "ResourceName",
      "description": "Resource Name",
      "Type": 0,
      "required": true
    },
    {
      "name": "User",
      "description": "Comma separated list of users/spns",
      "Type": 0,
      "required": true
    },
    {
      "name": "Role",
      "description": "Role Name",
      "Type": 0,
      "required": true
    }
  ],
  "TargetSubscriptions": [
    "string"
  ],
  "plan": {
    "Deployment": [
      {
        "Id": "resource-uam-sanitize",
        "Mapping": {
          "Input": {
            "Environment": "parameters.Environment",
            "BusinessName": "parameters.Business",
            "PrimaryRequestor": "parameters.PrimaryRequestor",
            "ResourceType": "parameters.ResourceType",
            "ResourceName": "parameters.ResourceName",
            "User": "parameters.User",
            "Role": "parameters.Role"
          },
          "Output": {
            "Environment": "Environment",
            "Business": "BusinessName",
            "PrimaryRequestor": "PrimaryRequestor",
            "Members": "ValidatedMembers",
            "TargetSubscriptionId": "TargetSubscriptionId",
            "SecurityGroupName": "SecurityGroupName",
            "SecurityGroupObjectId": "SecurityGroupObjectId",
            "ResourceType": "AzureResourceType",
            "Scope": "Scope"
          }
        },
        "Name": "Sanitize Inputs",
        "ScriptType": 0
      },
      {
        "Id": "fetch-resource-tag",
        "Mapping": {
          "Input": {
            "PrimaryOwner": "variables.PrimaryRequestor",
            "ResourceType": "variables.ResourceType",
            "ResourceName": "parameters.ResourceName",
            "TagName": "PrimaryRequestor",
            "SubscriptionId": "variables.TargetSubscriptionId"
          },
          "Output": {
            "TagValue": "TagValue",
            "ResourceGroupName": "ResourceGroupName"
          }
        },
        "Name": "Fetch and Validate Resource Tags",
        "ScriptType": 0
      },
      {
        "Id": "validate-role-assignment",
        "Mapping": {
          "Input": {
            "ResourceType": "parameters.ResourceType",
            "AzureResourceType": "variables.ResourceType",
            "ResourceName": "parameters.ResourceName",
            "ResourceGroupName": "variables.ResourceGroupName",
            "ObjectId": "variables.SecurityGroupObjectId",
            "RoleName": "parameters.Role",
            "SubscriptionId": "variables.TargetSubscriptionId",
            "Scope": "variables.Scope"
          },
          "Output": {
            "SGObjectId": "SGObjectId"
          }
        },
        "Name": "Validate Role Assignments",
        "ScriptType": 0
      },
      {
        "Id": "az-pscore-add-sg-members",
        "Mapping": {
          "Input": {
            "SecurityGroupObjectID": "variables.SecurityGroupObjectId",
            "SecurityGroupMembers": "variables.Members"
          },
          "Output": {}
        },
        "Name": "Add members to Security Group",
        "ScriptType": 0
      }
    ],
    "output": {
      "RequestId": {
        "name": "RequestId",
        "value": "parameters.RequestId",
        "description": "Original RequestId from the requestor"
      },
      "ResourceName": {
        "name": "ResourceName",
        "value": "parameters.ResourceName",
        "description": "Name of the deployed resource"
      },
      "Members": {
        "name": "Members",
        "value": "variables.Members",
        "description": "Users/SPNs assigned as Members"
      },
      "SecurityGroupObjectId": {
        "name": "SecurityGroupObjectId",
        "value": "variables.SecurityGroupObjectId",
        "description": "SecurityGroupObjectId"
      },
      "SecurityGroupName": {
        "name": "SecurityGroupName",
        "value": "variables.SecurityGroupName",
        "description": "SecurityGroupName"
      }
    },
    "IsSequential": false
  },
  "SchemaVersion": null,
  "AuthorId": null
}

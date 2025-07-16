# CST 8917 Lab 3

## image of the workflow

![image of the workflow](workflow.PNG)

##

{
    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "contentVersion": "1.0.0.0",
        "triggers": {
            "When_a_new_channel_message_is_added": {
                "type": "ApiConnection",
                "inputs": {
                    "host": {
                        "connection": {
                            "name": "@parameters('$connections')['teams']['connectionId']"
                        }
                    },
                    "method": "get",
                    "path": "/trigger/beta/teams/@{encodeURIComponent('863425d7-9091-4e56-8c22-4bb68f895a2e')}/channels/@{encodeURIComponent('19:NMAq7UESJTiCD0bmJ20fNtPpJCnGBTKQAv0iU9Z826w1@thread.tacv2')}/messages",
                    "queries": {
                        "$top": 50
                    }
                },
                "recurrence": {
                    "interval": 1,
                    "frequency": "Minute"
                },
                "splitOn": "@triggerBody()"
            }
        },
        "actions": {
            "Initialize_variables": {
                "type": "InitializeVariable",
                "inputs": {
                    "variables": [
                        {
                            "name": "message",
                            "type": "string",
                            "value": "@{triggerBody()?['body']?['content']}"
                        }
                    ]
                },
                "runAfter": {}
            },
            "Condition": {
                "type": "If",
                "expression": {
                    "or": [
                        {
                            "contains": [
                                "@variables('message')",
                                "hate"
                            ]
                        },
                        {
                            "contains": [
                                "@variables('message')",
                                "hated"
                            ]
                        },
                        {
                            "contains": [
                                "@variables('message')",
                                "suck"
                            ]
                        }
                    ]
                },
                "actions": {
                    "Send_an_email_(V2)": {
                        "type": "ApiConnection",
                        "inputs": {
                            "host": {
                                "connection": {
                                    "name": "@parameters('$connections')['outlook']['connectionId']"
                                }
                            },
                            "method": "post",
                            "body": {
                                "To": "balc0022@algonquinlive.com",
                                "Subject": "someone has inaproprate behavor in the team chat",
                                "Body": "<p class=\"editor-paragraph\">someone has inaproprate behavor in the team chat and say \"@{variables('message')}\"</p>",
                                "Importance": "Normal"
                            },
                            "path": "/v2/Mail"
                        }
                    }
                },
                "else": {
                    "actions": {}
                },
                "runAfter": {
                    "Initialize_variables": [
                        "Succeeded"
                    ]
                }
            }
        },
        "outputs": {},
        "parameters": {
            "$connections": {
                "type": "Object",
                "defaultValue": {}
            }
        }
    },
    "parameters": {
        "$connections": {
            "type": "Object",
            "value": {
                "teams": {
                    "id": "/subscriptions/3b36c431-a92e-4ac8-927b-17a0f4b30054/providers/Microsoft.Web/locations/eastus/managedApis/teams",
                    "connectionId": "/subscriptions/3b36c431-a92e-4ac8-927b-17a0f4b30054/resourceGroups/cst8919-lab3/providers/Microsoft.Web/connections/teams-1",
                    "connectionName": "teams-1"
                },
                "outlook": {
                    "id": "/subscriptions/3b36c431-a92e-4ac8-927b-17a0f4b30054/providers/Microsoft.Web/locations/eastus/managedApis/outlook",
                    "connectionId": "/subscriptions/3b36c431-a92e-4ac8-927b-17a0f4b30054/resourceGroups/cst8919-lab3/providers/Microsoft.Web/connections/outlook-2",
                    "connectionName": "outlook-2"
                }
            }
        }
    }
}
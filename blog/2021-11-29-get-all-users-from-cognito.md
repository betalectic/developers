---
slug: how-get-list-of-all-users-from-AWS-cognito-Userpool
title: How get list of all Users from AWS cognito Userpool
authors: [rajesh]
tags: [AWS, cognito, cognito userpool, listUsers]
---

Sometime you might need the list of users from AWS cognito userpool,
so lets discuss how to do it?
so first things is how do we check users in cognito userpool?

You can get the list of all users with `listUsers` function of `CognitoIdentityServiceProvider`

    const AWS = require("aws-sdk");


    function getCognitoUsers(){
        var params = {
            UserPoolId: Aws_User_Pool_Id,
        };

        var cognitoidentityserviceprovider = new AWS.CognitoIdentityServiceProvider();

        return cognitoidentityserviceprovider
        .listUsers(params)
        .promise()
        .then(async (response) => {
            return response.Users
        })
        .catch((error) => {
            console.log("cognito error", error);
        });
    }

But `listUsers` function have some limitations it gives only `60` users in response. But good thing is that it gives
`PaginationToken` with the response and with that pagination token you can get next `60` users from cognito userool.

So to get the all users from cognito userpool, you have to call `listUsers` function with `PaginationToken` in params.
Here an example how i did it with recursive function, to get all the users from cognito userpool:

    const AWS = require("aws-sdk");

    // let users = [];
    async function getAllCognitoUsers(paginationToken = ""){
        var params = {
            UserPoolId: Aws_User_Pool_Id,
        };

        if (paginationToken !== "") {
         params.PaginationToken = paginationToken;
        }

        var cognitoidentityserviceprovider = new AWS.CognitoIdentityServiceProvider();

        return cognitoidentityserviceprovider
        .listUsers(params)
        .promise()
        .then(async (response) => {
            if (response.Users.length < 60) {
            users.push(response.Users)
            } else {
            users.push(response.Users);
            await getAllCognitoUsers(response.PaginationToken);
            }
        })
        .catch((error) => {
            console.log("cognito error", error);
        });
    }

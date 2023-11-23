---
layout: post
title:  "Terraform, AzApi and Parallelism"
date:   2023-11-22 14:40:00 +0000
categories: technology
---

For the past few months I've been buried neck deep in Terraform, pipelines and such - building large amounts of Infrastructure as Code, simplifying life for engineers where possible (but not me lol) which is why I've been fairly quiet.

But I hit something recently that I thought I'd mention, as it was a bit of a lightbulb moment for me and the team ... despite it really being under our noses all the time, we completely missed it due to the poor documentation that exists - something that so many Terraform providers seem to suffer with.

We have a problem where we were using the Microsoft Azure AzAPI provider to manipulate subnets (something we are having to do due to the number of policies applied to our tenant, meaning that the actual azurerm_subnet resource can not apply due to it not applying in a single atomic ARM operation, whereas we can do this directly against the API ourselves - this is honestly an interesting limitation on the AzureRM provider IMO) but any deployment that had multiple subnets in obviously was causes issues with parallelisation given you can not have multiple operations against the same vnet occurring at the same time. 

Obviously the simple solution would be to drop apply and destroy paralleisation down to 1, slowly the entire deployment and destroy runs down significantly given everything would then be serialised unnecessarily.

But there was another solution that came to light after poking through the repository code for AzAPI.  The code included mutex handling, which made me realise it HAD to support locking and prevention of multiple operations in some way. After going over the documentation again, there is a property called locks which takes a resource ID. This is a little ambiguously named, given there are resource types called locks in Azure, and the small amount of description with this property doesn't exactly give much insight into what it does under the covers - and there are no examples of it in use.

Essentially, it seems, you pass in resource id's to the lock list, a mutex is taken against this name and therefore any following operations looking for the same mutex will be blocked until the preceding one completes - therefore if, in our case, you are manipulating subnets, you pass in the vnet reference and ... problem solved. Enforced serialisation for only the operations that need it against the single resource that can not support parallel operations.
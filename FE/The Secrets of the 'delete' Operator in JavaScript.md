---
title: The Secrets of the 'delete' Operator in JavaScript  
date: 2024-12-27T16:53:00  
lastMod: 2024-12-27T16:53:00  
tags: [Javascript]  
category: 笔记  
summary: The Secrets of the 'delete' Operator in JavaScript
---

TL;DR: The real purpose of the ‘delete operator’ in JavaScript is to delete a property reference of an object. It can behave strangely in some special cases, so it’s best to only use it to remove configurable properties that exist on the object itself. 

Additionally, when you remove a property from an object, and this property’s value is an object without any remaining references, the JavaScript runtime will eventually automatically free the object owned by that property.

source: [https://webdeveloper.beehiiv.com/p/secrets-delete-operator-javascript](https://webdeveloper.beehiiv.com/p/secrets-delete-operator-javascript)

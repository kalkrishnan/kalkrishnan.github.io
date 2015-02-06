---
layout: post
title: "Angular JS Introduction"
description: ""
category: "angular"
---

##Salient features:
>

*Based on MVC pattern

*Scopes hold your Models (that's your data), they cooperate with your Controllers, and they give the Views everything they need (that's what the user sees and interacts with) e.g ng-app, ng-controller, ng-repeat

*ng-model used for real time updates of fields

*filters are used to filter fields according to regex:
>
*var keyword = new RegExp($scope.nameFilter, 'i');
    return !$scope.nameFilter || keyword.test(driver.Driver.givenName) || keyword.test(driver.Driver.familyName);
};
*'i' is used in RegExp to perform case insensitive comparisons

*Routes are used to route urls to new pages

*ng-view to generate partial views on pages

*Strings must use single quotes

*ng-show display if present

*ng-keyup detect keystrokes

*ng-cloak hide script variables on page load

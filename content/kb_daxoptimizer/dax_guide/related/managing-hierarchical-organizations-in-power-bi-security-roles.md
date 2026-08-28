---
title: "Managing hierarchical organizations in Power BI security roles"
url: "https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles"
published: "2023-08-28"
updated: 
source: "sqlbi.com"
---

# Managing hierarchical organizations in Power BI security roles

> 发布：2023-08-28

The security model in Tabular used by Power BI can filter rows of a table based on a DAX expression. When security is applied to a hierarchical structure, every hierarchy level is represented by a different column in the table. This structure can make it challenging to define a dynamic security filter based on the name of a node in the hierarchy, because the DAX expression must filter the column corresponding to the hierarchical level in which that node exists. If the security needs to be maintained dynamically in a configuration table, the resulting code may end up being extremely complex and hard to maintain, as well as create possible performance issues.

Without describing the complexity of solutions based on a filter applied directly to the appropriate hierarchy level, we want to describe a solution that minimizes the effort required in maintaining a configuration table for the dynamic security rules, while also providing good performance at execution time by minimizing the processing overhead required to apply the dynamic security.

## Scenario

The *Organization* table has one row for each employee. Each employee is identified by their company email and reports to a manager. Because there is a hierarchy of managers that can have up to six levels, the columns from *Level1* to *Level6* show the path of managers above each employee, one for each level. For example, as shown below, [margarita@contoso.com](mailto:margarita@contoso.com) reports to Mary, a top manager at Level1, whereas [lori@contoso.com](mailto:lori@contoso.com) reports to Adele, who reports to Sarah, who reports to Stephen, who reports to Mary. As a manager, Mary can access both [margarita@contoso.com](mailto:margarita@contoso.com) and [lori@contoso.com](mailto:lori@contoso.com), but cannot access [adeline@contoso.com](mailto:adeline@contoso.com) because Adeline reports to Russell, who reports to John.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-61.png)

The security requirements define access based on a manager node: a user that can access the data of a manager must also access all the employees who report to that manager (like [margarita@contoso.com](mailto:margarita@contoso.com), who reports directly to Mary), directly and indirectly (like [lori@contoso.com](mailto:lori@contoso.com) who reports directly to Adele and reports indirectly to Mary).

Because different managers may have the same name, a manager must be identified with the complete path: for example, Russell is identified with the string John|Russell, whereas Adele is identified with Mary|Stephen|Sarah|Adele. This information is available in the *ManagerID* column of the *Organization* table.

## Implementing the security data model

The security configuration stored in the *Permissions* table contains the minimum information required: the user identifier and the *ManagerID*. For example, [marco@contoso.com](mailto:marco@contoso.com) has access to three managers: John, Heather, and Mikil. Please note that Mikil does not exist in the *Organization* table: this is a configuration error that we will analyze later in the article (the correct name would be Nikil, with an N instead of an M).

![](https://cdn.sqlbi.com/wp-content/uploads/image2-58.png)

If the manager’s name is not unique, an alternative way to identify the manager is through the complete path to reach the manager node. In this scenario, the three managers to which [marco@contoso.com](mailto:marco@contoso.com) has access are John, Peter|Heather, and Jeff|Mikil.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-51.png)

The goal is to provide access to all the employees that report to any of these three managers directly or indirectly. For example, the following picture highlights all the direct managers of the employees accessible to [marco@contoso.com](mailto:marco@contoso.com).

![](https://cdn.sqlbi.com/wp-content/uploads/image4-46.png)

The same configuration we have seen before also specifies that [alberto@contoso.com](mailto:alberto@contoso.com) can access all the employees that report to Mary and Stephen (or John|Russell|Stephen) directly or indirectly.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-39.png)

The *Permissions* table minimizes the administrative effort, minimizing the amount of input data to define the security rules. However, querying this table at runtime using complex DAX code in the security rules would be inefficient because we should look for the *ManagerID* as a partial string in the *Organization* table. To avoid that, we expand the list of managers below each *ManagerID* specified in the *Permissions* table. Here is the code of the *PermissionExpanded* calculated table by using the unique manager names:

```dax


PermissionsExpanded = 

GENERATEALL (

    SELECTCOLUMNS ( 

        Permissions,

        "User", Permissions[User],

        "AssignedManagerID", Permissions[ManagerID]

    ),

    VAR _AssignedManagerID = [AssignedManagerID]

    RETURN 

        CALCULATETABLE (

            DISTINCT ( Organization[ManagerID] ),

            REMOVEFILTERS(),

            PATHCONTAINS ( Organization[ManagerID], _AssignedManagerID )

)

```

The code required when *ManagerID* identifies a node through its complete path differs in only one line, where [[LEFT]] is used instead of [[PATHCONTAINS]]:

```dax


PermissionsExpanded = 

GENERATEALL (

    SELECTCOLUMNS ( 

        Permissions,

        "User", Permissions[User],

        "AssignedManagerID", Permissions[ManagerID]

    ),

    VAR _AssignedManagerID = [AssignedManagerID]

    RETURN 

        CALCULATETABLE (

            DISTINCT ( Organization[ManagerID] ),

            REMOVEFILTERS(),

            LEFT ( Organization[ManagerID], LEN ( _AssignedManagerID ) ) = AssignedManagerID

        )

)

```

The result has a larger number of rows compared to the original *Permissions* table. Still, the *Permissions[ManagerID]* column has all the possible values that could be an exact match in the *Organization* table for the security role.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-34.png)

Because the *PermissionsExpanded* table contains both the username (*Permissions[User]*) and the managers that should be visible (*Permissions[ManagerID]*), this is also the table to filter in a dynamic security role. In order to transfer the security filter to *Organization* more efficiently, we create a many-to-many cardinality relationship that propagates the filter from *PermissionsExpanded* to *Organization*.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-26.png)

It is important to control the direction of the propagation of the filter. Because none of the two relationship endpoints are unique in the underlying table, by default Power BI proposes the many-to-many cardinality with a bidirectional filter. Do not accept that bidirectional filter: you want a single-direction filter from *PermissionsExpanded* to *Organization*.

![](https://cdn.sqlbi.com/wp-content/uploads/image8-21.png)

The ManagerView security role can be implemented with a straightforward condition for the *PermissionsExpanded* table: *PermissionsExpanded[User] == [[USERNAME]]()*.

![](https://cdn.sqlbi.com/wp-content/uploads/image9-20.png)

The minimal amount of DAX executed in the security filter guarantees good performance. A large organization with thousands of employees and hundreds of managers will generate many rows in *PermissionsExpanded*, but this is not a big issue from a performance standpoint. Indeed, the maximum cardinality of the security filter is the number of unique values in *ManagerID*, and the implementation made through the relationship in the model minimizes the effort required from the formula engine as well as the related materialization. Most of the execution cost is born by the storage engine, which could also take advantage of other optimizations like bitmap indexes and a storage engine cache.

## Detecting errors in the configuration

The *Permissions* configuration table must have the exact path that identifies the hierarchy node (the manager) to enable in the dynamic security configuration. If there is a typo in the path, the manager is not enabled, and the security does not work as expected. To identify errors in *Permissions[ManagerID]*, it is possible to create a report by displaying a table with the *PermissionsExpanded[User]* and *PermissionsExpanded[AssignedManagerID]* columns filtered by *PermissionsExpanded[ManagerID]* with a blank value.

For example, we intentionally assigned to [marco@contoso.com](mailto:marco@contoso.com) a manager that does not exist in the *Organization* table, which is correctly reported as a configuration error.

![](https://cdn.sqlbi.com/wp-content/uploads/image10-17.png)

## Conclusions

You may efficiently implement the dynamic security configuration for a hierarchical organization, by reducing the administrative cost of maintaining a configuration table and expanding the security rule into a table that minimizes the DAX code required to implement the security. By relying on the data model and the filter context propagation through relationships, we can also achieve optimal performance at query time with a small price in memory for the additional calculated table.

[[LEFT]]

Returns the specified number of characters from the start of a text string.

`LEFT ( <Text> [, <NumberOfCharacters>] )`

[[PATHCONTAINS]]

Returns TRUE if the specified Item exists within the specified Path.

`PATHCONTAINS ( <Path>, <Item> )`

[[USERNAME]]

Returns the domain name and user name of the current connection with the format of domain-name\user-name.

`USERNAME ( )`

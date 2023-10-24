# Application User Roles

_Application User Roles_ are a feature that lets you define various roles you can assign to your users, or they can choose between themselves.

You might have parts of your map that can only be accessed by employees, so you define "Employee" and "Visitor" roles. When using the application to search for directions, users assigned to the "Visitor" role may be shown a different route from "Employee" users based on what they have access to.

### How to Configure App User Roles[​](https://docs.mapsindoors.com/app-user-roles#how-to-configure-app-user-roles) <a href="#how-to-configure-app-user-roles" id="how-to-configure-app-user-roles"></a>

App User Roles are configured via the MapsIndoors CMS. Go to `Solution Details > App Settings > App Configuration`, and find `App User Roles` on the page. Here, you can configure existing roles, and add new ones.

Click `Add App User Role` and enter the name of the newly created Role in all defined languages for your Solution.

### How to Assign/Change a Role to a User[​](https://docs.mapsindoors.com/app-user-roles#how-to-assignchange-a-role-to-a-user) <a href="#how-to-assignchange-a-role-to-a-user" id="how-to-assignchange-a-role-to-a-user"></a>

Assigning or changing App User Roles to users is done in the app itself. The method depends on which platform you're developing for. Here are some examples:

To get the available Roles in the Web SDK, you use `SolutionsService`:

```javascript
mapsindoors.services.SolutionsService.getUserRoles().then(userRoles => {
  console.log(userRoles);
});
```

User Roles can be set on a global level using `mapsindoors.MapsIndoors.setUserRoles()`.

```javascript
mapsindoors.MapsIndoors.setUserRoles(['myUserRoleId']);
```

> For more information, see the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html#.setUserRoles).

### Features Affected by App User Roles[​](https://docs.mapsindoors.com/app-user-roles#features-affected-by-app-user-roles) <a href="#features-affected-by-app-user-roles" id="features-affected-by-app-user-roles"></a>

The App User Roles are useful for setting limits on who can find certain Locations. App User Rules influence the map in three ways; which Locations are displayed on the map, whether they show up in search results, and the directions you can get.

For any Location defined on the map, there is a menu named `Restrictions`, where you are presented with options for limiting functionality certain App User Roles.

![app user role restrictions](https://docs.mapsindoors.com/img/map/app-user-role-restrictions.png)

* **Open for all** - All users can view, search for, and get directions to this Location.
* **Open for specific App User Roles** - Select which App User Roles have access to viewing, searching and getting directions to this specific Location.
* **Closed for All** - No users will see this Location on the map.

#### The Map[​](https://docs.mapsindoors.com/app-user-roles#the-map) <a href="#the-map" id="the-map"></a>

If a Location has been restricted to certain App User Roles, it will not be displayed on the map for those who do not have permission.

#### Search[​](https://docs.mapsindoors.com/app-user-roles#search) <a href="#search" id="search"></a>

Similarly to the effect on the map, if a Location has restrictions, it will not show up in the search results for users without sufficient permissions.

#### Directions[​](https://docs.mapsindoors.com/app-user-roles#directions) <a href="#directions" id="directions"></a>

App User Roles can be used to determine the directions users get. For instance, you can restrict Doors between two Locations to only be passable by a certain App User Role.

Note: If you restrict a Room to only be accessible to certain App User Roles, you restrict both directions _to_ and _through_ that Room. It effectively sets restrictions on all Doors leading to that Room as well.

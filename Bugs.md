1) Permission Issues for non admin - MEDIUM severity as it occuring only on first attempt can be considered as Data Integrity issue.

When logging in as arjun@taskboard.dev and  kavya@example.com for the first time the app Dashboard shows all the 3 modules initially even when the user doesn't have the permission to those but it should show only the tasks where the user is invited and has permission. 

2) Viewer is able to edit the task details - High Severity as the system is allowing to edit the tasks status and assignee. It can be considered as both security and data integrity issue.

When logging in as viewer role the app is allowing the user to edit the tasks but viewer role user should not be allowed to edit any details of the tasks.

3) Assertion Issue. Medium severity and be considered as security issue.
In the codebase part2.test.ts file line number 70 the expectation should be to receive the 403(forbidden) error instead the code is expecting 401(unauthorized). As the viewer is authorised to access the tasks but not create any tasks.

4) No tasks shows up for admin after an user is registered.High Severity issue and can be considered as Data integrity issue.

When admin user logs in after an registration of other user, the app doesn't show all the tasks which are availabe for admin user but it should show all the assigned tasks to the admin user